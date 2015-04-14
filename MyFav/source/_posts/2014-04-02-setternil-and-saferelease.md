---
layout: post
title: "setter=nil and saferelease"
description: ""
category: [技术,iOS]
tags: [setter]
---


如果一个界面内有个arr暂时存储数据，在这个界面类中，不对这个arr进行alloc,给这个界面传值时简单调用其`setter方法`，那么在这个界面类的dealoc方法中，我还能直接写成`_Pointer release,_Pointer = nil;`呢？

在一个界面类dealloc方法中，有两种常用的retainCount减一方法：


	*	#define SAFERELEASE(_Pointer){[_Pointer release],_Pointer = nil;} 
	*	self.pointer = nil;   //调用setter方法


>当界面`主动`创建alloc该retain对象时，执行采用release和置空；

>当界面`被动`接收传值时用置空self.piont = nil;

>同时，当是改变量是属性用点语法置空self.pont = nil;当为成员变量部位属性时使用SAFERELEASE！

