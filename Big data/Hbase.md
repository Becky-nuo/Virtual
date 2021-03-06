# HBase安装配置

## 上机步骤：
>安装并配置HBase,按照伪分布式模式安装;

>启动进入HBase Shell环境，利用命令行语句访问HBase数据库；

>使用Eclipse工具编写Java程序访问HBase数据库;

## 安装并配置HBase
>说明：HBase的版本一定要和之前已经安装的Hadoop的版本保持兼容，不能随便选择版本;

>HBase1.1.2和Hadoop2.7.1兼容，而HBase2.2.2和Hadoop3.1.3兼容；本次安装的是HBase1.1.2；

### HBase 1.1.2安装
#### 解压安装包
>解压安装包hbase-1.1.2-bin.tar.gz至路径 /usr/local，命令如下：
`sudo tar -zxf ~/下载/hbase-1.1.2-bin.tar.gz -C /usr/local  #Shell 命令`

>解压的文件名hbase-1.1.2改为hbase，命令如下：
`sudo mv /usr/local/hbase-1.1.2 /usr/local/hbase   #Shell 命令`

#### 配置环境变量
>将hbase下的bin目录添加到path中，启动hbase就不用到/usr/local/hbase目录下，方便了hbase的使用，编辑~/.bashrc文件：
`vi ~/.bashrc   #Shell 命令`

>如果没有引入过PATH请在~/.bashrc文件尾行添加如下内容：
`export PATH=$PATH:/usr/local/hbase/bin   #Shell `

