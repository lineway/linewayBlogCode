---
title: python开发工具Anaconda使用
date: 2018-05-24 00:04:20
tags: python
categories: 编程学习
---
## Anaconda简介

`Anaconda`是一套集成的python开发环境，据说是为了方便实用python做科学计算而做出的集成环境，但是由于其涵盖了大多数的主流第三方库，以及集成了`conda`工具，因此越来越多的python开发者选择使用`Anaconda`来替代原始python环境。

## Anaconda简单使用

`Anaconda`可以方便的下载，官方网址是：[https://anaconda.org/](https://anaconda.org/),如果不方便在官网进行下载的话，国内也推荐使用清华大学的开源镜像站点进行下载，链接地址：[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)，按照自己操作系统的类别和需要安装的版本进行选择即可，关于安装部分就不详细介绍。  

安装完成后，打开终端，输入`python`，如果出现一下图片，则证明安装成功并且环境信息配置正常：  
![](/images/anaconda_1.jpg)  

python开发者通常会纠结于各种开发环境的配置问题，因此会选择例如`virtualenv`、`pyenv`等第三方库来管理不同的开发环境，当我们使用`Anaconda`之后，其自带的`conda`也可以帮助我们有效的管理各种开发环境的配置，比如以下的几种操作：  

```python
conda info -e			# 查看host中所有的python开发环境

# 创建新的虚拟环境
conda create -n <env_name> <python_version> <python_lib>

# 激活要使用的环境
source activate <env_name> # linux
activate <env_name>        # windows

# 退出环境
source deactivate			# linux
deactivate					# windows

# 删除host中不需要的环境
conda remove -n <env_name> --all
```

python有一个方便的包管理工具`pip`,当我们使用`conda`之后，也可以用`conda`来替代`pip`，使用方法基本类似，就不做过多的介绍了。根据个人喜好的不同，大家可以自由选择，另外，如果因为网络问题造成的`pip`或者`conda`安装第三方库缓慢，可以在上述清华大学的开源镜像网站中配置清华大学的源。