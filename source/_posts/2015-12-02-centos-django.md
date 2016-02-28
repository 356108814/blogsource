title: centos下搭建Django开发环境
date: 2015-12-02 22:28:36
categories: 编程语言
tags:
  - Django
---

# CentOS-6.7搭建Django环境
## 安装Python2.7.10

1.安装sqlite

    $ yum install sqlite-devel
2.安装zlib

    $ yum install zlib-devel

3.安装openssl

    $ yum install openssl -y
    $ yum install openssl-devel -y

<!-- more -->

4.编译安装Python

    $ wget https://www.python.org/ftp/python/2.7.10/Python-2.7.10.tar.xz
    $ tar jxvf Python-2.7.10.tar.xz
    $ cd Python-2.7.10
    $ ./configure --prefix=/usr/local/python2.7.10
    $ make && make install

5.设置软链接

    $ cp /usr/bin/python /usr/bin/python.bak //备份
    $ rm -f python
    $ ln -s /usr/local/python2.7.10/bin/python /usr/bin/python

## 安装pip

    $ wget https://bootstrap.pypa.io/get-pip.py
    $ python get-pip.py

## 安装Django

    $ pip install Django==1.8.5
## 设置django-admin
    $ ln -s /usr/local/python2.7.10/lib/python2.7/site-packages/django/bin/django-admin.py /usr/bin/django-admin

## 安装MySQL-python
    $ yum install mysql-devel
    $ wget http://sourceforge.net/projects/mysql-python/files/mysql-python/1.2.3/MySQL-python-1.2.3.tar.gz
    $ tar zxvf MySQL-python-1.2.3.tar.gz
    $ cd MySQL-python-1.2.3
    $ python setup.py build
    $ python setup.py install

## 创建项目
    $ cd 你的目录
    $ django-admin startproject blog
## 运行项目
    $ cd blog
    $ manage.py runserver 0.0.0.0:8000
    $ service iptables stop //若在浏览器无法访问，则关闭防火墙