---
title: Allow Two Rails Servers Running on the Same Session
abbrlink: ceb8c668
date: 2013-09-11 19:11:17
tags:
---
Rails server runs the `development` server on default, in order to open another one for HTTP communication test, we need to apply few options on `rails server` command.

```bash
$ RAILS_ENV=production rails s --port=4000 --pid /home/bernie/orbit_intern/orbit/tmp/pids/server_p.pid
```
