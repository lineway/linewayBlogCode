---
title: python-opencv环境安装
date: 2018-03-19 12:32:21
tags: 
  - python
  - opencv 
---

# anaconda介绍

使用python开发opencv相关的程序，首先需要搭建开发环境。本人的opencv环境是在Anaconda集成环境下配置的，因此本篇主要是关于在Anaconda环境下安装opencv，提供开发接口。  
首先，我们安装Anaconda3的环境，这个环境的安装比较简单，在此不做介绍。Anaconda3默认安装的是python3解释器，这个集成环境的好处就是，可以创建虚拟的python3环境以及python2环境：

```bash
conda create -n py2 python=2 anaconda
```
上述命令中，`conda create -n`是创建虚拟环境的固定语句，`py2`是对环境的命名，`python=2`指定解释器版本为python2, `anaconda`表明安装所有anaconda环境中的第三方库。其中，`python=2`和`anaconda`是可选参数,如果不指定解释器版本，那么默认安装的是和Anaconda同样的python版本，`anaconda`不指定的话，默认安装的是纯净的python。  

创建环境完成后，可以使用以下命令激活&退出环境：

```bash
# 激活环境
source activate py2
# 退出环境
source deactivate
```

# opencv安装
首先，按照上一步的操作，我们创建一个需要的python环境`vision`。然后激活这个环境。  

激活环境之后，使用如下命令安装opencv:
```bash
conda install -c conda-forge opencv
```
安装过程需要依赖的安装包都会在这一步进行安装。安装完成后，激活python环境，然后键入：
```
import cv2
```
过程中没有报错，则证明安装成功。
