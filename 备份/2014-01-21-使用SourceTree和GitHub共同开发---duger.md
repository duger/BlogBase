title: 使用SourceTree和GitHub共同开发 - duger
tags: [GitHub,SourceTree,Git]
date: 2014-01-21 23:10:00
category: [技术,Git]
---

<span>**GitHub详细教程**</span>

&nbsp;

&nbsp;

<span>**1&nbsp;Git详细教程**</span>

&nbsp;

<span>**1.1&nbsp;Git简介**</span>

&nbsp;

<span>**1.1.1&nbsp;Git是何方神圣?**</span>

&nbsp;

<span>Git是用C语言开发的分布版本控制系统。版本控制系统可以保留一个文件集合的历史记录，并能回滚文件集合到另一个状态（历史记录状态）。另一个状态可以是不同的文件，也可以是不同的文件内容。举个例子，你可以将文件集合转换到两天之前的状态，或者你可以在生产代码和实验性质的代码之间进行切换。文件集合往往被称作是&ldquo;源代码&rdquo;。在一个分布版本控制系统中，每个人都有一份完整的源代码（包括源代码所有的历史记录信息），而且可以对这个本地的数据进行操作。分布版本控制系统不需要一个集中式的代码仓库。</span>

&nbsp;

<span>当你对本地的源代码进行了修改，你可以标注他们跟下一个版本相关（将他们加到index中），然后提交到仓库中来（commit）。Git保存了所有的版本信息，所以你可以转换你的源代码到任何的历史版本。你可以对本地的仓库进行代码的提交，然后与其他的仓库进行同步。你可以使用Git来进行仓库的克隆（clone）操作，完整的复制一个已有的仓库。仓库的所有者可以通过push操作（推送变更到别处的仓库）或者Pull操作（从别处的仓库拉取变更）来同步变更。</span>
<!--more-->
&nbsp;

<span>Git支持分支功能（branch）。如果你想开发一个新的产品功能，你可以建立一个分支，对这个分支的进行修改，而不至于会影响到主支上的代码。</span>

&nbsp;

<span>Git提供了命令行工具；这个教程会使用命令行。你也可以找到图形工具，譬如与Eclipse配套的EGit工具，但是这些都不会在这个教程中进行描述。</span>

&nbsp;

<span>**1.1.2&nbsp;重要的术语**</span>

&nbsp;

<span>Git 术语</span>

<span>**术语**</span>

<span>**定义**</span>

&nbsp;

<span>**仓库**</span>

<span>一个仓库包括了所有的版本信息、所有的分支和标记信息.</span>

<span>**Repository**</span>

<span>在Git中仓库的每份拷贝都是完整的。仓库让你可以从中</span>

<span>&nbsp;</span>

<span>取得你的工作副本。</span>

<span>&nbsp;</span>

<span>一个分支意味着一个独立的、拥有自己历史信息的代码线</span>

<span>**分支**</span>

<span>（code line）。你可以从已有的代码中生成一个新的分支</span>

<span>Branches</span>

<span>，这个分支与剩余的分支完全独立。默认的分支往往是叫</span>

<span>&nbsp;</span>

<span>master。用户可以选择一个分支，选择一个分支叫做</span>

<span>&nbsp;</span>

<span>**checkout**.</span>

<span>标记</span>

<span>一个标记指的是某个分支某个特定时间点的状态。通过标</span>

<span>Tags</span>

<span>记，可以很方便的切换到标记时的状态，例如2009年1月25</span>

<span>&nbsp;</span>

<span>号在testing分支上的代码状态</span>

<span>**提交**</span>

<span>提交代码后，仓库会创建一个新的版本。这个版本可以在</span>

<span>Commit</span>

<span>后续被重新获得。每次提交都包括作者和提交者，作者和</span>

<span>&nbsp;</span>

<span>提交者可以是不同的人</span>

<span>URL</span>

<span>URl用来标识一个仓库的位置</span>

<span>&nbsp;</span>

<span>用来表示代码的一个版本状态。Git通过用SHA1 hash算法</span>

<span>修订</span>

<span>表示的id来标识不同的版本。每一个 SHA1 id都是160位长</span>

<span>Revision</span>

<span>,16进制标识的字符串.最新的版本可以通过HEAD来获取.</span>

<span>&nbsp;</span>

