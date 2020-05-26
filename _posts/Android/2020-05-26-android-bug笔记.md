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
**区别一** build.gradle头文件
+ library的文件头文件 apply plugin: 'com.android.library'
+ application 的文件头 apply plugin: 'com.android.application'

**区别二** build.gradle 中的defaultConfig标签内的applicationId
+ library的中没有applicationId 
+ application 中有applicationId



