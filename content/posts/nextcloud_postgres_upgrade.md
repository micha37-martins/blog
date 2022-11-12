---
title: "Postgres running containerized using Docker major version upgrade Nextcloud"
summary: "This summarizes my experience with upgrading Postgres for my containerized Nextcloud instance."
date: 2020-09-15T11:30:03+00:00
# aliases: ["/nc-pg-upgrade"]
tags: ["nextcloud", "postgres"]
author: "Michael Martins"
description: "Nextcloud Postgres upgrade."
---
## How to upgrade Postgres major version

This post was the reason for me to start my blog. Because I already started to upgrade my Nextcloud setup a couple of month ago. This project turned out to run into a roadblock. It is called Postgres.
If you ever had to get your hands dirty with pg_dump or pg_dumpall you may guess what my struggle was. You may ask why I struggled with this. Well good question. Recently a valued colleague of mine pointed out that doing a major Postgres upgrade does not necessarily need to involve aforementioned tools as it should be possible to simply bum the major version and let Postgres modify the data. I felt a bit stupid but than I remembered why I wanted to do my upgrade using a dump of the database.
Because when I make a major version upgrade of my precious database I want to make sure if something went wrong for whatever reason I can restore a backup and ensure everything works as before. So I want not just an upgrade I also want a validated backup and restore.

### Concept
So with this preconditions I had to decide between pg_dump and pg_dumpall. 

### Preparation
Before you upgrade any component of your Nextcloud stack do a backup. Especially if you touch the database. I can recommend BorgBackup https://www.borgbackup.org as it is simple, efficient and reliable.

### 