<span>之前的版本可以通过"HEAD~1"来获取，以此类推。</span>

&nbsp;

<span>**1.1.3&nbsp;索引**</span>

&nbsp;

<span>Git 需要将代码的变化显示的与下一次提交进行关联。举个例子，如果你对一个文件继续了修改，然后想将这些修改提交到下一次提交中，你必须将这个文件提交到索引中，通过git add file命令。这样索引可以保存所有变化的快照。</span>

&nbsp;

<span>新增的文件总是要显示的添加到索引中来。对于那些之前已经提交过的文件，可以在commit命令中使用-a 选项达到提交到索引的目的。</span>

&nbsp;

<span>**1.2&nbsp;Git 安装 配置**</span>

&nbsp;

<span>我们使用SourceTree这个软件，他将Git已经配置好了，我们只需要用就可以了。</span>

&nbsp;

<span>**1.3&nbsp;开始操作Git**</span>

<span>后续将通过一个典型的Git工作流来学习。在这个过程中，你会使用SourceTree创建一些文件、创建一个本地的Git仓库、提交你的文件到这个仓库中。这之后，你会克隆一个仓库、在仓库之间通过pull和push操作来交换代码的修改。</span>

<span>**1.3.1&nbsp;创建仓库、添加文件和提交更改**</span>

<span>每个Git仓库都是放置在.git文件夹下.这个目录包含了仓库的所有历史记录，.git/config文件包含了仓库的本地配置。</span>

<span>以下我们会创建一个本地Git工程仓库。</span>

<span>Xcode自带有Git管理，在创建工程时，勾选Source Control即可。</span>

