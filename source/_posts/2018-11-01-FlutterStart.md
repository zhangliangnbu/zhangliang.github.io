---
title: Flutter入门
date: 2018-11-01 09:04:21
tags:
- Flutter
- 入门
categories:
- 其他
---

# 前言

照着官方[get-started](https://flutter.io/get-started/install/)教程做，略作补充。

-- -- --

<br>

# 入门

**1 下载SDK安装包并解压**

**2 使用`$ flutter doctor`做检查并安装相关工具**

安装IOS相关工具时，如果报错

```bash
configure: error: Package requirements (libusbmuxd >= 1.1.0) were not met:
```

可以使用如下方法解决[IOS toolchain安装异常解决](https://stackoverflow.com/questions/52602425/libusbmuxd-version-error-during-flutter-install/52604913#52604913)

```bash
brew update
brew uninstall --ignore-dependencies libimobiledevice
brew uninstall --ignore-dependencies usbmuxd
brew install --HEAD usbmuxd
brew unlink usbmuxd
brew link usbmuxd
brew install --HEAD libimobiledevice
```

**3 配置IDE，安装插件**

**4 尝试运行项目**

创建、在Android和IOS模拟器上运行、修改字符串并热加载。

**5  写第一个APP** 

分为两部分，入门文档主要介绍第一部分。这两部分的内容都可以在[Google Codelas](https://codelabs.developers.google.com/?cat=Flutter)中找到。

-- -- --

<br>



# 移动端开发演进

详细请看文章[为什么说Flutter是革命性的？](<http://www.infoq.com/cn/articles/why-is-flutter-revolutionary>)

**传统(Android&IOS)**。APP直接调用平台控件和服务。

![传统构架](/images/mobile_dev_oem.png)

**WebView**。APP直接调用平台的WebView以及通过桥接的方式调用服务。

![WebView](/images/mobile_dev_webview.png)

**ReactNative**。JS代码通过桥接的方式调用平台控件和服务。桥接的转化效率较低。

![WebView](/images/mobile_dev_rn.png)

**Flutter**。APP端通过接口直接调用平台的Canvas、Events和服务，转化效率较桥接的方式快几个数量。

![WebView](/images/mobile_dev_flutter.png)

> 上面的图皆来自文章[为什么说Flutter是革命性的？](<http://www.infoq.com/cn/articles/why-is-flutter-revolutionary>)

-----

<br>



# 其他

一切皆控件。所有的组成部分都是控件，包括位置、补白、主题、导航等。

Flutter运行机制。请看文章[Flutter原理解析](https://mp.weixin.qq.com/s/CQQXD0TrlbaNWjoClIcDtw)



----

<br>





# 参考

1. [Flutter官网](https://flutter.io/get-started/install/)
2. [Flutter Github](https://github.com/flutter/flutter)
3. [Flutter中文社区](https://flutter-io.cn/#section-keynotes)
4. [Flutter官网中文翻译](https://flutterchina.club/get-started/install/)
5. [IOS toolchain安装异常解决](https://stackoverflow.com/questions/52602425/libusbmuxd-version-error-during-flutter-install/52604913#52604913)
6. [为什么说Flutter是革命性的？](<http://www.infoq.com/cn/articles/why-is-flutter-revolutionary>)
7. [Flutter原理解析](https://mp.weixin.qq.com/s/CQQXD0TrlbaNWjoClIcDtw)