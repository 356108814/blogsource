title: 离线安装CDH
categories: 技术
tags:
  - CDH
date: 2016-11-17 22:21:17
---
#### 系统环境
##### 软件
操作系统：centos7	
CDH：cdh5.8.0	
java:jdk1.7
##### 硬件
机器列表	
master：172.16.2.190		
slaves：172.16.2.191~172.16.2.194	

<!-- more -->

#### 配置
注：所有操作都在root下	

###### 修改host
	# 在190上
	vim /etc/hosts
	172.16.2.190 CDH-0 
	172.16.2.191 CDH-1 
	172.16.2.192 CDH-2
	172.16.2.193 CDH-3
	172.16.2.194 CDH-4
	
	# 依次复制hosts到子节点
	scp /etc/hosts root@CDH-1:/etc
	# 修改hostname，190修改为 CDH-0，其他依次类推
	vim /etc/hostname
	CDH-0
	
###### 关闭防火墙和selinux
	systemctl status firewalld.service    # 查看
	systemctl stop firewalld.service      # 停止firewall
	systemctl disable firewalld.service   # 禁止firewall开机启动
	
	# centos6关闭防火墙如下，centos7如果安装了iptables，也要按如下关掉
	service iptables stop
	chkconfig iptables off
	
	# 关闭selinux
	vim /etc/selinux/config
	SELINUX=enable 改为 SELINUX=disabled
	
###### ssh无密码登录
	# 在主节点190操作
	cd ~
	ssh-keygen -t rsa    # 一路按回车后，会在当前目录新建.ssh文件夹
	cd .ssh
	cat id_rsa.pub > authorized_keys
	chmod 600 authorized_keys
	
	# 复制authorized_keys到各子节点
	scp authorize_keys root@CDH-1:~/.ssh/
	
###### ntp时间同步
	# 在所有节点安装ntp
	yum install ntp
	# 在主节点190，加入server xxx
	vim /etc/ntp.conf
	# 在191~194各子节点，加入server CDH-0，从主节点同步时间
	vim /etc/ntp.conf
	server CDH-0
	# 启动ntp，在所有节点执行，先主后从
	service ntpd start
	
	

#### 下载
##### 下载Cloudera Manager
http://www.cloudera.com/downloads.html
http://archive.cloudera.com/cm5/redhat/7/x86_64/cm/5.8.0/RPMS/x86_64/
http://archive.cloudera.com/cm5/cm/5/	
下载RPMS/x86_64下所有的rpm文件
cm下载保存路径为：/home/dream/download/cm
##### 下载CDH
http://archive.cloudera.com/cdh5/parcels/5.8.0/
下载对应版本的文件
CDH-5.8.0-1.cdh5.8.0.p0.42-el7.parcel
CDH-5.8.0-1.cdh5.8.0.p0.42-el7.parcel.sha1
manifest.json
cdh下载保存路径为：/opt/cloudera/parcel-repo/

#### 安装
##### 安装JDK
在所有节点安装jdk
这里安装cloudera推荐的jdk版本：oracle-j2sdk1.7-1.7.0+update67-1.x86_64.rpm
yum localinstall --nogpgcheck oracle-j2sdk1.7-1.7.0+update67-1.x86_64.rpm
默认安装路径为：
配置环境变量，在/etc/profile中加入

	export JAVA_HOME=/usr/java/cloudera/jdk1.7.0_67
	export JRE_HOME=$JAVA_HOME/jre  
	export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
	export PATH=$PATH:$JAVA_HOME/bin
##### 安装cloundera Manager
	# 主节点安装所有
	cd /home/dream/download/cm
	yum localinstall --nogpgcheck *.rpm
	# 子节点安装如下2个即可
	yum localinstall --nogpgcheck cloudera-manager-agent-5.8.0-1.cm580.p0.42.el7.x86_64.rpm.rpm
	yum localinstall --nogpgcheck cloudera-manager-daemons-5.8.0-1.cm580.p0.42.el7.x86_64.rpm
	
###### 启动	
	# 主节点
	/etc/init.d/cloudera-scm-server restart
	# 子节点
	/etc/init.d/cloudera-scm-agent start
	# 修改系统参数，然后加入开机启动
	vim /etc/rc.local 
	echo never > /sys/kernel/mm/transparent_hugepage/defrag
	source /etc/rc.local
	# 在/etc/sysctl.conf 文件里添加如下参数：
	vm.swappiness=10
	
###### 访问	
浏览器输入：http://CDH-0:7180/，默认用户名和密码都为：admin

##### 安装CDH服务	
准备文件	

	# 所有节点都需要有下面的文件
	cd /opt/cloudera/parcel-repo/
	# .sha1文件后缀更改为.sha
	mv CDH-5.8.0-1.cdh5.8.0.p0.42-el7.parcel.sha1 CDH-5.8.0-1.cdh5.8.0.p0.42-el7.parcel.sha

安装直接在http://CDH-0:7180/按照提示安装即可

