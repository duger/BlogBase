title: "[转]ios中关于delegate （委托）的使用心得 - duger"
tags: []
date: 2013-09-11 20:24:00
category:
---

<div id="article_content" class="article_content"><span style="font-size: 15px;">从开始从事OC工作到现在大概1年多了，从当初接触oc的"协议"的不明白，到现在代码中随处可见的委托，协议，其中感悟颇多。</span><div><span style="font-size: 15px;">&nbsp;</span></div><div><span style="font-size: 15px;">首先，大家应该都明白的是委托是协议的一种，顾名思义，就是委托他人帮自己去做什么事。也就是当自己做什么事情不方便的时候，就可以建立一个委托，这样就可以委托他人帮自己去实现什么方法。</span></div><div><span style="font-size: 15px;">&nbsp;</span></div><div><span style="font-size: 15px;">其次，我简单的总结了一下自己用到的委托的作用有两个，一个是传值，一个是传事件。</span></div><div><span style="font-size: 15px;">1.所谓传值经常用在b类要把自己的一个数据或者对象传给a类，让a类去展示或者处理。（切分紧耦合，和代码分块的时候经常用）</span></div><div><span style="font-size: 15px;">2.所谓传事件就是a类发生了什么事，把这件事告诉关注自己的人，也就是委托的对象，由委托的对象去考虑发生这个事件后应该做出什么反映。（这个经常见，例如在异步请求中，界面事件触发数据层改变等等）</span></div><div><span style="font-size: 15px;">3.利用委托赋值，这种方法感觉是为了不暴露自己的属性就可以给自己复值，而且这样更方便了类的管理，只有在你想要让别人给你赋值的时候才调用，这样的赋值更可控一些。（例如tableView中的委托（dateSource）中常见）。</span></div><div><span style="font-size: 15px;">&nbsp;</span></div><div><span style="font-size: 15px;">最后，我想分享一下在使用委托的时候的一些心得和注意事项。</span></div><div><span style="font-size: 15px;">&nbsp;</span></div><div><span style="font-size: 15px;">心得：delegate的命名要准确，尽量看名字就知道用法。delegate和通知有的用法有些象，但是前者是单对单的，后者是单对多的情况。</span></div><div><span style="font-size: 15px;">注意：在dealloc要把delegate至为nil，还有就是delegate设置属性的时候要用assign，不要用retain。</span></div><div><span style="font-size: 15px;">&nbsp;</span></div><div><span style="font-size: 15px;">**委托**</span>

<span style="font-size: 15px;">在IOS中委托通过一种@protocol的方式实现,所以又称为协议.协议是多个类共享的一个方法列表,在协议中所列出的方法没有响应的实现，由其它人来实现.这叫好比我想买个手机，所以我有个buyIphone 方法,但是我不知道谁那买手机,所以把这个需求发布出去(比如公布在网站上),如果有卖手机的商人(也就是说他能实现buyIphone这个方法)看到，他就会接受我的委托,(在商人自己的类中实现&lt;XXXdelegate&gt;),那么我的委托对象就指向了这个商人..当我要买手机的时候，直接找他就行了.</span>

<span style="font-size: 15px;">例如:</span>
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="font-size: 15px;">@protocol MyDelegate
-(void)buyIphone:(NSString *)iphoneType money:(NSString *)money;

@end
@interface My : NSObject
{
    id&lt;MyDelegate&gt; deleage;
}
@property(assign,nonatomic)id&lt;MyDelegate&gt; delegate;
@end</span></div>

<span style="font-size: 15px;">代码中声明了一个协议 名叫Mydelegate,在其中有一个buyIphone方法，即一个委托项。当我要购买手机的时候只需要通过delegate 调用 BuyIphone方法即可.</span>

<span style="font-size: 15px;">如下:</span>
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="font-size: 15px;">-(void)willbuy
{
    [delegate buyIphone:@"iphone 4s" money:@"4888"];

}</span></div>

<span style="font-size: 15px;">我不必关心谁现实了这一委托，只要实现了这个委托的类，并且buyIphone是声明的委托中必须实现的方法，那么就一定能够得到结果.</span>

