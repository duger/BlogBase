title: iOS中 nil Nil NULL 的区别 - duger
tags: []
date: 2014-01-19 11:51:00
category:
---

<!--?xml version="1.0" encoding="UTF-8" standalone="no"?-->

nil用来给对象赋值（Objective-C中的任何对象都属于id类型），NULL则给任何指针赋值，NULL和nil不能互换，nil用于类指针赋值（在Objective-C中类是一个对象，是类的meta-class的实例）， 而NSNull则用于集合操作，虽然它们表示的都是空值，但使用的场合完全不同。

<span>示例如下：</span>

1.  id&nbsp;object&nbsp;=&nbsp;nil;&nbsp;&nbsp;
2.  //&nbsp;判断对象不为空&nbsp;&nbsp;
3.  if&nbsp;(object)&nbsp;{&nbsp;&nbsp;
4.  }&nbsp;&nbsp;
5.  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
6.  //&nbsp;判断对象为空&nbsp;&nbsp;
7.  if&nbsp;(object&nbsp;==&nbsp;nil)&nbsp;{&nbsp;&nbsp;
8.  }&nbsp;&nbsp;
9.  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
10.  //&nbsp;数组初始化，空值结束&nbsp;&nbsp;
11.  NSArray&nbsp;*array&nbsp;=&nbsp;[[NSArray&nbsp;alloc]&nbsp;initWithObjects:@"First",&nbsp;@"Second",&nbsp;nil];&nbsp;&nbsp;
12.  &nbsp;&nbsp;
13.  //&nbsp;判断数组元素是否为空&nbsp;&nbsp;
14.  NSString&nbsp;*element&nbsp;=&nbsp;[array&nbsp;objectAtIndex:2];&nbsp;&nbsp;
15.  if&nbsp;((NSNull&nbsp;*)element&nbsp;==&nbsp;[NSNull&nbsp;null])&nbsp;{&nbsp;&nbsp;
16.  }&nbsp;&nbsp;<div><span><span>今天做项目的时候就遇到，要判断数组元素是否为空，我的以下写法，都无效</span></span></div><div><span><span>if(!element)</span></span></div><div><span><span>if([element length]&gt;0)</span></span></div><div><span><span>if(element== NULL)</span></span></div><div><span><span>if(element == Nil)</span></span></div><div><span><span>&nbsp;</span></span></div><div><span><span>&nbsp;</span></span></div>

1.  &nbsp;&nbsp;
2.  //&nbsp;判断字典对象的元素是否为空&nbsp;&nbsp;
3.  NSDictionary&nbsp;*dictionary&nbsp;=&nbsp;[NSDictionary&nbsp;dictionaryWithObjectsAndKeys:&nbsp;&nbsp;
4.  &nbsp;&nbsp;&nbsp;&nbsp;@"iPhone",&nbsp;@"First",&nbsp;@"iPad",&nbsp;@"Second",&nbsp;nil];&nbsp;&nbsp;
5.  NSString&nbsp;*value&nbsp;=&nbsp;[dictionary&nbsp;objectForKey:@"First"];&nbsp;&nbsp;
6.  if&nbsp;((NSNull&nbsp;*)value&nbsp;==&nbsp;[NSNull&nbsp;null])&nbsp;{&nbsp;&nbsp;
7.  } &nbsp;<div><span><span>欢迎大家继续补充他们的区别。
</span></span></div><div><span><span>加深理解一</span></span></div><div><span><span>&nbsp;</span></span></div><div>

1、nil：一般赋值给空对象；

2、NULL：一般赋值给nil之外的其他空值。如SEL等；

　　举个栗子（好重啊~）：

　　　　[NSApp beginSheet:sheet
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;　　&nbsp;modalForWindow:mainWindow

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;　　modalDelegate:nil //pointing to an object

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;　　didEndSelector:NULL&nbsp;//pointing to a non object/class

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;　　contextInfo:NULL]; //pointing to a non object/class

