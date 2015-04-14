title: "苹果开发者中心 Certificates，Identifiers & Profiles 备忘录"
tags: [苹果证书,Certificates,Profiles]
date: 2014-01-19 21:12:00
category: [技术,iOS]
---

&nbsp;![](http://images.cnitblog.com/blog/557746/201401/192139128456.png)

**概述：**

<span>首先得描述一下各个证书的定位，作用，这样在制作的时候心中有谱，对整个流程的把握也会准确一些；</span>
<!--more-->
![](http://images.cnitblog.com/blog/557746/201401/192140041736.png)

<span>**1、Certificate&nbsp;开发者证书**（分为开发和发布两种，类型为ios Development,ios Distribution）</span>

<span>这个是最基础的，不论是真机调试，还是上传到appstore都是需要的，是一个基证书，用来证明自己开发者身份的；</span>

![](http://images.cnitblog.com/blog/557746/201401/192142093610.png)

APNS证书是给服务器开发push notification用的，详见3.

&nbsp;

&nbsp;

&nbsp;

![](http://images.cnitblog.com/blog/557746/201401/192142442529.png)

**2、Identifiers:标识**

<span>2.1、App ID,这是每一个应用的独立标识，可以包含通配符也可以不含，在设置项中可以配置该应用的权限，比如是否用到了PassBook,GameCenter,以及更常见的push服务，如果选中了push服务，那么就可以创建生成下面第3条所提到的推送证书，所以，在所有和推送相关的配置中，首先要做的就是先开通支持推送服务的appID;</span>

<span>格式如：com.company.product.*, 或 com.company.product.1.0。</span>

![](http://images.cnitblog.com/blog/557746/201401/192144400027.png)

&nbsp;

**3、推送证书**（分为开发和发布两种，类型分别为APNs Development ios,APNs Distribution ios）

APNS证书是给服务器开发push notification用的；该证书在appID配置（标题2.1）中创建生成，和开发者证书一样，安装到开发电脑上；

![](http://images.cnitblog.com/blog/557746/201401/192147186898.png)

![](http://images.cnitblog.com/blog/557746/201401/192147337679.png)

生成是如上图，需要生成两个证书，Development SSL Certificate和Production SSL Certification,前者用于开发测试，后者用于发布，如果是使用第三方推送或是自己后台写的PHP推送服务器，要使用后者。

<span style="color: #ff0000;">**注意如果程序支持game center, in app purchase,push notification,这个app id中不能包含通配符&ldquo;*&rdquo;。**</span>&nbsp;

&nbsp;

![](http://images.cnitblog.com/blog/557746/201401/192148382361.png)

**4、Devices&nbsp;管理测试设备：**

<span>使用你的测试设备的UUID添加进来，然后在Profiles里面选择这些设备。生成Provisioning Profiles后再添加的测试设备，如想要用它进行真机调试，需要更新Provisioning Profiles,编辑对应Provisoning Profiles,将设备添加进来。</span>

![](http://images.cnitblog.com/blog/557746/201401/192150280487.png)

![](http://images.cnitblog.com/blog/557746/201401/192150595179.png)

删除设备会使所有关联这个设备的Provisioning Profiles失效，所有不要随便删，要删在下次续费时删除。

&nbsp;

&nbsp;

![](http://images.cnitblog.com/blog/557746/201401/192151336580.png)

**5、Provisioning Profiles**

<span>这个东西是很有苹果特色的一个东西，该文件将App ID,开发者证书，硬件Device绑定到一块儿，在开发者中心配置好后可以添加到Xcode上，也可以直接在Xcode上连接开发者中心生成，真机调试时需要在PP文件中添加真机的udid；是真机调试必备之珍品；</span>

![](http://images.cnitblog.com/blog/557746/201401/192153160021.png)

Provisioning Profiles配置文件，分为两种

&nbsp;&nbsp; **5.1、 Development 开发版**，建立开发版本的profile时只能选择证书中的开发证书，并且支持多选多个证书，多个开发人员可以共用一个profile；同时可以选择多个3建立的设备；
&nbsp;&nbsp; **5.2、 Distribution 正式版**，此版本profile分为

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; _a. App Store版本_，是发布到App Store时使用的profile，创建时先选择Identifiers中的AppID，再选择一个证书，选择时应该你创建的证书;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;_ b. Ad Hoc版本_，Ad Hoc是给某些企业发布紧企业内部使用的;

平常我们的制作流程一般都是按以上序列进行，先利用开发者帐号登陆开发者中心，创建开发者证书，appID,在appID中开通推送服务，在开通推送服务的选项下面创建推送证书（服务器端的推送证书见下文），之后在PP文件中绑定所有的证书id,添加调试真机等；

在Xcode中管理，5.0之后，打开Preference，选择Accounts；点击View Details...

![](http://images.cnitblog.com/blog/557746/201401/192155201420.png)

点击刷新，可以刷新XCode和设备上的Provisioning Prefiles；同时可以制作Development&nbsp;Provisioning Prefiles和Distribution&nbsp;Provisioning Prefiles。

![](http://images.cnitblog.com/blog/557746/201401/192157532050.png)

&nbsp;

**现在上具体流程：**

**1、开发者证书制作**<span>**安装步骤：**大概过程是：</span>选择授权类型，开发还是发布－&gt;提交在mac设备中创建的公钥－&gt; 下载证书，并安装

<span>　　**1.1、创建证书签名请求公钥**</span>

<span><span>　　　　<span>打开应用程序中的&ldquo;钥匙串访问&rdquo;，在菜单里点击&ldquo;</span><span><span>证书</span></span><span>助理&rdquo;下面的&ldquo;从</span><span><span>证书</span></span><span>代理请求</span><span><span>证书</span></span><span>&rdquo;，注意下面不要选中任何行，否则菜单会变。</span></span></span>

![](http://images.cnitblog.com/blog/557746/201401/192158514399.png)

<span><span><span>输入自己的注册Email，常用名称可以写详细一点，比如&ldquo;Unfish Dev Key&rdquo;，下面选中&ldquo;存储到磁盘&rdquo;，点继续，可能会出现未知错误，多试几次就OK了。</span></span></span>

![](http://images.cnitblog.com/blog/557746/201401/192159129554.png)

**1.2、创建开发者证书**

<span>在浏览器中进入开发者中心的Certificaties导航页面，<span>点击右上角的加号；</span></span>

![](http://images.cnitblog.com/blog/557746/201401/192201156897.png)

证书类型选择iOS App Development,点击继续;

![](http://images.cnitblog.com/blog/557746/201401/192201576421.png)

下个界面需要一个CSR文件，就是我们在1.1中创建的公钥;选取你生成的csr文件，点击生成。

![](http://images.cnitblog.com/blog/557746/201401/192202275175.png)

点击生成后会生成一个cer证书<span>，如图所示；</span>

![](http://images.cnitblog.com/blog/557746/201401/192203302989.png)

点击Download，下载下来，双击打开<span>双击会自动导入钥匙串，导入类型选择&ldquo;登录&rdquo;就可以。</span>

![](http://images.cnitblog.com/blog/557746/201401/192205136736.png)

&nbsp;

**1.3、添加你的测试设备**（<span style="color: #ff0000;">这一步只是开发证书有用，发布证书是不需要这个东西的。</span>）

把手机连到电脑上，在xcode的windows菜单下打开Organizer，左边的devices下面会列出你的手机，右边的Identifier项就是你的手机的udid，复制下来，待会要用。如果这个手机还没有设置过作为开发机，这个界面上应该会有两个按钮，点一下就可以把它设为开发机，在xcode里点Build and go的时候就可以自动的安装你的程序到手机上，不需要通过ITunes了。

![](http://images.cnitblog.com/blog/557746/201401/192207485170.png)

在开发者中心的左边Devices导航页，点击右上角Add Device;

![](http://images.cnitblog.com/blog/557746/201401/192208315025.png)

起个自己认识的名字，把udid粘贴进去就可以了,在所有设备中会出现你的设备。

　![](http://images.cnitblog.com/blog/557746/201401/192209073611.png)

**1.4、创建App ID:**

我们需要为应用创建一个App ID，他是识别这个应用的唯一标识，在开发者中心的Identifier导航页的App ID页面，点击右上角创建新的App ID,<span>这个App ID需要跟程序的info.plist里面定义的程序Identifier相对应；</span>

![](http://images.cnitblog.com/blog/557746/201401/192209409089.png)

为App ID命名，注意命名规则：

![](http://images.cnitblog.com/blog/557746/201401/192210528774.png)

选择App ID类型：

Explicit类型是不使用*通配符，支持所有App Services;Wildcard App ID类型，可以使用*通配符命名。

如果这里只输入一个*，那么info.plist里Bundle identifier写什么都可以，使用*通配符，适用于批量的，没有推送，Game Center等服务的应用。如果你的应用要用到推送、GameCenter、PassCard等服务请勿使用。

格式规则：苹果建议使用域名倒序的方式命名；

　　　　　若使用通配符com.*命名，那么在应用的info.plist中Bundle identifier只要以com.开头，后面写什么都可以。

![](http://images.cnitblog.com/blog/557746/201401/192211400804.png)

![](http://images.cnitblog.com/blog/557746/201401/192211573451.png)

继续点击提交创建成功。

**1.5、创建Provisioning Prefiles文件**

　　<span style="line-height: 1.5;">进入开发者中心的Provisioning菜单，点击右上角创建；</span>

![](http://images.cnitblog.com/blog/557746/201401/192215080333.png)

Provisioning profile选择iOS App Development;

![](http://images.cnitblog.com/blog/557746/201401/192215502526.png)

选择对应应用的App ID;

![](http://images.cnitblog.com/blog/557746/201401/192216330959.png)

选择所使用的证书；

![](http://images.cnitblog.com/blog/557746/201401/192217081587.png)

选择对应要使用的设备，我一般全选，省事儿；

![](http://images.cnitblog.com/blog/557746/201401/192217352523.png)

取个见名知意的名儿，并点击生成；

![](http://images.cnitblog.com/blog/557746/201401/192218024706.png)

生成以后下载这个provisioning文件，把下载下来的文件拖到Dock上的Xcode的图标上就会自动安装进去。

然后退出xcode重新打开，打开你的项目，打开Project菜单下面的Edit Project Settings，进入Build页面，找到下面的<span id="rlt_8">Code</span>&nbsp;Signing Identity，把Any iPhone OS Device项后面选中你的开发者证书，然后就可以把项目的编译目标设为Device（而不是模拟器），试一下Run。程序应该会编译完并自动安装到你的手机上，并自动启动了。

![](http://images.cnitblog.com/blog/557746/201401/192220138453.png)

&nbsp;

&nbsp;

<span>**2、发布证书制作安装步骤：**
</span>

**2.1、**同1.1一样，我们创建证书签名请求公钥，如果已经创建过，就直接使用以前的那个就行（CSR文件）。

![](http://images.cnitblog.com/blog/557746/201401/192221037366.png)

进入开发者中心Certificates里面的Distribution页面，选择右上角添加；

![](http://images.cnitblog.com/blog/557746/201401/192221262059.png)

证书类型选择Production App Store and Ad Hoc;

![](http://images.cnitblog.com/blog/557746/201401/192221499080.png)

之后步骤和创建Development证书一样，提交SRC文件，创建成功；

![](http://images.cnitblog.com/blog/557746/201401/192223475179.png)

然后下载这个证书，双击导入，类型选登录。

**2.2、创建Distribution Provisioning Profiles**：

<span>进入开心者中心Provisioning里面的Distribution页面，点击右上角添加新的Provisioning；</span>

![](http://images.cnitblog.com/blog/557746/201401/192224275179.png)

<span>类型选App Store，（如果选了Ad Hoc，就只有你添加过udid的手机才能安装这个程序了，<span>一般用于企业帐号，这里我们不介绍</span>）；</span>

![](http://images.cnitblog.com/blog/557746/201401/192225186117.png)

<span><span><span>之后步骤和创建Development证书基本一致（少了一个选择设备步骤），Distribution证书选好</span></span>，App ID选好，取好名称，创建完成；下载这个文件，拖到xcode的图标上安装完成。退出xcode再重新打开，否则加载不了你刚刚添加的那个<span><span>证书</span></span>，会出现Provisioning无效的提示。</span>

<span>打开工程，进入Build Settings页面，在Code Signing Identity里面的Any iPhone OS Device后面对应的值选中你的那个Distribution的<span><span>证书</span></span>。然后就可以编译程序了。</span>

![](http://images.cnitblog.com/blog/557746/201401/192225542837.png)

**3、在Xcode中创建Provisioning Prefiles**

在Xcode中管理，5.0之后，打开Preference，选择Accounts；点击View Details...

![](http://images.cnitblog.com/blog/557746/201401/192226258920.png)

点击添加Development&nbsp;Provisioning Prefiles，步骤和MemberCenter添加过程基本一致；

![](http://images.cnitblog.com/blog/557746/201401/192226552836.png)

&nbsp;　　账号密码换了，验证失败，下回在给大家介绍吧。

&nbsp;

&nbsp;

<span>**4、如何要把测试版发给别人：**&nbsp;</span>

*   <span>a.进入member center，把device id注册为测试机；</span>
*   <span>b.建立一个Ad hoc类型的provisioning profile，包含刚才注册的device id ；</span>
*   <span>c.打包时code signing使用刚生成的文件 ；</span>
*   <span>d.把ipa给要测试人员即可；</span>

&nbsp;

**5、程序发布到AppStore的步骤：**

1.  在开发者中心的Distribution导航里面的App Store页面，在下面的App Store下面的Learn More展开，就可以看到Go to iTunes Connect的链接。进入以后点击Manage My Application，就可以添加自己的程序了。
2.  <span>需要选择程序的语言，国家，介绍等等基本信息，然后下一步上传程序和其它<span id="rlt_7">图片</span>。这个地方上传的程序，就是把你的项目编译以后生成的那个.app的目录直接压缩成zip上传，（这个.app目录里面的任何文件都不可以更改，否则签名就不对了）。你需要准备一张512&times;512的png图片作为大Logo，还需要准备5张程序运行的界面截图，在这个页面上挨个选Choose file，再点击upload file，出现对号就没<span id="rlt_4">问题</span>了。然后选择程序的价格，默认有很多可以选，free是免费，1是0.99美元，2是1.99美元，诸如此类。如果非免费程序，你还得准备一份购买协议。</span>
3.  <span>再点下一步，就可以等待程序审核了。</span>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;