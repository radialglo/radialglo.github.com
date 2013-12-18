---
layout: post
title: "Ahoy! Octopress"
date: 2013-03-29 18:10
comments: true
categories: 
---

Ay mate! I recently decided to start blogging about code with [octopress](http://octopress.org) as my blogging engine. This blog will focus on documenting my coding ventures. I've encountered other file based cms(content management systems) such as  [kirby](http://getkirby.com) and [stacey](http://www.staceyapp.com) (which run on PHP as opposed to Ruby), but ultimately decided to work with octopress because this framework is designed for Jekyll, the blog aware static site generator that powers [Github Pages](pages.github.com) (so this site can be hosted on github). As an added bonus working with octopress provides beginners a good opportunity to tinker around in a Ruby environment.
  

Installing octopress on my local machine Ubuntu 11.10 (Oneiric Ocelot) was met with some setbacks which I resolved as describe below: 

At the time of installing octopress,a prerequistie for installation was Ruby 1.9.3. I used [rvm](http://rvm.io)(Ruby Version Manager) as a utility to download the ruby binary. Unfortunately my installiation failed when running rvm install 1.9.3, so I re-ran the installation command in *debug mode* to print out additional output to locate the source of this issue.
{% codeblock Ruby Installation in Debug mode%}
rvm install 1.9.3 --debug
{% endcodeblock %}
{% codeblock Installation Response Snippet%}
1.9.3 - install
Searching for binary rubies, this might take some time.
Remote file does not exist https://rvm.io/binaries/ubuntu/11.10/i386/ruby-1.9.3-p392.tar.bz2
Remote file does not exist http://jruby.org.s3.amazonaws.com/downloads/ruby-1.9.3-p392.tar.bz2
Remote file does not exist http://binaries.rubini.us/ubuntu/11.10/i386/ruby-1.9.3-p392.tar.bz2
...
No remote file name found
No binary rubies available for: ubuntu/11.10/i386/ruby-1.9.3-p392.
{% endcodeblock %}

It turned out the public release 392(p392) binary file for 32-bit Ubuntu 11.10 could not be located from the various urls referenced by rvm and I couldn't locate the binaries via a Google search either.

After browsing around I also tried a solution that uses ruby-rvm, but also discovered that [Ubuntu warps ruby-rvm](http://stackoverflow.com/questions/9056008/installed-ruby-1-9-3-with-rvm-but-command-line-doesnt-show-ruby-v/9056395#9056395).

I ended install the head of ruby-1.9.3 and also ruby-2.0.0. So far I have been running octopress on ruby-2.0.0 and have not encountered any issues.
{% codeblock Ruby-head Installation%}
rvm install ruby-1.9.3-head --autolibs=3 --debug
#--autolibs=3 automatically installs packages/dependicies needed or that require updates
{% endcodeblock %}

After downloading the 1.9.3-head and 2.0.0 binaries, I had issues running bundle install (which installs dependencies specified in my Gemfile). The error reported the bundler itself not being installed even though I installed it in the previous command. 
It turned out that my bundler was installed to a specific gemset and once I installed the bundler to the global gemset, bundle install could be executed. 
{% codeblock Installing bundler to global gemset%}
bundle install
ERROR: Gem bundler is not installed, run `gem install bundler` first.

rvm gemset use global && gem install bundler
http://stackoverflow.com/questions/12326705/error-gem-bundler-is-not-installed-run-gem-install-bundler-first
{% endcodeblock %}

