---
categories:
- posts
date: "2014-12-29T00:00:00Z"
tags:
- development
- ruby
- rbenv
- osx
- jekyll
- homebrew
title: Getting Started With Github Pages and Jekyll on OS X
---

This isn't necessarily the best way but here's how I went about getting
[Jekyll](http://jekyllrb.com/) going on my local machine in preparation for
using [Github Pages](https://pages.github.com/) to server content for my
web page.


Installing a Separate Version of Ruby
---

As Jekyll requires a whole mess of Ruby Gems to get going I decided it
would be best to install a separate Ruby instance to avoid messing with
the OS X system Ruby. I'm using Mac OS X 10.10 (Yosemite) for this and
I've already got [Homebrew](http://brew.sh/) installed which makes it
very easy to install extra tools on OS X. 

I chose [rbenv](https://github.com/sstephenson/rbenv) as it's the simplest
of the tools to manage multiple Ruby installs. It has a companion tool
called [ruby-build](https://github.com/sstephenson/ruby-build) that can
build any version of Ruby we need. So assuming you have Homebrew installed,
getting these installed is as simple as:

    brew install rbenv ruby-build

Then add the following snippet somewhere your shell will read it.

    if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi

After relaunching your shell (or just manually executing the above
snippet in the current shell) installing a separate Ruby is as simple as 

    rbenv install 2.2.0

I've chosen to use the latest Ruby (2.2.0 at time of writing) because
why not. Once that's done installing you should be able to execute 

    rbenv versions

which results in output like this:

    * system (set by /Users/deverton/.rbenv/version)
      2.2.0

This shows the system installed Ruby and our just installed 2.2.0
version. We can switch to that version with a quick

    rbenv global 2.2.0

Now the output of `rbenv versions` should look like this:

      system (set by /Users/deverton/.rbenv/version)
    * 2.2.0


Installing Jekyll
---

The next step is to create somewhere for your site content to go. Rather
than document that here, follow the instructions at
[Github](https://pages.github.com/).

Now that you've got a directory with your site content checked out it's
time to get Jekyll installed. It's easiest to use
[Bundler](http://bundler.io/) to manage the install so to that end if
you do the following:

    gem install bundle
    rbenv rehash

You'll have the latest Bundler installed and available on the path. You
can now create a `Gemfile` with the following contents

    source 'https://rubygems.org'
    gem 'github-pages'

then execute

    bundle install
    rbenv rehash

which will install all the gems we need and the command line tools. We
can now use Jekyll to create all the basic files needed for the site.
Assuming you're currect directory is your site directory:

    jekyll new .

should create all the needed files. You can then test things out with

    jekyll serve

which creates a webserver on port 4000 serving up your site. Once you're
happy, you can now commit your changes, push them to Github and see your
new, Jekyll powered sites :tada:

