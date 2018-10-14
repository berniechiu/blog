---
title: Tips for Installing Oh My ZSH on Mac with Up to Date Version
abbrlink: '228462e4'
date: 2014-02-06 20:02:56
tags:
---
In order to reduce the frustration that I've encountered while installing [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) on my new MacBook, I decided to write down this post to remind me of some steps that I need to beware.

## Install ZSH from Homebrew

```bash
$ brew install zsh
```

## Make sure the ZSH is running the Homebrew version, not the built-in one

```bash
$ which zsh
```

Should return the path `/usr/local/bin/zsh` not `/bin/zsh`

## Install [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

```bash
$ curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```

## Under `/.zshrc` file, we should add few more lines that include the following

```bash .zshrc
[[ -s "$HOME/.profile" ]] && source "$HOME/.profile" # Load the default .profile
export PATH="/usr/local/bin:$PATH" # Load the Homebrew version of commands
```

Run `brew doctor` if encounter any further problems, it will tell us what to do.
