---
title: VSCode&Chrome进行Egret开发
author: liuaf
categories: Egret开发工具
tags:
     - Egret
     - 插件
---

### 一、前言
> 众所周知，白鹭的Wing是基于在VSCode上进行开发的，不过Wing用起来始终怪怪的，而且还有各种bug，相信它在发展一段时间后能够更加完善。既然有这么多bug，还不如用原生的VSCode来的舒服，大不了EUI的Skin用Wing来编辑。

<!--more-->

### 二、准备工作
> VSCode[官网下载地址](https://code.visualstudio.com/)
> Debugger for Chrome (VSCode中下载)
> EgretCode (VSCode中下载)
> nginx[官方下载地址](http://nginx.org/en/download.html)

### 三、基础环境配置
> Egret社区已经有一片很好的教程[使用VSCode 编译，断点调试Egret项目](http://bbs.egret.com/forum.php?mod=viewthread&tid=24635&highlight=vscode)
> 但有个遗憾，根据他的教程进行配置后，每次调试启动时都要进行 Egret StartServer / Run
> 使用nginx来反向代理一个服务器

### 四、nginx.conf配置
``` java
    location /项目名 {
        alias  工程目录;
            index  index.html index.htm;

        autoindex on;
        autoindex_exact_size off;
        autoindex_localtime on;
    }
```

> 到此就可以直接F5进行Chrome调试了。不过上面中没有提到在Chrome中像Wing一样的查看当前舞台中的对象，其实白鹭官方已经给过插件了，只是没有注意而已。
> 找到下载好的 Egret Inspector文件解压后有一个EgretInspector-install文件夹，在Chrome设置中 更多工具-扩展工具 点击 加载已解压的扩展程序，找到解压后的EgretInspector-install文件夹。
> F5运行游戏后，F12打开开发者工具后，菜单项中会出现一个Egret项，这里就和Wing工具中的舞台一样。