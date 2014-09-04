title: realloc 工作原理 - duger
tags: []
date: 2013-08-29 13:40:00
category:
---

<div id="box"><div id="box_inner"><div id="text"><div id="pages"><div id="page1" class="page"><div class="page_content"><div id="articleHeader">

**<span style="font-size: 16px;">realloc 用过很多次了<strong id="speechFragmentSeparator__1_1" class="speechFragmentSeparator">。**无非就是将已经存在的一块内存扩大。</span></strong>
</div>

<span style="font-size: 16px;">char* p = malloc(1024);</span>
<span style="font-size: 16px;">char* q = realloc(p,2048);</span>

<span style="font-size: 16px;">现在的问题是我们应该如何处理指针 p**。**&nbsp;刚开始按照我最直观的理解，如果就是直接将 p = NULL;。 到最后只需要释放 q的空间就可以了**。**</span>

<span style="font-size: 16px;">因为最近在做个封装。结果在做单元测试的时候发现。有时候我在 free(q); 的时候会出错。这样我就郁闷了**。**</span>

<span style="font-size: 16px;">后来仔细一跟踪，发现 realloc 完以后 q 和 p 的指针地址是一样**。**不过有时候又不一样**。**</span>

<span style="font-size: 16px;">仔细查了下资料。得到如下信息：</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1**.**如果 当前连续内存块足够 realloc 的话，只是将p所指向的空间扩大，并返回p的指针地址。 这个时候 q 和 p 指向的地址是一样的**。**</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.如果 当前连续内存块不够长度，再找一个足够长的地方，分配一块新的内存，q，并将 p指向的内容 copy到 q，返回 q**。**并将p所指向的内存空间删除。</span>

<span style="font-size: 16px;">这样也就是说 realloc 有时候会产生一个新的内存地址 有的时候不会**。**所以在分配完成后。我们需要判断下 p 是否等于 q。并做相应的处理。</span>

<span style="font-size: 16px;">这里有点要注意的是要避免 p = realloc(p,2048); 这种写法**。**有可能会造成 realloc 分配失败后，p原先所指向的内存地址丢失。</span>

<span style="font-size: 16px;">=========================================</span>

<span style="font-size: 16px;">关于realloc函数说明的补充：</span>
<span style="font-size: 16px;">函数定义：</span>
<span style="font-size: 16px;">void *realloc(void *ptr, size_t size);</span>
<span style="font-size: 16px;">上面的分析基本没有问题，但有两点要注意：</span>
<span style="font-size: 16px;">1**.**返回值可能与ptr的值不同，如果是不同的话，那么realloc函数完成后，ptr指向的旧内存已被free掉了**。**</span>
<span style="font-size: 16px;">2。如果返回NULL值，则分配不成功，而原来的ptr指向的内存还没有被free掉，要求程序显式free.</span>

<span style="font-size: 16px;">故p = (int *) realloc (p, sizeof(int) *15);语句有这么一个问题，</span>
<span style="font-size: 16px;">调用前p指向一个已分配成功的内存,而调用realloc时却失败（即返回NULL），此时，p原来指向的内存还没有free掉，而现在又找不到地址，这样就出现memory leak了**。**</span>

<span style="font-size: 16px;">关于这一点的确要注意，最好如下：</span>
<span style="font-size: 16px;">int *q</span>
<span style="font-size: 16px;">q = (int *) realloc (p, sizeof(int) *15);</span>

<span style="font-size: 16px;">if(**!**q) p =q;</span>

<span style="font-size: 16px;">======================================================</span>

<span style="font-size: 16px;">首先看一下下面的C程序片断：</span>

<span style="font-size: 16px;">#include &lt;malloc**.**h&gt;</span>

<span style="font-size: 16px;">char&nbsp;&nbsp;*p;</span>

<span style="font-size: 16px;">p = (char * ) malloc (10);</span>

<span style="font-size: 16px;">p = (char * ) realloc (p,20);</span>

<span style="font-size: 16px;">&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp; 这段程序的意思很简单，只有稍有点C基础的人都可以看懂**。**函数首先定义了一个字符型的指针p，然后为指针p分配了一个10个字节大小的内存空间，接着将这个内存块的大小**增加到**20个字节**。**</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp; 这里有什么问题吗？上机运行一下，好像没有问题！</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp; 是的，这样上机运行是没有问题的，但是这里存在着也许我们不太注意的**隐患**！隐患在那里？这就是我在本文中要详细说明的realloc（）函数了**。**</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp; 再看一下下面一段来自MSDN的话：</span>

