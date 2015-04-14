title: OS X 10.9 XCode5.1升级后 安装Jekyll报错 - duger
tags: [XCode,Jekyll]
date: 2014-03-26 20:55:00
category: [技术]
---

报错如下：
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="color: #000000;">sudo gem install jekyll
Password:
Building native extensions.  This could take a while...
ERROR:  Error installing jekyll:
    ERROR: Failed to build gem native extension.

    /System/Library/Frameworks/Ruby.framework/Versions/</span>2.0<span style="color: #000000;">/usr/bin/ruby extconf.rb
creating Makefile

make </span>"DESTDIR="<span style="color: #000000;"> clean

make </span>"DESTDIR="<span style="color: #000000;">
compiling porter.c
porter.c:</span>359:27: warning: '&amp;&amp;' within '||' <span style="color: #800000; font-weight: bold;">[</span><span style="color: #800000;">-Wlogical-op-parentheses</span><span style="color: #800000; font-weight: bold;">]</span><span style="color: #000000;">
      if (a &gt; </span>1 || a == 1 &amp;&amp; !cvc(z, z-&gt;k - 1)) z-&gt;k--<span style="color: #008000;">;
</span><span style="color: #000000;">                ~~ ~~~~~~~^~~~~~~~~~~~~~~~~~~~
porter.c:</span>359:27<span style="color: #000000;">: note: place parentheses around the '&amp;&amp;' expression to silence this warning
      if (a &gt; </span>1 || a == 1 &amp;&amp; !cvc(z, z-&gt;k - 1)) z-&gt;k--<span style="color: #008000;">;
</span><span style="color: #000000;">                          ^
                   (                          )
</span>1<span style="color: #000000;"> warning generated.
compiling porter_wrap.c
linking shared-object stemmer.bundle
clang: error: unknown argument: '-multiply_definedsuppress' </span><span style="color: #800000; font-weight: bold;">[</span><span style="color: #800000;">-Wunused-command-line-argument-hard-error-in-future</span><span style="color: #800000; font-weight: bold;">]</span><span style="color: #000000;">
clang: note: this will be a hard error (cannot be downgraded to a warning) in the future
make: *** </span><span style="color: #800000; font-weight: bold;">[</span><span style="color: #800000;">stemmer.bundle</span><span style="color: #800000; font-weight: bold;">]</span> Error 1<span style="color: #000000;">
<!--more-->
make failed</span>, exit code 2<span style="color: #000000;">

Gem files will remain installed in /Library/Ruby/Gems/</span>2.0.0/gems/fast-stemmer-1.0.2<span style="color: #000000;"> for inspection.
Results logged to /Library/Ruby/Gems/</span>2.0.0/extensions/universal-darwin-13/2.0.0/fast-stemmer-1.0.2/gem_make.out</div>

<span>This is due to Xcode 5.1 - its already reported as a bug to Ruby.</span>

I&nbsp;found three different ways to fix the bug:

*   Install using:<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;">sudo ARCHFLAGS=-Wno-error=unused-command-line-argument-hard-error-<span style="color: #0000ff;">in</span>-future gem install cheatset</div>

*   Install Ruby 2.1.1\. You can do this using&nbsp;`brew install ruby`&nbsp;([http://brew.sh/](http://brew.sh/))
*   Follow the instructions from&nbsp;[http://stackoverflow.com/a/22340285/1270034](http://stackoverflow.com/a/22340285/1270034)&nbsp;to remove the offending arg from rbconfig.![](http://counter.cnblogs.com/blog/rss/3626969)

本文链接：[OS X 10.9 XCode5.1升级后 安装Jekyll报错](http://www.cnblogs.com/duger/p/3626969.html)，转载请注明。