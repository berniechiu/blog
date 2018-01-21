---
title: Installing PostgresDB on Mac
date: 2016-01-31 20:39:27
tags:
---
### Open a Termianl

As a developer, you either user [iTerm2](https://www.iterm2.com/) or built-in Terminal very often. Both of them are fine.

### Install Homebrew

[Homebrew](http://brew.sh/)
We'll will start installing Postgres through homebrew, which is a package manager for OSX. Just follow the steps on the official website.

### Brew Install Postgres

Enter `brew install postgres` to tell brew to install PostgreSQL for you. You'll see messages to tell you start the postgres now, but I suggest to manage our background progress in another way.

### Install LaunchRocket

![lauch-rocket-sample-image.png](/blog/images/launch-rocket-sample-image.png)

I believe [LaunchRocket](https://github.com/jimbojsb/launchrocket) is great for managing brew-installed services, all you need to do is to click start and the service will be up. Becuase I use my personal laptop for many things, I don't want most of the service to run automatically at startup to eat up my memory. Therefore, this is a great tool to work with.

### Create a Default DB Based on Your Username

Since Postgres use the username as a default DB entrance. Type `createdb 'whoami'` in your terminal to make sure you can have a database to enter and test.

### Try Out the psql Command

Try out `psql` in the terminal. If you enter the command-line interface to PostgreSQL, then you're good to go!
