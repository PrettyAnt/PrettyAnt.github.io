---
layout: post
title:  Android 常见bug(实时更新)
categories: Android
keywords: Android
mathjax: true
prism: [Android]
---

# 一些常见的bug

##  新建library的问题

**区别一:** build.gradle头文件
+ library的文件头文件 apply plugin: 'com.android.library'
+ application 的文件头 apply plugin: 'com.android.application'

**区别二:** build.gradle 中的defaultConfig标签内的applicationId
+ library的中没有applicationId 
+ application 中有applicationId

## 项目引入aar包可能遇到的坑

+ 需要依赖的那个项目(主项目)中需要添加 
``` 
repositories {
    flatDir {
        dirs 'libs'
    }
} 
```

+ 主题样式的冲突，直接打开主项目的清单文件，预览清单文件(清单文件底部的Merged Mainfest选项)

+ 子项目的依赖方式，如果依赖了第三方库，那主工程也需要依赖同样的第三方库

**几种依赖方式的特点**
* implementation：依赖库私有，只会在当前module中生效，无法传递至上层
* api：依赖库可传递，上层依赖了该module时，可以使用该module下api依赖的库
* provided（compileOnly）只编译，不参与打包。
* apk（runtimeOnly）不编译，只参与打包，很少用。
* testCompile（testImplementation）只在单元测试代码的编译以及最终打包测试apk时有效。
* debugCompile（debugImplementation）只参与debug模式的编译和最终的debug apk打包
* releaseCompile（releaseImplementation） 只参与release模式的编译和最终的Release apk打包


