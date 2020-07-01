---
title: nju-GPA-Calculator 说明文档
date: 2020-07-01 12:34:37
tags:
---

针对南京大学教务处网站的GPA计算器 `chrome`扩展程序

[项目主页](https://github.com/cw-Guo/NJU_GPA_Calculator)

## 缘起
想做一个GPA计算器的想法已经萌发很久了，最开始的时候，学校还没有推出信息平台，并没有一个官方的GPA计算工具，只能自己手动计算。
后来学校推出了微信信息平台，自己推出了一个GPA计算功能，但是不支持更改GPA算法。对于有出国需求的同学来说，WES绩点计算还是有必要的.
虽然说有官方的IGPA网站,但是需要自己一项项课程输入,计算颇为复杂。于是考虑自己做一个计算器。
J
为了使用方便，考虑使用Chrome extension 的形式。由于笔者并没有接触过JavaScript 相关内容，也没有接受过系统的学习，所以本个项目是基于一个学长写的程序，主体框架我并未改动。

本拓展程序的原开发者为：@[waterxjw](https://github.com/waterxjw/NJU-GPA-Calculator)

@cwGuo在其基础上作二次开发

- 增加了wes 算法
- 修复了不能计算有关重修，退选的相关GPA 的bug
- 调整了一些ui设置

完成这个项目之后，对Chrome extension 有了一定的了解，并且对JavaScript 也有了一定的接触。

个人认为，这种基于Web的轻量化的应用使用便捷，安装简便，我比较喜欢这样的设计.


## 使用
- 将项目文件夹下载到本地，chrome extensions打开开发者模式，加载已解压的扩展程序，直接选择项目文件夹。
- 安装完毕后，登录教务处网站，点击插件图标即可。
![运行截图](https://cdn.jsdelivr.net/gh/cw-Guo/NJU_GPA_Calculator/images/screenshot.png)

## 已知的一些问题

有一些同学可能因为网络环境的原因无法连接到cdn网站,从而无法正常使用该插件.

解决方法是采用其他网站的cdn



