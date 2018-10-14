---
title: Play Around with Chef Install on New EC2 Instance
abbrlink: d299683d
date: 2015-12-10 20:28:51
tags:
---
#### GitHub Connection Issues

Not sure why server's SSH connection use the different algoritems for GitHub connection

References:

- http://www.openssh.com/legacy.html
- https://stribika.github.io/2015/01/04/secure-secure-shell.html
- https://developer.github.com/guides/using-ssh-agent-forwarding

How to Resolve:

```bash
# ~/.ssh/config Add here

Host github.com
    KexAlgorithms curve25519-sha256@libssh.org,diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1

Host *
    KexAlgorithms curve25519-sha256@libssh.org,diffie-hellman-group-exchange-sha256
```

#### MySQL Database Socket Path is not Default to `/tmp/mysql.sock`

How to Resolve:

```bash
$ mysqladmin variables -u root -p | grep sock
```

#### MySQL Securities

Since MySQL default to root users, we should provide different roles for specific authorizations

By preventing application to access root auth, less impact will be caused by database being compromised

#### Colorful Terminal

```bash
# touch ~/.bash_aliases
PS1='\[\033[1;33m\]\u\[\033[1;37m\]@\[\033[1;32m\]\h\[\033[1;37m\]:\[\033[1;31m\]\w\[\033[1;36m\]\$ \[\033[0m\]'
```

#### NGINX Always Showing the Default Page

How to Resolve:

`cd etc/nginx/sites-available` delete `default` file

`sudo vi etc/nginx/nginx.conf` delete `include` command under  `default`
