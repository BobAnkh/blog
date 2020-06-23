Title: Typora 使用指南
Date: 2020-06-23 22:35
Category: Markdown
Tags: Markdown, Software
Slug: blog/typora
Author: BobAnkh
Summary: 一个用于写markdown文件的轻量化的编辑器——Typora的简单介绍
Illustration: background.jpg

[TOC]

> 个人比较喜欢使用Typora来作为单独需要写Markdown的时候的编辑器，因为其使用起来相对直观而且轻量化

## 简单特点介绍

- 轻量
- 所见即所得
- 支持基本 $\LaTeX$
- 支持 Mermaid
- 支持高亮和表情
- 支持更改主题
- 使用 Pandoc 转换成各种格式

## 安装使用

从官网上下载对应OS的版本，然后一路`next->install->finish`就安装完成了

此处提供官网下载地址如下：

> [Download Link](https://www.typora.io/#)

### 基本操作

打开Typora的初始界面是这样的：

![Initial](https://image.bobankh.com/2020/06/23/5a9e11ee6f943.png)

界面主要构成如下：

顶部是**菜单**栏目，左侧边栏是**文件**和**内容大纲**栏目，底栏左侧有**显示/隐藏侧边栏**和**启用源代码模式**两个选项，右侧有**拼写检查**和**字数统计**两项功能

#### 菜单介绍

1. 文件菜单

    ![File](https://image.bobankh.com/2020/06/23/92947dc67651a.png)

    其中的基本功能，新建、新建窗口、打开、打开文件夹、快速打开、打开最近文件、保存、另存为、保存全部打开的文件、导入、导出、打印、关闭等基本功能与常用软件相仿，此处不再赘述。偏好设置即Typora的设置，具体包括5个部分——通用、外观、编辑器、图像、Markdown：

    ![Settings](https://image.bobankh.com/2020/06/23/67b345ef2b35f.png)

    通用部分主要包括一些基本的设置特别是语言、检查更新等设置。

    ![Appearance](https://image.bobankh.com/2020/06/23/e1508781de30a.png)

    外观部分就是对于Typora样式的一些调整

    ![Editor](https://image.bobankh.com/2020/06/23/e5ca6742f6e08.png)

    编辑器部分主要就是对于相关的编辑选项的一些设置，主要包括换行符、缩进和拼写检查等内容

    ![Images](https://image.bobankh.com/2020/06/23/e076da5443307.png)

    图像部分主要就是对于你通过Typora插入的图片进行何种操作的一个设置部分，一般来说，正常使用只要在插入图片时选择无特殊操作即可，如果自己有使用现成的图床，则可以配合PicGo直接将插入的图片进行上传至对应图床的操作，从而加强文档的通用性和易迁移性

    ![Markdown](https://image.bobankh.com/2020/06/23/c13607e3e6545.png)

    最后的Markdown部分，主要是对于一些markdown语法等个性化的设置，可以根据自己的需求进行相关调整

2. 编辑菜单

    ![Edit](https://image.bobankh.com/2020/06/23/3910b6c5e187f.png)

    编辑菜单中主要就是一些常规的对于文本的相关操作

3. 段落菜单

    ![Passage](https://image.bobankh.com/2020/06/23/f7daa6b8c4d03.png)

    段落菜单主要可以为不熟悉Markdown的朋友提供方便快捷的段落布局等的格式标记

4. 格式菜单

    ![Fromat](https://image.bobankh.com/2020/06/23/215c63559f9aa.png)

    格式菜单主要可以为不熟悉Markdown的朋友们提供关于内容和样式等方面的一些格式标记

5. 视图菜单

    ![View](https://image.bobankh.com/2020/06/23/9b3cb53b40208.png)

    视图菜单主要是管理当前Typora显示的栏目布局情况和样式等

6. 主题菜单

    ![Theme](https://image.bobankh.com/2020/06/23/d78b6aa7d205a.png)

    主题菜单主要是用于切换你已安装的Markdown的主题（如何安装新主题可见后文）

7. 帮助菜单

    ![Help](https://image.bobankh.com/2020/06/23/5224126676a50.png)

    帮助菜单就是一些和Typora相关的信息

### 添加新的主题

点击**帮助**菜单中的`更多主题`进入Typora的support界面，找到对应的Theme栏目，或直接进入Typora的[主题画廊](http://theme.typora.io/)，界面如下：

![Theme-gallery](https://image.bobankh.com/2020/06/23/385b92116861c.png)

挑选一个你感兴趣/喜欢的主题，如以`OneDark`为例：

![OneDark](https://image.bobankh.com/2020/06/23/97205819f0a5d.png)

进入到对应的下载界面进行下载

然后在文件->偏好设置->外观中，点击`打开主题文件夹`这一按钮，将下载下来的内容直接全部解压到这一文件夹中，如下：

![Add-Theme](https://image.bobankh.com/2020/06/23/22dfec300bd2d.png)

随后重启Typora即可看到对应的主题：

![New-theme](https://image.bobankh.com/2020/06/23/7e495aa4c927a.png)
