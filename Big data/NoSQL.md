# NoSQL实验

## 上机步骤：
>键值数据库Redis的安装与使用；

>文档数据库MongoDB的安装与使用;

## 实验1：键值数据库Redis安装与运行
### Redis简介
>Redis是一个key-value存储系统，即键值对非关系型数据库，和Memcached类似，目前正在被越来越多的互联网公司采用;

>Redis 是一个高性能的key-value数据库, redis的出现，很大程度补偿了memcached这类keyvalue存储的不足，提供了Python，Ruby，Erlang，PHP客户端，使用很方便;

>Redis支持存储的value类型包括string(字符串)、list(链表)、set(集合)和zset(有序集 合)。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的;

>Redis支持各种不同方式的排序,与memcached一样，为了保证效率，数据都是缓存在内存中,区别的是Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步;

### Redis安装
>从网站https://github.com/dmajkic/redis/downloads下载Redis最新版本2.4.5，然后解压至计算机某个磁盘中;
>接着运行两个Doc窗口，一个用户Redis服务器，一个用于Redis客户端;
>两个Doc窗口都进入Redis数据库根目录，服务器窗口在根目录下输入redis-server redis.conf即可运行Redis服务器;

>启动Redis服务器, 客户端在根目录下输入redis-cli，即可运行Redis客户端;

>启动Redis客户端, 服务器端显示接受一个客户端，显示为IP地址：端口;

>Redis显示接受一个客户端, Redis运行成功，即可操作Redis存取数据;

### Redis实例数据库插入
>Redis的增删改查,Redis支持5中数据类型，不同数据类型，增删改查可能不同;

### Redis插入数据
>Redis插入一条数据，只需要先设计好键值，然后用set命令存入即可;
>例如在课程表插入新的课程算法，4学分，可输入set Course:8:Cname 算法和set Course:8:Ccredit 4;

### Redis修改数据
>Redis并没有修改数据的命令，所以如果在Redis中要修改一条数据，只能在使用set命令时，使用同样的键值，然后用新的value值来覆盖旧的数据。例如修改新添加的课程，名字改为编译原理;
>先调用get命令，输出原先的值，然后set新的值，最后再get得到新值，所以修改成功;

### Redis删除数据
>Redis有专门删除数据的命令del，用法为del key值,所以如果要删除之前新增的课程编译原理，只需输入命令del Course:8:Cname，同时还应该把本课程的学分删除del Course:8:Ccredit;
>当输入del Course:8:Cname时，返回1，说明成功操作一条数据,当再次输入get命令时，输出为空，说明删除成功;

### Redis查询数据
>Redis最简单的查询方式为用get命令，如果要进行关系数据库表格连接查询，则需要进行多步查询，这时要先将(姓名，学号)和(课程名，课程号)键值对存储数据库，用于后续查询;
>查询李勇的数学分数，在关系数据库中需要连接3个表，Redis的做法为:
```
①输入命令：get 李勇 获得李勇的学号；
②输入命令：get 数学 获得数学的课程号；
③获得李勇学号和数学的课程号之后，在输入命令get SC:学号:课程号:Grade 即可得到李勇的分数;
```

## 实验2：文档数据库Ubuntu下MongoDB安装与使用教程

### MongoDB介绍:
>MongoDB 是一个基于分布式文件存储的数据库，介于关系数据库和非关系数据库之间，是非关系数据库当中功能最丰富，最像关系数据库的;

>它支持的数据结构非常松散，类似json的bson格式，可以存储比较复杂的数据类型；

>Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引；

### 安装MongoDB
>ongoDB安装很简单，直接用apt-get命令进行安装,如果网速太差，并且已经下载MongoDB的安装包时，可以离线方式安装,推荐使用apt-get命令进行在线安装，可以避免很多莫名其妙的问题;

#### 安装3.2.8版本
>首先打开终端，导入公共key到包管理器，输入以下命令：
`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927  #Shell 命令`

>创建MongoDB的文件列表
>更新包管理器，安装MongoDB,输入以下命令：
`sudo apt-get updatesudo apt-get install -y mongodb-org   #Shell 命令`

