---
layout: post
title: "Jekyll小窝搭好了"
description: "小窝搭好，就差个独立域名了"
category: 技术
tags: [jekyll,博客]
---

花了一天半，小窝终于搭好了，使用的是	[jekyllbootstrap](http://www.jekyllbootstrap.com)的模板，很好用，很效率。
在安装Jekyll的时候遇到了些问题,报错如下：

```
sudo gem install jekyll
Password:
Building native extensions.  This could take a while...
ERROR:  Error installing jekyll:
    ERROR: Failed to build gem native extension.

    /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby extconf.rb
creating Makefile

make "DESTDIR=" clean

make "DESTDIR="
compiling porter.c
porter.c:359:27: warning: '&&' within '||' [-Wlogical-op-parentheses]
      if (a > 1 || a == 1 && !cvc(z, z->k - 1)) z->k--;
                ~~ ~~~~~~~^~~~~~~~~~~~~~~~~~~~
porter.c:359:27: note: place parentheses around the '&&' expression to silence this warning
      if (a > 1 || a == 1 && !cvc(z, z->k - 1)) z->k--;
                          ^
                   (                          )
1 warning generated.
compiling porter_wrap.c
linking shared-object stemmer.bundle
clang: error: unknown argument: '-multiply_definedsuppress' [-Wunused-command-line-argument-hard-error-in-future]
clang: note: this will be a hard error (cannot be downgraded to a warning) in the future
make: *** [stemmer.bundle] Error 1

make failed, exit code 2

Gem files will remain installed in /Library/Ruby/Gems/2.0.0/gems/fast-stemmer-1.0.2 for inspection.
Results logged to /Library/Ruby/Gems/2.0.0/extensions/universal-darwin-13/2.0.0/fast-stemmer-1.0.2/gem_make.out
```
This is due to Xcode 5.1 - its already reported as a bug to Ruby.

I found three different ways to fix the bug:

* Install using:
``` sudo ARCHFLAGS=-Wno-error=unused-command-line-argument-hard-error-in-future gem install jekyll ```
* Install Ruby 2.1.1. You can do this using brew install [ruby](http://brew.sh/)
* Follow the instructions from [http://stackoverflow.com/a/22340285/1270034](http://stackoverflow.com/a/22340285/1270034) to remove the offending arg from rbconfig.

>还有些有用的Jekyll命令：
	jekyll serve	*开启本地浏览服务 <localhost:4000>*
	jekyll build --watch   内容或者设置有更改时 自动更新