>如果已经引入过PATH请在export PATH这行追加/usr/local/hbase/bin，这里的“：”是分隔符,如下(  // 表示注释  )：
```
export JAVA HOME= /usr /lib/jvm/ java- 7-openjdk- and64
export HADOOP HOME= /usr /Local/hadoop
export HADOOP COMMON LIB_ NATIVE DIR=$HADOOP_ HOME / lib/native 
export PATH=SPATH: /usr /local/hadoop/sbin: /usr /local/hadoop/bin: /usr /local/hbase/bin
 //  ~/ .bashrc:executed by bash(1) for non- Login shells
//  see /usr /share/doc/bash/examples/startup-files (in the package bash-doc )
// for examples
//  If not running interactively, don't do anything
case $- in
return; ;
esac
//  don't put duplicate lines or lines starting with space in the history.
```
>编辑完成后，再执行source命令使上述配置在当前终端立即生效，命令如下：
`source ~/.bashrc    # hell 命令`


#### 添加HBase权限
`cd /usr/localsudo chown -R hadoop ./hbase       #将hbase下的所有文件的所有者改为hadoop，hadoop是当前用户的用户名`

#### 查看HBase版本
>确定hbase安装成功,命令如下：
`/usr/local/hbase/bin/hbase version    #Shell 命令`

>命令执行后，输出信息如下：
```
hadoop@hadoop- Lenovo:~$ /us / Local/hbase /bin/hbase ver sion
2020-05-06 16:38:52，195 INFO  [main] util.VersionInfo:  HBase 1.1.2
2020-05-06 16:38:52,195 INFO [main] util. VersionInfo: Source code repository gi
t:/ /hw11397. Local/Volumes /hbase-1.1.2RC2 /hbase revis ion=cc2b70cf03e3378800661ec5
cab11eb43fafe0fc
2020-05-06 16:38:52,196 INFO [main] util. VersionInfo: Compiled by ndimiduk on W
ed Aug 26 20:11:27 PDT 2015
2020-05-06 16:38:52,196 INFO [main] util.VersionInfo: From source with checksum
73da41f3d1b867b7aba6166c77fafc17
```
>看到以上输出消息表示HBase已经安装成功;

### HBase配置
>HBase有三种运行模式，单机模式、伪分布式模式、分布式模式;
>先决条件很重要，如没有配置JAVA_HOME环境变量，就会报错:
```
– jdk
– Hadoop( 单机模式不需要，伪分布式模式和分布式模式需要)
– SSH
```
#### 单机模式配置
> 配置/usr/local/hbase/conf/hbase-env.sh ,配置JAVA环境变量，并添加配置HBASE_MANAGES_ZK为true，用vi命令打开并编辑hbase-env.sh，命令如下：
`vi /usr/local/hbase/conf/hbase-env.sh   #Shell 命令`

>配置JAVA环境变量，jdk的安装目录默认是 /usr/lib/jvm/java-1.7.0-openjdk， 则JAVA _HOME =/usr/lib/jvm/java-7-openjdk-amd64，其中java-1.7.0-openjdk是你的jdk版本；配置HBASE_MANAGES_ZK为true，表示由hbase自己管理zookeeper，不需要单独的zookeeper。hbase-env.sh中本来就存在这些变量的配置，只需要删除前面的#并修改配置内容：

`xport JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64export HBASE_MANAGES_ZK=true   #Shell`

>添加完成后保存退出即可;

>配置/usr/local/hbase/conf/hbase-site.xml ,打开并编辑hbase-site.xml，命令如下：

`vi /usr/local/hbase/conf/hbase-site.xml  #Shell 命令`

>在启动HBase前需要设置属性hbase.rootdir，用于指定HBase数据的存储位置，因为如果不设置的话，hbase.rootdir默认为/tmp/hbase-${user.name},这意味着每次重启系统都会丢失数据;处设置为HBase安装目录下的hbase-tmp文件夹即（/usr/local/hbase/hbase-tmp）,添加配置如下：
```
<configuration>        
	<property>                
		<name>hbase.rootdir</name>                
		<value>file:///usr/local/hbase/hbase-tmp</value>        
	</property>
</configuration>   #XML
```

>测试运行,先切换目录至HBase安装目录/usr/local/hbase；再启动HBase,命令如下：

`cd /usr/local/hbasebin/start-hbase.shbin/hbase shell   #Shell 命令`

>停止HBase运行,命令如下：
`bin/stop-hbase.sh   # Shell 命令`

>如果在操作HBase的过程中发生错误，可以通过{HBASE_HOME}目录（/usr/local/hbase）下的logs子目录中的日志文件查看错误原因;

#### 伪分布式模式配置
>配置/usr/local/hbase/conf/hbase-env.sh。命令如下：
`vi /usr/local/hbase/conf/hbase-env.sh   #Shell 命令`

>配置JAVA_HOME，HBASE_CLASSPATH，HBASE_MANAGES_ZK.HBASE_CLASSPATH设置为本机Hadoop安装目录下的conf目录（即/usr/local/hadoop/conf）:
```
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
export HBASE_CLASSPATH=/usr/local/hadoop/conf 
export HBASE_MANAGES_ZK=true    #Shell
```

>内容如下(  // 表示注释  ):
```
export JAVA_ HOME= /usr/Lib/ jvm/java- 7 - openjdk- and64
export HBASE MANAGES_ ZK=true
export HBASE CLASSPATH= /usr/Local/hadoop/conf
//
//  /**
//  * Licensed to the Apache Software Foundation (ASF) under one
//  * or more contributor license agr eements .See the NOTICE file
//  distributed with this work for additional information
//  * regarding copyright ownership. The ASF licenses this file
//  * to you under the Apache License, Version 2.0 ( the
//  * "License"); you may not use this file except in compliance 
//   with the License. You may obtain a copy of the License at
//   *
//  http: / /www. apache .org/licenses/L ICENSE-2.0
```

>配置/usr/local/hbase/conf/hbase-site.xml ,用命令vi打开并编辑hbase-site.xml，命令如下：
`vi /usr/local/hbase/conf/hbase-site.xml   #Shell 命令`

>修改hbase.rootdir，指定HBase数据在HDFS上的存储路径；将属性hbase.cluter.distributed设置为true,假设当前Hadoop集群运行在伪分布式模式下，在本机上运行，且NameNode运行在9000端口：
```
<configuration>        
	<property>                
		<name>hbase.rootdir</name>                
		<value>hdfs://localhost:9000/hbase</value>        
	</property>        
	<property>                
		<name>hbase.cluster.distributed</name>                
		<value>true</value>        
	</property>
</configuration>  #XML
```

>hbase.rootdir指定HBase的存储目录；hbase.cluster.distributed设置集群处于分布式模式.内容如下：
```
<configuration>l
	<property>
		<name>hbase . rootdir</name>
			<! --  对应于hdfs中配置-->
		<value> hdfs: / /Localhost : 9000 /hbase< /value>
	</property>
	<property>
		<name> hbase. cluster . distributed</name>
		<value> true< /value>
	</property>
</configuration>
```

##### 测试运行HBase:
>登陆ssh，切换目录至/usr/local/hadoop ；再启动hadoop，如果已经启动hadoop请跳过此步骤,命令如下：

`ssh localhostcd /usr/local/hadoop./sbin/start-dfs.sh   #Shell 命令`

>输入命令jps，能看到NameNode,DataNode和SecondaryNameNode都已经成功启动，表示hadoop启动成功;

>切换目录至/usr/local/hbase;再启动HBase.命令如下：
`cd /usr/local/hbasebin/start-hbase.sh    #Shell 命令`

>启动成功，输入命令jps，看到以下界面说明hbase启动成功;

>停止HBase运行,命令如下：
`bin/stop-hbase.sh   #Shell 命令`

>在操作HBase的过程中发生错误，可以通过{HBASE_HOME}目录（/usr/local/hbase）下的logs子目录中的日志文件查看错误原因;

>启动关闭Hadoop和HBase的顺序一定是：启动Hadoop—>启动HBase—>关闭HBase—>关闭Hadoop;

## 编程实践
### 利用Shell命令
#### HBase中创建表

>HBase中用create命令创建表，如下：
`create 'student','Sname','Ssex','Sage','Sdept','course'   #Shell 命令`

>命令执行如下：

`hbase(main) :008:0> create ' student', ' Sname' , ' ssex','Sage ',' Sdept' ,'course' row(s ) in 1.0770 seconds`

>创建了一个“student”表，属性有：Sname,Ssex,Sage,Sdept,course。因为HBase的表中会有一个系统默认的属性作为行键，无需自行创建，默认为put命令操作中表名后第一个数据。创建完“student”表后，可通过describe命令查看“student”表的基本信息;

#### HBase数据库基本操作
##### 添加数据
>HBase中用put命令添加数据，注意：一次只能为一个表的一行数据的一个列，也就是一个单元格添加一个数据，所以直接用shell命令插入数据效率很低，在实际应用中，一般都是利用编程操作数据;当运行命令：put ‘student’,’95001’,’Sname’,’LiYing’时，即为student表添加了学号为95001，名字为LiYing的一行数据，其行键为95001:
`put 'student','95001','Sname','LiYing'   #Shell 命令`

>命令执行截图如下，即为student表添加了学号为95001，名字为LiYing的一行数据，其行键为95001:
`put 'student','95001','course:math','80'   #Shell 命令`

>命令执行截图如下，即为95001行下的course列族的math列添加了一个数据。

##### 删除数据
>在HBase中用delete以及deleteall命令进行删除数据操作，它们的区别是：1. delete用于删除一个数据，是put的反向操作；2. deleteall操作用于删除一行数据。

>delete命令:
`elete 'student','95001','Ssex'   #shell 命令`

>命令执行截图如下， 即删除了student表中95001行下的Ssex列的所有数据;

>deleteall命令
`deleteall 'student','95001'   #Shell 命令`

##### 查看数据
>HBase中有两个用于查看数据的命令：1. get命令，用于查看表的某一行数据；2. scan命令用于查看某个表的全部数据;

>get命令:
`get 'student','95001'  #Shell 命令`

>命令执行截图如下， 返回的是‘student’表‘95001’行的数据:
```
hbase(main):024:0> get ' student'，'95001'
COL UMN           		CELL       
Sage :				times tamp= 1442912525676, value=20
Sdept:				t imestamp= 1442912586483, value=CS
Sname :			times tamp= 1442912495442,value=LiYing
Ssex :				times tamp= 1442912510852, value=male
cour se :math			t imes tamp= 1442912802499，va Lue=80
5 row(s) in 0.0080 seconds
```

>scan命令:
`scan 'student'   #Shell 命令`

>命令执行后， 返回的是‘student’表的全部数据：
```
hbase(main):025:0> scan ' student
ROW			COL UMN+CELL
95001			colurn=Sage:，timestamp=1442912525676, value=20
95001			column=Sdept:，times tamp= 1442912586483, value=CS
95001			column=Sname:，times tamp= 1442912495442
va Lue=LiYing
95001			column=Ssex:，timestamp=1442912510852
va lue=male
95001			column=course:math, times tamp= 1442912802499, value=80
1 row(s) in 0.0120 seconds
```

##### 删除表
>删除表有两步，第一步先让该表不可用，第二步删除表:
`disable 'student'  drop 'student'   #Shell 命令`

>命令执行结果如下：
```
hbase(main):007:0> list
TABL E
Scor e
s tudent
2 row(s) in 0.0180 seconds
=>	["Score", ”student" ]
hbase(main):008:0> disable ' student'
0 row(s) in 2.3450 seconds

hbase(main):009:0> drop ' student '
0 row(s) in 1.2760 seconds

hbase(main):010:0> list
TABL E
Scor e
1 row(s) in 0.0350 seconds

=>	["Score"]
hbase(main) :011:0>
```

#### 查询表历史数据
>在创建表的时候，指定保存的版本数（假设指定为5）:
`  create 'teacher',{NAME=>'username',VERSIONS=>5}   #Shell 命令`
>插入数据然后更新数据，使其产生历史版本数据，注意：这里插入数据和更新数据都是用put命令:
```
put 'teacher','91001','username','Mary'
put 'teacher','91001','username','Mary1'
put 'teacher','91001','username','Mary2'
put 'teacher','91001','username','Mary3'
put 'teacher','91001','username','Mary4'  
put 'teacher','91001','username','Mary5'   #Shell 命令
````

>查询时，指定查询的历史版本数。默认会查询出最新的数据。（有效取值为1到5）:
`  get 'teacher','91001',{COLUMN=>'username',VERSIONS=>5}   #Shell 命令`

>查询结果结果如下：
```
hbase(main) :020:0>
get ' teacher' ,' 91001'，{COLUMN=> ' username '，VERSIONS=>5}
COL UMN			CEL L
user name :			time s tamp= 1469451374420, value=Mary5
user name :			timestamp=1469451369561, value=Mary4
user name :			timestamp=1469451 366448，value=Mary3
user name :			timestamp=1469451363530, value=Mary2
user name :			timestamp= 1469451351102, value=Mary1
5 row(s) in 0.0290 seconds 

hbase(main) :021:0>
get ' teacher' ,' 91001'，{COLUMN=> ' username '，VERSIONS=>3}
COL UMN			CELL .
user name :			timestamp=1469451374420, va Lue=Mary5
user name :			timestamp=1469451369561, value=Mary4
user name :			timestamp=1469451366448, value=Mary3
3 row(s) in 0.0310 seconds
```

#### 退出HBase数据库表操作
>退出数据库操作，输入exit命令即可退出;
>退出HBase数据库是退出对数据库表的操作，而不是停止启动HBase数据库后台运行:
`exit     #Shell 命令`

### Java API编程实例
#### 安装Eclipse
>启动hadoop，启动hbase:
`cd /usr/local/hadoop./sbin/start-dfs.shcd /usr/local/hbase./bin/start-hbase.sh    #Shell 命令`

>新建Java Project——>新建Class

>在工程中导入外部jar包：导入hbase安装目录中的lib文件中的所有jar包,新版的Hbase 1.1.2的java api已经发生变化，旧版的部分api已经停止使用;

>一个编程实例,
>每次执行完，都可以回到shell界面查看是否执行成功，如：执行完插入数据后，在shell界面中执行scan 'Score';


