---
title: Common MongoDB Commands for Restore and Backup
abbrlink: f8543605
date: 2013-12-27 19:53:55
tags:
---
One-line backup command for MongoDB
```bash
mongodump --db <dbname>
```

One-line drop command for MongoDB Database.
```bash
$ mongo <dbname> --eval "db.dropDatabase()"
```

Then, we could easily resotre the backup folder that contains both BSON and JSON files.
```bash
$ mongorestore <folder_name> --db <dbname>
```