>注意：如果执行“sudo apt-get update”命令后出现如下错误：
···
update completed, but some metadata was ignored due to errors.
E: 无法获得锁 /var/lib/dpkg/lock – open (11: 资源暂时不可用)
E: 无法锁定管理目录(/var/lib/dpkg/)，是否有其他进程正占用它？
···

>请按照如下方法解决错误，输入以下三条命令：
```
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock
sudo apt-get update   #Shell 命令
```
>安装完成MongoDB以后，在终端输入以下命令查看MongoDB版本：
`mongo -version    #Shell 命令`

>输出版本信息，说明安装成功;

>启动和关闭mongodb命令如下：
`sudo service mongodb startsudo service mongodb stop    #Shell 命令`
>内容如下：
```
dblab@ubuntu:~$service mongodb start
dblab@ubuntu:~$ service mongodb stop
```

默认设置MongoDB是随Ubuntu启动自动启动的,输入以下命令查看是否启动成功：
>`pgrep mongo -l   #注意：-l是英文字母l，不是阿拉伯数字1;  Shell 命令`

#### 出现安装错误的解决方案：
>输入“sudo service mongodb start”启动mongodb的时候，如果报这个错误：Failed to start mongod.service: Unit not found
请按照如下步骤解决该错误：

1. 使用vim编辑器创建配置文件:
`sudo vim /etc/systemd/system/mongodb.service   #Shell 命令`

2. 在该配置文件中添加如下内容：
```
[Unit]
Description=High-performance, schema-free document-oriented database
After=network.target

[Service]
User=mongodb
ExecStart=/usr/bin/mongod --quiet --config /etc/mongod.conf

[Install]
WantedBy=multi-user.target
保存退出vim编辑器。
```
3. 输入如下命令启动mongodb：
`sudo systemctl start mongodbsudo systemctl status mongodb   #Shell 命令`

>每次启动和关闭MongoDB，仍然使用如下命令：
>sudo service mongodb startsudo service mongodb stop  #Shell 命令`

### 使用MongoDB
#### shell命令模式
>输入如下命令进入MongoDB的shell命令模式：
`mongo   #Shell 命令`

#### 常用操作命令：数据库相关
```
show dbs:显示数据库列表
show collections：显示当前数据库中的集合（类似关系数据库中的表table）
show users：显示所有用户
use yourDB：切换当前数据库至yourDB
db.help() ：显示数据库操作命令
db.yourCollection.help() ：显示集合操作命令，yourCollection是集合名
```
>MongoDB没有创建数据库的命令，如果你想创建一个“School”的数据库，先运行use School命令，之后做一些操作（如：创建聚集集合db.createCollection('teacher')）,这样就可以创建一个名叫“School”的数据库;内容如下：
```
> show dbs
admin		0.078GB 
Local		0.078GB
test		0.078GB

> use School
switched to db school

> db. createCollection( ' teacher')
{"ok": 1}

> show dbs
school		0.078GB
admin		0.078GB
Loca L		0.078GB
test		0.078GB
```

>以一个School数据库为例，在School数据库中创建两个集合teacher和student，并对student集合中的数据进行增删改查基本操作（集合Collection相当于关系型数据库中的表table);

1. 切换到School数据库:
`use School     #切换到School数据库。MongoDB 无需预创建School数据库，在使用时会自动创建；Shell 命令`

2. 创建Collection:

`db.createCollection('teacher') #创建一个聚集集合。MongoDB 在插入数据的时候，会自动创建对应的集合，无需预定义集合;  Shell 命令`

>>内容如下：
```
>show collections
sys tem . indexes

>db.createcotLectton( ' teacher')
{”ok"  1}
show collections
s ys tem. indexes
teacher
```

3. 插入数据:

>与数据库创建类似，插入数据时也会自动创建集合;
>插入数据有两种方式：insert和save:
```
db.student.insert({_id:1, sname: 'zhangsan', sage: 20}) #_id可选
db.student.save({_id:1, sname: 'zhangsan', sage: 22}) #_id可选   #Shell 命令
```
>>这两种方式，其插入的数据中_id字段均可不写，会自动生成一个唯一的_id来标识本条数据;
>>insert和save不同之处在于：在手动插入_id字段时，如果_id已经存在，insert不做操作，save做更新操作；如果不加_id字段，两者作用相同都是插入数据;
>>添加的数据其结构是松散的，只要是bson格式均可，列属性均不固定，根据添加的数据为准,先定义数据再插入，就可以一次性插入多条数据;

