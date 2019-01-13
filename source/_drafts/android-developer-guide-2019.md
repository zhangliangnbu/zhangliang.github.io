---
title: Android Developer Guide-2019
tags:
- android
- guide
- 文档
categories:
- 其他
---

# 前言

温故知新，官方文档每年都应该读一遍。

---

# Animations & Transitions

**动画实现方式** 

Property Animation。最常用、最主要的动画，应用于UI可见性和运动、布局改变过渡、Activity切换过渡等。 

- API。主要位于`android.aniamtion`包，插补器在`android.view.animation`包。 基础API有：`ValueAnimator`、`ObjectAnimator`、`AnimatorListenerAdapter`、`AnimatorSet`。其他API有：过渡-`LayoutTransition`、状态-`StateListAnimator`、多动画和便捷-`ViewPropertyAnimator`。
- XML。位于`res/animator`，可使用`AnimatorInflater`加载。

Drawable Animation。图形动画，静态图片或矢量图动画 

- 逐帧图形动画。`AnimationDrawable`，资源为`res/drawable/animation-list`。 
- 矢量图动画。`AnimatedVectorDrawable`，资源为 `res/drawable/animated-vector`。 

View Animation。有局限，一般情况下，可用Property Animation替代。 

> 之前许多文档把动画分为属性动画、补间动画和图形动画三种，这里的补间动画就是View Animation，但感觉最新官方文档并没有这样划分。
>
> 对于最新的文档，论述最多的是Property Animation，其他两种动画说的很少，所以主要掌握Property Animation，其他两种动画了解即可。

**应用场景** 

- View显示和隐藏。前后连续的View的显示切换。crossfade、card flip？circular reveal animation 
- View移动。直线、曲线、速度控制等。使用API有：`ObjectAnimator` 、`Path` 、`Interpolar`、`FlingAnimation`等。 
- View缩放过渡。图片的缩放过渡，如从小图过渡到大图预览。利用属性有：`alpha`、`x`、`sacleX`等。 
- 布局变更过渡。布局更改后的多个View位置改变动画；利用系统动画直接设置`android:animateLayoutChanges="true”`即可，或自定义动画，利用API有：Scene、Transition、TransitionManager等。 
- Activity及其内部元素切换过渡。用到的API有`ActivityOptions.makeSceneTransitionAnimation()`等 

---

# Resources

资源组织。位于`res/`和`assets/`目录，独立于代码，利于复用。

可选性资源。提供可选性资源用于不同配置的设备。

* 资源文件夹名称格式*<resources_name>-<config_qualifier>*，其中*<config_qualifier>*(限定符)命名有自己的规则：可多个、有顺序、目录不能嵌套、大小写不敏感、限定类型不能相同。
* 资源匹配规则。根据[匹配规则-BestMatch](https://developer.android.com/guide/topics/resources/providing-resources#BestMatch)并依据[限定符表单](https://developer.android.com/guide/topics/resources/providing-resources#table2)从上到下依次匹配。
* 必须要设置默认资源，即无限定符的资源，避免APP找不到资源时崩溃。

代码里引用资源。

* 引用一般`res/`资源，`[<package_name>.]R.<resource_type>.<resource_name>`；
* 引用`res/raw`资源，` Resources.openRawResource()`；
* 引用`assets/`资源，`AssetManager.open("filename")`。

XML里引用资源。

* 引用文件资源：`@[<package_name>:]<resource_type>/<resource_name>`。
* 引用样式属性：`?[<package_name>:][<resource_type>/]<resource_name>`

本地化。使用可选资源、总是在`strings.xml`定义字符串、使用`<xliff:g>`保持部分文本不变。

Unicode和国际化。[ICU](http://userguide.icu-project.org/icufaq#TOC-How-is-the-ICU-licensed-)

使用`<aapt:attr>`内联复杂的XML资源。

字符串。数量字符串（Quantity Strings）`plurals`；样式设置-HTML、Spannables、Annotation。

---

# Permissions

Android安全架构的核心设计点是，默认情况下，任何应用都无权执行任何会对其他应用，操作系统或用户产生负面影响的操作。

概述

* 可选硬件功能。如无必须，设置`android:required="false"`。
* 权限强制。对服务的任何调用都可以强制执行任意细粒度的权限。四大组件、URI等。
* 自动权限适配。低`targetSdkVersion `的APP在高SDK版本平台运行时，可无视新增加的权限限制。
* 保护级别。*normal*, *signature*, and *dangerous*。另外，特殊权限如 `SYSTEM_ALERT_WINDOW` and `WRITE_SETTINGS`需要发起Intent请求。
* 权限组。仅影响*dangerous*权限用户交互体验，组内被手动授权后，其他自动被授权。
* 总是显示请求每个需要的权限。权限级别、组的划分可能会随着版本的更新而有所改变。
* ADB命令自动授权所有权限。`$ adb shell install -g MyApp.apk`

申请权限

* 声明。在Manifest中声明，可附加API等级、最大SDK版本信息等。
* 检查。`ContextCompat.checkSelfPermission()`。
* 申请。` ActivityCompat.requestPermissions()`。
* 处理。在`onRequestPermissionsResult()`处理。

权限使用原则

- 只添加必要权限。如能用其他方式实现则不要添加权限，如用Intent调用其他APP、使`onAudioFocusChange()`判断电话状态、使用`com.google.android.gms.iid`或`Advertising Identifier`获取身份凭证等。
- 注意依赖库的权限。添加依赖则会集成其所需权限。
- 对用户透明。让用户明白为何需要权限。
- 显示使用权限功能。让用户知道你在使用相关权限功能，不要偷偷使用。

自定义权限。(⊙o⊙)

---

# Testing

概述

* 测试驱动开发 Test-Driven Development。工作流：Failing Test -> Passing Test -> Refactor -> Failing Test -> ... 
* 测试层级。Small Tests（Unit Tests）70% -> Medium Tests 20% -> Large Tests 10%。

Small Tests或单元测试

- Local Unit Tests。最主要的单元测试。运行于本地JVM，源码位于*[module-name]/src/test/java/*；如果测试单元引用了Android层框架或三方依赖代码，可使用Robolectric或Mockito等来模拟。依赖库包括JUnit 4、Robolectric、Mockito。
- Instrumented Unit Tests。运行于模拟器或真机，源码位于*[module-name]/src/androidTest/java/*。依赖库包括AndroidX test runner和rules、Hamcrest、Espresso、UI Automator。

资源

- [googlesamples/**android-testing**](https://github.com/googlesamples/android-testing)
- [Test-Driven Development on Android with the Android Testing Support Library (Google I/O '17)](https://www.youtube.com/watch?v=pK7W5npkhho&start=111)
- [googlesamples/android-sunflower](https://github.com/googlesamples/android-sunflower)
- [Android Testing Codelab](https://codelabs.developers.google.com/codelabs/android-testing/index.html#0)。教程不错，可惜依赖库不是最新的，也没有用Robolectric。

实践。优化既有项目，测试工具类（单元测试或小型测试）、测试页面逻辑（简单UI测试或中型测试，可先优化MVP结构）、测试页面间逻辑（集成UI测试或大型测试）。