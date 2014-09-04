title: Xcode升级5.1 iOS升级7.1无法真机调试 - duger
tags: []
date: 2014-03-25 11:49:00
category:
---

<div style="background-color: #F5F5F5;border: 1px solid #CCCCCC;padding:10px;">warning: ignoring debug info with an invalid version (<span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
warning: ignoring debug info with an invalid version (</span><span style="color: #800080;">0</span><span style="color: #000000;">)
</span><span style="color: #800080;">0</span>  <span style="color: #800080;">0x10fca9f93</span>  __assert_rtn + <span style="color: #800080;">144</span>
<span style="color: #800080;">1</span>  <span style="color: #800080;">0x10fd4ced4</span>  ld::passes::stubs::Pass::makeStub(ld::Atom <span style="color: #0000ff;">const</span>&amp;, <span style="color: #0000ff;">bool</span>) + <span style="color: #800080;">0</span>
<span style="color: #800080;">2</span>  <span style="color: #800080;">0x10fd4d5f7</span>  ld::passes::stubs::Pass::process(ld::Internal&amp;) + <span style="color: #800080;">497</span>
<span style="color: #800080;">3</span>  <span style="color: #800080;">0x10fd4dc07</span>  ld::passes::stubs::doPass(Options <span style="color: #0000ff;">const</span>&amp;, ld::Internal&amp;) + <span style="color: #800080;">111</span>
<span style="color: #800080;">4</span>  <span style="color: #800080;">0x10fcaab50</span>  main + <span style="color: #800080;">772</span>
<span style="color: #800080;">5</span>  <span style="color: #800080;">0x7fff8a3af5fd</span>  start + <span style="color: #800080;">1</span><span style="color: #000000;">
A linker snapshot was created at:
     </span>/tmp/MyApp-<span style="color: #800080;">2014</span>-<span style="color: #800080;">02</span>-<span style="color: #800080;">11</span>-<span style="color: #800080;">123906</span>.ld-<span style="color: #000000;">snapshot
ld: Assertion failed: (target </span>!= NULL), function stubableFixup, file /SourceCache/ld64/ld64-<span style="color: #800080;">236.3</span>/src/ld/passes/stubs/stubs.cpp, line <span style="color: #800080;">126</span><span style="color: #000000;">.
clang: error: linker command failed with exit code </span><span style="color: #800080;">1</span> (use -v to see invocation)</div>

手机升级完7.1，XCode升级到5.1后，真机调试时遇到编译不过，报以上错误！

原因是XCode的LTO 的bug，暂时的解决方法是关掉它！！

将XCode BuildSettings 中，Link 下的DEAD_CODE_STRIPPING 至为NO！

![](http://images.cnitblog.com/i/557746/201403/251147469676647.png)

<span>This is a bug with LTO and -dead_strip.&nbsp; The workaround is to stop using one of them.</span>

<span><span>way Link Time Optimization (LTO) works is that the compiler stops half way through and emits the .o file as "bit code" (the internal clang IR) instead of compiling down to mach-o.&nbsp; This is drive by the -flto compiler option. When the linker encounters bit-code .o files, it loads up the back end of clang (libLTO.dylib) and merges/compiles all the bit-code files into mach-o then completes the link.</span></span>

参见：

https://devforums.apple.com/message/950372#950372
![](http://counter.cnblogs.com/blog/rss/3622799)

本文链接：[Xcode升级5.1 iOS升级7.1无法真机调试](http://www.cnblogs.com/duger/p/3622799.html)，转载请注明。