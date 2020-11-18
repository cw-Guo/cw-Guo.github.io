---
title: 如何在hexo博客中预览一个pdf
date: 2020-11-18 23:52:17
tags:
---
#### 源起
突发奇想，想在博客上展示自己的 CV, 于是搜索了一些关于在hexo博客中预览pdf的文章并整合如下。

#### 教程
依靠一个hexo下的插件实现。[官方文档](https://github.com/superalsrk/hexo-pdf)

简单介绍一下：
- 安装hexo-pdf 插件 
```shell
npm install --save hexo-pdf
```
- 在文章中嵌入如下代码
``` python
{% pdf ./mydocument.pdf %} %这是使用本地文档的方式
{% pdf url of your resource %} %使用在线资源
```

基本上就可以实现了，这是我的[效果](https://cw-guo.github.io/profile/)

#### 问题

目前展示pdf的效果初步成功了，但是还有一个问题。就是pdf的长显得非常短，预览起来非常丑。猜想是由于网站的主题格式限制的。
在官方文档的[issue](https://github.com/superalsrk/hexo-pdf/issues/7) 中，有一段解答。目前还未实现。



-------
参考的网站：
- https://www.jianshu.com/p/3a0c19160b32?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation pdf 显示不全 长度不够

- 调整pdf显示的大小 https://github.com/superalsrk/hexo-pdf/issues/7

- 如何预览pdf https://blog.csdn.net/qq_43827595/article/details/104574959