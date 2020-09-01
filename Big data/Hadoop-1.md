# Hadoop安装_单机/伪分布式配置
>本实验适合 Hadoop 2，包括 Hadoop 2.6.0, Hadoop 2.7.1 等版本；

## 环境
>使用 Ubuntu 14.04 64位 作为系统环境（Ubuntu 12.04，Ubuntu16.04 也行，32位、64位均可）;
本教程基于原生 Hadoop 2，在 Hadoop 2.6.0 (stable) 版验证通过，可适合任何 Hadoop 2.x.y 版本，如 Hadoop 2.7.1、2.6.3、2.4.1等。

>请确保系统处于联网状态下，建议您使用双系统安装ubuntu,然后再安装；

## 创建hadoop用户

>安装 Ubuntu 的时候不是用的 “hadoop” 用户，那么需要增加一个名为 hadoop 的用户;

>按 ctrl+alt+t 打开终端窗口，输入如下命令创建新用户 :
`sudo useradd -m hadoop -s /bin/bash   #Shell 命令
这条命令创建了可以登陆的 hadoop 用户，并使用 /bin/bash 作为 shell；

### sudo命令
>sudo是ubuntu中一种权限管理机制，管理员可以授权给一些普通用户去执行一些需要root权限执行的操作。当使用sudo命令时，就需要输入您当前用户的密码;

### 密码
>在Linux的终端中输入密码，终端是不会显示任何你当前输入的密码，也不会提示你已经输入了多少字符密码。而在windows系统中,输入密码一般都会以“*”表示你输入的密码字符;

### 输入法中英文切换
>ubuntu中终端输入的命令一般都是使用英文输入。linux中英文的切换方式是使用键盘“shift”键来切换，也可以点击顶部菜单的输入法按钮进行切换。ubuntu自带的;Sunpinyin中文输入法已经足够读者使用；

### Ubuntu终端复制粘贴快捷键
>在Ubuntu终端窗口中，复制粘贴的快捷键需要加上 shift，即粘贴是
` ctrl+shift+v `

>接着使如下命令设置密码，可简单设置为 hadoop，按提示输入两次密码：
`sudo passwd Hadoop`


>可为 hadoop 用户增加管理员权限，方便部署，避免一些对新手来说比较棘手的权限问题：
`sudo adduser hadoop sudo`

>最后注销当前用户（点击屏幕右上角的齿轮，选择注销），返回登陆界面。在登陆界面中选择刚创建的 hadoop 用户进行登陆;

## 更新apt
>用 hadoop 用户登录后，我们先更新一下 apt，后续我们使用 apt 安装软件，如果没更新可能有一些软件安装不了。按 ctrl+alt+t 打开终端窗口，执行如下命令：
`sudo apt-get update`

>若出现 “Hash校验和不符” 的提示，可通过更改软件源来解决,出现“Hash校验和不符”的提示，也不会影响Hadoop的安装;

### 如何更改软件源
>需要更改一些配置文件，用 vim（vi增强版，基本用法相同），安装一下（如果不会用 vi/vim ，可以将后面用到 vim 的地方改为 gedit，这样可以使用文本编辑器进行修改，并且每次文件更改完成后请关闭整个 gedit 程序，否则会占用终端）：
`sudo apt-get install vim`

>安装软件时若需要确认，在提示处输入 y 即可;

## 安装SSH、配置SSH无密码登陆
>集群、单节点模式都需要用到 SSH 登陆（类似于远程登陆，你可以登录某台 Linux 主机，并且在上面运行命令），Ubuntu 默认已安装了 SSH client，此外还需要安装 SSH server：
`sudo apt-get install openssh-server  #Shell命令`

>安装后，可以使用如下命令登陆本机：
`ssh localhost  #Shell 命令`

>此时会有如下提示(SSH首次登陆提示)，输入 yes ,然后按提示输入密码 hadoop，就登陆到本机了；

>SSH首次登陆提示登陆是需要输入密码的，需要配置成SSH无密码登陆比较方便；

