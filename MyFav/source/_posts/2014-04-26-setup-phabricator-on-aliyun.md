---
title: "在CentOS上搭建Phabricator"
description: ""
category: 技术
tags: [code review,Phabricator,LAMP,ContOS]

---


前段时间在阿里云上租了个ECS云服务器，试着自己搭建个XMPP+Openfire的即时通讯平台。服务器系统是CentOS 6.3的，同时配置好了`LAMP(Linux+Apache+MySQL+PHP)`环境和管理MySQL的phpMyAdmin，在**mysite/phpMyAdmin**下。Openfire平台也顺利的搭建好了，在**9090端口**。

今天看到个讲Code Review的文章，分析了两个CodeReview软件：

* Facebook的Phabricator 
* Google的Gerrit

看了CodeReview那么多好处，在单位也没时间搞这东西，干脆自己先研究一下，自己先把Code Review做起来。
<!--more-->
###安装
本来想着应该是个软件吧，下下来就能用。去[Phabricator官网](http://phabricator.org)上溜达了一圈儿。翻了翻[**官方教程**](https://secure.phabricator.com/book/phabricator/article/installation_guide/)，发现原来这厮还得要台Web服务器：
>* **Apache**: Many installs use Apache + mod_php.
>* **Nnginx**: Many installs use nginx + php-fpm.
>* **lighttpd** : lighttpd is less popular than Apache or nginx, but it works fine.
>* **Other**: Other webservers which can run PHP are also likely to work fine, although these installation instructions will not cover how to set them up.
>* **PHP Builtin Server**: You can use the builtin PHP webserver for development or testing, although it should not be used in production.

还需要:
> * **MySQL**: You need MySQL.
> * **PHP**: You need PHP 5.2 or newer.

正想着蛮好，我那阿里云服务器不是为这货准备的嘛，接着看，湿了，这家伙还要个域名`domain name`，还必须是整个域名或者子域名：
> **NOTE**: Phabricator must be installed on an entire domain. You can not install it to a path on an existing domain, like `example.com/phabricator/`. Instead, install it to an entire domain or subdomain, like `phabricator.example.com`.

唉，要求那么高，非得把我的服务器给霸占了？再说我也没域名，就一个IP地址啊！！ 咋办，想了想，能不能跟Openfire的管理后台一样，也给分配个端口可以不。就这么干，先试试呗~

按着[**官方教程**](https://secure.phabricator.com/book/phabricator/article/installation_guide/)走，哇~ 有脚本，太好了，好省事儿。
> **RedHat Derivatives**: <http://www.phabricator.com/rsrc/install/install_rhel-derivs.sh>
	脚本包含了配置LAMP环境和下载Phabricator!

找个合适的地儿运行一下，命令行输入`sh rhel-derivs.sh`。很快执行完成，对应目录下多出了三个文件夹：`libphutil`、`arcanist`、`arcanist`。这就是Phabricator的主要文件，是从gitHub上clone下来的，手动的操作对应：


      $cd somewhere/ # pick some install directory
      somewhere/ $ git clone git://github.com/facebook/libphutil.git
      somewhere/ $ git clone git://github.com/facebook/arcanist.git
      somewhere/ $ git clone git://github.com/facebook/phabricator.git

还有一些选装的内容，就先忽略，这样安装就算完成啦。

###更新Phabricator
Pharbricator经常会更新，如果想更新的话，手动方式是：

* Stop the webserver (including `php-fpm`, if you use it).
* Run `git pull` in `libphutil/`, `arcanist/` and `phabricator/`.
* Run `phabricator/bin/storage upgrade`.
* Restart the webserver (and `php-fpm`, if you stopped it earlier).

当然也是有脚本滴！哈哈~

[`http://www.phabricator.com/rsrc/install/update_phabricator.sh`](http://www.phabricator.com/rsrc/install/update_phabricator.sh)

文件目录下运行`sh update_phabricator.sh`就好啦!

##配置
####配置Apache
首先保证Apache可以正常访问，咱们是要设置个Apache虚拟主机,相同IP下不同端口(Port)的虚拟主机。
在服务器中找到目录`/etc/httpd/conf.d/`我的是**CentOS系统**，
它的特色之一就是如果你不想修改原始配置文件httpd.conf的话，那么你可以在此目录下创建一个`*.conf`文件，
将自己额外参数独立出来，启动apache时，这个文件就会被读入到主要配置文件。

我这里创建`phabricator.conf`,增加一个7878端口，文件中写如下内容：

```
//增加监听端口
Listen 7878
NameVirtualHost myIP:7878

//更改虚拟主机设置
<VirtualHost myIP:7878>
  # Change this to the domain which points to your host.
  ServerName myIP:7878

  # Change this to the path where you put 'phabricator' when you checked it
  # out from GitHub when following the Installation Guide.
  #
  # Make sure you include "/webroot" at the end!
  DocumentRoot /mnt/codeReview/phabricator/webroot

  RewriteEngine on
  RewriteRule ^/rsrc/(.*)     -                       [L,QSA]
  RewriteRule ^/favicon.ico   -                       [L,QSA]
  RewriteRule ^(.*)$          /index.php?__path__=$1  [B,L,QSA]
</VirtualHost>


<Directory "/mnt/codeReview/phabricator/webroot">
  DirectoryIndex index.php
  AllowOverride None
    Order allow,deny
    Allow from all
</Directory>
```

OK!WebServer配置好啦，现在我们可以启动啦~在浏览器中输入`http://myIP:7878`,哦喔~
怎么会报一个MySQL的错误！OK~还没完，继续！

####配置MySQL 
配置一下phabricator中的信息，使他能连上咱们的MySQL。

如果你的能正常连接MySQL，那么你只需要启动一下Phabricator schemata就可以了。
进入phabricator所在目录，运行如下命令：


	phabricator/ $ ./bin/storage upgrade
	

如果你像我一样被一个无权限用户的错误挡在门外，那么就设置个MySQL中有权限的用户！
phabricator所在目录，运行如下命令：

	phabricator/ $ ./bin/storage upgrade --user <user> --password <password>

OK！下一步，启动deamon,重启apache！

	./bin/phd start
	service apache2 restart
	
OK！重新进浏览器试试吧！大功告成！

下面就是Phabricator咋配置咋用啦！官方有些文档可以参考一下：
>* 设置你的管理帐号和登陆/注册，请参见 [Configuring Accounts and Registration](http://www.phabricator.com/docs/phabricator/article/Configuring_Accounts_and_Registration.html); 
>* 了解更高级的配置主题，请参见 [Configuration User Guide: Advanced Configuration](http://www.phabricator.com/docs/phabricator/article/Configuration_User_Guide_Advanced_Configuration.html);
>* 配置上传的文件和附件的存储位置，请参见 [Configuring File Storage](http://www.phabricator.com/docs/phabricator/article/Configuring_File_Storage.html);
>* 配置Phabricator以支持发送邮件，请参见 [Configuring Outbound Email](http://www.phabricator.com/docs/phabricator/article/Configuring_Outbound_Email.html);
>* 配置Phabricator以支持接收邮件，请参见 [Configuring Inbound Email](http://www.phabricator.com/docs/phabricator/article/Configuring_Inbound_Email.html);
>* 导入版本仓库，请参见 [Diffusion User Guide](http://www.phabricator.com/docs/phabricator/article/Diffusion_User_Guide.html);
>* 了解守护进程工作，请参见 [Managing Daemons with phd](http://www.phabricator.com/docs/phabricator/article/Managing_Daemons_with_phd.html);
>* 配置备份工作，请参见 [Configuring Backups](http://www.phabricator.com/docs/phabricator/article/Configuring_Backups.html);
>* 为Phabricator贡献力量，请参见[Contributor Introduction](http://www.phabricator.com/docs/phabricator/article/Contributor_Introduction.html).