3、NSNULL：NSNull只有一个方法：+ (NSNull *) null;

　　[NSNull null]用来在NSArray和NSDictionary中加入非nil（表示列表结束）的空值.&nbsp;&nbsp;&nbsp;**[NSNull null]是一个对象，他用在不能使用nil的场合。**

4、当向nil发送消息时，返回NO，不会有异常，程序将继续执行下去；

　　而向NSNull的对象发送消息时会收到异常。

&nbsp;

因为在NSArray和NSDictionary中nil中有特殊的含义（表示列表结束），所以不能在集合中放入nil值。如要确实需要存储一个表示&ldquo;什么都没有&rdquo;的值，可以使用NSNull类。NSNull只有一个方法：

+ (NSNull *) null;

`<span>nil是一个对象指针为空，Nil是一个类指针为空，NULL是基本数据类型为空。这些可以理解为nil，Nil， NULL的区别吧。</span>`
</div><div><span><span>&nbsp;</span></span></div><div><span><span>加深理解二</span></span></div><div><span><span>&nbsp;</span></span></div><div>

浅谈关于nil和 null区别及相关问题

&nbsp;

&nbsp;

1、nil和null从字面意思来理解比较简单，nil是一个对象，而NULL是一个值，我的理解为nil是将对象设置为空，而null是将基本类型设置为空的，个人感觉有点像属性当中，基本类型分配为assign NSString类型一般分配copy，而对象一般用retain。而且我们对于nil调用方法，不会产生crash或者抛出异常。

看一段

nil -&gt; Null-pointer to objective- c object

NIL -&gt; Null-pointer to objective- c class

null-&gt; null pointer to primitive type or absence of data.

看一下用法

NSURL *url = nil；

Class&nbsp;&nbsp;class = Nil；

int *pointerInt = NULL；

nil是一个对象指针为空，Nil是一个类指针为空，NULL是基本数据类型为空。这些可以理解为nil，Nil， NULL的区别吧。

2、一个可以研究一下的问题

在dealloc中

-（void） dealloc

{

self.test = nil;&nbsp;

[_test release];

test = nil;

}

这几个的区别

先说最简单的&nbsp;&nbsp;[_test release]; 这个就是将引用技术减1，所谓的引用计数就是看看有多个指针指向一块内存实体，当release一次，就是指针减少一个，release到了0的时候，就是真正把这块内存归还给系统的时候了

&nbsp;

再说self.test = nil;说明一下 属性和setter和getter方法就不难理解了

&nbsp;

-（void） setTest：（NSString *）newString

{

if（_test != newString）

[_test release];

_test = [newString retain];

}

-（NSString *）test

{

return&nbsp;&nbsp;_test;

}

这个是setter和getter方法，而在这个问题中相当于刚才的代码改变为

if（_test != nil）

[_test release];

_test = nil;

现在就比较容易解释了，setter方法会retain nil对象，在这之前已经先release了旧的对象，这个方法优点是成员变量连指向随机数据的机会都没有，而通过别的方式，就可能会出现指向随机数据的情况。当release了之后，万一有别的方法要用要存取它，如果它已经dealloc了，可能就会crash，而指向nil之后，就不会发生错误了。nil说白了就是计数器为0，这么说吧，当真正release一个对象的时候，NSLog是打印不了它指向的内存控件的，而当nil的时候，是可以打印出来指向的一个内存空间。

那么现在也不难解释test = nil;&nbsp;&nbsp;单纯的这种用法可以说是自己给自己制造内存泄露，这里可以这么理解，就是相当于将指向对象的指针直接和对象一刀两断了。直接让test指向nil，而内存实体不会消失，也不会有系统回收。
</div>![](http://counter.cnblogs.com/blog/rss/3525818)

本文链接：[iOS中 nil Nil NULL 的区别](http://www.cnblogs.com/duger/p/3525818.html)，转载请注明。