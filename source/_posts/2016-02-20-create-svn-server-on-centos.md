title: centos上搭建svn服务器
date: 2016-02-20 10:56:37
categories: 技术
tags:
  - svn
---
目前软件工程中项目版本管理最常用的就是svn和git，现将带领你如何在centos一步步搭建svn服务器。

<!-- more -->

#### 安装svn
	$yum install subversion

#### 创建版本库目录
	mkdir /usr/local/svn/
	cd /usr/local/svn/
	svnadmin create repo    #repo为版本库名称

#### 配置版本库
创建版本库后，svn自动在repo下生成了一些文件和文件夹，配置需要关注的是conf下面的3个文件：
authz  passwd  svnserve.conf
##### 用户密码配置
passwd

	[users]
	dream = 0iooQAOMzl2k
##### 权限配置
authz
	#用户组
	[groups]
	master = dream,chengx #管理员组
	test = t1 #测试组

	#配置目录权限
	[svn_pero:/]
	@master = rw #管理员组拥有根目录读写权限
	* = r
	
	[svn_pero:/test/]
	@test = rw #测试组只拥有test目录读写权限
	* = r

##### svn服务配置
svnserve.conf

	[general]
	anon-access = read
	auth-access = write

	password-db = passwd #用户密码文件，默认当前目录下的，可配置路径

	authz-db = authz     #用户权限文件，默认当前目录下的，可配置路径

	# 版本库唯一id
	realm = 凡人修真

#### 启动/停止

	# 启动
	svnserve -d -r /usr/local/svn/

	# 停止
	ps -ef|grep svn
	kill -9 pid

#### 导入项目
假设在/home/dream下有项目PDM,包含trunk、branches、tags

	cd /home/dream/PDM
	svn import PDM svn://ip/repo/PDM -m "数据管理系统第一次提交" #ip换为真实ip地址

#### 检测出项目

	svn co svn://ip/repo/PDM #ip换为真实ip地址

#### 多项目管理
如果版本库需要管理多个项目，重复导入项目即可。如DA数据分析项目，则DA项目地址为：

	svn co svn://ip/repo/DA #ip换为真实ip地址

这种方式下，一个版本库管理多个项目，svn配置只有一个

另外一种多项目管理为，对每一个项目分别建立一个版本库，多个版本库放在一个文件夹下