<span>![](webkit-fake-url://4E7B7AFF-CB51-47FC-9F4D-33B81868BB1F/image.tiff)</span>

<span>此工程将自带git配置，但是由于Xcode 在git管理上优化有问题，会让电脑出现卡顿，所以我们不建议用Xcode管理git仓库。</span>

<span>我们一般采用的方式是，用Xcode建立好工程之后，再运行终端，将此工程变为Git仓库，使用git init命令：</span>

<span>![](webkit-fake-url://573A3325-F19F-4ED9-989B-992E1293A0D4/image.tiff)</span>

<span>然后，我们将此工程添加入SourceTree进行管理。</span>

<span>![](webkit-fake-url://313E10D2-3405-4169-BA5F-388FBDABA39E/image.tiff)</span>

<span>一个本地工程已经添加进来了。</span>

<span>![](webkit-fake-url://9B96EEF3-E9D8-4E82-8DA5-88ADA19BF99C/image.tiff)</span>

<span>点击进入TuYaPRO工程：</span>

&nbsp;![](webkit-fake-url://F9A1CDA9-375B-49C9-97A8-8869ACC1577F/image.tiff)

<span>**1.3.2&nbsp;commit更改**</span>

<span>下方有问号的就是我们的工程文件，现在我们将他们添加到Git仓库内。全选他们，点击Commit键。</span>

<span>Commit成功后，出现一条记录和描述。</span>

&nbsp;![](webkit-fake-url://3D5A6A5F-B64E-4A17-89FB-DCAC649FEE05/image.tiff)

&nbsp;

<span>**1.4&nbsp;远端仓库（remote repositories）**</span>

&nbsp;

<span>**1.4.1&nbsp;设置一个远端的GitHub仓库**</span>

&nbsp;

<span>我们将创建一个远端的GitHub仓库。这个仓库可以存储在本地或者是网络上。</span>

&nbsp;

<span>远端Git仓库和标准的Git仓库有如下差别：一个标准的Git仓库包括了源代码和历史信息记录。我们可以直接在这个基础上修改代码，因为它已经包含了一个工作副本。但是远端仓库没有包括工作副本，只包括了历史信息。</span>

&nbsp;

<span>**1.4.2&nbsp;添加远端仓库**</span>

<span>首先我们需要创建一个Repository;</span>

&nbsp;![](webkit-fake-url://B99D9AE0-D4B6-4B39-808F-586C26B4DEE3/image.tiff)

<span>填写工程信息：</span>

&nbsp;![](webkit-fake-url://381B2A89-CC05-4E3F-AF58-2AA19F6EC68F/image.tiff)

<span>创建完成后，Clone此工程下来，复制此工程的URL：</span>

&nbsp;![](webkit-fake-url://98922CF5-63F4-4286-A390-1FC2A71DD7D4/image.tiff)&nbsp;

<span>在SourceTree中Clone Repository。</span>

<span>![](webkit-fake-url://6D63622A-2FEF-40C5-AE15-1C6A3B67B7B3/image.tiff)</span>

![](webkit-fake-url://B231F988-E6A0-442E-998C-A77B512FE840/image.tiff)

<span>将原来的工程文件拖入TuYaPRO目录中。</span>

![](webkit-fake-url://2B49FFFF-6278-437C-897B-DDA9F9224C8D/image.tiff)

<span>全选下面的工程文件，点击commit提交到本地仓库。</span>

&nbsp;![](webkit-fake-url://5B63F70D-7AA9-4D23-87FE-D73163DFCA4A/image.tiff)

<span>master中出现一个pull标记，同时多了一条记录：</span>

&nbsp;![](webkit-fake-url://E72CB933-BF39-4B1C-BB84-6BD950C1484F/image.tiff)

<span>**1.4.3&nbsp;推送更改到GitHub的仓库**</span>

<span>在下方的Origin中，显示的是GitHub服务器上的工程，它显示的进度还在第一条Initial commit;这时我们需要将本地master中的更改Push到GitHub服务器。</span>

<span>![](webkit-fake-url://3EDC009B-E4BA-41D6-8BBB-24DBE69D5C01/image.tiff)</span>

<span>点击右上方的Push按钮。</span>

&nbsp;![](webkit-fake-url://162FE251-B8FA-47E6-875C-DA74CEC6A6BA/image.tiff)

<span>提交成功后，我们看到服务器端的进度已经和我们本地同步了。</span>

&nbsp;![](webkit-fake-url://6AD108DF-F309-4E13-B305-4B0B6335C45D/image.tiff)

<span>**1.4.4&nbsp;拉取（Pull）更改**</span>

<span>通过拉取，可以从GitHub的仓库中获取最新的更改。</span>

&nbsp;![](webkit-fake-url://BF5674A0-98F6-43A2-B32B-B51E745CA2F4/image.tiff)

<span>**1.4.5&nbsp;还原更改**</span>

<span>如果你还未把更改加入到索引中，你也可以直接还原所有的更改；也可以通过revert命令进行还原操作 。</span>

&nbsp;![](webkit-fake-url://9430373C-3AA0-40F2-AD3C-1DF42701369A/image.tiff)

<span>即使你删除了一个未添加到索引和提交的文件，你也可以还原出这个文件，点击Checkout。</span>

<span>![](webkit-fake-url://4290AFE6-1E22-4E8D-B9F5-EB26E5F03780/image.tiff)</span>

<span>如果你已经添加一个文件到索引中，但是未提交。可以通过git resetfile 命令将这个文件从索引中删除。</span>

&nbsp;![](webkit-fake-url://1BF91582-68F1-41FF-B1D6-92FED18638DA/image.tiff)

&nbsp;

<span>**1.5&nbsp;分支、合并**</span>

&nbsp;

<span>**1.5.1&nbsp;分支**</span>

<span>通过分支，可以创造独立的代码副本。默认的分支叫master。Git消耗很少的资源就能创建分支。Git鼓励开发人员多使用分支。</span>

<span>点击Branch添加分支;</span>

<span>![](webkit-fake-url://71DAA4FC-359E-4FDB-9A71-A7CC556B49E7/image.tiff)&nbsp; &nbsp; &nbsp; &nbsp;![](webkit-fake-url://007E1B0A-131E-43EA-843F-15862518A1F3/image.tiff)</span>

<span>Branches列表会出现Dev分支，我们可以在此分支开发我们负责的部分。</span>

&nbsp;![](webkit-fake-url://26242A6C-EF1B-47E5-9B7D-226E4AA7086B/image.tiff)

<span>**1.5.2&nbsp;合并**</span>

<span>通过Merge我们可以合并两个不同分支的结果。Merge通过所谓的三路合并来完成。分别来自两个分支的最新commit和两个分支的最新公共commit。</span>

<span>我们在Dev分支进行进行一些工作之后，可以看到Dev和master的进度是不一样的。</span>

<span>![](webkit-fake-url://C0D4C6C7-6643-4B08-A953-03FD6C3FE310/image.tiff)</span>

<span>这时我们返回master分支，点击merge；</span>

<span>![](webkit-fake-url://33F52041-539E-4B1D-A0AA-2EA83B5997F3/image.tiff)</span>

<span>合并之后我们发现，master已经和Dev同步了。</span>

<span>![](webkit-fake-url://1BD436FC-AE55-4EC9-9635-61844CB6B3CA/image.tiff)</span>

<span>这时我们发现orgin和master并不同步，这时因为我们的更改并没有推送到在线的GitHub服务器，这时我们可以把我们的更改Push到Github上，点击Push。</span>

&nbsp;![](webkit-fake-url://E44D6FFF-4549-46B2-980C-110B9D348C3B/image.tiff)

<span>这时我们可以看到服务器和本地都同步了。</span>

<span>但是有时多人开发时，有可能会发生合并冲突。</span>

<span>一旦合并发生了冲突，Git会标志出来，开发人员需要手工的去解决这些冲突。解决冲突以后，就可以将文件添加到索引中，然后提交更改。</span>

&nbsp;

&nbsp;

<span>**1.5.3&nbsp;合并请求（pull Requests）一个分支到源仓库**</span>

<span>我们在Github上Fork的项目，当一个人完成开发后，需要提出一个合并请求，请求把自己写的部分与主项目合并。</span>

<span>在Github上的自己Fork的工程右边点击Pull Requests，点击New pull request。</span>

&nbsp;![](webkit-fake-url://4757F9BB-D6F6-4DA2-90CC-4E8D0EE8A744/image.tiff)

![](webkit-fake-url://4E9F7DC4-7A54-4384-ADD4-94CBE68B59AD/image.tiff)

<span>这里会比较你和主项目的不同，然后点击创建pull request。</span>

<span>![](webkit-fake-url://E2F35A47-0430-411B-879B-F66C57556C6D/image.tiff)</span>

<span>这里写入你要合并部分的介绍，完成了什么功能等等，点击发送。</span>

<span>![](webkit-fake-url://2C8570F0-027C-47B6-8745-D8E870F844E3/image.tiff)</span>

<span>项目拥有者收到你的请求，根据你完成的功能，和你沟通后可以选择合并或者否决你的请求。</span>

<span>合并需要手工完成，具体请向度娘了解。</span>

<span>**1.6&nbsp;解决合并冲突**</span>

&nbsp;

<span>如果两个不同的开发人员对同一个文件进行了修改，那么合并冲突就会发生。而Git没有智能到自动解决合并两个修改。</span>

&nbsp;

&nbsp;

&nbsp;

<span>**1.7&nbsp;链接和文章**</span>

<span>1.[<span>Git相关学习资源</span>](http://download.csdn.net/detail/tangbin330/5625037)</span>

<span>Github&nbsp; [<span>www.github.com</span>](http://www.github.com)</span>

<span>SourceTree出的Git教程：[<span>www.atlassian.com/git</span>](http://www.atlassian.com/git)</span>

<span>某个台湾人写的Git教程：[<span>http://pan.baidu.com/s/1eQBUIe2</span>](http://pan.baidu.com/s/1eQBUIe2)</span>

<span>一些GitHub资料：[<span>http://pan.baidu.com/s/1qW8m5fY</span>](http://pan.baidu.com/s/1qW8m5fY)</span>

<span>吴丽和王帅帅拍的片儿：</span>

<span>[http://pan.baidu.com/share/link?shareid=422160084&amp;uk=2922146712](http://pan.baidu.com/share/link?shareid=422160084&amp;uk=2922146712)</span>

<span>[http://pan.baidu.com/s/128WT3](http://pan.baidu.com/s/128WT3)</span>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

<span>Author: Duger.Wang&lt;[<span>wfyh007@gmail.com</span>](mailto:wfyh007@gmail.com)&gt;</span>

<span>Date:2013年12月29日 星期日</span>

<span>Github:&lt;[<span>github.com/duger</span>](http://github.com/duger)&gt;</span>

<span>&nbsp;</span>
![](http://counter.cnblogs.com/blog/rss/3496495)

本文链接：[使用SourceTree和GitHub共同开发](http://www.cnblogs.com/duger/p/3496495.html)，转载请注明。