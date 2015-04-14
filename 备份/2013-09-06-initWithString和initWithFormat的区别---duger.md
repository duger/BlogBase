title: initWithString和initWithFormat的区别 - duger
tags: [NSString,iOS]
date: 2013-09-06 14:02:00
category: [技术,iOS]
---

<span><span>网上看到一篇文章，分析initWithString和initWithFormat的区别，用一下他的用例。</span></span>
<div>&nbsp;</div><div>下面是测试代码：</div><div>

<span>NSString</span>&nbsp;<span>* str =[[</span><span>NSString</span>&nbsp;<span>alloc</span><span>]</span>&nbsp;<span>initWithString</span><span>:</span>@"this is from initWithString function"<span>];</span>

<span>NSLog</span><span>(</span>@"this is from [[NSString alloc] initWithString] m_addr is %ld retainCount is %i"<span>, str, [str</span><span>retainCount</span><span>]);</span>

[str&nbsp;<span>release</span>];

[str&nbsp;<span>release</span>];

[str&nbsp;<span>release</span>];

<span>NSLog</span><span>(</span>@"this is from [[NSString alloc] initWithString] m_addr is %ld retainCount is %i"<span>, str, [str</span><span>retainCount</span><span>]);</span>

&nbsp;

<span>str = [[</span><span>NSString</span>&nbsp;<span>alloc</span><span>]</span>&nbsp;<span>initWithFormat</span><span>:</span>@"this is from initWithFormat function"<span>];</span>

<span>NSLog</span><span>(</span>@"this is from [[NSString alloc] initWithFormat] m_addr is %ld retainCount is %d"<span>, str, [str</span><span>retainCount</span><span>]);</span>

<span><span>&nbsp;</span></span>
</div>
<!--more-->
<span><span>下面是LOG的结果：</span></span>
<div><span>&nbsp;this is from [[NSString alloc] initWithString] m_addr is 12356 retainCount is 2147483647</span></div>

&nbsp;this is from [[NSString alloc] initWithString] m_addr is 12356 retainCount is 2147483647

&nbsp;this is from [[NSString alloc] initWithFormat] m_addr is 82076688 retainCount is 1

&nbsp;

&nbsp;this is from [[NSString alloc] initWithString] m_addr is 12356 retainCount is 2147483647

&nbsp;this is from [[NSString alloc] initWithString] m_addr is 12356 retainCount is 2147483647

&nbsp;this is from [[NSString alloc] initWithFormat] m_addr is 78748112 retainCount is 1

&nbsp;

&nbsp;this is from [[NSString alloc] initWithString] m_addr is 12356 retainCount is 2147483647

&nbsp;this is from [[NSString alloc] initWithString] m_addr is 12356 retainCount is 2147483647

&nbsp;this is from [[NSString alloc] initWithFormat] m_addr is 78777072 retainCount is 1

<span>&nbsp;</span>
<div>我将上面这段测试代码调用了三次，得到以上的LOG结果。顺便得出这么几条结论。对于不对，请大家鉴定。</div><div>1.从两个变量的地址看，两个变量的地址差据较大。前者的地址非常靠前。</div><div>2.从<span>release</span>看，前者无论被release多少次，都不会被释放，而且的值不变，而后者只要release一次，变量即消亡。</div><div>3.前者的releaseCount=&nbsp;<span>NSIntegerMax</span>,而<span>NSIntegerMax ==</span><span>INT_MAX&nbsp;</span>,而<span>&nbsp;UINT_MAX== (INT_MAX *&nbsp;<span>2U</span>&nbsp;+&nbsp;<span>1U</span>)</span>。</div><div>U是指无符号整型，而我们默认的int和NSInteger是有符号的，在32位系统中，NSIntegerMax=0X7FFFFFFF，对其乘以2U，即一次向左挪一位。结果为0XFFFFFFFE，再加1U为0XFFFFFFFF，即无符号的最大值。</div><div>然后根据苹果官方对于retainCount方法的描述：</div><div>

**<span>retainCount</span>**

Returns the receiver&rsquo;s reference count. (required)
<div>- (NSUInteger)retainCount</div><div>

**Return Value**

The receiver&rsquo;s reference count.
</div><div>

**Discussion**

You might override this method in a class to implement your own reference-counting scheme. For objects that never get released (that is, their&nbsp;`[release](file:///Developer/Platforms/iPhoneOS.platform/Developer/Documentation/DocSets/com.apple.adc.documentation.AppleiOS4_2.iOSLibrary.docset/Contents/Resources/Documents/documentation/Cocoa/Reference/Foundation/Protocols/NSObject_Protocol/Reference/NSObject.html#//apple_ref/occ/intfm/NSObject/release)`&nbsp;method does nothing), this method should return&nbsp;`UINT_MAX`, as defined in&nbsp;`&lt;limits.h&gt;`.

<span>小生窃以为，此处的UINT_MAX和</span><span>NSIntegerMax是一样的，都是表示所在类型的最大值。所以，initWithString这个方法初始化后的对象是不可能被release的或者说，它的release方法啥也不干。</span>

<span style="font-size: large;"><span>验证了上面的分析2.因为他</span></span><span>不可能被release的或者说，它的release方法啥也不干，所以我们调用无数次release都没有起到预先想想的作用。</span>

<span style="text-decoration: line-through;">为什么会导致这样的情况呢？</span>

<span style="text-decoration: line-through;">我们再次把目光转向地址。眼尖的同学可能会看到initWithString申请的地址每次都是一样的，而initWithFormat的地址每次都不一样，这个说明什么？</span>

<span style="text-decoration: line-through;">说明initWithString的地址是静态的，而initWithFormat是动态的。为什么前者是静态的，而后者是动态的？</span>

