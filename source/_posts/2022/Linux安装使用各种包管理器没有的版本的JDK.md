---
title: Linux安装使用各种包管理器没有的版本的JDK
date: 2022-05-3 01:45:00
tags: [Java, Linux]
categories: [技术]
---


## 下载你需要的版本的JDK

网上有许多预编译的OpenJDK，我选择的是[Adoptium](https://adoptium.net/)的Temurin。

文件可以解压到任何稳定安全的文件夹中，但是建议放在```/usr/lib/jvm/```，因为包管理器的JDK也是放到这里。

解压好的目录像这样：
```
/usr/lib/jvm/
└── jdk8u322
    ├── bin
    ├── include
    ├── jre
    ├── lib
    ├── man
    └── sample
```

反正能访问到就行。


## 配置环境变量

只介绍永久生效的配制方法。

在文件```/etc/profile```的末尾添加：
```
export JAVA_HOME=/usr/lib/jvm/jdk8u322/
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```

根据你的实际情况修改路径。

保存文件后，还需要重新加载才能生效，使用：
```
source /etc/profile
```
即可。


## 注意

使用这种方法安装JDK，你最好卸载掉包管理器中的版本。