---
title: How to Start with Resque
date: 2013-11-12 19:31:39
tags:
---
Start the Resque Scheduler

```bash
$ bundle exec rake resque:scheduler
```

Start the Resque Worker for All Queues

```bash
$ QUEUE=* bundle exec rake environment resque:work
```
