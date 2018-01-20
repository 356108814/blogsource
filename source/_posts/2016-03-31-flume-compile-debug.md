title: Flume编译调试
categories: 技术
tags:
  - flume

date: 2016-03-31 20:44:38
---
#### 简介
>Flume是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。
官网：https://flume.apache.org/

#### 源码编译
##### 下载源码
[apache-flume-1.6.0-src.tar.gz](http://www.apache.org/dyn/closer.lua/flume/1.6.0/apache-flume-1.6.0-src.tar.gz)
Maven方式导入IntelliJ IDEA，Maven设置为自动导入
{% asset_img 1.jpg %}

##### 增加maven repository
修改pom.xml
{% asset_img 2.jpg %}

注：由于国内网络环境原因，有可能自动下载依赖jar时，有的下载失败了，导致编译时各种乱七八糟的错误。因此，需要增加一个maven仓库地址，这里推荐一个：
http://maven.oschina.net/service/local/repositories/sonatype-public-grid/content/

<!-- more -->

#### Debug调试
Flume运行的主类为flume-ng-node下的Application.java。因此调试可以从这里开始。Flume调试需要日志配置和flume配置，因此需要增加2个文件。
##### flume.conf
在如下图位置增加flume.conf
{% asset_img 3.jpg %}
flume.conf
	# Licensed to the Apache Software Foundation (ASF) under one
	# or more contributor license agreements.  See the NOTICE file
	# distributed with this work for additional information
	# regarding copyright ownership.  The ASF licenses this file
	# to you under the Apache License, Version 2.0 (the
	# "License"); you may not use this file except in compliance
	# with the License.  You may obtain a copy of the License at
	#
	#  http://www.apache.org/licenses/LICENSE-2.0
	#
	# Unless required by applicable law or agreed to in writing,
	# software distributed under the License is distributed on an
	# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
	# KIND, either express or implied.  See the License for the
	# specific language governing permissions and limitations
	# under the License.
	
	# Name the components on this agent
	flume-master.sources = r1
	flume-master.sinks = k1
	flume-master.channels = c1
	
	
	flume-master.sources.r1.type = spooldir
	flume-master.sources.r1.spoolDir = H:/temp/
	
	
	#flume-master.sources.r1.type = netcat
	#flume-master.sources.r1.bind = localhost
	#flume-master.sources.r1.port = 44444
	#flume-master.sources.r1.channels = c1
	
	
	# Use a channel which buffers events in memory
	flume-master.channels.c1.type = file
	flume-master.channels.c1.checkpointDir = H:/ccheckpoint
	flume-master.channels.c1.dataDirs =  H:/cdata/
	
	# Describe the sink
	flume-master.sinks.k1.type = file_roll
	flume-master.sinks.k1.sink.directory = H:/sdata/
	
	# Bind the source and sink to the channel
	flume-master.sources.r1.channels = c1
	#flume-master.sources.r2.channels = c1
	flume-master.sinks.k1.channel = c1
##### log4j.properties
在如下图位置增加日志配置
{% asset_img 4.jpg %}
log4j.properties
	# Licensed to the Apache Software Foundation (ASF) under one
	# or more contributor license agreements.  See the NOTICE file
	# distributed with this work for additional information
	# regarding copyright ownership.  The ASF licenses this file
	# to you under the Apache License, Version 2.0 (the
	# "License"); you may not use this file except in compliance
	# with the License.  You may obtain a copy of the License at
	#
	#  http://www.apache.org/licenses/LICENSE-2.0
	#
	# Unless required by applicable law or agreed to in writing,
	# software distributed under the License is distributed on an
	# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
	# KIND, either express or implied.  See the License for the
	# specific language governing permissions and limitations
	# under the License.
	#
	
	# Define some default values that can be overridden by system properties.
	#
	# For testing, it may also be convenient to specify
	# -Dflume.root.logger=DEBUG,console when launching flume.
	
	#flume.root.logger=DEBUG,console
	flume.root.logger=INFO,LOGFILE
	flume.log.dir=./logs
	flume.log.file=flume.log
	
	log4j.logger.org.apache.flume.lifecycle = INFO
	log4j.logger.org.jboss = WARN
	log4j.logger.org.mortbay = INFO
	log4j.logger.org.apache.avro.ipc.NettyTransceiver = WARN
	log4j.logger.org.apache.hadoop = INFO
	log4j.logger.org.apache.hadoop.hive = ERROR
	
	# Define the root logger to the system property "flume.root.logger".
	log4j.rootLogger=${flume.root.logger}
	
	
	# Stock log4j rolling file appender
	# Default log rotation configuration
	log4j.appender.LOGFILE=org.apache.log4j.RollingFileAppender
	log4j.appender.LOGFILE.MaxFileSize=100MB
	log4j.appender.LOGFILE.MaxBackupIndex=10
	log4j.appender.LOGFILE.File=${flume.log.dir}/${flume.log.file}
	log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
	log4j.appender.LOGFILE.layout.ConversionPattern=%d{dd MMM yyyy HH:mm:ss,SSS} %-5p [%t] (%C.%M:%L) %x - %m%n
	
	
	# Warning: If you enable the following appender it will fill up your disk if you don't have a cleanup job!
	# This uses the updated rolling file appender from log4j-extras that supports a reliable time-based rolling policy.
	# See http://logging.apache.org/log4j/companions/extras/apidocs/org/apache/log4j/rolling/TimeBasedRollingPolicy.html
	# Add "DAILY" to flume.root.logger above if you want to use this
	log4j.appender.DAILY=org.apache.log4j.rolling.RollingFileAppender
	log4j.appender.DAILY.rollingPolicy=org.apache.log4j.rolling.TimeBasedRollingPolicy
	log4j.appender.DAILY.rollingPolicy.ActiveFileName=${flume.log.dir}/${flume.log.file}
	log4j.appender.DAILY.rollingPolicy.FileNamePattern=${flume.log.dir}/${flume.log.file}.%d{yyyy-MM-dd}
	log4j.appender.DAILY.layout=org.apache.log4j.PatternLayout
	log4j.appender.DAILY.layout.ConversionPattern=%d{dd MMM yyyy HH:mm:ss,SSS} %-5p [%t] (%C.%M:%L) %x - %m%n
	
	
	# console
	# Add "console" to flume.root.logger above if you want to use this
	log4j.appender.console=org.apache.log4j.ConsoleAppender
	log4j.appender.console.target=System.err
	log4j.appender.console.layout=org.apache.log4j.PatternLayout
	log4j.appender.console.layout.ConversionPattern=%d (%t) [%p - %l] %m%n

##### 设置启动命令参数
说明：
1、-n 配置的代理名称。flume-master必须与flume.conf中的代理名称一致
2、-f 配置的是配置文件路径
{% asset_img 5.jpg %}

设置完就可以debug调试了