<span style="font-size: 15px;">例如:商人类实现了这一委托(用&lt;Mydelegate&gt;表示实现)</span>
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="font-size: 15px;">#import &lt;Foundation/Foundation.h&gt;
#import "My.h"
@interface Business : NSObject&lt;MyDelegate&gt;

@end</span></div>

<span style="font-size: 15px;">然后在 @implementation Business 中调用 buyIphone方法</span>
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="font-size: 15px;">#import "Business.h"

@implementation Business

-(void)buyIphone:(NSString *)iphoneType money:(NSString *)money
{
    NSLog(@"手机有货，这个价钱卖你了，发货中!!");
}
@end</span><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="font-size: 15px;">委托是Cocoa中最简单、最灵活的模式之一。委托是指给一个对象提供机会对另一个对象中的变化做出反应或者影响另一个对象的行为。其基本思想是：两个对象协同解决问题。一个对象非常普通，并且打算在广泛的情形中重用。它存储指向另一个对象（即它的委托）的引用，并在关键时刻给委托发消息。消息可能只是通知委托发生了某件事情，给委托提供机会执行额外的处理，或者消息可能要求委托提供一些关键的信息以控制所发生的事情。
![](http://www.1000phone.net/data/attachment/forum/201112/23/191755zeji8jas6qyidyqe.jpg)&nbsp;4 天前 上传下载附件 (18 KB) 委托方法通常包括3种动词：should、will、did。

should表示一个动作发生前，通常带有返回值，可以在动作发生之前改变对象状态。
will在动作发生前，委托可以对动作做出响应，但不带有返回值。
did在动作发生后做出的响应。

从方法的定义我们不难看出委托模式能够起到两方面的作用：

第一：委托协助对象主体完成某项操作，将需要定制化的操作通过委托对象来自定义实现，达到和子类化对象主体同样的作用。
第二：事件监听，委托对象监听对象主体的某些重要事件，对事件做出具体响应或广播事件交给需要作出响应的对象。

个人理解采用委托模式的好处在于：

1、避免子类化带来的过多的子类以及子类与父类的耦合
2、通过委托传递消息机制实现分层解耦

委托模式的实现思路：

1、通常是在对象主体包含一个委托对象的弱引用：
[@interface](http://tieba.baidu.com/i/sys/jump?un=interface)&nbsp;A : NSObject
{
IBOutlet id delegate;
} -(id) delegate;
-(void) setDelegate:(id)obj;
2、委托对象的实现有两种方式：正式协议和非正式协议，对象主体在协议中定义委托方法，委托对象可以选择实现其中某些委托方法，因此如果通过正式协议定义委托方法需要使用@option。
[@protocol](http://tieba.baidu.com/i/sys/jump?un=protocol)&nbsp;NSSearchDelegate
@option
-(void)didSearchFinish:(*NSNotification) aNotification;
@end
3、连接对象主体和委托，无非就是通过setDelegate:(id)obj来实现。

4、触发委托方法。
</span>

<span style="font-size: 15px;">昨天做了一个demo，用到了简单代理。</span>

<span style="font-size: 15px;">delegate是ios编程的一种设计模式。我们可以用这个设计模式来让单继承的objective-c类表现出它父类之外类的特征。昨天这个代理实现如下：</span>

<span style="font-size: 15px;">&nbsp;</span>

<span style="font-size: 15px;">类GifView是继承自UIView的，它加载在RootViewController上来通过一个Timer播放动画。同时，RootViewController需要知道Timer的每次执行。</span>

<span style="font-size: 15px;">代码如下。</span>

<span style="font-size: 15px;">首先，定义GifView，在其头文件中定义代理EveryFrameDelegate，同时声明方法- (void)DoSomethingEveryFrame;</span>
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="font-size: 15px;">#import &lt;UIKit/UIKit.h&gt;

@protocol EveryFrameDelegate &lt;NSObject&gt;

- (void)DoSomethingEveryFrame;

@end

@interface GifView : UIView 
{
    NSTimer *timer;
    id &lt;EveryFrameDelegate&gt; delegate;
    NSInteger currentIndex;
}

@property (nonatomic, retain) id &lt;EveryFrameDelegate&gt; delegate;

@end</span></div>

<span style="font-size: 15px;">然后，只要在GifView.m中让Timer在每次执行的时候调用delegate来执行DoSomethingEveryFrame，代码如下</span>
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="font-size: 15px;">- (id)initWithFrame:(CGRect)frame
{

    self = [super initWithFrame:frame];
    if (self)
    {
        timer = [NSTimer scheduledTimerWithTimeInterval:0.05 target:self selector:@selector(play) userInfo:nil repeats:YES];
        [timer fire];
    }
    return self;
}

-(void)play
{
        [delegate DoSomethingEveryFrame];

}</span></div>

<span style="font-size: 15px;">GifView上的工作就完成了。</span>

<span style="font-size: 15px;">下面是RootViewController中的代码，RootViewController只要在定义GifView的时候指定其代理为自身，就可以知道Timer的每次执行：</span>
<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;"><span style="font-size: 15px;">- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.    CGRect rect = CGRectMake(0, 0, 200, 200);
    GifView *tmp = [[GifView alloc] initWithFrame:rect];
    tmp.delegate = self;
    [self.view addSubview:tmp];
    [tmp release];
}

- (void)DoSomethingEveryFrame
{
    NSLog(@"I'm the delegate! I'm doing printing!");
}</span></div>

<span style="font-size: 15px;">GifView中Timer每次执行都会打印一行</span>

<span style="font-size: 15px;">I'm the delegate! I'm doing printing!</span>

<span style="font-size: 15px;">故，RootViewController就知道Timer的每次执行了。</span>
</div><div class="cnblogs_code_toolbar">

<span style="font-size: 15px;">做程序时，经常会碰到这样一种情况：在对象A中有一个对象B，在B中做某个操作时需要调用A对象的某个方法。这时，我们就需要用[代理机制](http://www.wuleilei.com/)，也叫委托机制。</span>

<span style="font-size: 15px;">还记得刚接触面向对象的时候，居然在B对象中又alloc了一个A对象，发现执行方法时没有works，那时不理解新alloc的对象和原来的对象A不是一个东东。</span>
<span style="font-size: 15px;">今天专门补习了一下哈，在网上找了一些资料，综合了一下，写了这篇菜鸟教程。</span>

<span style="font-size: 15px;">[委托代理](http://www.wuleilei.com/)（delegate），顾名思义，把某个对象要做的事情委托给别的对象去做。那么别的对象就是这个对象的代理，代替它来打理要做的事。反映到程序中， 首先要明确一个对象的委托方是哪个对象，委托所做的内容是什么。委托机制在很多语言中都用到的，这只是个通用的思想，网上会有很多关于这方面的[介绍](http://www.wuleilei.com/)。</span>

<span style="font-size: 15px;">下面以一个简单的例子介绍一下委托：</span>

<span style="font-size: 15px;">一、新建iPhone项目DelegateDemo；</span>

<span style="font-size: 15px;">二、添加UIView类ViewA；</span>

<span style="font-size: 15px;">三、ViewA.h的内容如下：</span>
<div class="dp-highlighter bg_cpp"><div class="bar"><div class="tools"><span style="font-size: 15px;">**[cpp]**&nbsp;[view plain](http://blog.csdn.net/huifeidexin_1/article/details/7567731 "view plain")[copy](http://blog.csdn.net/huifeidexin_1/article/details/7567731 "copy")</span><div><span style="font-size: 15px;">&nbsp;</span></div></div></div>

1.  <span style="font-size: 15px;"><span class="preprocessor">#import&nbsp;&lt;UIKit/UIKit.h&gt;</span>&nbsp;&nbsp;</span>
2.  <span style="font-size: 15px;">&nbsp;&nbsp;</span>
3.  <span style="font-size: 15px;">@protocol&nbsp;&lt;a&nbsp;href=<span class="string">"http://www.wuleilei.com/"</span>&nbsp;target=<span class="string">"_blank"</span>&gt;&lt;span&nbsp;style=<span class="string">"color:#ff0000"</span>&gt;ViewADelegate&lt;/span&gt;&lt;/a&gt;;&nbsp;//申明代理协议&nbsp;&nbsp;</span>
4.  <span style="font-size: 15px;">&nbsp;&nbsp;</span>
5.  <span style="font-size: 15px;">@interface&nbsp;ViewA&nbsp;:&nbsp;UIView&nbsp;{&nbsp;&nbsp;</span>
6.  <span style="font-size: 15px;">&nbsp;&nbsp;&nbsp;&nbsp;id&nbsp;&lt;&lt;a&nbsp;href=<span class="string">"http://www.wuleilei.com/"</span>&nbsp;target=<span class="string">"_blank"</span>&gt;&lt;span&nbsp;style=<span class="string">"color:#ff0000"</span>&gt;ViewADelegate&lt;/span&gt;&lt;/a&gt;&gt;&nbsp;_viewADelegate;&nbsp;&nbsp;</span>
7.  <span style="font-size: 15px;">}&nbsp;&nbsp;</span>
8.  <span style="font-size: 15px;">&nbsp;&nbsp;</span>
9.  <span style="font-size: 15px;">@property&nbsp;(nonatomic,&nbsp;assign)&nbsp;id&nbsp;viewADelegate;&nbsp;<span class="comment">//定义代理的属性，同时要在.m加中</span>&nbsp;&nbsp;</span>
10.  <span style="font-size: 15px;">&nbsp;&nbsp;</span>
11.  <span style="font-size: 15px;">@end&nbsp;&nbsp;</span>
12.  <span style="font-size: 15px;">&nbsp;&nbsp;</span>
13.  <span style="font-size: 15px;"><span class="comment">//代理协议的内容</span>&nbsp;&nbsp;</span>
14.  <span style="font-size: 15px;">@protocol&nbsp;&lt;a&nbsp;href=<span class="string">"http://www.wuleilei.com/"</span>&nbsp;target=<span class="string">"_blank"</span>&gt;&lt;span&nbsp;style=<span class="string">"color:#ff0000"</span>&gt;ViewADelegate&lt;/span&gt;&lt;/a&gt;&nbsp;&lt;NSObject&gt;&nbsp;&nbsp;</span>
15.  <span style="font-size: 15px;">-&nbsp;(<span class="keyword">void</span>)&lt;span&nbsp;style=<span class="string">"color:#008080"</span>&gt;viewACallBack&lt;/span&gt;;&nbsp;&nbsp;</span>
16.  <span style="font-size: 15px;">@end&nbsp;&nbsp;</span>
17.  <span style="font-size: 15px;">&nbsp;&nbsp;</span>
18.  <span style="font-size: 15px;">View.m中：&nbsp;&nbsp;</span>
19.  <span style="font-size: 15px;">&lt;span&gt;&nbsp;&lt;/span&gt;@synthesize&nbsp;viewADelegate&nbsp;=&nbsp;_viewADelegate;&nbsp;&nbsp;</span></div>

<span style="font-size: 15px;">三、在DelegateDemoViewController.m中：</span>
<span style="font-size: 15px;">- (void)viewDidLoad {
ViewA *viewA = [[ViewA alloc] initWithFrame:CGRectMake(50, 100, 200, 100)];
viewA.viewADelegate = self; //[设置viewA的代理为当前对象自己](http://www.wuleilei.com/)
[self.view addSubview:viewA];
[viewA release];
[super viewDidLoad];
}

- (void)viewACallBack {
NSLog(@"Hi, I am back!");
}</span>

<span style="font-size: 15px;">四、</span>

<span style="font-size: 15px;">点击[此处](http://www.wuleilei.com/Public/download.php?id=28)下载示例。</span>
</div></div></div></div><div id="sharePanel" class="share_buttons"><span style="font-size: 15px;">分享到：&nbsp;<a class="share_sina" title="分享到新浪微博" name="sina"></a><a class="share_qq" title="分享到腾讯微博" name="qq"></a></span></div>

<span style="font-size: 15px;">&nbsp;</span>
<div class="article_next_prev"><span style="font-size: 15px;">&nbsp;</span></div>![](http://counter.cnblogs.com/blog/rss/3315494)

本文链接：[[转]ios中关于delegate （委托）的使用心得](http://www.cnblogs.com/duger/p/3315494.html)，转载请注明。