4. 查找数据:
>`db.youCollection.find(criteria, filterDisplay) `
>criteria ：查询条件，可选;
>filterDisplay：筛选显示部分数据，如显示指定列数据，当选择时，第一个参数不可省略，若查询条件为空，可用{}做占位符，如第三句;
```
db.student.find()  #查询所有记录, 相当于：select * from student
db.student.find({sname: 'lisi'})  #查询sname='lisi'的记录, 相当于： select * from student where sname='lisi'
db.student.find({},{sname:1, sage:1}) #查询指定列sname、sage数据，相当于：select sname,sage from student
sname:1表示返回sname列，默认_id字段也是返回的，可以添加_id:0（意为不返回_id）写成{sname: 1, sage: 1,_id:0}，就不会返回默认的_id字段了；
db.student.find({sname: 'zhangsan', sage: 22}) #and 与条件查询，相当于：select * from student where sname = 'zhangsan' and sage = 22
db.student.find({$or: [{sage: 22}, {sage: 25}]}) #or 条件查询。相当于：select * from student where sage = 22 or sage = 25   #Shell 命令
```
>查询操作类似，这里只给出db.student.find({sname: 'lisi'})查询的内容，如下：
```
>db. student. find({snane: 'lisi'})
`{"_id" : objectId(”577b3a1dacae8736eab65abf" )，” snane" "lisi"，”sage : 20 }`
```
>db.youCollection.find(criteria, filterDisplay).pretty()表示格式化输出，内容如下：
```
>db. student. find().pretty()
{
	id" : objectId(" 5784945bd8fdaa05c847ccbb" )，
	sname" :”zhangsan"，
	'sage" : 23
}
{
	id" : objectId(”5784953da7140456f7e7b2c7"),
	sname" :”xueqian"，
	”sage" : 22
}
```

5. 修改数据：
>`db.youCollection.update(criteria, objNew, upsert, multi )`
>>criteria: update的查询条件，类似sql update查询内where后面的;
>>objNew : update的对象和一些更新的操作符（如$set）等，也可以理解为sql update查询内set后面的;
>>upsert : 如果不存在update的记录，是否插入objNew，true为插入，默认是false，不插入;
>>multi: mongodb默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。默认false，只修改匹配到的第一条数据。

>criteria和objNew是必选参数，upsert和multi可选参数, 举例如下：
`db.student.update({sname: 'lisi'}, {$set: {sage: 30}}, false, true) #相当于：update student set sage =30 where sname = 'lisi';  #Shell 命令`

>操作内容如下：
```
> db. student . update( {sname: ' lisi' },{$set:{sage:30}}, false, true )
WriteResult({ " nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db. student. find( {sname: 'lisi'})
{ ”_id" : objectid”577b3a1dacae8736eab65abf" )，” snane " "lisi", "sage" : 30 }
```
6. 删除数据:
`db.student.remove({sname: 'chenliu'}) #相当于：delete from student where sname='chenliu'   #Shell 命令`
>操作内容如下：
```
> db. student. remove( {sname: ' chenliu'})
Wr iteResult({ ”nRemoved" : 1 } )

>db. student. find( )
{ "id":1, ”sname" :" zhangsan" ，”sage":22}
{ "_id" : ob jectId(”577b3a1dacae8736eab65abf"),”sname" : "lisi"，"sage" : 30 }
{ "id" : objectId(" 577b3a1dacae8736eab65acO"),” sname" : ”wangwu"，"sage" : 20 }
```
7. 删除集合:
>`db.student.drop()  #Shell 命令`
>操作内容如下：
```
>show collections
student
system.indexex

>db.student.drop( )
true

>show collections
system.indexes
```

8. 退出shell命令模式:
>输入exit或者Ctrl+C退出shell命令模式
>注意：MongoDB相较安全性更偏向易用性，默认是没有开启用户权限的;

