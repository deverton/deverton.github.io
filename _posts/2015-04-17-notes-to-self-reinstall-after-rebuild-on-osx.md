---
title: Notes To Self: Reinstall After Rebuild on OS X
layout: post
category: posts
tags: [notes, osx, homebrew]
---

For various reasons I've had to reinstall from scratch all the software
on my work MacBook a lot lately. These are some notes on how I get back
to usable state from a clean OS X install. I can't imagine this is super
useful to other people, so consider this skippable content.

Set up Homebrew
---------------

[Homebrew](http://brew.sh/) is a package manager that makes it
significantly easier to get software installed on OS X in a simple way.
With some extensions [Homebrew Cask](http://caskroom.io/), it can also
managed GUI installers. This will also install the command line complier
tools from XCode.

```shell
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
xcode-select --install
brew install caskroom/cask/brew-cask
```

Get a Nicer Terminal Environment Going
-------------------------------------

Even OS X Yosemite ships with Bash 3.2 which is a little old for my
taste. I also much prefer [iTerm 2](http://iterm2.com/) to the built-in
Terminal application. Once those are installed we can also get our
dotfiles going by cloning from Github and using
[dfm](http://endot.org/projects/dfm/).

```shell
brew install bash
brew cask install iterm2
git clone git@github.com:deverton/dotfiles.git ~/Documents/dotfiles
cd ~/Documents/dotfiles
git checkout deverton
git submodule init
git submodule update
dfm install
```

To make the new Bash the default login for our user we need to tell OS X
about it.

```shell
sudo -c "echo '/usr/local/bin/bash' >> /etc/shells"
sudo -c "dscl . change /users/$USER UserShell /bin/bash /usr/local/bin/bash"
```

Now start iTerm2 and all my customisations should be back.

Manage Installed Ruby Versions Sanely
-------------------------------------

In the great [RVM](https://rvm.io/) vs [rbenv](https://github.com/sstephenson/rbenv)
fight I've come down on the side of rbenv. Homebrew makes it simple to
install.

```shell
brew install rbenv ruby-build
rbenv install 2.2.0
```

Which gives us a Ruby 2.2.0 installation in a nice contained manner.

And The Rest....
----------------

Install various command line tools. Some tools of noteL

  * [jq](http://stedolan.github.io/jq/) for munging JSON on the command
    line.
  * The [The Silver Searcher](http://geoff.greer.fm/ag/) is a much
    faster grep with sensible defaults and nice output.
  * [vim](http://www.vim.org/) because of course vim. The keep
    an eye on [Neovim](http://neovim.org/).
  * [pv](http://www.ivarch.com/programs/pv.shtml) to make those long
    running pipe command lines friendly.
  

```shell
brew install
    awscli \
    jq \
    maven \
    pv \
    the_silver_searcher \
    vim
```

Install various GUI apps.

```shell
brew cask install
    boot2docker \
    chrome \
    firefox \
    hipchat \
    java \
    keepassx \
    sourcetree \
    vagrant \
    virtualbox
```

