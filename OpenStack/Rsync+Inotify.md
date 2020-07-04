#Rsync+Inotify实现异地实时同步

##背景知识
###项目背景
>已搭建由3台服务器构成的Web集群，Web集群系统中 的静态数据集中存放在一台NFS服务器上，为了防止这台NFS服务器单点故障造成整个Web 集群不可用，现要求配置一台备份服务器，实时同步NFS服务器中的Web数据； 

###项目要求
>1. 备份服务器能与NFS服务器中的Web_data目录实现数据同步 
>2. 同步数据时，为了避免独占带宽造成服务器不可用，要求限制同步数据仅能使用5M带 宽 
>3. 数据发生变化时，数据同步需要自动进行

###什么是Rsync
>Rsync = Remote sync（远程同步） 
>开源备份工具 
>实现不同主机之间进行同步 
>rsync官方地址：rsync.samba.org 
>rsync监听端口：873 
>rsync运行模式：C/S

![](https://github.com/Becky-nuo/git-test/blob/master/images/Rsync/001.png)

###怎么使用Rsync 
>工作模式 
>>本地方式——命令模式 
>>>类似于本地拷贝（cp） 
```
yum install rsync 
rsync /etc/passwd /tmp 
```

>远程方式——命令模式 
>>类似于网络拷贝（scp） 
`rsync /etc/passwd root@192.168.99.101:/tmp` 

>守护进程——服务模式

###Rsync与scp的区别 
>同步类型 
>>全备
>>>第一次备份时 

>>增量
>>>后续备份时，只传输增量 
>>>scp
```
scp -r /etc root@192.168.99.102:/tmp 
touch /etc/11111111111 
scp -r /etc root@192.168.99.102:/tmp 
```
>>>rsync 
```
rsync -avz /etc root@192.168.99.102:/tmp 
touch /etc/2222222222 
rsync -avz /etc root@192.168.99.102:/tmp
```
###远程方式的问题 
>远程方式存在的缺陷 
>>需要使用ssh协议 
>>需要使用高权限系统用户（安全问题） 

>>使用普通用户（权限可能不足，需要定制）
``` 
useradd user01;passwd user01 
rsync -avz /etc user01@192.168.99.102:/tmp //查看输出的错误信息
```
###守护进程工作模式 
>问题1： 
>>服务应该配置在哪台服务器？ 
>>>数据源服务器 
>>>目标服务器 

>同步方式 
>>推（push） ：上传 
>>拉（pull） ：下载

##情镜一
###配置rsync守护进程工作模式 

>修改配置文件vim 
```
vim /etc/rsyncd.conf 
uid = rsync 
gid = rsync 
use chroot = yes 
max connections = 100 
pid file = /var/run/rsyncd.pid 
read only = no //设定是否允许客户端上传文件 
fake super = yes //不需要daemon以root运行，就可以存储文件的完整属性 
transfer logging = yes 
timeout = 900 
dont compress = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb *.bz2 

[data]
	path = /backup 
	comment = web_data from NFS server 
	auth users = rsync_backup 
	secrets file = /etc/rsyncd.secrets
```

>创建用户（用于运行rsync服务的用户） 
>>useradd -M -s /sbin/nologin rsync //如不使用缺省的nobody用户，可创建 
>>rsync用户，同时设置不允许登录，不创建家目录 

>创建备份目录
```
mkdir /backup
chown rsync.rsync /backup 
```

>创建虚拟用户密码文件（用于客户端连接时使用的虚拟用户）
```
echo "rsync_backup:123" > /etc/rsyncd.secrets 
chmod 600 /etc/rsyncd.secrets //如不修改，others有读取权限，系统 将拒绝身份认证
```
###配置rsyncd相关服务
>系统配置——服务
``` 
systemctl enable rsyncd 
systemctl restart rsyncd 
```
>防火墙 
```
firewall-cmd --add-service=rsyncd
firewall-cmd --add-service=rsyncd --per 
```
>SELinux 暂时关闭

###客户端测试 
>创建测试用备份数据
``` 
mkdir /web_data 
echo “<h1>Test Data</h1>” > /web_data/index.html 
```

>手动发起同步 
`rsync -avz /web_data rsync_backup@192.168.99.102::data` 

>测试同步效果
>>文件增量
``` 
mkdir /web_data/images 
rsync -avz /web_data rsync_backup@192.168.99.102::data 
```
>>已有文件内容变化
```
vi web_data/index.html 
rsync -avz /web_data rsync_backup@192.168.99.102::data
``` 

###客户端测试——避免交互 
>利用密码文件避免交互 
>>创建密码文件 
`echo 123 > /etc/rsync-102.pass `

>>限制密码文件访问权限 
`chmod 600 /etc/rsync-102.pass `

>>免交互同步测试 
```
rsync -avz /web_data rsync_backup@192.168.99.102::data --password-file=/etc/rsync- 102.pass
```
###客户端测试——带宽限速 
>生成测试文件（大小1G） 
`dd if=/dev/zero of=/web_data/test.dat bs=100M count=10 //造一个1G的测试文件` 

>测试不限速状态 
`rsync -avzP /web_data/test.dat rsync_backup@192.168.99.102::backup //-P：显示传输速度` 

>设置限速同步
```
rsync -avzP --bwlimit=5 /web_data/test.datrsync_backup@192.168.99.102::backup //--bwlimit设 置带宽限制
```

##实时同步 
>实时同步的方式 
>>Rsync+Inotify 
>>效率低 
>>需要编写脚本，管理复杂

>>Rsync+sersync 
>>>支持配置文件管理 
>>>不需要写脚本 
>>>可以对失败文件定时重传 
>>>默认多线程rsync同步

###安装sersync 
>问题1： 
>>服务应该配置在哪台服务器？ 
>>数据源服务器 
>>目标服务器 

>>实时同步的关键 
>>>监控数据源变化，发生变化立即触发rsync同步

###准备sersync环境 
>服务应该配置在哪台服务器？（数据源端） 
`NFS Server->Backup Server `

>安装基础环境
``` 
	rsync 
	Inotify 
		ls /proc/sys/fs/inotify/ //查看是否支持Inotify 
			max_queued_events 
			max_user_instances 
			max_user_watches
```
###安装sersync 

>解压：`tar xzvf sersync2.5.4_64bit_binary_stable_final.tar.gz`  
>改名：`mv GNU-Linux-x86 /usr/local/sersync`

###配置sersync 
>修改配置文件
``` 
vim /usr/local/sersync/confxml.xml 
5 <fileSystem xfs="true"/> 
23 <sersync> 
24 <localpath watch="/web_data"> 
25 <remote ip="192.168.99.102" name="data/web_data"/> 
28 </localpath> 
29 <rsync> 
30 <commonParams params="-avz"/> 
31 <auth start="true" users="rsync_backup" passwordfile="/etc/rsync-102.pass"/> 
33 <timeout start="true" time="100"/><!-- timeout=100 --> 
35 </rsync> 
44 </sersync> 
```
>启动服务 
```
/usr/local/sersync/sersync2 -h //查询帮助 
/usr/local/sersync/sersync2 -dro /usr/local/sersync/confxml.xml 
```