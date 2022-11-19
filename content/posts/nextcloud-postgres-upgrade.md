---
title: "Postgres major version upgrade \U0001F4C2\U0001F525 (containerized with Docker)"
summary: "This summarizes my experience with upgrading Postgres for my containerized Nextcloud instance."
date: 2020-09-15T11:30:03+00:00
# aliases: ["/pg-upgrade"]
tags: ["nextcloud", "postgres", "container", "docker"]
author: "Michael Martins"
description: "Nextcloud Postgres upgrade."
---

![spiderclamps](spiderclamps.jpeg)

## Motivation
Truth be told. How often do you update your home setup? I often have the feeling I should upgrade my home server running Nextcloud. But then there comes out of the blue an
unexpected roadblock. And all the good intentions are blown away by frustation.

This post was the reason for me to start my blog. Because I already started to upgrade my Nextcloud setup a couple of month ago. This project directly led me to run into a roadblock. It was called Postgres.

If you ever had to get your hands dirty with `pg_dump` or `pg_dumpall` you may guess what my struggle was. You may ask yourself why I struggled with this. Well, recently a valued colleague of mine pointed out that doing a major Postgres upgrade does not necessarily need to involve aforementioned tools. He said it should be possible to simply bump the major version and let Postgres modify the data. I felt a bit stupid but than I remembered why I wanted to do my upgrade using a dump of the database.

Because when I do a major version upgrade of my precious database I want to make sure if something goes wrong, for whatever reason, I can restore a backup and ensure everything works as before. So I not just want to have an upgrade I also want a validated backup and restore.

### Concept
So with this preconditions I had to decide between `pg_dump` and `pg_dumpall`. Which seemed to be easy at first glance but turned out o be tricky.
Use pg_dump to create a dump of your database and pg_restore to restore it. Easy peasy, but! if you now think - as I foolishly did - you can do the same with pg_dumpall you are so wrong!
What I wanted to have was a convenient dumping of my database:
>_pg_dump dumps only a single database at a time, and it does not dump information about roles or tablespaces (because those are cluster-wide rather than per-database). To support convenient dumping of the entire contents of a database cluster, the pg_dumpall program is provided. pg_dumpall backs up each database in a given cluster, and also preserves cluster-wide data such as role and tablespace definitions._

source: [postgresql docs](https://www.postgresql.org/docs/current/backup-dump.html#BACKUP-DUMP-ALL)

So pg_dumpall it is. But how can I restore it now without having the benefits of pg_restore? Especially in my containerized environment!
A second Postgres container needs to be deployed. It will have its own volume and the desired Postgres version. In this setup you will have the benefit of never changing your current data. The current data will be dumped and restored into the separate database. Afterwards you point to the database and check if everything works. If not you still have the old database and can switch back anytime.

```yaml
   db_postgres_14:
    image: postgres:14.6-alpine
    restart: always
    volumes:
      - <NEW_VOL>:/var/lib/postgresql/data
    env_file:
      - db.env
```
>Note that the mountpoint is important

### Preparation
Before you upgrade any component of your Nextcloud stack do a backup. Especially if you touch the database. I can recommend BorgBackup https://www.borgbackup.org as it is simple, efficient and reliable.
Turn on maintenance mode so there will be no writes to the database:

```sh
docker-compose exec --user www-data app php occ maintenance:mode --on
```

#### Dump Data
Get your data out of the running database container:

```sh
docker-compose exec -T YOUR-DB-CONTAINER \
  pg_dumpall --clean -U postgres --quote-all-identifiers \
  > /PATH/TO/DUMP/pgdumpall_2022-11-01.sql
```
For the pg_dumpall parameters look at [postgresql docs](https://www.postgresql.org/docs/current/backup-dump.html#BACKUP-DUMP-ALL)

#### Copy Dump Into New DB
>There are multiple options to get the data into the new database but I decided
to copy it into the volume that is mounted into the container.

```sh
docker cp pgdumpall_2022-11-01.sql NEW-DB_postgres_14:/var/lib/postgresql/data
```

#### Restore Data
And insert it into the newly created database:
```sh
docker-compose exec NEW-DB_postgres_14 \
  psql -U postgres -f /var/lib/postgresql/data/pgdumpall_2022-11-01.sql
```

