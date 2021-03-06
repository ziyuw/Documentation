
---
title: 集成客户端
description: 
platform: Windows
updatedAt: Fri Jul 19 2019 08:29:18 GMT+0800 (CST)
---
# 集成客户端
本文介绍在正式使用 Agora SDK for Windows 进行通话/直播前，需要准备的开发环境，包含前提条件及 SDK 集成方法等内容。

## 前提条件

请确保满足以下开发环境要求:

-   操作系统： Microsoft Windows 7+

-   编译器：Microsoft Visual C++ 2013+

-   开发环境：Microsoft Visual Studio 2013 （推荐）


> 使用版本号高于 Microsoft Visual Studio 2013 的开发环境时，可能会有兼容性问题。


## 添加 SDK

1.  下载 [Windows SDK](https://docs.agora.io/cn/Agora%20Platform/downloads)，解压并打开。
2.  将 `sdk/include` 目录添加到项目的 INCLUDE 目录下。
3.  将 `sdk/lib` 目录放入项目的 LIB 目录下，并确认 agora_rtc_sdk.lib 与项目有连接。 
4.  将 `sdk/dll` 下的 dll 文件复制到你的可执行文件所在的目录下。

现在你已经设置好了 Windows 开发环境，可以开始使用 Agora SDK 了！

## 相关文档
完成了客户端集成后，你可以使用 Agora SDK，依次实现左侧《快速开始》菜单栏下的步骤，进行通话/直播：

- 初始化
- 加入频道
- 发布和订阅流



