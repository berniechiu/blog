---
title: How to Build And Install Local Ruby Gem
date: 2013-10-06 19:27:53
tags:
---
### Build Local Gem

Sometimes it's really neat to customize or fork some Gems from GitHub, and this is how I install my modified version of a Gem locally and install it at will.

```bash
$ gem build bootstrappers.gemspec
Successfully built RubyGem
Name: bootstrappers
Version: 0.2.3
File: bootstrappers-0.2.3.gem
```

### Insall Local Gem

```bash
$ gem install ./bootstrappers-0.2.3.gem
Successfully installed bootstrappers-0.2.3.gem
1 gem installed
```
