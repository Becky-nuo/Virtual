# HDFS文件操作
## 上机步骤：
>打开Ubuntu系统，打开一个命令终端，进入Shell环境，利用命令行语句操作HDFS；
>进入Ubuntu系统，安装Eclipse开发工具，利用Elipse工具编写程序操作HDFS;

>启动Hadoop,执行下面命令：

`cd /usr/local/hadoop./sbin/start-dfs.sh #启动Hadoop    #Shell 命令`

## 利用Shell命令与HDFS进行交互
>Hadoop支持很多Shell命令，其中fs是HDFS最常用的命令，利用fs可以查看HDFS文件系统的目录结构、上传和下载数据、创建文件等:
```
hadoop fs适用于任何不同的文件系统，比如本地文件系统和HDFS文件系统
hadoop dfs只能适用于HDFS文件系统
hdfs dfs跟hadoop dfs的命令作用一样，也只能适用于HDFS文件系统;
```
>在终端输入如下命令，查看fs总共支持了哪些命令:

`./bin/hadoop fs  #Shell 命令`


>在终端输入如下命令，可以查看具体某个命令的作用，如：我们查看put命令如何使用，可以输入如下命令:

`./bin/hadoop fs -help put   #Shell 命令`


### 目录操作
>Hadoop系统安装好以后，第一次使用HDFS时，需要首先在HDFS中创建用户目录,在HDFS中为hadoop用户创建一个用户目录，命令如下：

`cd /usr/local/hadoop./bin/hdfs dfs –mkdir -p/user/hadoop    #Shell 命令`

>该命令中表示在HDFS中创建一个“/user/hadoop”目录，“–mkdir”是创建目录的操作，“-p”表示如果是多级目录，则父目录和子目录一起创建，这里“/user/hadoop”就是一个多级目录，因此必须使用参数“-p”，否则会出错;
“/user/hadoop”目录就成为hadoop用户对应的用户目录，可以使用如下命令显示HDFS中与当前用户hadoop对应的用户目录下的内容：

 `./bin/hdfs dfs –ls .   #Shell 命令`

>该命令中，“-ls”表示列出HDFS某个目录下的所有内容，“.”表示HDFS中的当前用户目录，也就是“/user/hadoop”目录，上面的命令和下面的命令是等价的：

`./bin/hdfs dfs –ls /user/hadoop   #Shell 命令`

>如果要列出HDFS上的所有目录，可以使用如下命令：

`./bin/hdfs dfs –ls   #Shell 命令`

>使用如下命令创建一个input目录：

` ./bin/hdfs dfs –mkdir input   #Shell 命令`

>在创建个input目录时，采用了相对路径形式，实际上，这个input目录创建成功以后，它在HDFS中的完整路径是“/user/hadoop/input”。如果要在HDFS的根目录下创建一个名称为input的目录，则需要使用如下命令：

`./bin/hdfs dfs –mkdir /input   #Shell 命令`

>可以使用rm命令删除一个目录，可以使用如下命令删除在HDFS中创建的“/input”目录（不是“/user/hadoop/input”目录）：

`./bin/hdfs dfs –rm –r /input   #Shell 命令`

>上面命令中，“-r”参数表示如果删除“/input”目录及其子目录下的所有内容，如果要删除的一个目录包含了子目录，则必须使用“-r”参数，否则会执行失败;

### 文件操作
>在实际应用中，经常需要从本地文件系统向HDFS中上传文件，或者把HDFS中的文件下载到本地文件系统中；首先，使用vim编辑器，在本地Linux文件系统的“/home/hadoop/”目录下创建一个文件myLocalFile.txt，里面可以随意输入一些单词，比如，输入如下三行：
```
Hadoop
Spark
XMU DBLAB
```

>然后，可以使用如下命令把本地文件系统的“/home/hadoop/myLocalFile.txt”上传到HDFS中的当前用户目录的input目录下，也就是上传到HDFS的“/user/hadoop/input/”目录下：

`./bin/hdfs dfs -put /home/hadoop/myLocalFile.txt  input   #Shell 命令`

>可以使用ls命令查看一下文件是否成功上传到HDFS中：

`./bin/hdfs dfs –ls input   #Shell 命令`

