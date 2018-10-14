---
title: Switching to puma-dev on New MacOS
abbrlink: add7b875
date: 2018-04-22 12:47:58
tags:
---
Since I'm getting a new Mac recently, I have realized that [pow](http://pow.cx/manual.html#section_6) is no longer being maitained and lack of several features like SSL support and WebSockets. I think it's time to switch for a better alternative at this time being.

## Getting Puma-Dev on MacOS

### Install Puma-Dev

```bash
$ brew install puma/puma/puma-dev
```

### Setup

```bash
$ sudo puma-dev -setup
```

### Link Puma-Dev Application

```bash
$ cd /your/app/path
$ puma-dev link
```

### Start Puma-Dev Server

```bash
$ puma-dev -d localhost

#=> * Directory for apps: /Users/user/.puma-dev
#=> * Domains: localhost
#=> * DNS Server port: 9253
#=> * HTTP Server port: 9280
#=> * HTTPS Server port: 9283
```

Note: [Why using default .dev is an issue](https://ma.ttias.be/chrome-force-dev-domains-https-via-preloaded-hsts/)

### SSL Certificate Issue

If you encounter SSL issue in browers, add the trusted cert in your MacOS

```bash
security add-trusted-cert -k login.keychain-db ~/Library/Application\ Support/io.puma.dev/cert.pem
```

