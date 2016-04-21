---
layout: post
title:  "Sublime Text 快捷键去除空行"
date:   2016-01-21
excerpt: "使用 Sublime Text 的插件 DeleteBlankLines 实现快速去除空白行的功能。"
tag:
- Sublime Text
- Walter
comments: true
---

#### Sublime Text 简介 ####

Sublime Text 是一个代码编辑器(Sublime Text 2是收费软件，但可以无限期试用)，也是HTML和散文先进的文本编辑器。Sublime Text 是由程序员 Jon Skinner 于2008年1月份所开发出来，它最初被设计为一个具有丰富扩展功能的 Vim。

Sublime Text 具有漂亮的用户界面和强大的功能，例如代码缩略图，Python 的插件，代码段等。还可自定义键绑定，菜单和工具栏。Sublime Text 的主要功能包括：拼写检查，书签，完整的 Python API ， Goto 功能，即时项目切换，多选择，多窗口等等。Sublime Text 是一个跨平台的编辑器，同时支持 Windows、Linux、Mac OS X 等操作系统。

#### Sublime Text 删除空白行插件 ####

之前使用 Dreamweaver 的时候，其中就有一个快捷键可以快速清楚代码中的空白行，可是到了 Sublime Text 中却是没有发现任何一个快捷键或者组合可以做到一键去除空白行。不过 Sublime Text 有一个插件可以解决这个问题，它就是本文所要讲述的 DeleteBlankLines。

下面就说一下安装 DeleteBlankLines 插件的过程：

1. 通过 ctrl+shift+p 组合键打开命令面板
2. 输入 install package 后回车
<img src="http://198.199.116.214/wp-content/uploads/2016/01/20150415112106176.jpg" alt="20150415112106176" />
Sublime Text 通过 install packge 安装插件

3. 输入 DeleteBlankLines 后回车进行安装
<img src="http://198.199.116.214/wp-content/uploads/2016/01/20150415112106497.jpg" alt="20150415112106497" />
Sublime Text 去除空白行插件 DeleteBlankLines

上文所述安装 DeleteBlankLines 插件的前提：Package Control 已经安装好。

#### Sublime Text 一键删除空白行 ####
安装好 DeleteBlankLines 插件后，该如何使用 DeleteBlankLines 插件一键删除空白行呢?可以按如下的步骤操作：

1. 选中要删除空白行的文本范围，一般是 ctrl+A 直接全选
2. 通过 ctrl+alt+backspace 执行一键去除空白行功能

提示：初次使用可以拿些带有大量空白行的文件操作，以便明显地了解 DeleteBlankLines 的功能。
