title: 在Mac OS X中配置Apache ＋ PHP ＋ MySQL - duger
tags: []
date: 2013-08-29 13:44:00
category:
---

<!--?xml version="1.0" encoding="UTF-8" standalone="no"?-->
<div>

**<span style="font-size: 14px;">&nbsp;</span>**
</div>

<span style="font-size: 14px;">Mac OS X 内置Apache 和 PHP，使用起来非常方便**。**本文以Mac OS X 10.6.3和<ins>&nbsp;10.8**.**1</ins>为例。主要内容包括：</span>

**<span style="font-size: 14px;">启动Apache<strong>&nbsp;**</span></strong>

<span style="font-size: 14px;">有两种方法：</span>

1.  <span style="font-size: 14px;">打开&ldquo;系统设置偏好（System Preferences）&rdquo; -&gt; &ldquo;共享（Sharing）&rdquo; -&gt; &ldquo;Web共享（Web Sharing）&rdquo;**。**<ins>注意，从Mac OS X从10.8开始取消了 &ldquo;Web共享（Web Sharing）&rdquo;**。**</ins></span>
2.  <span style="font-size: 14px;">打开&ldquo;终端（terminal）&rdquo;，然后（注意，sudo需要的密码就是系统的root帐号密码）</span>

        1.  运行&ldquo;`sudo apachectl start`&rdquo;，再输入帐号密码，这样Apache就运行了**。**
    2.  运行&ldquo;`sudo apachectl －v`&rdquo;，你会看到Mac OS X的Apache版本信息，如10**.**8.1中：`Server version: Apache/2.2.22 (Unix)
Server built:   Jun 20 2012 13:57:09
`

<span style="font-size: 14px;">如此在浏览器中输入&ldquo;http://localhost&rdquo;，就可以看到一个内容为&ldquo;It works**!**&rdquo;的页面，其位于&ldquo;/Library（资源库）/WebServer/Documents/&rdquo;下，这就是Apache的默认根目录**。**</span>

<span style="font-size: 14px;">注意：开启了Apache就是开启了&ldquo;Web共享&rdquo;，这时联网用户就会通过&ldquo;http://[本地IP]/&rdquo;来访问&ldquo;/Library（资源库）/WebServer/Documents/&rdquo;目录，通过&ldquo;http://[本地IP]/~[用户名]&rdquo;来访问&ldquo;/Users/[用户名]/Sites/&rdquo;目录**。**<ins>值得注意的是，Mac OS X在10.8中取消&rdquo;Web共享（Web Sharing）&rdquo;时，也移除了&ldquo;/Users/[用户名]/Sites/&rdquo;目录，所以10**.**8中访问&ldquo;http://[本地IP]/~[用户名]&rdquo;会显示&ldquo;403 Forbidden&rdquo;，但http://[本地IP]/依旧可以访问**。**</ins>可以到&ldquo;系统偏好设置&rdquo; -&gt; &ldquo;安全（Security）&rdquo; -&gt; &ldquo;防火墙（Firewall）&rdquo;，开启防火墙，然后在&ldquo;防火墙选项（Firewall Options）&rdquo;中勾上&ldquo;组织所有进入连接（block all incoming connections）&rdquo;即可**。**也可以通过设置httpd.conf来只允许localhost和127.0**.**0.1访问&ldquo;/Library（资源库）/WebServer/Documents/&rdquo;。</span>
<span style="font-size: 14px;">`&lt;Directory "/Library/WebServer/Documents"&gt;
    ......
    #
    # Controls who can get stuff from this server**.**
    #
    Order allow,deny
    #Allow from all
    Allow from 127**.**0.0**.**1
    Allow from localhost 

&lt;/Directory&gt;
`</span>

**<span style="font-size: 14px;">运行PHP<strong>&nbsp;**</span></strong>