### SSH无密码登录
>首先退出刚才的 ssh，就回到了我们原先的终端窗口，然后利用 ssh-keygen 生成密钥，并将密钥加入到授权中：
`exit                           # 退出刚才的 ssh localhostcd ~/.ssh/                     

> 若没有该目录，请先执行一次：
`ssh localhostssh-keygen -t rsa              # 会有提示`

>按回车就可以：
`cat ./id_rsa.pub >> ./authorized_keys     # 加入授权`

### ~的含义
>在 Linux 系统中，~ 代表的是用户的主文件夹，即 “/home/用户名” 这个目录，如你的用户名为 hadoop，则 ~ 就代表 “/home/hadoop/”； 此外，命令中的 # 后面的文字是注释，只需要输入前面命令即可，再用 ssh localhost 命令，无需输入密码就可以直接登陆了;


## 安装Java环境

### 安装JDK方式（手动安装，采用这个方式）
>需要按照下面步骤来自己手动安装JDK1.8：
把JDK1.8的安装包jdk-8u162-linux-x64.tar.gz放在了百度云盘，可以点击百度云盘下载JDK1.8安装包,把压缩格式的文件jdk-8u162-linux-x64.tar.gz下载到本地电脑，假设保存在“/home/linziyu/Downloads/”目录下；
在Linux命令行界面中，执行如下Shell命令（注意：当前登录用户名是hadoop）：
```
cd /usr/libsudo mkdir jvm #创建/usr/lib/jvm目录用来存放JDK文件
cd ~ #进入hadoop用户的主目录
cd Downloads  #注意区分大小写字母
通过FTP软件把JDK安装包jdk-8u162-linux-x64.tar.gz上传到该目录下sudo 
tar -zxvf ./jdk-8u162-linux-x64.tar.gz -C /usr/lib/jvm  #把JDK文件解压到/usr/lib/jvm目录下    #Shell 命令
```

>JDK文件解压缩以后，可以执行如下命令到/usr/lib/jvm目录查看一下：
`cd /usr/lib/jvmls  #Shell 命令`

>继续执行如下命令，设置环境变量：
`cd ~vim ~/.bashrc    #Shell 命令`

>上面命令使用vim编辑器（查看vim编辑器使用方法）打开了hadoop这个用户的环境变量配置文件，请在这个文件的开头位置，添加如下几行内容：
```
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_162
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

>保存.bashrc文件并退出vim编辑器,继续执行如下命令让.bashrc文件的配置立即生效：
`source ~/.bashrc  #Shell 命令`

>查看是否安装成功：
`java -version  #Shell 命令`

>如果能够在屏幕上返回如下信息，则说明安装成功：

```
hadoop@ubuntu:~$ java -version
java version "1.8.0_162"
Java(TM) SE Runtime Environment (build 1.8.0_162-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.162-b12, mixed mode)

```
### 安装JDK方式：
`sudo apt-get install openjdk-7-jre openjdk-7-jdk  #Shell 命令`

>安装好 OpenJDK 后，需要找到相应的安装路径，这个路径是用于配置 JAVA_HOME 环境变量的。执行如下命令：

`dpkg -L openjdk-7-jdk | grep '/bin/javac'  #Shell 命令

>该命令会输出一个路径，除去路径末尾的 “/bin/javac”，路径为 /usr/lib/jvm/java-7-openjdk-amd64;

>接着需要配置一下 JAVA_HOME 环境变量:

`vim ~/.bashrc  #Shell 命令`

>在文件最前面添加如下单独一行（注意 = 号前后不能有空格），将“JDK安装路径”改为上述命令得到的路径，并保存：
`export JAVA_HOME=JDK安装路径  #Shell命令`

>接着还需要让该环境变量生效，执行如下代码：
`source ~/.bashrc    # 使变量设置生效`

>设置好后我们来检验一下是否设置正确：
```
echo $JAVA_HOME     # 检验变量值java -version
$JAVA_HOME/bin/java -version  # 与直接执行 java -version 一样

```

>如果设置正确的话，$JAVA_HOME/bin/java -version 会输出 java 的版本信息，且和 java -version 的输出结果一样;

>成功配置JAVA_HOME变量,Hadoop 所需的 Java 运行环境就安装好了;

