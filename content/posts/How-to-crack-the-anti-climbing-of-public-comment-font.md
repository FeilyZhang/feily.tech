---
title: "How to crack the anti climbing of Dazhongdianping's font?"
date: 2021-02-06T15:17:05+08:00
categories: ["Python crawler"]
tags: ["Python", "crawler"]
draft: true
---

**Abstract**：_token的生成技术仅仅是大众点评网在网络请求阶段的反爬措施，除此之外，在文档解析阶段还存在字体反爬。大众点评的字体反爬措施使得常规的文档解析技术无法使用，因此对字体反爬的破解有着极为重要的意义。本文从字体反爬的思路出发，重点介绍大众点评字体文件与文字的映射，以实现文档的正确解析。

**Key words**：大众点评网&nbsp;&nbsp;&nbsp;&nbsp;爬虫&nbsp;&nbsp;&nbsp;&nbsp;字体反爬&nbsp;&nbsp;&nbsp;&nbsp;WOFF

# Introduction

大众点评网的字体反爬技术实际上是利用自定义字体取代了用户本地字体，从而在用户浏览器上渲染的实际上是svg图片，因此无法复制评论内容，也无法解析HTML文档或JSON数据，这给正常的文档解析造成了不小的麻烦。笼统的看来，解决的办法至少有两种：

1. 通过审查元素获取节点的标记（其中以XPATH最为方便，其余方式还包括TAG_NAME、class、id、name等属性），然后用selenium以无头模式（或者利用PhantomJS）驱动浏览器对整体评论进行截图，然后利用OCR识别获取评论正文。这种方式的优点在于简单，缺点在于截图中SVG是透明的，无法进行下一步操作；而利用selenium以有头模式驱动浏览器虽然可以解决SVG透明的问题，但是却无法实现截取全尺寸图片，一种变通的方法是，在该基础之上利用pynput之类的库模拟键盘分别执行ctrl + shift + i、ctrl + shift + p快捷键，然后键入screenshot命令，再按enter键实现全尺寸截图，但是因浏览器差异不一定奏效。况且，在HTML文档中如果存在局部的滑动条，则无法截取完整。

2. 根据评论对应DOM节点的class或id属性值定位相应的css文件会得到font-family属性值，再利用该属性值定位具体的字体文件，一般为woff文件。然后将该woff文件以svg形式表现在HTML文档当中，再对其整体进行截图。之后，利用selenium在有头模式下以全屏方式遍历所有svg节点的x、y坐标以及width、height，对其进行截图保存为png形式，以该svg的code+name对其命名，然后再用OCR逐一识别，并建立起code/name与文字的映射关系。从而在文档解析时，通过对应的code/name检索文字并实现替换。其中，最关键的一步就是对其进行全尺寸截图，如果自动化截图就必须用无头模式，因为文档高度大于屏幕高度，但是无头截图的话得到的依然是svg图片，最终影响OCR识别；如果用有头模式的话虽然截图没有问题，但是无法截取全尺寸图片。因此，必须手动截图。

显而易见，第一种方法存在天然的缺陷，因此，这里使用第二种方法。

# 