>该命令执行后会显示如下的信息：
```
Found 1 items   
-rw-r--r--   1 hadoop supergroup         36 2020-5-05 14:23  input/myLocalFile.txt
```
>下面使用如下命令查看HDFS中的myLocalFile.txt这个文件的内容：

`./bin/hdfs dfs –cat input/myLocalFile.txt  #Shell 命令`

>下面把HDFS中的myLocalFile.txt文件下载到本地文件系统中的“/home/hadoop/下载/”这个目录下，命令如下：

`./bin/hdfs dfs -get input/myLocalFile.txt  /home/hadoop/下载  #Shell 命令`

>使用如下命令，到本地文件系统查看下载下来的文件myLocalFile.txt：

`$ cd ~$ cd 下载$ ls$ cat myLocalFile.txt   #Shell 命令`

>最后，把文件从HDFS中的一个目录拷贝到HDFS中的另外一个目录；如，把HDFS的“/user/hadoop/input/myLocalFile.txt”文件，拷贝到HDFS的另外一个目录“/input”中（注意，这个input目录位于HDFS根目录下），可以使用如下命令：

`./bin/hdfs dfs -cp input/myLocalFile.txt  /input   #Shell 命令`

## 利用Web界面管理HDFS
>打开Linux自带的Firefox浏览器，点击此链接HDFS的Web界面，即可看到HDFS的web管理界面；

## 利用Java API与HDFS进行交互
>Hadoop不同的文件系统之间通过调用Java API进行交互，本质上就是Java API的应用；

>利用Java API进行交互，需要利用软件Eclipse编写Java程序；

### 在Ubuntu中安装Eclipse
>利用Ubuntu左侧边栏自带的软件中心安装软件，在Ubuntu左侧边栏打开软件中心；

>打开软件中心；

>在软件中心搜索栏输入“ec”，软件中心会自动搜索相关的软件；

>点击Eclipse，进行安装;

>安装需要管理员权限，Ubuntu系统需要用户认证，弹出“认证”窗口，要输入当前用户的登录密码;

>ubuntu便会进入安装过程中,安装结束后安装进度条便会消失；

>点击Ubuntu左侧边栏的搜索工具，输入“ec”，自动搜索已经安装好的相关软件,打开Eclipse；

### 在Eclipse创建项目
>第一次打开Eclipse,需要填写workspace(工作空间)，用来保存程序所在的位置，这里按照默认，不需要改动;

>点击“OK”按钮，进入Eclipse软件,由于当前是采用hadoop用户登录了Linux系统，因此，默认的工作空间目录位于hadoop用户目录“/home/hadoop”下;

>选择“File->New->Java Project”菜单，开始创建一个Java工程;
```
在“Project name”后面输入工程名称“HDFSExample”，选中“Use default location”，让Java工程的所有文件都保存
到“/home/hadoop/workspace/HDFSExample”目录下;

在“JRE”这个选项卡中，可以选择当前的Linux系统中已经安装好的JDK，如java-8-openjdk-amd64;

点击界面底部的“Next>”按钮，进入下一步的设置;
```

### 为项目添加需要用到的JAR包
>进入下一步的设置:
```
在这个界面中加载该Java工程所需要用到的JAR包，这些JAR包中包含了可以访问HDFS的Java API；
JAR包都位于Linux系统的Hadoop安装目录下，对于本教程而言，就是在“/usr/local/hadoop/share/hadoop”目录下;
点击界面中的“Libraries”选项卡，然后，点击界面右侧的“Add External JARs…”按钮;
```