###3.安装JDK方式
>第一种安装方式出现安装失败的情况，可以采用另外一种安装方式，命令如下：
`sudo apt-get install default-jre default-jdk #Shell 命令`

>安装过程需要访问网络下载相关文件，请保持联网状态,安装结束以后，需要配置JAVA_HOME环境变量，请在Linux终端中输入下面命令打开当前登录用户的环境变量配置文件.bashrc：
``vim ~/.bashrc  #Shell 命令`

>在文件最前面添加如下单独一行（注意，等号“=”前后不能有空格），然后保存退出：
`export JAVA_HOME=/usr/lib/jvm/default-java`

>让环境变量立即生效，执行如下代码：
`source ~/.bashrc    # 使变量设置生效 `

>执行上述命令后，可以检验一下是否设置正确：

>`echo $JAVA_HOME     # 检验变量值java -version$JAVA_HOME/bin/java -version  # 与直接执行java -version一样`;

>就成功安装了Java环境。下面就可以进入Hadoop的安装;


## 安装 Hadoop 2
>Hadoop 2 可以通过 http://mirror.bit.edu.cn/apache/hadoop/common/ 或者 http://mirrors.cnnic.cn/apache/hadoop/common/ 下载，一般选择下载最新的稳定版本，即下载 “stable” 下的 hadoop-2.x.y.tar.gz 这个格式的文件，这是编译好的，另一个包含 src 的则是 Hadoop 源代码，需要进行编译才可使用;

>下载完 Hadoop 文件后一般就可以直接使用。但是如果网络不好，可能会导致下载的文件缺失，可以使用 md5 等检测工具可以校验文件是否完整;

>选择将 Hadoop 安装至 /usr/local/ 中：
```
sudo tar -zxf ~/   #下载
/hadoop-2.6.0.tar.gz -C /usr/local    # 解压到/usr/local中
cd /usr/local/sudo mv ./hadoop-2.6.0/ ./hadoop            # 将文件夹名改为hadoopsudo 
chown -R hadoop ./hadoop       # 修改文件权限
```

>Hadoop 解压后即可使用。输入如下命令来检查 Hadoop 是否可用，成功则会显示 Hadoop 版本信息：
`cd /usr/local/hadoop./bin/hadoop version   #Shell 命令`

### 相对路径与绝对路径
>注意命令中的相对路径与绝对路径，后续出现的 ./bin/...，./etc/... 等包含 ./ 的路径，均为相对路径，以 /usr/local/hadoop 为当前目录;

>例如在 /usr/local/hadoop 目录中执行 ./bin/hadoop version 等同于执行 /usr/local/hadoop/bin/hadoop version;

>将相对路径改成绝对路径来执行，但是在主文件夹 ~ 中执行 ./bin/hadoop version，执行的会是 /home/hadoop/bin/hadoop version;

## Hadoop单机配置(非分布式)
>Hadoop 默认模式为非分布式模式（本地模式），无需进行其他配置即可运行。非分布式即单 Java 进程，方便进行调试;

## Hadoop伪分布式配置
>Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为 DataNode，同时，读取的是 HDFS 中的文件;

>Hadoop 的配置文件位于 /usr/local/hadoop/etc/hadoop/ 中，伪分布式需要修改2个配置文件 core-site.xml 和 hdfs-site.xml ,Hadoop的配置文件是 xml 格式，每个配置以声明 property 的 name 和 value 的方式来实现;

>修改配置文件 core-site.xml (通过 gedit 编辑会比较方便: gedit ./etc/hadoop/core-site.xml)，将当中的 <configuration></configuration> #XML

```
<configuration>    <property>        <name>hadoop.tmp.dir</name>        <value>file:/usr/local/hadoop/tmp</value>        <description>Abase for other temporary directories.</description>    </property>    <property>        <name>fs.defaultFS</name>        <value>hdfs://localhost:9000</value>    </property></configuration>  #XML
···

>修改配置文件 hdfs-site.xml：
···
<configuration>    <property>        <name>dfs.replication</name>        <value>1</value>    </property>    <property>        <name>dfs.namenode.name.dir</name>        <value>file:/usr/local/hadoop/tmp/dfs/name</value>    </property>    <property>        <name>dfs.datanode.data.dir</name>        <value>file:/usr/local/hadoop/tmp/dfs/data</value>    </property></configuration> #XML
```
### Hadoop配置文件说明

