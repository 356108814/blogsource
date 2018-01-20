title: Python访问Oracle操作
categories: 技术
tags:
  - python
date: 2016-03-30 22:20:48
---
#### 安装
python操作Oracle数据库使用的是cx_Oracle，而cx_Oracle需要使用Oracle Instant Client
##### 安装cx_Oracle
官网：http://cx-oracle.sourceforge.net/

	pip install cx_Oracle
##### 安装Oracle Instant Client
官网：http://www.oracle.com/technetwork/database/features/instant-client/index.html

<!-- more -->

**注意：下载时需要下载对应平台，对应Oracle版本的安装包，如我的机子为centos 64位，Oracle版本为12.1.0.2.0，则需要下载的版本为：
oracle-instantclient12.1-basic-12.1.0.2.0-1.x86_64.rpm **

	sudo rpm -ivh oracle-instantclient12.1-basic-12.1.0.2.0-1.x86_64.rpm
	# 编辑/etc/profile设置环境变量
	export ORACLE_CLIENT=/usr/lib/oracle/12.1/client64/lib/
	export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_CLIENT
  
#### 数据库操作
##### 连接
	def connect_oracle_dsn(host, port, user, passwd, sid):
	    """
	    dsn方式连接oracle
	    """
	    dsn = cx_Oracle.makedsn(host, port, sid)
	    return cx_Oracle.connect(user, passwd, dsn)
	
	def connect_oracle_tns(host, port, user, passwd, sid):
	    """
	    tns连接oracle
	    """
	    return cx_Oracle.oracle.connect(user, passwd, host + ":" + port + "/" + sid)
**注：如果tns方式连接报错cx_Oracle.DatabaseError: ORA-12514: TNS:listener does not currently know of service requested in connect descriptor的话，则需要在Oracle服务器配置中加入sid对应的tns**
##### 查询
	def query(sql):
	    """
	    查询
	    """
	    db_config = {
	        'host': '172.16.2.101',
	        'port': 1521,
	        'user': 'data_team',
	        'passwd': 'oracle',
	        'sid': 'testDB'
	    }
	    conn = connect_oracle(db_config['host'], db_config['port'], db_config['user'], db_config['passwd'], db_config['sid'])
	    cursor = conn.cursor()
	    cursor.execute(sql)
	    query_result = cursor.fetchall()
	    cursor.close()
	    conn.close()
	    return query_result

##### 更新
	def execute(sql):
	    """
	    更新
	    """
	    db_config = {
	        'host': '172.16.2.101',
	        'port': 1521,
	        'user': 'data_team',
	        'passwd': 'oracle',
	        'sid': 'testDB'
	    }
	    conn = connect_oracle(db_config['host'], db_config['port'], db_config['user'], db_config['passwd'], db_config['sid'])
	    cursor = conn.cursor()
	    cursor.execute(sql)
	    conn.commit()
	    cursor.close()
	    conn.close()