1.  <span style="font-size: 14px;">在终端中运行&ldquo;`sudo vi /etc/apache2/httpd.conf`&rdquo;，打开Apache的配置文件**。**（如果不习惯操作终端和vi，可以设置[在Finder中显示所有系统隐藏文件&nbsp;](http://apple.tgbus.com/tutorial/soft/200811/20081125100105.shtml)，记得设置完毕后需要[重启Finder&nbsp;](http://www.macdocks.com/2009/07/14/mac-tips-%E4%B8%89%E7%A7%8D%E6%96%B9%E6%B3%95%E9%87%8D%E5%90%AFmac-os-x-finder/)，然后就可以找到对应文件，随心所欲编辑了，需要注意的是某些文件的修改还是需要[开启root帐号&nbsp;](http://support.apple.com/kb/HT1528?viewlocale=zh_CN)，但整体上还是在终端上使用`sudo`来临时获取root权限比较安全**。**）</span>
2.  <span style="font-size: 14px;">找到&ldquo;`#LoadModule php5_module libexec/apache2/libphp5**.**so`&rdquo;，把前面的#号去掉，保存（在命令行输入`:w`）并退出vi（在命令行输入`:q`）**。**</span>
3.  <span style="font-size: 14px;">运行&ldquo;`sudo cp /etc/php.ini.default /etc/php.ini`&rdquo;，这样就可以运行`sudo vi /etc/php.ini`来编辑php.ini配置各种功能了**。**比如：</span><span style="font-size: 14px;">`;通过下面两项来调整PHP提交文件的最大值，如phpMyAdmin中导入数据的最大值
upload_max_filesize = 2M
post_max_size = 8M
;通过display_errors来控制是否显示PHP程序的报错信息，这在调试PHP程序时非常有用
display_errors = Off
`</span>
4.  <span style="font-size: 14px;">运行&ldquo;`sudo apachectl restart`&rdquo;，重启Apache，这样PHP就可以用了**。**</span>
5.  <span style="font-size: 14px;">运行&ldquo;`sudo cp /Library/WebServer/Documents/index.html**.**en /Library/WebServer/Documents/info.php`&rdquo;，即在Apache的根目录下复制index.html**.**en文件并重命名为info.php。</span>
6.  <span style="font-size: 14px;">在终端中运行&ldquo;`sudo vi /Library/WebServer/Document/info.php`&rdquo;，这样就可以在vi中编辑info.php文件了**。**在&ldquo;It&rsquo;s works!&rdquo;后面加上&ldquo;`&lt;**?**php phpinfo(); ?&gt;`&rdquo;，然后保存之。如此就可以在http://localhost/info.php中看到有关PHP的信息，比如10**.**8中内置PHP版本号是5.3.13**。**</span>

**<span style="font-size: 14px;">安装MySQL<strong>&nbsp;**</span></strong>

<span style="font-size: 14px;">Mac OS X没有内置MySQL，所以需要自己手动安装，目前MySQL的最稳定版本是5**.**5**。**[MySQL提供了Mac OS X下的安装说明&nbsp;](http://dev.mysql.com/doc/refman/5.5/en/macosx-installation.html)。</span>

1.  <span style="font-size: 14px;">[下载MySQL 5**.**5&nbsp;](http://dev.mysql.com/downloads/mysql/5.5.html)。选择合适版本，如这里选择了mysql-5**.**5.27-osx10.6-x86_64.dmg。</span>
2.  <span style="font-size: 14px;">运行dmg，会发现里面有4个文件**。**首先点击安装mysql-5.5.27-osx10.6-x86_64.pkg，这是MySQL主安装包**。**一般情况下，安装文件会自动把MySQL安装到`/usr/local`下的同名文件夹下**。**如运行&ldquo;`mysql-5**.**5.27-osx10.6-x86_64.dmg`&rdquo;会把MySQL安装到&ldquo;`/usr/local/mysql-5**.**5.27-osx10.6-x86_64`&rdquo;中，一路默认安装完毕**。**（注意，从10.8开始Mac OS X的权限更加严格，直接点击会提示&ldquo;mysql-5.5.27-osx10**.**6-x86_64.pkg can&rsquo;t be opened because it is from an unidentified developer**.**&nbsp;Your security preferences allow installation of only apps from the Mac App Store and identified developers**.**&rdquo;阻止了安装，你可以使用双指单击该安装文件，在弹出菜单中选择&ldquo;用&hellip;打开（open with）&rdquo;，再选择&ldquo;安装（Installer）&rdquo;就可以接着安装了**。**）</span>
3.  <span style="font-size: 14px;">安装第2个文件MySQLStartupItem.pkg，MySQL就会自动在开机时启动了**。**（注意，10.8的安装方法同上。）</span>
4.  <span style="font-size: 14px;">安装第3个文件MySQL.prefPane，就会在&ldquo;系统设置偏好&rdquo;中看到&ldquo;MySQL&rdquo;的ICON，通过它就可以控制MySQL是否开启，以及开机时是否自动运行**。**到这里MySQL就基本安装完毕了。（注意，10.8中用双指单击该安装文件，在弹出的菜单中选择&ldquo;用&hellip;打开（open with）&rdquo;，然后选择&ldquo;系统偏好（System Perference）&rdquo;就可以接着安装了**。**）</span>
5.  <span style="font-size: 14px;">通过运行&ldquo;`sudo vi /etc/bashrc`&rdquo;，在bash配置文件中加入`mysqlstart`**、**`mysql`和`mysqladmin`的别名（注意：修改完毕之后需要退出&ldquo;终端（Terminal）&rdquo;之后重新进入，这些命令才会生效）：</span><span style="font-size: 14px;">`#mysql
alias mysqlstart='sudo /Library/StartupItems/MySQLCOM/MySQLCOM restart'
alias mysql='/usr/local/mysql/bin/mysql'
alias mysqladmin='/usr/local/mysql/bin/mysqladmin'
`</span>

    <span style="font-size: 14px;">这样就可以在终端中比较简单地通过命令进行相应的操作**。**由于开始安装MySQLStartupItem.pkg到&ldquo;`/Library/StartupItems/MySQLCOM/`&rdquo;来控制MySQL的运行**、**自动运行**、**停止、关闭之类。在MySQL没有启动时，直接运行`mysql`或`mysqladmin`命令会提示&ldquo;`Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)`&rdquo;，所以我们可以通过控制面板或者直接运行`mysqlstart`命令来启动MySQL，之后再运行`mysql`或`mysqladmin`命令就正常了**。**比如安装完毕后MySQL的`root`默认密码为空，如果要设置密码可以在终端运行&ldquo;`mysqladmin -u root password "mysqlpassword"`&rdquo;来设置，其中mysqlpassword即root的密码**。**更多相关内容可以参考[B.5**.**4.1\. How to Reset the Root Password&nbsp;](http://dev.mysql.com/doc/refman/5.5/en/resetting-permissions.html)。</span>

<span style="font-size: 14px;">注意：Mac OS X的升级或其他原因可能会导致ＭySQL启动或开机自动运行时，在ＭySQL操作面板上会提示&ldquo;`Warning:The /usr/local/mysql/data directory is not owned by the 'mysql' or '_mysql'&nbsp;`&rdquo;，这应该是某种情况下导致`/usr/local/mysql/data`的宿主发生了改变，只需要运行&ldquo;`sudo chown -R mysql /usr/local/mysql/data`&rdquo;即可**。**</span>

<span style="font-size: 14px;">另外，[使用PHP连接MySQL可能会报错&nbsp;](http://www.exp2up.com/2009/03/11/mac%E4%B8%8A%E7%9A%84php%E6%97%A0%E6%B3%95%E8%BF%9E%E6%8E%A5mysql/)&ldquo;Can&rsquo;t connect to local MySQL server through socket &lsquo;/var/mysql/mysql.sock&rsquo;&rdquo;，或使用localhost无法连接MySQL而需要127**.**0**.**0.1，原因是连接时php默认去找`/var/mysql/mysql.sock`了，但MAC版的MYSQL改动了文件位置，放在/tmp下了**。**处理办法是按如下修改php.ini：</span>
<span style="font-size: 14px;">`mysql.default_socket = /tmp/mysql.sock`</span>

**<span style="font-size: 14px;">使用phpMyAdmin<strong>&nbsp;**</span></strong>

<span style="font-size: 14px;">[phpMyAdmin&nbsp;](http://www.phpmyadmin.net/)是用PHP开发的管理MySQL的程序，非常的流行和实用**。**能够使用phpMyAdmin管理MySQL是检验前面几步效果的非常有效方式。</span>

1.  <span style="font-size: 14px;">[下载phpMyAdmin&nbsp;](http://www.phpmyadmin.net/home_page/downloads.php)**。**选择合适的版本，比如这里选择phpMyAdmin-3.5.22-all-languages.tar**.**bz2这个版本。</span>
2.  <span style="font-size: 14px;">把&ldquo;下载（downloads）&rdquo;中phpMyAdmin-3**.**5.22-all-languages.tar**.**bz2文件解压到&ldquo;`&nbsp;/Library/WebServer/Documents/`&rdquo;中，并改名为phpmyadmin**。**</span><span style="font-size: 14px;">`sudo tar -xf ~/Downloads/phpMyAdmin-3**.**5.2.2-all-languages.tar.bz2 -C
             /Library/WebServer/Documents/
sudo mv /Library/WebServer/Documents/phpMyAdmin-3**.**5.2.2-all-languages
            /Library/WebServer/Documents/phpmyadmin
`

    </span>
3.  <span style="font-size: 14px;">复制&ldquo;`/Library/WebServer/Documents/phpmyadmin/`&rdquo;中的config.sample.inc.php，并命名为config.inc.php</span>
4.  <span style="font-size: 14px;">编辑config.inc.php，修改如下：</span><span style="font-size: 14px;">`用于Cookie加密，随意的长字符串
$cfg['blowfish_secret'] = 'a8b7c6d';

    当phpMyAdmin中出现&ldquo;#2002 无法登录 MySQL 服务器（#2002 Cannot log in to the MySQL server）&rdquo;时，
[请把localhost改成127**.**0.0.1就ok了 ](http://achan.me/2010/04/mysql-error-2002.html)，
这是因为MySQL守护程序做了IP绑定（bind-address =127**.**0.0.1）造成的
$cfg['Servers'][$i]['host'] = 'localhost';

    把false改成true，这样就可以访问无密码的MySQL了，
即使MySQL设置了密码也可以这样设置，然后登录phpMyAdmin时输入密码
$cfg['Servers'][$i]['AllowNoPassword'] = false;
`</span>
5.  <span style="font-size: 14px;">这样就可以通过`http://localhost/phpmyadmin`访问phpMyAdmin了**。**此时会看到一个提示&ldquo;无法加载 mcrypt 扩展，请检查您的 PHP 配置。（The mcrypt extension is missing. Please check your PHP configuration**.**）&rdquo;，这会涉及到下一节安装MCrypt扩展了。</span>

**<span style="font-size: 14px;">配置PHP的MCrypt<strong>&nbsp;**扩展</span></strong>

<span style="font-size: 14px;">[MCrypt&nbsp;](http://mcrypt.sourceforge.net/)是一个功能强大的加密算法扩展库，它包括有22种算法，phpMyAdmin依赖这个PHP扩展库**。**但在Mac OS X下的安装却不那么友善，具体如下：</span>

1.  <span style="font-size: 14px;">下载[libmcrypt-2**.**5.8.tar.gz&nbsp;](http://sourceforge.net/projects/mcrypt/files/Libmcrypt/)**。**</span>
2.  <span style="font-size: 14px;">在终端执行如下命令（注意如下命令需要安装[Xcode&nbsp;](http://developer.apple.com/xcode/)支持，可直接去Mac App Store下载，安装完毕后可能会发现在终端运行`**.**/configure --disable-posix-threads --enable-static`会报错，运行`make`会提示命令不存在，此时还需要打开Xcode，然后在Xcode的软件&ldquo;配置（Preference&hellip;）&rdquo;）-&gt; &ldquo;下载（Downloads）&rdquo; 中安装 &ldquo;命令行工具（Command Line Tools）&rdquo;：</span><span style="font-size: 14px;">`cd ~/Downloads
tar -zxvf libmcrypt-2**.**5.8.tar.bz2
cd libmcrypt-2.5**.**8
./configure --disable-posix-threads --enable-static
make
sudo make install`</span>
3.  <span style="font-size: 14px;">下载[PHP源码文件php-5**.**3.13.tar**.**bz2&nbsp;](http://us2.php.net/get/php-5.3.13.tar.bz2/from/a/mirror)，记得选择中国镜像会比较快。Mac OS X 10.6.3中预装的PHP版本是5**.**3.1，10.8的版本是5.3**.**13，而现在[最新的PHP版本是5.4.6&nbsp;](http://us2.php.net/downloads.php)，所以需要依据自己的实际情况选择对应的版本，本文以10.8的PHP版本为例**。**</span>
4.  <span style="font-size: 14px;">在终端执行如下命令，把php-5.3.13.tar.bz2，并[配置autoconf&nbsp;](http://www.coolestguyplanettech.com/how-to-install-mcrypt-for-php-on-mac-osx-lion-10-7-development-server/)（在新的Mac OS X的Xcode中需要自己配置），然后才能运行`phpize`命令：</span><span style="font-size: 14px;">`cd ~/Downloads
tar -zxvf php-5**.**3.13.tar.bz2

    cd php-5.3**.**13/ext/mcrypt
curl -O http://ftp.gnu.org/gnu/autoconf/autoconf-latest.tar**.**gz
tar -zxvf autoconf-latest.tar**.**gz
cd autoconf-2.69
./configure
make
sudo make install

    cd ..
phpize
**.**/configure
make
sudo make install
`</span>
5.  <span style="font-size: 14px;">打开php.ini</span><span style="font-size: 14px;">`sudo vi /etc/php.ini`</span>

    <span style="font-size: 14px;">在php.ini中加入如下代码，并保存后退出，然后重启Apache</span>
<span style="font-size: 14px;">`extension=mcrypt**.**so`</span>

<span style="font-size: 14px;">当你再访问`http://localhost/phpmyadmin`时，会发现&ldquo;无法加载 mcrypt 扩展，请检查您的 PHP 配置**。**&rdquo;提示没有了，这就表示MCrypt扩展库安装成功了。如果还不能加载，尝试把php.ini中的加入的`extension`修改为：</span>
<span style="font-size: 14px;">`extension=/usr/lib/php/extensions/no-debug-non-zts-20090626/mcrypt**.**so`</span>

<span style="font-size: 14px;">Mac OS X下安装MCrypt扩展的确比较复杂，而且稍微不小心会有各种小问题出现，大家还可以参考[How to Install mcrypt for php on Mac OSX Lion 10**.**8 &amp; 10.7 Development Server&nbsp;](http://www.coolestguyplanettech.com/how-to-install-mcrypt-for-php-on-mac-osx-lion-10-7-development-server/)和[Adding MCRYPT to your OSX Lion PHP install](http://remonpel.nl/2012/01/adding-mcrypt-to-your-osx-lion-php-install/)</span>

**<span style="font-size: 14px;">设置虚拟主机</span>**

1.  <span style="font-size: 14px;">在终端运行&ldquo;`sudo vi /etc/apache2/httpd.conf`&rdquo;，打开Apche的配置文件</span>
2.  <span style="font-size: 14px;">在httpd.conf中找到&ldquo;`#Include /private/etc/apache2/extra/httpd-vhosts.conf`&rdquo;，去掉前面的&ldquo;`＃`&rdquo;，保存并退出**。**</span>
3.  <span style="font-size: 14px;">运行&ldquo;`sudo apachectl restart`&rdquo;，重启Apache后就开启了虚拟主机配置功能**。**</span>
4.  <span style="font-size: 14px;">运行&ldquo;`sudo vi /etc/apache2/extra/httpd-vhosts.conf`&rdquo;，就打开了配置虚拟主机文件httpd-vhost.conf，配置虚拟主机了**。**需要注意的是该文件默认开启了两个作为例子的虚拟主机：</span><span style="font-size: 14px;">`&lt;VirtualHost *:80&gt;
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/usr/docs/dummy-host.example.com"
    ServerName dummy-host.example.com
    ErrorLog "/private/var/log/apache2/dummy-host.example.com-error_log"
    CustomLog "/private/var/log/apache2/dummy-host.example.com-access_log" common
&lt;/VirtualHost&gt;

    &lt;VirtualHost *:80&gt;
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "/usr/docs/dummy-host2.example.com"
    ServerName dummy-host2.example.com
    ErrorLog "/private/var/log/apache2/dummy-host2.example.com-error_log"
    CustomLog "/private/var/log/apache2/dummy-host2.example.com-access_log" common
&lt;/VirtualHost&gt; `</span>

    <span style="font-size: 14px;">而实际上，这两个虚拟主机是不存在的，在没有配置任何其他虚拟主机时，可能会导致访问localhost时出现如下提示：</span>
<span style="font-size: 14px;">`Forbidden
You don't have permission to access /index.php on this server`</span>

    <span style="font-size: 14px;">最简单的办法就是在它们每行前面加上#，注释掉就好了，这样既能参考又不导致其他问题**。**</span>

5.  <span style="font-size: 14px;">增加如下配置</span><span style="font-size: 14px;">`&lt;VirtualHost *:80&gt;
    DocumentRoot "/Library/WebServer/Documents"
    ServerName localhost
    ErrorLog "/private/var/log/apache2/localhost-error_log"
    CustomLog "/private/var/log/apache2/localhost-access_log" common
&lt;/VirtualHost&gt;
    &lt;VirtualHost *:80&gt;
    DocumentRoot "/Users/[用户名]/Sites"
    ServerName sites
    ErrorLog "/private/var/log/apache2/sites-error_log"
    CustomLog "/private/var/log/apache2/sites-access_log" common
    &lt;Directory /&gt;
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order deny,allow
                Allow from all
      &lt;/Directory&gt;
&lt;/VirtualHost&gt; `</span>

    <span style="font-size: 14px;">保存退出，并重启Apache**。**</span>

6.  <span style="font-size: 14px;">运行&ldquo;`sudo vi /etc/hosts`&rdquo;，打开hosts配置文件，加入"`127**.**0**.**0.1 sites`"，这样就可以配置完成sites虚拟主机了，可以访问&ldquo;http://sites&rdquo;了，在10**.**8之前Mac OS X版本其内容和&ldquo;http://localhost/~[用户名]&rdquo;完全一致。</span>
7.  <span style="font-size: 14px;">注意，记录log的&ldquo;`ErrorLog "/private/var/log/apache2/sites-error_log"`&rdquo;也可以删掉，但记录日志其实是一个好习惯，在出现问题时可以帮助我们判断**。**如果保留这些log代码，一定log文件路径都是存在的，如果随便修改一个不存在的，会导致Apache无法服务而没有错误提示，这个比较恶心**。**</span>

<span style="font-size: 14px;">这里利用Mac OS X 10**.**6.3和10.8.1中原生支持的方式来实现的配置，也可以参考&ldquo;[Mac OS X Leopard: 配置Apache, PHP, SQLite, MySQL, and phpMyAdmin(一)&nbsp;](http://blog.csdn.net/afatgoat/archive/2008/12/26/3615026.aspx)&rdquo;和&ldquo;[Mac OS X Leopard: 配置Apache, PHP, SQLite, MySQL, and phpMyAdmin(二)&nbsp;](http://blog.csdn.net/afatgoat/archive/2008/12/28/3628710.aspx)&rdquo;**。**实际上，还可以使用[XAMPP&nbsp;](http://www.apachefriends.org/en/xampp-macosx.html)或[MacPorts&nbsp;](http://www.macports.org/)这种第三方提供的集成方案来实现简单的安装和使用**。**</span>

**<span style="font-size: 14px;">&nbsp;</span>**
![](http://counter.cnblogs.com/blog/rss/3288982)

本文链接：[在Mac OS X中配置Apache ＋ PHP ＋ MySQL](http://www.cnblogs.com/duger/p/3288982.html)，转载请注明。