>Hadoop 的运行方式是由配置文件决定的（运行 Hadoop 时会读取配置文件），需要从伪分布式模式切换回非分布式模式，需要删除 core-site.xml 中的配置项；

>伪分布式需要配置 fs.defaultFS 和 dfs.replication 就可以运行，不过没有配置 hadoop.tmp.dir 参数，则默认使用的临时目录为/tmp/hadoo-hadoop，而这个目录在重启时有可能被系统清理掉，导致必须重新执行 format 才行，所以需设置，同时也要指定 dfs.namenode.name.dir 和 dfs.datanode.data.dir，否则在接下来的步骤中可能会出错；

>配置完成后，执行 NameNode 的格式化:
`cd /usr/local/hadoop./bin/hdfs namenode -format  #Shell 命令`

>成功的话，会看到 “successfully formatted” 和 “Exitting with status 0” 的提示，若为 “Exitting with status 1” 则是出错；

>接着开启 NameNode 和 DataNode 守护进程:
`cd /usr/local/hadoop./sbin/start-dfs.sh  #start-dfs.sh是个完整的可执行文件，中间没有空格   #Shell 命令`

>若出现如下SSH提示，输入yes即可；

>启动时可能会出现如下 WARN 提示：WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-java classes where applicable WARN 提示可以忽略，并不会影响正常使用;


### 启动 Hadoop 时提示 Could not resolve hostname

>如果启动 Hadoop 时遇到输出非常多“ssh: Could not resolve hostname xxx”的异常情况，这个并不是 ssh 的问题，可通过设置 Hadoop 环境变量来解决；首先按键盘的 ctrl + c 中断启动，然后在 ~/.bashrc 中，增加如下两行内容（设置过程与 JAVA_HOME 变量一样，其中 HADOOP_HOME 为 Hadoop 的安装目录）：

`export HADOOP_HOME=/usr/local/hadoopexport HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native   #Shell`

>保存后，执行 source ~/.bashrc 使变量设置生效，然后再次执行 ./sbin/start-dfs.sh 启动 Hadoop;

>启动完成后，可以通过命令 jps 来判断是否成功启动，若成功启动则会列出如下进程: “NameNode”、”DataNode” 和“SecondaryNameNode”（如果 SecondaryNameNode 没有启动，请运行 sbin/stop-dfs.sh 关闭进程，然后再次尝试启动尝试），如果没有 NameNode 或 DataNode ，那就是配置不成功，检查之前步骤，或通过查看启动日志排查原因;

### Hadoop无法正常启动的解决方法
>查看启动日志来排查原因，注意事项：启动时时会提示形如 “DBLab-XMU: starting namenode, logging to/usr/local/hadoop/logs/hadoop-hadoop-namenode-DBLab-XMU.out”，其中 DBLab-XMU 对应你的机器名，但其实启动日志信息是记录在 /usr/local/hadoop/logs/hadoop-hadoop-namenode-DBLab-XMU.log 中，所以应该查看这个后缀为 .log 的文件；

>每一次的启动日志都是追加在日志文件之后，所以得拉到最后面看，对比下记录的时间就知道了,一般出错的提示在最后面，通常是写着 Fatal、Error、Warning 或者 Java Exception 的地方,若是 DataNode 没有启动，可尝试如下的方法（注意这会删除 HDFS 中原有的所有数据）：

```
 针对 DataNode 没法启动的解决方法:
	cd /usr/local/hadoop./sbin/stop-dfs.sh   # 关闭
	rm -r ./tmp     # 删除 tmp 文件，注意这会删除 HDFS 中原有的所有数据
	./bin/hdfs namenode -format   # 重新格式化 
	NameNode./sbin/start-dfs.sh  # 重启     #Shell 命令
```

>成功启动后，可以访问 Web 界面 http://localhost:50070 查看 NameNode 和 Datanode 信息;

## 运行Hadoop伪分布式实例