>在该界面中，上面的一排目录按钮（即“usr”、“local”、“hadoop”、“share”、“hadoop”、“mapreduce”和“lib”）；编写一个能够与HDFS交互的Java应用程序，一般需要向Java工程中添加以下JAR包：
```
（1）”/usr/local/hadoop/share/hadoop/common”目录下的hadoop-common-2.7.1.jar和haoop-nfs-2.7.1.jar；
（2）/usr/local/hadoop/share/hadoop/common/lib”目录下的所有JAR包；
（3）“/usr/local/hadoop/share/hadoop/hdfs”目录下的haoop-hdfs-2.7.1.jar和haoop-hdfs-nfs-2.7.1.jar；
（4）“/usr/local/hadoop/share/hadoop/hdfs/lib”目录下的所有JAR包。
如，把“/usr/local/hadoop/share/hadoop/common”目录下的hadoop-common-2.7.1.jar和haoop-nfs-2.7.1.jar添加到当前的Java工程中;
在界面中点击目录按钮，进入到common目录,面会显示出common目录下的所有内容;
```
>用鼠标点击选中hadoop-common-2.7.1.jar和haoop-nfs-2.7.1.jar，然后点击界面右下角的“确定”按钮，就可以把这两个JAR包增加到当前Java工程中;

>hadoop-common-2.7.1.jar和haoop-nfs-2.7.1.jar已经被添加到当前Java工程中；按照类似的操作方法，可以再次点击“Add External JARs…”按钮，把剩余的其他JAR包都添加进来；需要注意的是，当需要选中某个目录下的所有JAR包时，可以使用“Ctrl+A”组合键进行全选操作；全部添加完毕以后，点击界面右下角的“Finish”按钮，完成Java工程HDFSExample的创建；

### 编写Java应用程序代码
>编写一个Java应用程序，用来检测HDFS中是否存在一个文件,请在Eclipse工作界面左侧的“Package Explorer”面板中，找到刚才创建好的工程名称“HDFSExample”，然后在该工程名称上点击鼠标右键，在弹出的菜单中选择“New->Class”菜单;

>选择“New->Class”菜单;

>在该界面中，在“Name”后面输入新建的Java类文件的名称，采用名称“HDFSFileIfExist”，其他都可以采用默认设置，然后，点击界面右下角“Finish”按钮;

>Eclipse自动创建了一个名为“HDFSFileIfExist.java”的源代码文件，在该文件中输入以下代码：
```
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

public class HDFSFileIfExist
 {    
	public static void main(String[ ] args )
	{        
	try
	{   
		String fileName = "test";           
		Configuration conf = new Configuration();            
		conf.set("fs.defaultFS", "hdfs://localhost:9000");            
		conf.set("fs.hdfs.impl", "org.apache.hadoop.hdfs.DistributedFileSystem");           
		FileSystem fs = FileSystem.get(conf);         
		            
		if(fs.exists(new Path(fileName)))
		{                
			System.out.println("文件存在");            
		}
		else
		{               
			 System.out.println("文件不存在");            
		}    
		catch (Exception e)
		{           
			 e.printStackTrace(); 
		 }   
	 }
}   #Java
```

>该程序用来测试HDFS中是否存在一个文件，其中有一行代码：

`String fileName = "test"`

>这行代码给出了需要被检测的文件名称是“test”，没有给出路径全称，表示是采用了相对路径，实际上就是测试当前登录Linux系统的用户hadoop，在HDFS中对应的用户目录下是否存在test文件，测试HDFS中的“/user/hadoop/”目录下是否存在test文件;

### 编译运行程序
>在开始编译运行程序之前，确保Hadoop已经启动运行，如果还没有启动，需要打开一个Linux终端，输入以下命令启动Hadoop：

`cd /usr/local/hadoop./sbin/start-dfs.sh   #Shell 命令`

>现在就可以编译运行上面编写的代码,可以直接点击Eclipse工作界面上部的运行程序的快捷按钮，当把鼠标移动到该按钮上时，在弹出的菜单中选择“Run As”，继续在弹出来的菜单中选择“Java Application”;

>在“Select type”下面的文本框中输入“HDFSFileIfExist”，Eclipse就会自动找到相应的类“HDFSFileIfExist-(default package)”（注意：这个类在后面的导出JAR包操作中的Launch configuration中会被用到），然后，点击界面右下角的“OK”按钮，开始运行程序；程序运行结束后，会在底部的“Console”面板中显示运行结果信息；目前HDFS的“/user/hadoop”目录下还没有test文件，因此，程序运行结果是“文件不存在”，“Console”面板中还会显示一些类似“log4j:WARN…”的警告信息，可以不用理会；


