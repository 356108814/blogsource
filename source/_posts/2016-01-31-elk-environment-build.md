title: ELK stash环境搭建
date: 2016-01-31 17:28:46
categories: ELK
tags:
 elk
---
ELK是Elasticsearch、logstash、Kibana的英文简称。是一套实时日志分析展示的开源框架集。其中Logstash负责日志的收集、处理，Elasticsearch是基于Lucene的搜索引擎，提供搜索功能，Kibana负责可视化的实时数据。
官网为：[https://www.elastic.co/](https://www.elastic.co/ "https://www.elastic.co/")

注：安装环境为centos6.7
#### 安装java环境
1、卸载系统自带的Open Jdk  
		java -version #查看当前版本
		yum -y remove java-xxx #xxx为上步查看到的java信息
2、从官网下载对应的jdk：jdk-8u71-linux-x64.tar.gz
[http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html]( "jdk-8u71-linux-x64.tar.gz")  
3、解压、配置环境变量  

	tar jdk-8u71-linux-x64.tar.gz -C /usr/local/java/
	vim /etc/profile
	export JAVA_HOME=/usr/local/java/jdk-8u71-linux-x64
	export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	export PATH=$PATH:$JAVA_HOME/bin
	source /etc/profile    # 环境变量生效

#### 安装Logstash
1、从官网[https://www.elastic.co/downloads/logstash](https://www.elastic.co/downloads/logstash)下载logstash-2.1.1.tar.gz
2、解压运行  

	tar -xzvf logstash-2.1.1.tar.gz -C /usr/local/logstash-2.1.1
	cd /usr/local/logstash-2.1.1
	bin/logstash -e ""
	hello world # 输入然后回车测试输出

#### 安装Elasticsearch
1、从官网[https://www.elastic.co/downloads/elasticsearch](https://www.elastic.co/downloads/elasticsearch)下载elasticsearch-2.1.1.tar.gz
2、解压

	tar -xzvf elasticsearch-2.1.1.tar.gz -C /usr/local/elasticsearch-2.1.1
3、安装marvel-agent插件

	cd /usr/local/elasticsearch-2.1.1
	bin/plugin install license
	bin/plugin install marvel-agent
4、测试

	curl 'http://localhost:9200/?pretty'
#### 安装Kibana

1、从官网[https://www.elastic.co/downloads/kibana](https://www.elastic.co/downloads/kibana)下载kibana-4.3.1-linux-x64.tar.gz
2、解压运行

	tar -xzvf kibana-4.3.1-linux-x64.tar.gz -C /usr/local/kibana-4.3.1-linux-x64
	cd /usr/local/kibana-4.3.1-linux-x64
	bin/kibna
3、打开
在浏览器中输入http://localhost:5602/