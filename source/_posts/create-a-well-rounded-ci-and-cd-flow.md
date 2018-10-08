---
title: Create a Well-Rounded CI/CD Flow
date: 2018-10-08 22:37:26
tags: devops
keywords: devops,rails,continuous delivery, continuous deployment
description:
---

![PaGamO CI/CD Flow Chart](/blog/images/bonio-ci-cd-flow-chart.png)

Long time ago, I joined my previous company [PaGamO](https://www.pagamo.org/) and the deployment is still raw, using `git clone` and some manual restart script specifically for the project, so I decided to give it a change.

The above figure is the first draft for my redesign CI/CD, also with the help of few gems like:

- [Asset Sync](https://github.com/AssetSync/asset_sync)
- [Capistrano](https://github.com/capistrano/capistrano)

Of course, those are common gems used in Rails community. As a Senior Software Engineer, I always seek challenges to automate as much as I could and use tools to resolve problems ahead to improve team performance.

Besides the tech part of introducing tools and revamped workflow, keys to achieve such agreement are always about communication between teams.

Like QA needs to know about tasks at what certain point of time needs to be tested, understanding the new task assignment changes without too much communication effort.

Because a good workflow speaks for itself, everyone should know what to do when an assignment changes and what the status of the task is meant for each member. Therefore, teams can accomplish their jobs easily. As a Scrum Lead, I should always keep track of progress but not by asking team members, but using tools and job board to quickly ship and track products automatically.
