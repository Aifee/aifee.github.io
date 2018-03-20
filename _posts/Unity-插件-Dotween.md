---
title: Dotween缓动插件的使用
author: liuaf
categories: Unity插件
tags:
     - Unity
     - Unity插件
     - 插件
     - 缓动
---

### 一、准备工作
> 前一直使用iTween插件，后来接触了DoTween后，发现后者如此的强大，下面记录一下使用心得。
> 下载[Dotween](http://dotween.demigiant.com/download.php)导入工程中

<!--more-->

### 二、使用
> DoTween使用方法有两种方式
> 1、直接调用Dotween.To方法

``` java

// 两个myVector是缓动的变量名字
// ()=> myVector 是一个lambda表达式 获取缓动变量的值
// x=> myVector = x lambda表达式，设置缓动变量的值
// new Vector3(3,4,8) 目标值
// 1 持续时间
// 这段代码处理的是:在1秒内把向量 myVector 缓动到 Vector3(3,4,8)
DOTween.To(()=> myVector, x=> myVector = x, new Vector3(3,4,8), 1);

```


> 2、快捷方式的使用

``` java

// 在1秒内把transform的位置移动到Vector3(2,3,4)的位置
transform.DOMove(new Vector3(2,3,4), 1);

```

> 快捷方式的使用要注意扩展方法的所属类别。下面是我进入dll里面看到的接口


``` java
public static Tweener DOMove(this Rigidbody target, Vector3 endValue, float duration, bool snapping = false);
public static Tweener DOShadowStrength(this Light target, float endValue, float duration);
public static int DOSmoothRewind(this Material target);
public static int DOTogglePause(this Component target);
```

> this 的参数是把函数扩展到的对象上。
> 具体都有那些扩展函数可以到[官网](http://dotween.demigiant.com/documentation.php)去查看

### 三、缓动类型
> SetEase(Ease) 缓动类型支持了大部分的曲线类型。下面一张图为了方便查阅我记录了下来

![缓动曲线](/img/UnityPlugins/DoTween/Ease.png)