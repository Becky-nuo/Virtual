# 构建本地OpenStack源

> 将centos7、train、train-extras复制到任意文件夹下，比如/opt

> 安装基础软件   
`yum -y install vim yum-utils createrepo yum-plugin-priorities` 

> 重构yum 元数据数据库
```
	createrepo -p -d -o /var/www/html/centos7 /var/www/html/centos7
	createrepo -p -d -o /var/www/html/train /var/www/html/train
	createrepo -p -d -o /var/www/html/train-extras /var/www/html/train-extras
```

> 备份系统软件库，编写新yum软件库文件
```
cd /etc/yum.repos.d
mkdir backup
mv Cen* backup
vi CentOS-Media.repo

	[CentOS-base]
	name=CentOS base 
	baseurl=http://192.168.43.88/centos7
	enabled=1
	gpgcheck=0
	[train]
	name=train
	baseurl=http://192.168.43.88/train
	enabled=1
	gpgcheck=0
	[train-extras]
	name=train-extras
	baseurl=http://192.168.43.88/train-extras
	enabled=1
	gpgcheck=0
	[Virt]
	name=CentOS-$releasever - Base
	#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
	baseurl=http://mirrors.sohu.com/centos/7.5.1804/virt/x86_64/kvm-common/
	#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
	gpgcheck=0
	enable=1
```

> 清除历史缓存，新建yum缓存
```
	yum clean all
	yum makecache
```


>下载阿里train完整包
```
wget -m -c -np -nH --cut-dirs=4 -e robots=off https://mirrors.aliyun.com/centos/7/cloud/x86_64/openstack-train/ -P /root/train

wget -m -c -np -nH --cut-dirs=4 -e robots=off https://mirrors.aliyun.com/centos/7/extras/x86_64/ -P /root/train-extras

wget -m -c -np -nH --cut-dirs=4 -e robots=off https://dl.fedoraproject.org/pub/epel/6/x86_64/ -P /root/epel
```
















