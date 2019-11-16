---
title: Android组件化
date: 2019-04-11 09:02:21
tags:
- Android
- 组件化
categories:
- Android开发
---

#导引

主要参考以下两种组件化方案

 [知乎 Android 客户端组件化实践](<https://zhuanlan.zhihu.com/p/45374964>)

- 多工程多仓库。主工程通过 aar 依赖各个组件。
- 组件划分：主工程、业务组件（完整业务）、基础组件（基础业务）、基础SDK（业务无关）
- 组件解耦：公用代码处理、初始化（任务依赖）、通信（路由、接口、EventBus、组件 API 模块）。
- 组件半自动拆分。
- 联合编译完整包。动态引入组件。
- 包含子业务线的组件的处理。子业务模块独立配置、编译和运行，组合发布。

[网易友品 Android 客户端组件化演进](<https://zhuanlan.zhihu.com/p/59245063>)

- 通信：路由、服务和全局通知
- 路由。公共路由表、注解路由地址
- 接口。
- 拆分流程：建Module -> 移动业务代码到Module -> 根据编译报错修改 -> 接口解耦 -> 配置独立编译功能。
- 组件库的独立发布和维护；本地开发调试模式，开发时使用本地Module依赖，发布时使用远程ARR依赖。

---

# 实践

组件划分。主工程；业务组件（Home、Login、News、Attendance、PersonCenter）及其相对应的API组件；基础业务层（Common、Design等）；基础SDK（Tools、Net等）。

重构。从最底层开始抽离。

路由。使用Arouter。

业务组件间调用。使用相对应的API组件。

本地module与AAR依赖切换：当依赖无需改变时使用AAR依赖，当依赖需要更改时使用源码依赖。

**相关代码**

新建名为`deps.gradle`的Gradle文件，在其中添加组件与AAR的对应关系；并在每个Module里引入该文件。

```
// 其中key为各module的名称，value为上传至Maven仓库里AAR全称
// com.xxx.xxx为公司和部门名称，yyy为项目名称
// 项目基础模块
def xxx = [
				// ---
        net         : 'com.xxx.xxx:android-net:1.0.1',
        utils       : 'com.xxx.xxx:android-utils:1.1.1',
        common  		: 'com.xxx.xxx:android-yyy-common:1.0.5',
        design  		: 'com.xxx.xxx:android-yyy-design:1.0.1',
        // ---
]

// 项目业务模块
def xxxBusiness = [
				// ---
        login     	: 'com.xxx.xxx:android-login:1.0.0',
        login_api		: 'com.xxx.xxx:android-login-api:1.0.0',
        news    		: 'com.xxx.xxx:android-news:1.0.0',
        news-api		: 'com.xxx.xxx:android-news-api:1.0.0',
        // ---
]

ext.deps = [
        'xxx'        : xxx,
        'xxxBusiness': xxxBusiness,
]
```



`Settings.gradle`文件配置如下

```groovy
include ':app'

/** -----------组件化-动态构建----------- */
apply from: getRootDir().getAbsolutePath() + '/deps.gradle'

// 构建中使用源码的组件配置  默认依赖的组件都使用AAR
def useSource(String prj, String artifact) {
    include "$prj"
    ext.useSourceModuleConfigs.put("$prj", "$artifact")
}
// 构建中使用源码的组件配置  默认依赖的组件都使用AAR
ext.useSourceModuleConfigs = [:]
// 业务层
useSource(':news_api', deps.xxxBusiness.news_api)
useSource(':news', deps.xxxBusiness.news)
useSource(':login_api', deps.xxxBusiness.news_api)
useSource(':login', deps.xxxBusiness.news)
// 基础组件
useSource(':net', deps.xxx.net)
useSource(':common', deps.xxx.common)
useSource(':design', deps.xxx.design)
useSource(':utils', deps.xxx.utils)
println "use source modules -> ${ext.useSourceModuleConfigs.keySet()}"

// 这一步是使用源码替换AAR依赖
// 注释上面的某一行就可以使用其对应组件的AAR依赖
gradle.allprojects { project ->
    if (project == project.rootProject) {
        return
    }

    project.configurations.all {
        resolutionStrategy.dependencySubstitution {
            settings.ext.useSourceModuleConfigs.each { prj, artifact ->
                substitute module(artifact) with it.project(prj)
            }
        }
    }
}
```

各组件需要当度维护，能生成AAR文件并上传至Maven仓库。

各模块之间的资源名不能相同，需要配置`resourcePrefix`。

---



# 参考

- [Android模块化实践](<https://juejin.im/post/5b44a0d76fb9a04f932fe147#heading-25>)
- [Android组件化方案](<https://blog.csdn.net/guiying712/article/details/55213884>)
- [Android 组件化最佳实践](<https://juejin.im/post/5b5f17976fb9a04fa775658d>)
- [从零开始的Android新项目11 - 组件化实践（1）](<https://zhuanlan.zhihu.com/p/23147164>)
- [AppJoint](https://juejin.im/post/5bb9c0d55188255c7566e1e2)
- [ARouter](https://github.com/alibaba/ARouter)
- [使用ARouter实现组件化](<https://juejin.im/post/58f79130a0bb9f006abb066b>)

