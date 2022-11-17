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

This post was the reason for me to start my blog. Because I already started to upgrade my Nextcloud setup a couple of month ago. This project directly let me to run into a roadblock. It was called Postgres.

If you ever had to get your hands dirty with `pg_dump` or `pg_dumpall` you may guess what my struggle was. You may ask yourself why I struggled with this. Well, recently a valued colleague of mine pointed out that doing a major Postgres upgrade does not necessarily need to involve aforementioned tools as it should be possible to simply bump the major version and let Postgres modify the data. I felt a bit stupid but than I remembered why I wanted to do my upgrade using a dump of the database.

Because when I do a major version upgrade of my precious database I want to make sure if something went wrong for whatever reason I can restore a backup and ensure everything works as before. So I want not just an upgrade I also want a validated backup and restore.

### Concept
So with this preconditions I had to decide between `pg_dump` and `pg_dumpall`. Which seemed to be easy at first glance but turned out o be tricky. Because both have its pros and cons.
Use `pg_dump` to create a dump of your database und `pg_restore` to restore it. Easy peasy, but! if you now think - as I foolishly did - you can do the same with `pg_dumpall` you are so wrong! I wanted to have a full backup of my database including all users so my way was `pg_dumpall` but how can I now restore it? Especially in my containerized environment.

### Preparation
Before you upgrade any component of your Nextcloud stack do a backup. Especially if you touch the database. I can recommend BorgBackup https://www.borgbackup.org as it is simple, efficient and reliable.

### 