>上面的单机模式，grep 例子读取的是本地数据，伪分布式读取的则是 HDFS 上的数据,要使用 HDFS，首先需要在 HDFS 中创建用户目录：
`./bin/hdfs dfs -mkdir -p /user/Hadoop   #Shell 命令`

>接着将 ./etc/hadoop 中的 xml 文件作为输入文件复制到分布式文件系统中，即将 /usr/local/hadoop/etc/hadoop 复制到分布式文件系统中的 /user/hadoop/input 中,使用的是 hadoop 用户，并且已创建相应的用户目录 /user/hadoop ，因此在命令中就可以使用相对路径input，其对应的绝对路径就是./user/hadoop/input:
`./bin/hdfs dfs -mkdir input./bin/hdfs dfs -put ./etc/hadoop/*.xml input   #Shell 命令`

>复制完成后，可以通过如下命令查看文件列表：
`./bin/hdfs dfs -ls input     #Shell 命令`

>伪分布式运行 MapReduce 作业的方式跟单机模式相同，区别在于伪分布式读取的是HDFS中的文件（可以将单机步骤中创建的本地 input 文件夹，输出结果 output 文件夹都删掉来验证这一点）：

`./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'    #Shell 命令`

>查看运行结果的命令（查看的是位于 HDFS 中的输出结果）：
`./bin/hdfs dfs -cat output/*    #Shell 命令`

>将运行结果取回到本地：
```
rm -r ./output    # 先删除本地的 output 文件夹（如果存在）
./bin/hdfs dfs -get output ./output     # 将 HDFS 上的 output 文件夹拷贝到本机cat ./output/*    #Shell 命令
```

>Hadoop 运行程序时，输出目录不能存在，否则会提示错误 “org.apache.hadoop.mapred.FileAlreadyExistsException: Output directory hdfs://localhost:9000/user/hadoop/output already exists” ，因此若要再次执行，需要执行如下命令删除 output 文件夹:

`./bin/hdfs dfs -rm -r output    # 删除 output 文件夹     #Shell 命令`

### 运行程序时，输出目录不能存在

>运行 Hadoop 程序时，为了防止覆盖结果，程序指定的输出目录（如 output）不能存在，否则会提示错误，因此运行前需要先删除输出目录。在实际开发应用程序时，可考虑在程序中加上如下代码，能在每次运行时自动删除输出目录，避免繁琐的命令行操作：
```
Configuration conf = new Configuration();Job job = new Job(conf); /* 删除输出目录 */Path outputPath = new 
Path(args[1]);outputPath.getFileSystem(conf).delete(outputPath, true);  #Java
```

>若要关闭 Hadoop，则运行:
`./sbin/stop-dfs.sh   #Shell 命令`

>注意:下次启动 hadoop 时，无需进行 NameNode 的初始化，只需要运行 ./sbin/start-dfs.sh 就可以;


## 配置PATH环境变量
> PATH 环境变量（可执行 echo $PATH 查看，当中包含了多个目录）,例如在主文件夹 ~ 中执行 ls 这个命令时，实际执行的是 /bin/ls 这个程序，而不是 ~/ls 这个程序,系统是根据 PATH 这个环境变量中包含的目录位置，逐一进行查找，直至在这些目录位置下找到匹配的程序（若没有匹配的则提示该命令不存在）；

>上面的过程中，是先进入到 /usr/local/hadoop 目录中，再执行 sbin/hadoop，实际上等同于运行 /usr/local/hadoop/sbin/Hadoop，可以将 Hadoop 命令的相关目录加入到 PATH 环境变量中，可以直接通过 start-dfs.sh 开启 Hadoop，也可以直接通过 hdfs 访问 HDFS 的内容，方便平时的操作;

>在 ~/.bashrc 中进行设置（vim ~/.bashrc，与 JAVA_HOME 的设置相似），在文件最前面加入如下单独一行:
`export PATH=$PATH:/usr/local/hadoop/sbin:/usr/local/hadoop/bin`

>添加后执行 source ~/.bashrc 使设置生效，生效后，在任意目录中，都可以直接使用 hdfs 等命令了，可以执行 hdfs dfs -ls input 查看 HDFS 文件;

