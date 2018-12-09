---
title: 发布项目到远程JCenter仓库
date: 2018-11-30 08:38:36
tags:
- Gradle
- Dependency
- Maven
- Jcenter
categories:
- 其他
---

# 简介

 JCenter是JFrog公司旗下Bintray平台上一个公开的Java仓库。要发布项目到JCenter，首先需要上传项目到Bintray平台，然后才能发布到它的公开库——JCenter。

* Bintray官网： https://bintray.com/
* JCenter仓库地址：https://jcenter.bintray.com/

---

<br>

# 准备工作

**注册账号**。在[Bintray官网](https://bintray.com)注册账号。

**浏览Bintray平台的用户手册**。见[JFrog用户指南](https://www.jfrog.com/confluence/display/BT/Introduction)，里面详细介绍各种概念、使用、操作等。浏览一遍即可，有许多是用不上的。主要章节如下：

* 了解核心概念：仓库、包和版本。见章节：[Key Concepts](https://www.jfrog.com/confluence/display/BT/Key+Concepts)。
* 了解发布的三个步骤：创建版本、上传、发布。见章节：[Uploading](https://www.jfrog.com/confluence/display/BT/Uploading)、[Uploads](https://www.jfrog.com/confluence/display/BT/Uploads)
* 了解上传的方法：UI手动、cURL、Maven、Gradle。见章节：[Maven Repositories](https://www.jfrog.com/confluence/display/BT/Maven+Repositories)



下面以Android项目为例，说明发布流程。其中library为需要上传的项目。

---

<br>

# 使用UI手动发布项目

## 生成构件

首先确定几个必要的参数

* groupId。如 *com.liang.android*
* artifactId。如 *nicehttp*
* version。如 *0.1.2*

生成需要上传到Maven库的四个文件：

* {artifactId}-{version}.aar
* {artifactId}-{version}-sources.jar
* {artifactId}-{version}-javadoc.jar
* {artifactId}-{version}.pom

**.aar**。执行`./gradlew library:build`之后，就会生成，在*library/build/outputs/aar/*中。

**sources.jar**和**javadoc.jar**。在*library*的*build.gradle*中建立任务

```groovy
// build a jar with source files
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}

task javadoc(type: Javadoc) {
    failOnError false
    source = android.sourceSets.main.java.sourceFiles
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
    classpath += configurations.compile
}
// build a jar with javadoc
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}

artifacts {
    archives sourcesJar
    archives javadocJar
}
```

然后执行`./gradlew library:build`，就可以生成所需要的文件，在*library/build/libs/*中。

**.pom**。参考[POM Reference](http://maven.apache.org/pom.html)创建对应的文件，或者使用[Gradle Maven Plugin](https://docs.gradle.org/current/userguide/maven_plugin.html)生成，在*library*的*build.gradle*中添加如下代码

```groovy
apply plugin: 'maven'

task createPom << {
    pom {
        project {
            groupId 'org.example'
            artifactId 'test'
            version '1.0.0'
            licenses {
                license {
                    name 'The Apache Software License, Version 2.0'
                    url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    distribution 'repo'
                }
            }
        }
    }.writeTo("pom.xml")
}
```



## 创建版本

在*bintray.com*上创建版本



## 上传文件

路径为{groupId}/{artifactId}/{version}，其中“.”都需要替换为“/”。



遇到问题

>Please fix the following before submitting a JCenter inclusion request: the POM is invalid or was uploaded to the wrong coordinates

是因为文件名必须为{artifactId}-{version}.aar等



点击上传到JCenter后，会在{groupId}/{artifactId}中自动生成*maven-metadata.xml*文件

gradle-bintray-plugin 和 [bintray-release](javascript:void())

```
classpath 'com.github.dcendents:android-maven-plugin:1.2'
classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.0'
```

同步到MavenCentral

# 参考

1. [JCenter是什么](https://www.geekhub.cn/a/1295.html)
2. [Bintray官网](https://bintray.com)
3. [JCenter仓库](https://jcenter.bintray.com/)
4. [JFrog用户指南](https://www.jfrog.com/confluence/display/BT/Introduction)
5. [使用Gradle插件上传Artifacts到Bintrary](https://github.com/bintray/gradle-bintray-plugin#readme)
6. [使用Gradle插件上传示例](https://github.com/bintray/bintray-examples/tree/master/gradle-bintray-plugin-examples)
7. [博客-上传Gradle项目到Maven仓库](https://blog.csdn.net/xiangzhihong8/article/details/53869957)
8. [从Travis到Bintray](https://www.jianshu.com/p/905411bf4f8f)