<span style="text-decoration: line-through;">结合上面关于retainCount的分析，小生窃以为initWithString的地址申请是在编译是进行的，这样才能说明为什么它的地址空间如此靠前。只有在编译是进行的，他才是静态的。</span>

<span style="text-decoration: line-through;">对于initWithString生成的对象，对其进行dealloc时，程序会报错（这里就不贴LOG了）。而后者initWithFormat不会报错。这进一步验证了initWithString生成的是静态对象，而initWithFormat是动态的。</span>

<span style="text-decoration: line-through;">&nbsp;</span>

<span style="text-decoration: line-through;">结论：initWithString生成的对象是在编译是申请地址空间，而且在程序中不能释放，不建议使用。（当然也有可能在某种情况下会使用到这个方法，在此不多加讨论）。</span>
</div></div>

* * *

<span style="font-size: 14pt;">**以下解释更为准确：**</span>

<span style="font-size: 16px;">initWithString一个是作为常量字符串，只读数据，</span>
<span style="font-size: 16px;">[[NSString&nbsp;alloc]initWithString:@"this&nbsp;is&nbsp;from&nbsp;initWithString&nbsp;function"];</span>
<span style="font-size: 16px;">这段代码是返回一个不可变对象，而常量字符串本就是不可变，所以这个时候内存会通过isa优化，alloc的区域作废，对象直接指向了"this&nbsp;is&nbsp;from&nbsp;initWithString&nbsp;function"这段常量字符串存储区,作为常量字符串没有retain，所以你retain无数次还是哪个UINR_MAX</span>
<span style="font-size: 16px;">[[NSMutableString&nbsp;alloc]initWithString:@"this&nbsp;is&nbsp;from&nbsp;initWithString&nbsp;function"];</span>
<span style="font-size: 16px;">而这一个是返回一个可变的对象，会另申请空间存放后面的常量字符串，这时其retaincount&nbsp;就为1</span>
<span style="font-size: 16px;">initWithFormat:是一编码格式读入数据，数据存放在哪是根据读入的参数确定的，跟initWithString是右区别的的；</span>

<span style="font-size: 16px;">[[NSString&nbsp;alloc]initWithFormat:@"dddd"];</span>
<span style="font-size: 16px;">count＝1；</span>

<span style="font-size: 16px;">[[NSMutableString&nbsp;alloc]&nbsp;initWithFormat:@""];</span>
<span style="font-size: 16px;">count＝1；</span>
<span style="font-size: 16px;">不管返回的是可变和不可变对象，其都会发送一个retain&nbsp;和autoRelease指令</span>
<span style="font-size: 16px;">这两个结果都一样</span>

* * *

<span style="font-size: 16px;"><span>&nbsp;</span></span><span style="font-size: 16px;">NSString * str1 = [[NSString alloc] initWithString:@"string 1"];</span>
<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;NSLog(@"str1's address %ld, retain count %d", (long)str1, [str1 retainCount]);</span>
<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;NSString * str2 = @"This is string 2";</span>
<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;NSLog(@"str2's address %ld, retain count %d", (long)str2, [str2 retainCount]);</span>
<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;NSString * str3 = @"string 1";</span>
<span style="font-size: 16px;">&nbsp;&nbsp;&nbsp;&nbsp;NSLog(@"str3's address %ld, retain count %d", (long)str3, [str3 retainCount]);</span>

<span style="font-size: 16px;">，运行后输出：</span>
<span style="font-size: 16px;">2011-07-09 21:49:06.526 Ex5_push_pop_nav[1788:207] str1's address 18176, retain count 2147483647</span>
<span style="font-size: 16px;">2011-07-09 21:49:06.526 Ex5_push_pop_nav[1788:207] str2's address 18208, retain count 2147483647</span>
<span style="font-size: 16px;">2011-07-09 21:49:06.527 Ex5_push_pop_nav[1788:207] str3's address 18176, retain count 2147483647</span>

<span style="font-size: 16px;">可见，str1和str3是在复用内存地址的。或者可以说[[NSString alloc] initWithString:@"string 1"]; 等价于@"string 1";</span>

&nbsp;

<span style="font-size: 16px;">所以说只要你代码中写了&nbsp;</span>
<span style="font-size: 16px;">NSString * str3 = @"string 1";</span>
<span style="font-size: 16px;">实际上就会在程序的静态变量区存在一个值为 &rdquo;string 1"的字符串常量。</span>
<span style="font-size: 16px;">而str3只是指向这个常量。因为这是一个常量，所以它的retainCount是MAX_INT</span>
<span style="font-size: 16px;">多次值为"string 1&ldquo;的变量产生，都会指向这一个常量的地址。</span>

<span style="font-size: 16px;">如果你把代码写成：</span>
<span style="font-size: 16px;">str = [[NSString alloc] initWithFormat:@"this is from initWithFormat function"];</span>
<span style="font-size: 16px;">实际上是把常量区的复制了一份在堆中，所以你可以release了。但是 @"this is from initWithFormat function" 这个变量本身还是存放在常量区了，所以并没有省内存，反而是增加了内存的使用。</span>

<span style="font-size: 16px;">所以，如果直接用一个字符串产生另一个字符串，尽量少用 initWithFormat，而不是initWithString。</span>

<span style="font-size: 16px;">而且，从功能上来说，initWithFormat可以支持 %d 等格式化字串，所以功能更强大一些，但是缺点就是产生了额外的内存空间。</span>
![](http://counter.cnblogs.com/blog/rss/3305348)

本文链接：[initWithString和initWithFormat的区别](http://www.cnblogs.com/duger/p/3305348.html)，转载请注明。