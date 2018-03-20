---
title: egret-通讯模块(使用protobuff)
author: liuaf
toc: true
categories: 白鹭
tags:
     - egret
     - webSocket
     - protobuff
---

> 最近有幸接触到公司里另一个项目，是用白鹭引擎做的一款h5游戏，就想学习学习里面的知识要点。好在语言是想通的，而且C#和Typescript同是微软开发的。学习起来困难度也不会很大。我就以通讯模块开始接触了。

<!--more-->

### 一、WebSocket
> [webSocket](https://baike.baidu.com/item/WebSocket)基本概念可以在百科上看到，从而我们也可以知道它是一个__全双工通讯__，而且只有一次握手机会。
> 并且官方也提供了websocket的使用案例，可以点击[这里](http://developer.egret.com/cn/github/egret-docs/extension/WebSocket/WebSocket/index.html)查看。


### 二、protobuff
> 目前大部分游戏在通讯时序列化数据都会选在protobuff解决方案，选择protobuff优点太多了，就不一一列举了。
> 
> 要说的protobuff，这里我就多说一点，我在接触其他语言时的protocol都是在运行时外部进行编译protocol文件的。从而生成相应的脚本文件，使用时只需对脚本文件进行序列化和反序列化就可以。而Typescript是直接可以对"*.proto"进行加载并且序列化的。当然要看你使用的protobuffjs版本，我一直想找一个可以吧proto文件序列化d.ts文件的版本，不过在网上搜遍了只找到一些零散的文章，还拼凑不起来。
> 
> Typescript动态加载proto文件并解析和序列化对于性能上是否有影响无从得知。也没有看到那位大神对于这方面进行对比。我对于Typescript也了解的指示皮毛，运行外编译proto文件也没弄成功过，为了少走弯路还是老老实实用动态解析吧。

### 三、实现
> egret社区中有一片文章已经写的很详细了。可以[点击这里查看](http://bbs.egret.com/thread-28758-1-1.html),我是基于这个上面进行整理开发的。添加了协议消息注册列表，每一条所关心的协议都应一个返回回调。
> 
``` java
/**
     * 添加监听消息
     * @param  {number} id 所关心的协议号
     * @param  {Function} func 对应的协议返回函数
     * @param  {any} thisObject this指针
     * @returns void
     */
    public addListenerMessage(id: number, func: Function, thisObject: any): void {
        console.log(this.recFuncMaps);
        if (this.recFuncMaps[id] == null) {
            var reciveFunc: ReciveFunction = new ReciveFunction();
            reciveFunc.ID = id;
            reciveFunc.handler = func;
            reciveFunc.thisObj = thisObject;
            this.recFuncMaps[id] = reciveFunc;
        } else {
            console.log("The same ID already exists in the receive message list:" + id);
        }
    }
```
> 在消息返回响应里做这样处理

```java
/**
     * 接收服务器返回消息回调
     * @param  {egret.Event} e 消息参数
     * @returns void
     */
    private onReceive(e: egret.Event): void {
        var bytes: egret.ByteArray = new egret.ByteArray();
        this.socket.readBytes(bytes);

        var uint8: Uint8Array = new Uint8Array(this.getUint8Array(bytes));
        var message: any = NetUtil.ApcData.ApcData;
        var data: any = message.decode(uint8);
        if (this.recFuncMaps[data.ID] != null) {
            var reciveFunc: ReciveFunction = this.recFuncMaps[data.ID];
            reciveFunc.handler.call(reciveFunc.thisObj, data.Body);
        }
    }
```

> 需要特别说明的是proto文件的结构，我的初步想法是把一个proto消息体作为消息头，这样在后续扩展消息头时不受任何影响，下面是我的proto消息的定义

```java
message ApcData{
    int32 ID = 1; //协议ID
    //后续消息头增加可以再次扩展字段，不影响逻辑层的具体逻辑
    bytes Body = 2; //协议内容，一个proto序列化的二进制数据
}
```

### 四、Http
> 封装成了网络模块，当然普通的HTTP通讯也要支持的，特此添加了一个http通讯类。
> Http解决了HttpRequest没有超时响应的问题。每次建立一个Http请求对都会创建一个HttpRequest请求，并进行缓存。直到这个请求触发了返回响应、错误或超时的任意一个响应时，对自身上的回调监听和计时器进行清理。可以支持多个请求并发。如需串联请求，比如请求登录，返回成功后方可进行其他请求的类似需求，可以在上层逻辑中来实现。

> 具体代码可以到我的[github](https://github.com/Aifee/egret_network)上下载。