<span style="font-size: 16px;">**realloc**&nbsp;returns a&nbsp;**void**&nbsp;pointer to the reallocated (and possibly moved) memory block**.**&nbsp;The return value is&nbsp;**NULL**&nbsp;if the size is zero and the buffer argument is not&nbsp;**NULL**, or if there is not enough available memory to expand the block to the given size**.**&nbsp;In the first case, the original block is freed**.**&nbsp;In the second, the original block is unchanged. The return value points to a storage space that is guaranteed to be suitably aligned for storage of any type of object**.**&nbsp;To get a pointer to a type other than&nbsp;**void**, use a type cast on the return value**.**</span>

<span style="font-size: 16px;">这段E文还不算是晦涩难懂，所以我就不翻译了，大致的意思是说关于realloc返回值的**。**但是这里对他的返回值分了几种情况：</span>

<span style="font-size: 16px;">1、&nbsp;&nbsp;返回void *&nbsp;指针，调用成功**。**</span>

<span style="font-size: 16px;">2、&nbsp;&nbsp;返回NULL，当需要扩展的大小（第二个参数）为0并且第一个参数不为NULL，此时原内存变成了&ldquo;freed（游离）&rdquo;的了**。**</span>

<span style="font-size: 16px;">3、&nbsp;&nbsp;返回NULL，当没有足够的空间可供扩展的时候，此时，原内存空间的大小维持不变**。**</span>

<span style="font-size: 16px;">第一种情况告诉了我们在得到需要的内存空间后需要做类型转换的工作；</span>

<span style="font-size: 16px;">第二种情况可能只有傻瓜才会去使用吧！</span>

<span style="font-size: 16px;">第三种情况，内存空间不够的时候就会维持未来的大小不变**。**</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; MSDN上面说内存空间不够的时候就不会扩展原来的内存空间的大小，这话固然没有错，但是有点含糊，似乎遗漏了一种情况！我们知道，**realloc****是从堆上分配内存的，当扩大一块内存空间时，&nbsp;realloc()****试图直接从堆上现存的数据后面的那些字节中获得附加的字节，如果能够满足，自然天下太平；可如果数据后面的字节不够的话，问题就出来了，那么就使用堆上第一个有足够大小的自由块，现存的数据然后就被拷贝至新的位置，而老块则放回到堆上<strong id="speechFragmentSeparator__1_30" class="speechFragmentSeparator">。**</strong>这句话传递的一个重要的信息就是数据可能被移动！看到这里，也许我们已经发现一开始我给出的程序的问题了**。**为了更清楚地说明问题，可以将上面的程序改成下面的形式：</span>

<span style="font-size: 16px;">#include &lt;malloc**.**h&gt;</span>

<span style="font-size: 16px;">char&nbsp;&nbsp;*p，*****q;</span>

<span style="font-size: 16px;">p = (char * ) malloc (10);</span>

<span style="font-size: 16px;">**q=p;**</span>

<span style="font-size: 16px;">p = (char * ) realloc (p,20);</span>

<span style="font-size: 16px;">&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;&hellip;</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp; 这段程序也许在编译器中没有办法通过，因为编译器可能会为我们消除一些隐患！在这里我们只是增加了一个记录原来内存地址的指针q，然后记录了原来的内存地址p，如果不幸的话，数据发生了移动，那么所记录的原来的内存地址q所指向的内存空间实际上已经放回到堆上了**!**这样一来，我们应该终于意识到问题的所在和可怕了吧！</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp; 这个问题似乎有点牛角尖的味道，因为我们也许从来不曾遇上过，但是我们应该明白这样的事情的始终存在，只有这样，在万一我们碰上的时候才会去有意识的去避免这种隐患，否则，一旦这样的隐患一旦发作，程序崩溃不说，恐怕查错也不是一件容易的事！</span>

<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp; 候俊杰在《深入浅出MFC》中引用林语堂的《朱门》中的一句话，我很有感触，虽然不可能有他的感触深，但是抱着向前辈学习的心态，所以也拿来作为本为的结束：</span>

<span style="font-size: 16px;">&ldquo;只用一样东西，不明白他的道理，实在不高明&rdquo;**。**</span>
<span style="font-size: 16px;">**
**</span></div></div></div></div></div></div><div id="fitts" title="隐藏覆盖"><span style="font-size: 16px;">&nbsp;</span></div><div id="next_pages_container"><span style="font-size: 16px;">&nbsp;</span></div><div id="audio_elements_container"><span style="font-size: 16px;">&nbsp;</span></div><div id="sidebar"><span style="font-size: 16px;">&nbsp;</span></div>![](http://counter.cnblogs.com/blog/rss/3288974)

本文链接：[realloc 工作原理](http://www.cnblogs.com/duger/p/3288974.html)，转载请注明。