### 应用程序的部署
>把Java应用程序生成JAR包，部署到Hadoop平台上运行,在Hadoop安装目录下新建一个名称为myapp的目录，用来存放我们自己编写的Hadoop应用程序，可以在Linux的终端中执行如下命令：

`cd /usr/local/hadoopmkdir myapp    #Shell 命令`

>在Eclipse工作界面左侧的“Package Explorer”面板中，在工程名称“HDFSExample”上点击鼠标右键，在弹出的菜单中选择“Export”;

>选择“Runnable JAR file”，然后，点击“Next>”按钮;

>“Launch configuration”用于设置生成的JAR包被部署启动时运行的主类，需要在下拉列表中选择刚才配置的类“HDFSFileIfExist-HDFSExample”；在“Export destination”中需要设置JAR包要输出保存到哪个目录，如，设置为“/usr/local/hadoop/myapp/HDFSExample.jar”；在“Library handling”下面选择“Extract required libraries into generated JAR”，点击“Finish”按钮;

>忽略界面的信息，直接点击界面右下角的“OK”按钮，启动打包过程，打包过程结束后，会出现一个警告信息界面；

>忽略界面的信息，直接点击界面右下角的“OK”按钮；至此，已经顺利把HDFSExample工程打包生成了HDFSExample.jar；可以到Linux系统中查看一下生成的HDFSExample.jar文件，可以在Linux的终端中执行如下命令：

`cd /usr/local/hadoop/myappls  #Shell 命令`

>“/usr/local/hadoop/myapp”目录下已经存在一个HDFSExample.jar文件，在Linux系统中，使用hadoop jar命令运行程序，命令如下：

`cd /usr/local/hadoop./bin/hadoop jar ./myapp/HDFSExample.jar  #Shell 命令`

>也可以使用如下命令运行程序：

`cd /usr/local/hadoopjava -jar ./myapp/HDFSExample.jar   #Shell 命令`

>命令执行结束后，会在屏幕上显示执行结果“文件不存在”,至此，检测HDFS文件是否存在的程序，就顺利部署完成了;


### 写入文件

```
import org.apache.hadoop.conf.Configuration;         
import org.apache.hadoop.fs.FileSystem;        
import org.apache.hadoop.fs.FSDataOutputStream;        
import org.apache.hadoop.fs.Path;         

public class Chapter3 
{                    
	public static void main(String[] args) 
	{                         
		try 
		{                                
			Configuration conf = new Configuration( );
			conf.set("fs.defaultFS","hdfs://localhost:9000");
			conf.set("fs.hdfs.impl","org.apache.hadoop.hdfs.DistributedFileSystem");
			FileSystem fs = FileSystem.get(conf);                                
			byte[] buff = "Hello world".getBytes(); // 要写入的内容
			String filename = "test"; //要写入的文件名
			FSDataOutputStream os = fs.create(new Path(filename));                                			
			os.write(buff,0,buff.length); 
			System.out.println("Create:"+ filename);
			os.close();
			fs.close();                        
		   }	catch (Exception e)
			 {
				e.printStackTrace();                          
			 }                 
	}          
}  #Java
```

### 读取文件

```
import java.io.BufferedReader;        
import java.io.InputStreamReader;         
import org.apache.hadoop.conf.Configuration;        
import org.apache.hadoop.fs.FileSystem;        
import org.apache.hadoop.fs.Path;        
import org.apache.hadoop.fs.FSDataInputStream;         
public class Chapter3 
{                
	public static void main(String[] args)
	{                        
		try 
		{                                
			Configuration conf = new Configuration();                                
			conf.set("fs.defaultFS","hdfs://localhost:9000");
			conf.set("fs.hdfs.impl","org.apache.hadoop.hdfs.DistributedFileSystem");                                
			FileSystem fs = FileSystem.get(conf);                                
			Path file = new Path("test");                                 
			FSDataInputStream getIt = fs.open(file);                                
			BufferedReader d = new BufferedReader(new InputStreamReader(getIt));                                
			String content = d.readLine(); //读取文件一行                                
			System.out.println(content);                                
			d.close(); //关闭文件                                
			fs.close(); //关闭hdfs                        
		} 	catch (Exception e) 
			{                               
				 e.printStackTrace();                        
			}                
	}        
}    #Java
```







