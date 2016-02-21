title: 使用Tengine+uwsgi部署Django项目
categories: Linux
tags:
  - Tengine
  - uwsgi
  - Django
date: 2016-02-21 21:38:20
---
#### 安装Tengine
Tengine是在Nginx基础上开发的开源的高性能Web服务器。官网为：[http://tengine.taobao.org/](http://tengine.taobao.org/)
##### 下载安装
	wget http://tengine.taobao.org/download/tengine-2.1.2.tar.gz
	tar -xzvf tengine-2.1.2.tar.gz
	cd tengine-2.1.2
	./config --prefix=/usr/local/tengine2.1.2 #--prefix指定安装目录
	make
	sudo make install
	ln -s ./sbin/nginx /usr/local/sbin/ 

##### 启动
	nginx -c /usr/local/tengine-2.1.2/conf/nginx.conf
nginx -h #查看帮助
Options:
  -?,-h         : this help
  -v            : show version and exit
  -m            : show all modules and exit
  -l            : show all directives and exit
  -V            : show version, modules and configure options then exit
  -t            : test configuration and exit
  -d            : dump configuration and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/local/tengine-2.1.2/)
  -c filename   : set configuration file (default: conf/nginx.conf)
  -g directives : set global directives out of configuration file


##### 配置
在安装目录下的conf为tengine的配置目录，默认配置文件为：nginx.conf。若同时运行多个项目，可分别为多个项目建立配置。
###### 编辑nginx.conf
在倒数第二行，即}上面加入

	include /usr/local/tengine-2.1.2/conf/vhost/*.conf;
###### 建立vhost
	cd /usr/local/tengine-2.1.2/conf/
	mkdir vhost
	vim PDM.conf #PDM为项目名称
	server {
    listen 80;
    server_name PDM.236g.com;
    access_log  /usr/local/tengine-2.1.2/logs/PDM.access.log  main;

    req_status server;

    location ~ ^/favicon\.ico$ {
        root    html;
    }
    location / {
        include uwsgi_params;
        uwsgi_read_timeout 120;
        uwsgi_pass 127.0.0.1:9090;
    }
    location /static/ {
        alias /usr/local/tengine-2.1.2/html/PDM/static/;
    }
}

#### 安装uwsgi

##### pip安装
	pip install uwsgi
##### 源码编译安装
	wget http://projects.unbit.it/downloads/uwsgi-2.0.12.tar.gz
	tar -xzvf uwsgi-2.0.12.tar.gz
	cd uwsgi-2.0.12
	sudo python setup.py build
	make
	cp ./uwsgi /usr/local/bin #编译成功后将生成一个uwsgi文件

uwsgi --help查看


#### Django项目配置

##### django_socket.xml
此配置为uwsgi运行配置文件，支持xml和ini格式
###### xml格式
	<uwsgi>
	    <master/> <!--开启uwsgi的管理进程-->
	    <enable-threads/> <!--开启python的线程特性-->
	    <socket>127.0.0.1:9090</socket> <!-- 和nginx中定义的要一致 -->
	    <chdir>/usr/local/tengine-2.1.2/html/PDM</chdir>      <!-- 你django的项目目录 -->
	    <module>django_uwsgi</module> <!-- 名称为刚才上面定义的py文件名 -->
	    <processes>4</processes> <!-- 进程数 -->
	    <listen>200</listen> <!--增加uwsgi的监听队列-->
	    <max-requests>5000</max-requests> <!--每个工作线程处理多少个请求之后，就重新load-->
	    <daemonize>/usr/local/tengine-2.1.2/logs/uwsgi/PDM-9090.log</daemonize> <!--日志文件-->
	</uwsgi>
###### ini格式
	[uwsgi]
	master = true
	enable-threads = true
	socket = 127.0.0.1:9090
	chdir = /usr/local/tengine-2.1.2/html/PDM
	module = django_uwsgi
	processes = 4
	listen = 200
	max-requests = 5000
	daemonize = /usr/local/tengine-2.1.2/logs/uwsgi/PDM-9090.log


##### django_uwsgi.py
	#!/usr/bin/env python
	# coding: utf-8
	
	import os
	import sys
	from django.core.wsgi import get_wsgi_application 
	 
	# 将系统的编码设置为UTF8
	reload(sys)
	sys.setdefaultencoding('utf8')
	 
	# PDM为项目主文件夹名称
	os.environ.setdefault("DJANGO_SETTINGS_MODULE", "PDM.settings")
	 
	application = get_wsgi_application()

#### 运行访问
##### 运行uwsgi
	/usr/bin/uwsgi -x /usr/local/tengine-2.1.2/html/PDM/django_socket.xml
##### 运行Tengine
	nginx 
##### 访问
在浏览器输入：PDM.236g.com
tengine将请求转发到127.0.0.1:9090端口，127.0.0.1:9090为uwsgi监听的端口

#### 问题
<font color=#ff0000>nginx: [emerg] zero size shared memory zone "server"</font>
说明conf配置中的server配置错误了，如：使用了未安装的模块

uwsig启动不成功，查看uwsgi日志发现：
your processes number limit is 62525
your memory page size is 4096 bytes
detected max file descriptor number: 1024
lock engine: pthread robust mutexes
thunder lock: disabled (you can enable it with --thunder-lock)
<font color=#ff0000>Listen queue size is greater than the system max net.core.somaxconn (128).</font>

解决：

	vim /etc/sysctl.conf增加一行
	net.core.somaxconn = 65536 #默认值为128
	sysctl -p #然后执行命令使生效

