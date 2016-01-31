title: Windows下在Virtual Box中安装CentOS
date: 2015-12-03 10:23:36
categories: Linux
tags: 
- virtual 
- centos
---
## 安装Virtual Box
下载安装windows版
[点击从官网下载](http://download.virtualbox.org/virtualbox/5.0.10/VirtualBox-5.0.10-104061-Win.exe)

## 下载centos
[阿里云centos镜像地址](http://mirrors.aliyun.com/centos/)

## 安装
安装过程略
> 参考百度经验
> [点击查看](http://jingyan.baidu.com/article/cdddd41c66183953cb00e10e.html)

<!-- more -->

## 常见问题
1. 安装时无64位可选择。原因为PC未虚拟化
发现版本那里只有32bit，没有64bit的选项，原因是因为PC机还没有开启虚拟化开关，此时需要重启PC机，点击F1进入BIOS开启虚拟化设置。
![BIOS虚拟化](./bios.png)
2. 安装过程中，如提示：没有找到可用磁盘
下图中不要选是，选否即可
![选择是否覆盖](./nofound.jpg)