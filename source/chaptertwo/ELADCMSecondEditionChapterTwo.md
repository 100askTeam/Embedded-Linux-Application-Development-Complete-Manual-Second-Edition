# 第二篇. Linux基本操作与开发工具使用   
## 介绍与引导   
### 下载安装VMWARE，下载ubuntu映象、打开   
&emsp;&emsp;请参考各开发板的 “高级用户使用手册” ，这些手册可以使用 git 工具下载，请参 http://wiki.100ask.net 中的“初学者学习路线”。

&emsp;&emsp;参考各开发板的“高级用户使用手册”，去百度网盘中下载 VMWARE 软件，下载 100ask-vmware_ubuntu18.04.7z 。

&emsp;&emsp;VMWARE 安装好后，有两个软件，它们都可以使用，建议使用第 2 个：

&emsp;&emsp;&emsp;&emsp;1.Vmware Workstation Pro：这是收费的，可以试用 30 天

&emsp;&emsp;&emsp;&emsp;2.Vmware Workstation Player：这是免费的

   
### 在ubuntu中练习常用Linux命令   
&emsp;&emsp;Linux 常用命令的练习，既可以看视频，也可以看 WIKI 上的文档；录制视频时写的文档可以使用 GIT 命令下载，需要使用、修改这些文档的学员，尽可使用，没有版权限制。

&emsp;&emsp;这些命令的操作视频，大部是我同事王辉录制的，是之前 2017 年录制的 “S3C2440加强版裸机视频” 的一部分。是在 ubuntu16.04 上操作的，跟现在使用的 ubuntu18.04 没有显著差别。

&emsp;&emsp;视频可以从网盘中下列红框所示的目录下载：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_001.png) 
<300px>

&emsp;&emsp;WIKI 位于 http://wiki.100ask.net 中的 “初学者学习路线” ，点击该页面中如下图红色箭头所示位置：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_002.png) 
<600px>
   
录制视频时所写文档，可以使用GIT下载我们配套的文档，它位于如下图红框中所示位置：   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_003.png) 
<300px>
   
### 虚拟机和Ubuntu的网络设置   
&emsp;&emsp;Windows 、 Vmware 上的 Ubuntu 、开发板，三者的网终互通问题是初学者经常碰到的。 Ubuntu 的上网问题，也是初学者经常碰到的。

&emsp;&emsp;针对这些问题，我们录制过视频，但是效果不好。

&emsp;&emsp;后来写了一个文档，直接列出了所有情况，看文档的效果更好。

   
## Linux基本操作   
### Ubuntu桌面简单操作   
#### Ubuntu和Windows的最大差别：目录   
&emsp;&emsp;Windows 中每一个分区都对应一个盘符，盘符下可以存放目录与文件：
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_004.png) 
<800px>

&emsp;&emsp;<font color="#dd0000">注意</font>：目录就是文件夹。

&emsp;&emsp;Windows下某个文件的绝对路径以盘符开始，比如：C:\abc\def\hello.txt，这是在C盘的abc目录下，有def子目录；而def中有hello.txt文件。

&emsp;&emsp;Ubuntu中，以树状结构表示文件夹与文件，没有盘符的概念。比如：/abc/def/hello.txt，这表示在根目录下有abc子目录，而abc下又有def目录；def中有hello.txt文件。

&emsp;&emsp;从名字“/abc/def/hello.txt”中你无法知道hello.txt文件位于磁盘哪一个分区。

&emsp;&emsp;<font color="#dd0000">注意</font>：要想查看某个分区挂载在哪一个目录下，可以执行命令：df  -h

&emsp;&emsp;对于普通用户，在Ubuntu下不再关心分区、盘符。需要关心的是哪个目录存什么：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_005.png) 
<800px>

&emsp;&emsp;Ubuntu中的目录遵循FHS标准(Filesystem Hierarchy Standard，文件系统层次标准)。它定义了文件系统中目录、文件分类存放的原则、定义了系统运行所需的最小文件、目录的集合，并列举了不遵循这些原则的例外情况及其原因。FHS并不是一个强制的标准，但是大多的Linux、Unix发行版本遵循FHS。

&emsp;&emsp;这些目录简单介绍如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_006.png) 
<600px>
#### 启动终端   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_007.png) 
<600px>
   
#### 设置屏幕   
&emsp;&emsp;在我们的后续学习过程中，很少使用Ubuntu的桌面系统，都是远程登录上去的。但是如果Ubunut的桌面显示太小、太大，总是让人不舒服。这时可以修改屏幕分辨率，方法如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_008.png) 
<600px>
   
   
#### 设置网络   
&emsp;&emsp;这是很复杂的，有一个文档专门讲解怎么设置网络。

&emsp;&emsp;使用GIT下载文档后，设置网络的文档位于下图红框所示目录：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_009.png) 
<200px>
   
### Linux命令   
#### Linux命令的提示符   
&emsp;&emsp;按 “2.1.2 启动终端” 进行操作启动终端后，即可看到类似下图的提示符：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_010.png) 
<400px>
&emsp;&emsp;提示符中各项含义在上图中都列出来了。

   
#### Linux命令的格式   
&emsp;&emsp;Linux命令一般由三部分组成：

&emsp;&emsp;&emsp;&emsp;① command命令     

&emsp;&emsp;&emsp;&emsp;② options选项    

&emsp;&emsp;&emsp;&emsp;③ parameter参数

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_011.png) 
<400px>
&emsp;&emsp;说明：

&emsp;&emsp;&emsp;&emsp;① [ ]中括号表示 该部分可选，可有可无，需要根据命令的实际需要而添加

&emsp;&emsp;&emsp;&emsp;② 命令、选项、参数都以空格分隔，不管几个空格都算一个空格

&emsp;&emsp;&emsp;&emsp;③ 命令输入完毕后，按回车“Enter”键启动

&emsp;&emsp;示例：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_012.png) 
<400px>
    
#### 记住命令并不难, 先背几个单词   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_013.png) 
<400px>
#### 绝对路径和相对路径   
&emsp;&emsp;Linux下的根目录为“/”，从根目录下出发可以找到任意目录、任意文件。从根目录开始表示目录或文件的方法称为“绝对路径”。比如：

```bash
   /home/book   
   /home/book/1.txt   
   /bin/pwd   
```
   
&emsp;&emsp;有时候使用绝对路径太过麻烦，可以使用相对路径。假设当前正位于/home/book目录下，那么：

```bash
   ./1.txt      表示当前目录下的1.txt，即 /home/book/1.txt；“.”表示当前目录   
   ../book/1.txt   表示当前目录的上一级目录里，book子目录下的1.txt   
            “/home/book/..”就是”/home”目录，”..”表示上一级目录   
```
   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_014.png) 
<300px>
   
#### 目录/文件操作命令   
&emsp;&emsp;① pwd

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_015.png) 
<600px>
&emsp;&emsp;② cd

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_016.png) 
<600px>
cd命令有些缩略用法：   
```bash
   $ cd  -   // 进入上次目录, 比如先进入a目录再进入b目录，执行此命令后即回到a目录   
   $ cd  ~   // 进入家目录   
```
   
&emsp;&emsp;③ mkdir

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_017.png) 
<600px>

&emsp;&emsp;④ rmdir

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_018.png) 
<600px>

&emsp;&emsp;⑤ ls

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_019.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;使用示例：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_020.png) 
<600px>
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_021.png) 
<600px>

&emsp;&emsp;⑥ cp

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_022.png) 
<600px>
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_023.png) 
<600px>

&emsp;&emsp;复制目录时，常用如下命令：

```bash
   $ cp  -rfd  dir_a  dir_b   
```
&emsp;&emsp;&emsp;&emsp;r：recursive，递归地，即复制所有文件

&emsp;&emsp;&emsp;&emsp;f：force，强制覆盖

&emsp;&emsp;&emsp;&emsp;d：如果源文件为链接文件，也只是把它作为链接文件复制过去，而不是复制实际文件

   
⑦ rm   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_024.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;删除目录时，常用如下命令：

```bash
   $ rm  -rf  dir_a   
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;r：recursive，递归地，即复制所有文件

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;f：force，强制删除

&emsp;&emsp;⑧ cat

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_025.png) 
<600px>

&emsp;&emsp;⑨ touch

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_026.png) 
<600px>
   
   
#### 查找/搜索命令   
① find   
&emsp;&emsp;在Windows中搜索文件，一般查找文件需要传入两个条件：

&emsp;&emsp;&emsp;&emsp;a. 在哪些目录中查找；

&emsp;&emsp;&emsp;&emsp;b. 查找的内容；

&emsp;&emsp;在 Linux 中，查找文件的也需要这两个条件，不同于Windows使用搜索框查找，Linux中使用find命令查找文件。

&emsp;&emsp;find命令格式为： 

```bash
   find 目录名 选项 查找条件   
```
   
&emsp;&emsp;举例1：

```bash
    $ find  /home/book/dira/  -name  " test1.txt "    
```
      
&emsp;&emsp;说明:

&emsp;&emsp;&emsp;&emsp;a) /home/book/dira/指明了查找的路径。

&emsp;&emsp;&emsp;&emsp;b)“-name”表明以名字来查找文件 。

&emsp;&emsp;&emsp;&emsp;c)“test1.txt”，就指明查找名为“test1.txt”的文件。

&emsp;&emsp;举例2：

```bash
   $ find  /home/book/dira/ -name  " *.txt "    
```   
&emsp;&emsp;&emsp;&emsp;说明: 查找指定目录下面所有以“.tx”t结尾的文件，其中“*”是通配符。

&emsp;&emsp;举例3：

```bash
   find  /home/book/dira/  -name "dira"	   
```   
&emsp;&emsp;&emsp;&emsp;说明: 查找指定目录下面是否存在“dira”这个目录或文件，“dira”是名称。

&emsp;&emsp;注意：

&emsp;&emsp;&emsp;&emsp;1）	如果没有指定查找目录，则为当前目录。

```bash
      $ find . -name " *.txt "   //其中.代表当前路径。    
      $ find -name " *.txt "     //没加路径，默认是当前路径下查找。   
```
   
&emsp;&emsp;&emsp;&emsp;2）	find还有一些高级的用法，如查找最近几天(几个小时)之内(之前)有变动的文件 

```bash
      $ find  /home/book  -mtime -2      //查找/home目录下两天内有变动的文件。   
```
   
&emsp;&emsp;② grep

&emsp;&emsp;&emsp;&emsp;grep命令的作用是查找文件中符合条件的字符串，其格式如下：

```bash
   grep [选项] [查找模式] [文件名]。   
```
&emsp;&emsp;&emsp;&emsp;假设dira目录的test1.txt和dirb目录的test1.txt都含有如下内容： aaa AAAAAA abc abcabcabc cbacbacba match_pattern nand->erase。

&emsp;&emsp;&emsp;&emsp;通过查找字符串，希望显示如下内容： 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）所在的文件名----grep查找时默认已经显示目标文件名 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）所在的行号------使用-n选项。

&emsp;&emsp;&emsp;&emsp;grep -rn "字符串" 文件名 r(recursive)：递归查找 n(number)：显示目标位置的行号 字符串:要查找的字符串 文件名:要查找的目标文件，如果是*则表示查找当前目录下的所有文件和目录。

&emsp;&emsp;&emsp;&emsp;举例： 

```bash
      //在test1.txt中查找字符串abc grep -rn "abc" * 在当前目录递归查找字符串abc   
      $ grep -n "abc" test1.txt    
```
       
&emsp;&emsp;&emsp;&emsp;注意：可以加入-w全字匹配。

&emsp;&emsp;&emsp;&emsp;可以在grep的结果中再次执行grep搜索，比如搜索包含有ABC的头文件，可执行如下命令：

```bash
      $ grep  “ABC”  *  -nR  |  grep “\.h”   
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;上述命令把第1个命令“grep  “ABC”  *  -nR”通过管道传给第2个命令。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;即第2个命令在第1个命令的结果中搜索。

   
#### 压缩/解压命令   
&emsp;&emsp;压缩的目的： 在网络传递文件时，可以先将文件压缩，然后传递压缩后的文件，从而减少网络带宽。 接收到文件后，解压即可。

&emsp;&emsp;压缩的类型有2种：有损压缩、无损压缩：

&emsp;&emsp;&emsp;&emsp;a. 有损压缩： 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;如mp4视频文件，在压缩过程中减少了很多帧的数据，但是对观看者而言没有影响。当然mp3音乐文件也是有损压缩。

&emsp;&emsp;&emsp;&emsp;b. 无损压缩：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;如普通文件的压缩，为了保证信息的正确传递，不希望文件经过压缩或解压后，出现任何差异。

&emsp;&emsp;后面讲解的都是无损压缩。

&emsp;&emsp;单个文件的压缩(解压)使用gzip 和bzip2 ，多个文件和目录使用tar。

&emsp;&emsp;① gzip

&emsp;&emsp;&emsp;&emsp;gzip的常用选项： 

```bash
      -l(list)	   列出压缩文件的内容。   
      -k(keep)	   在压缩或解压时，保留输入文件。   
      -d(decompress)	将压缩文件进行解压缩。   
```
   
&emsp;&emsp;&emsp;&emsp;举例：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）	查看压缩文件

```bash
   $ gzip -l  pwd.1.gz   
```
      
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）	解压文件 

```bash
   $ gzip -kd pwd.1.gz   //该压缩文件是以.gz结尾的单个文件   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;3）	压缩文件 

```bash
   $ gzip -k mypwd.1   /得到了一个.gz结尾的压缩文件   
```
   
&emsp;&emsp;&emsp;&emsp;注意： 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）如果gzip不加任何选项，此时为压缩

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;压缩完该文件会生成后缀为.gz的压缩文件，并删除原来的文件。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;所以，推荐使用gzip -k来压缩源文件，这样会保留原来的文件。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）相同的文件内容，如果文件名不同，压缩后的大小也不同。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;3）gzip只能压缩单个文件，不能压缩目录。

&emsp;&emsp;② bzip2

&emsp;&emsp;&emsp;&emsp;bzip2的常用选项：

```bash
   -k(keep)	在压缩或解压时，保留输入文件；    
   -d(decompress)	将压缩文件进行解压缩；   
```
   
&emsp;&emsp;&emsp;&emsp;1）	压缩文件 

```bash
   $ bzip2 -k mypwd.1 得到一个.bz2后缀的压缩文。   
```
   
&emsp;&emsp;&emsp;&emsp;2）	解压文件 

```bash
   $ bzip2 -kd mypwd.1.bz2   
```
   
&emsp;&emsp;&emsp;&emsp;注意：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）如果bzip2不加任何选项，此时为压缩

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;压缩完该文件会生成后缀为.bz2的压缩文件， 并删除原来的文件。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;所以说，推荐使用bzip2 -k 来压缩文件，这样可以保留原来的文件。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）bzip2只能压缩单个文件，不能压缩目录。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;单个文件的压缩使用gzip或bzip2， 压缩有两个参数：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）压缩时间  

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）压缩比。

&emsp;&emsp;一般情况下，小文件使用gzip来压缩，大文件使用bzip2来压缩。bzip2的的压缩率更高。

&emsp;&emsp;③ tar

::tar常用选项：   
```bash
   -c(create)：表示创建用来生成文件包 。   
   -x：表示提取，从文件包中提取文件。   
   -t：可以查看压缩的文件。   
   -z：使用gzip方式进行处理，它与”c“结合就表示压缩，与”x“结合就表示解压缩。   
   -j：使用bzip2方式进行处理，它与”c“结合就表示压缩，与”x“结合就表示解压缩。    
   -v(verbose)：详细报告tar处理的信息。   
   -f(file)：表示文件，后面接着一个文件名。 -C <指定目录> 解压到指定目录。   
```
   
&emsp;&emsp;&emsp;&emsp;例1：tar打包、gzip压缩 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）把目录dira压缩、打包为dira.tar.gz文件：

```bash
      $ tar czvf dira.tar.gz dira   
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;注意：“tar –czvf”与“tar czvf”是一样的效果，所以说，后面统一取消“-”。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）查看压缩文件：

```bash
      $ tar tvf  dira.tar.gz   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;3）	解压文件，可以用-C 指定解压到哪个目录：

```bash
      $ tar xzvf dira.tar.gz             //解压到当前目录    
      $ tar xzvf dira.tar.gz -C /home/book   //解压到/home/book。     
```
   
&emsp;&emsp;&emsp;&emsp;例2：tar打包、bzip2压缩 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）把目录dira压缩、打包为dira.tar.bz2文件

```bash
      $ tar cjvf dira.tar.bz2 dira   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）查看压缩文件

```bash
      $ tar tvf dira.tar.bz2   
```


&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;3）解压文件，可以用-C 指定解压到哪个目录

```bash
      $ tar xjvf dira.tar.bz2               //解压到当前目录:     
      $ tar xjvf dira.tar.bz2 -C /home/book    //解压到/home/book   
```
   
   
#### 网络命令   
&emsp;&emsp;① ifconfig

&emsp;&emsp;&emsp;&emsp;查看网络、设置IP。ifconfig常用选项：

```bash
   -a ：显示所有网卡接口   
   up：激活网卡接口    
   down：关闭网卡接口   
   address：xxx.xxx.xxx.xxx，IP地址   
```
   
：示例：    
&emsp;&emsp;&emsp;&emsp;1）ifconfig：查看当前正在使用的网卡

```bash
   $ ifconfig   
   ens160: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500   
         inet 192.168.1.137  netmask 255.255.255.0  broadcast 192.168.1.255   
         inet6 fe80::4f45:59fb:ddb7:c274  prefixlen 64  scopeid 0x20<link>   
         ether 00:0c:29:ab:1d:05  txqueuelen 1000  (Ethernet)   
         RX packets 998794  bytes 176687882 (176.6 MB)   
         RX errors 0  dropped 0  overruns 0  frame 0   
         TX packets 801210  bytes 138020387 (138.0 MB)   
         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0   
```
   
&emsp;&emsp;&emsp;&emsp;2）ifconfig -a：查看所有网卡

```bash
   $ ifconfig -a   
   ens160: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500   
         inet 192.168.1.137  netmask 255.255.255.0  broadcast 192.168.1.255   
         inet6 fe80::4f45:59fb:ddb7:c274  prefixlen 64  scopeid 0x20<link>   
         ether 00:0c:29:ab:1d:05  txqueuelen 1000  (Ethernet)   
         RX packets 998889  bytes 176699569 (176.6 MB)   
         RX errors 0  dropped 0  overruns 0  frame 0   
         TX packets 801287  bytes 138033739 (138.0 MB)   
         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0   
   
   lo: flags=8<LOOPBACK>  mtu 65536   
         inet 127.0.0.1  netmask 255.0.0.0   
         loop  txqueuelen 1000  (Local Loopback)   
         RX packets 51460  bytes 3249553 (3.2 MB)   
         RX errors 0  dropped 0  overruns 0  frame 0   
         TX packets 51460  bytes 3249553 (3.2 MB)   
         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0   
```
   
&emsp;&emsp;&emsp;&emsp;3) 设置网IP：

```bash
   $ sudo ifconfig ens160 192.168.1.137   
```
   
&emsp;&emsp;② route和DNS

&emsp;&emsp;&emsp;&emsp;确保Windows和Ubuntu的网络能互相ping通之后，如果Ubuntu无法上网，原因通常有2个：路由没设置好，DNS没设置好。

&emsp;&emsp;&emsp;&emsp;如果执行以下命令不成功，表示路由没设置好：

```bash
   $ ping  8.8.8.8   
   connect: Network is unreachable   
```
   
&emsp;&emsp;&emsp;&emsp;如果“ping 8.8.8.8”成功，但是“ping  www.baidu.com”不成功，则是DNS没设置好：

```bash
   $ ping www.baidu.com   
   ping: unknown host www.baidu.com   
```
   
&emsp;&emsp;&emsp;&emsp;DNS的设置比较简单，8.8.8.8是好记好用的DNS服务器，修改Ubuntu中的/etc/resolv.conf文件，内容如下：

```bash
   nameserver  8.8.8.8   
```
   
&emsp;&emsp;&emsp;&emsp;路由信息使用route命令查看，其输出信息可以参考链接： [https://akaedu.github.io/book/ch36s05.html](https://akaedu.github.io/book/ch36s05.html)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;摘录如下：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;假设某主机上的网络接口配置和路由表如下：

```bash
      $ ifconfig   
      eth0     Link encap:Ethernet  HWaddr 00:0C:29:C2:8D:7E   
            inet addr:192.168.10.223  Bcast:192.168.10.255  Mask:255.255.255.0   
            UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1   
            RX packets:0 errors:0 dropped:0 overruns:0 frame:0   
            TX packets:10 errors:0 dropped:0 overruns:0 carrier:0   
            collisions:0 txqueuelen:100   
            RX bytes:0 (0.0 b)  TX bytes:420 (420.0 b)   
            Interrupt:10 Base address:0x10a0   
   
      eth1     Link encap:Ethernet  HWaddr 00:0C:29:C2:8D:88   
            inet addr:192.168.56.136  Bcast:192.168.56.255  Mask:255.255.255.0   
            UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1   
            RX packets:603 errors:0 dropped:0 overruns:0 frame:0   
            TX packets:110 errors:0 dropped:0 overruns:0 carrier:0   
            collisions:0 txqueuelen:100   
            RX bytes:55551 (54.2 Kb)  TX bytes:7601 (7.4 Kb)   
            Interrupt:9 Base address:0x10c0   
   
      lo      Link encap:Local Loopback     
            inet addr:127.0.0.1  Mask:255.0.0.0   
            UP LOOPBACK RUNNING  MTU:16436  Metric:1   
            RX packets:37 errors:0 dropped:0 overruns:0 frame:0   
            TX packets:37 errors:0 dropped:0 overruns:0 carrier:0   
            collisions:0 txqueuelen:0    
            RX bytes:3020 (2.9 Kb)  TX bytes:3020 (2.9 Kb)   
      $ route   
      Kernel IP routing table   
      Destination    Gateway       Genmask       Flags Metric Ref   Use Iface   
      192.168.10.0   *            255.255.255.0   U    0     0      0 eth0   
      192.168.56.0   *            255.255.255.0   U    0     0      0 eth1   
      127.0.0.0      *            255.0.0.0      U    0     0      0 lo   
      default       192.168.10.1    0.0.0.0       UG   0     0      0 eth0   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;上述route命令输出信息中各项的含义请看下表：

|-------------------------------------------------------------------<br>
| Destination     | 目标网段或者主机<br>
| Gateway         | 网关地址，”*” 表示目标是本主机所属的网络，不需要路由<br>
| Genmask         | 网络掩码<br>
|Flags            | 
   标记。一些可能的标记如下：<br>
   U - 路由是活动的<br>
   H - 目标是一个主机<br>
   G - 路由指向网关<br>
   R - 恢复动态路由产生的表项<br>
   D - 由路由的后台程序动态地安装<br>
   M - 由路由的后台程序修改<br>
   ! - 拒绝路由<br>
| Metric          | 路由距离，到达指定网络所需的中转数<br>
| Ref             | 路由项引用次数<br>
| Use             | 此路由项被路由软件查找的次数<br>
| Iface           | 该路由表项对应的输出接口<br>


&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;在上面的例子中，这台主机有两个网络接口：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 一个网络接口连到192.168.10.0/24网络

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② 另一个网络接口连到192.168.56.0/24网络。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;如果要发送的数据包的目的地址是192.168.56.3，跟第一行的子网掩码做与运算得到192.168.56.0，与第一行的目的网络地址不符，再跟第二行的子网掩码做与运算得到192.168.56.0，正是第二行的目的网络地址，因此从eth1接口发送出::: 去，由于192.168.56.0/24正是与eth1接口直接相连的网络，因此可以直接发到目的主机，不需要经路由器转发。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;如果要发送的数据包的目的地址是202.10.1.2，跟前三行路由表条目都不匹配，那么就要按缺省路由条目，从eth0接口发出去，首先发往192.168.10.1路由器，再让路由器根据它的路由表决定下一跳地址。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;可以使用route命令管理路由。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;示例：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1）添加路由：首先得确定网关IP，假设为192.168.1.1

```bash
            $ sudo  route  add  default  gw  192.168.1.1   
            $ ping 8.8.8.8   // 验证   
            PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.   
            64 bytes from 8.8.8.8: icmp_seq=1 ttl=53 time=19.8 ms   
            64 bytes from 8.8.8.8: icmp_seq=3 ttl=53 time=19.8 ms   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2）删除路由：

```bash
            $ sudo  route  del  default  gw  192.168.1.1   
```
   
   
#### 其他命令   
&emsp;&emsp;① file

&emsp;&emsp;&emsp;&emsp;查看文件类型。其格式如下： 

```bash
      file 文件名   
```
   
&emsp;&emsp;&emsp;&emsp;使用gcc编译得到的程序是运行于PC的，但是很多初学者经常把这些程序放到ARM板子上去运行，这时一般都会提示：

```bash
      xxx  not found   
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;它并非“找不到”，而是格式不正确。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;这时你可以执行“file xxx”查看它的类型，确定它是给PC还是给ARM编译的。

&emsp;&emsp;② which和whereis

&emsp;&emsp;&emsp;&emsp;which命令和whereis命令作用是查找命令或应用程序的所在位置，其格式如下：

```bash
      which   命令名/应用程序名   
      whereis  命令名/应用程序名。   
```
   
&emsp;&emsp;&emsp;&emsp;示例：

```bash
      $ which pwd    //定位到/bin/pwd   
      $ which gcc    //定位到/usr/bin/gcc    
      $ whereis pwd   //可得到可执行程序的位置和手册页的位置   
```
   
### vi编辑器   
&emsp;&emsp;vi是一个命令，也是一个命令行下的编辑器，它有如下功能：

&emsp;&emsp;&emsp;&emsp;a. 打开文件、新建文件、保存文件

&emsp;&emsp;&emsp;&emsp;b. 光标移动

&emsp;&emsp;&emsp;&emsp;c. 文本编辑

&emsp;&emsp;&emsp;&emsp;d. (多行间|多列间)复制、粘贴、删除

&emsp;&emsp;&emsp;&emsp;e. 查找和替换

&emsp;&emsp;很多人不习惯在命令行下编辑文件，实际开发中也不会经常在命令行下编辑文件。但是在Linux系统中对文件做些简单修改时，使用vi命令的效率非常高。并且在很多时候，比如现场调试时，并没有GUI形式的编辑工具，vi是唯一选择。

&emsp;&emsp;① 模式

&emsp;&emsp;&emsp;&emsp;vi编辑器有三种模式,各个模式侧重点不一样：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. 一般模式（光标移动、复制、粘贴、删除）

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. 编辑模式（编辑文本）

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. 命令行模式（查找和替换）

&emsp;&emsp;&emsp;&emsp;vi编辑器的三种模式间切换如下图所示

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_027.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;注意：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. 当不知道处于何种模式时，按ESC键返回到一般模式。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. wq(write quit)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. i(insert)

&emsp;&emsp;② 文件的打开、新建、保存

&emsp;&emsp;&emsp;&emsp;打开文件、新建文件，命令如下(如果文件存在则打开文件，否则新建文件并打开)：

```bash
      $ vi  文件名   
```
   
：修改结束之后，输入“:” 进入命令行模式，再输入“wq”保存退出：   
```bash
      :wq		保存并退出文件   
```
   
&emsp;&emsp;&emsp;&emsp;注意：如果文件不存在，也需要输入“:wq”才可以保存新文件，否则不会新建文件。

      
&emsp;&emsp;&emsp;&emsp;在编辑完成时，返回一般模式，方法如下：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. 输入“:w”则保存文件，如果已经保存文件，输入“:q”则退出文件

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. 直接输入“:wq”保存并退出

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. 如果不想保存被修改的内容，则输入“:q!”强制退出。

&emsp;&emsp;③ 编辑文件

&emsp;&emsp;&emsp;&emsp;打开文件后，默认处于“一般模式”，这时可以输入以下字母：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. “i”：insert, 表示在光标前开始插入文本

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. “a”：after，表示在光标后开始插入文本

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. “o”：other，表示在当前行之下新建一行，可以在行首输入字符

&emsp;&emsp;④ 快速移动光标

&emsp;&emsp;&emsp;&emsp;在一般模式下，hjkl这四个按键就可以移动光标。

&emsp;&emsp;&emsp;&emsp;“h”是左移，“j”是下移，“k”是上移，“l”是右移。

&emsp;&emsp;&emsp;&emsp;在一般模式下，还可以使用下述命令快速跳转。

&emsp;&emsp;&emsp;&emsp;1）快速的定位到某一行：文件头、文件尾、指定某一行

```bash
      ngg　　//光标移至第n行的行首（n为数字,想要跳转的行），   
      1gg　　//就跳到第一行的行首，就是文件头   
      2gg　　//就跳到第二行的行首    
      G　　　//转至文件结尾   
```
   
&emsp;&emsp;&emsp;&emsp;2）在某一行如何快速定位到某一列：

```bash
      0　　  //（数字零）光标移至当前行行首   
      $　　  //光标移至当前行行末   
      fx　   //搜索当前行中下一个出现字母x的地方   
```


&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;注意：当你不知道vi当前处于何种模式时，使用esc键返回到一般模式。

&emsp;&emsp;⑤ 文本复制、粘贴、删除、撤销 

&emsp;&emsp;&emsp;&emsp;在一般模式下，可以执行以下命令。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1) 复制

```bash
         yy　　	//复制当前行(y:yank(复制))      
         nyy　　 //复制当前行及其后的n-1行(n是数字)   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2) 粘贴

```bash
         p　　　 //粘贴(p:paste)   
```


&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;3) 删除

```bash
         dd　　 //删除光标所在行(d:delete)   
         ndd　　//删除当前行及其后的n-1行(n是数字)   
         x　　　//删除光标所在位置的字符   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;4) 撤销

```bash
         u　　　//撤销上一步操作   
```
   
&emsp;&emsp;⑥ 文本查找和替换

&emsp;&emsp;&emsp;&emsp;在一般模式下，可以执行以下命令。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1) 查找

```bash
         /pattern　　//从光标开始处向文件尾搜索pattern，后按下n或N   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;注意：

```bash
         n			在同一个方向重复上一次搜索命令   
         N			在反方向重复上一次搜索命令   
```
   
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;注意：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;在/pattern之前先跳到第一行则进行全文件搜索。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2) 替换

```bash
         :%s/p1/p2/g　　 //将文件中所有的p1均用p2替换   
         :%s/p1/p2/gc　　//替换时需要确认   
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;“s“ 全称：substitute替换；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;“g“ 全称：global全局；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;“c“ 全称：confirm，确认

   
   
## 虚拟机和Ubuntu的网络设置   
### Windows通过WIFI上网，开发板离无线路由器很近   
#### 连接网线   
&emsp;&emsp;如下图接线(开发板的网线，一定接到路由器，<font color="#dd0000">不要接到电脑</font>)：


&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和Ubuntu互通</font>，可以不接开发板的网线。


&emsp;&emsp;如果你<font color="#dd0000">只想让Ubuntu能上网</font>，可以不接开发板的网线。


![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_001.png)
#### VMWare里选择WIFI网卡   
&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和开发板互通</font>，可以不启动VMWare，当然也就不用去设置VMWare和Ubuntu。

&emsp;&emsp;很多电脑有多网卡，比如WIFI网卡、有线网卡。

&emsp;&emsp;在这种连接情况下，VMWare<font color="#dd0000">必须选择</font>桥接模式、<font color="#dd0000">必须选择</font>WIFI网卡。

&emsp;&emsp;VMWare选择桥接模式，方法如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_002.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_003.png) 
[//]: # (800px)
#### 设置IP   
&emsp;&emsp;Windows的WIFI网卡IP、ubuntu的IP、开发板的IP，**<font color="#dd0000">三个IP必须处于同一网段</font>**。

##### 设置Windows IP   
:Windows的IP一般都是自动分配的，在命令行执行：ipconfig，查看它的IP，假设为<code>192.168.1.10</code>。   
:如果想修改IP，可以手工修改，具体方法请自行百度搜：**windows 修改 IP**。   
   
##### 设置Ubuntu IP   
&emsp;&emsp;如果只是想让Windows、Ubuntu、开发板三者网络互通，只需要设置Ubuntu的IP。如果想让Ubuntu能上网，还需要设置它的网关、DNS。

&emsp;&emsp;一般来说无需手工设置Ubuntu的网络，<font color="#dd0000">如果、万一</font>Ubuntu的网络不好使，就需要手工设置。

&emsp;&emsp;方法如下(下面第1个图是给root用户设置密码，只需做1次)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_004.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_005.png) 
[//]: # (800px)
##### 设置开发板IP   
&emsp;&emsp;开发板运行UBOOT时、运行LINUX时，它们的IP需要分别设置，这两个阶段的IP没有联系。这两个阶段不会同时运行，所以它们的IP可以相同。

&emsp;&emsp;如果开发板正在运行UBOOT，执行以下命令设置IP：

 set  ipaddr  192.168.1.123   
 save

&emsp;&emsp;如果开发板正在运行LINUX，一般来说也不需要设置IP。如果有问题，比如执行ifconfig命令后无法查看到网卡的IP，你可以执行以下命令设置手工IP：

 echo  “ifconfig   eth0  192.168.1.123”  >> /etc/init.d/rcS   
 reboot   
   
#### 验证   
&emsp;&emsp;① 验证Windows和Ubuntu互通：

&emsp;&emsp;&emsp;&emsp;在Windows命令行执行：

 ping  192.168.1.100   // ping Ubuntu，如果有数据返回就表示通了   
&emsp;&emsp;如下图：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_006.png)

&emsp;&emsp;&emsp;&emsp;注意：UBOOT不回应PING数据，所以你是PING不通UBOOT的，


&emsp;&emsp;&emsp;&emsp;只能在UBOOT去PING别的电脑。


![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_007.png)
&emsp;&emsp;&emsp;&emsp;如果显示“<font color="#dd0000">alive</font>”就表示通了。

&emsp;&emsp;&emsp;&emsp;有时候Windows有防火墙导致PING不通，可以尝试去PING ubuntu的IP。

&emsp;&emsp;③ 验证Windows和“正在运行<font color="#dd0000">Linux</font>的开发板”互通(<font color="#dd0000">第②、③步，只要做一个就可以</font>)：

&emsp;&emsp;&emsp;&emsp;类似第①步，在Windows命令行下PING开发板的IP。

&emsp;&emsp;&emsp;&emsp;注意，开发板必须启动进入了Linux，它才能被Windows PING通。

&emsp;&emsp;④ 验证Ubuntu能否上网：打开terminal，执行命令“ping   news.qq.com”，有数据返回就表示成功。<font color="#dd0000">注意</font>：同时按住ctrl+c就可以退出当前命令。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_008.png) 
[//]: # (800px)
### Windows通过WIFI上网，开发板离无线路由器很远   
#### 连接网线   
##### Windows电脑和开发板的网线，都接到另一个集线器或路由器   
&emsp;&emsp;<font color="#dd0000">强烈建议</font>买一个网络集线器，很便宜的，20块钱之内；土壕可以买一个路由器代替集线器。

&emsp;&emsp;如下图接线(开发板和电脑的网线，接到网络集线器，开发板和电脑<font color="#dd0000">不要用网线</font>直连)：

&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和Ubuntu互通</font>，请看上一章(第1章)：不需要接开发板网线，不需要买集线器。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_009.png)
##### 电脑和开发板用网线直连(绝对不建议)   
&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和Ubuntu互通</font>，请看上一章(第1章)：不需要接开发板网线，不需要买集线器。

&emsp;&emsp;实在不想再买网络集线器了(强烈建议不要这样做，否则使用过程中麻烦时不时发生)，可以照下图接线：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_010.png)
&emsp;&emsp;电脑和开发板的网线直连时，

&emsp;&emsp;这是要特殊对待的情况！

&emsp;&emsp;如果电脑和开发板用网线直连，开发板上的程序，<font color="#dd0000">必须</font>使能网卡！

&emsp;&emsp;否则，Windows里看到的有线网卡就有一个红叉。

&emsp;&emsp;红叉表示"断开",

&emsp;&emsp;都"断开"了你别再问我为什么PING不通。

&emsp;&emsp;所以，

&emsp;&emsp;如果电脑和开发板用网线直连，开发板上的程序，<font color="#dd0000">必须</font>使能网卡：

&emsp;&emsp;a. 如果你要在开发板上玩UBOOT：

&emsp;&emsp;&emsp;&emsp;原生UBOOT是个奇葩，

&emsp;&emsp;&emsp;&emsp;它平时不使能网卡，

&emsp;&emsp;&emsp;&emsp;只有在使用网络命令那一小会，才使能网卡。

&emsp;&emsp;&emsp;&emsp;如果一定要直连，必须更换为"全程使能网卡的UBOOT"，

&emsp;&emsp;&emsp;&emsp;我们提供的JZ2440的uboot，已经全程使能网卡了，

&emsp;&emsp;&emsp;&emsp;你可以在它的前2行打印信息里看到：enable Ethernet alltime。

&emsp;&emsp;&emsp;&emsp;对于其他开发板所用的uboot，它并没有全程使能网卡，

&emsp;&emsp;&emsp;&emsp;对于这种情况，强烈建议不要用网线直接连接开发板和电脑。

	   
&emsp;&emsp;b. 如果你要在开发板上玩LINUX，

&emsp;&emsp;&emsp;&emsp;这个LINUX必须有网卡驱动，必须配置了网卡，

&emsp;&emsp;&emsp;&emsp;可以在开发板里执行类似这样的命令：

 ifconfig eth0 192.168.1.123    
#### VMWare里选择有线网卡   
&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和开发板互通</font>，可以不启动VMWare，当然也就不用去设置VMWare和Ubuntu。

&emsp;&emsp;很多电脑有多网卡，比如WIFI网卡、有线网卡。

&emsp;&emsp;在本章所描述的连接情况下，VMWare<font color="#dd0000">必须选择</font>桥接模式、<font color="#dd0000">必须选择有线</font>网卡。

&emsp;&emsp;VMWare选择桥接模式，方法如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_011.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_012.png) 
[//]: # (800px)
#### 设置IP   
&emsp;&emsp;**<font color="#dd0000">注意注意注意</font>**：Windows中的WIFI网卡、有线网卡，它们2个IP的网段<font color="#dd0000">绝不能相同</font>！

&emsp;&emsp;WIFI网卡的IP一般是自动分配的，一般都是192.168.<font color="#dd0000">1</font>.xxx，

&emsp;&emsp;那么我们就把有线网卡IP设为 192.168.2.xxx，不能跟WIFI网卡在同一网段。

&emsp;&emsp;Windows的有线网卡IP、ubuntu的IP、开发板的IP，**<font color="#dd0000">三个IP必须处于同一网段，所以需要有3个192.168.2.x的IP</font>**。

   
##### 设置Windows IP   
&emsp;&emsp;在本章的连接情况中，Windows的有线网卡的IP一般都无法自动分配。

&emsp;&emsp;需要手工修改有线网卡的IP，具体方法请自行百度搜：“windows 修改 IP”。

&emsp;&emsp;假设你已经把它的IP改为192.168.<font color="#dd0000">2</font>.10。

   
##### 设置Ubuntu IP   
&emsp;&emsp;在本章的网络连接中，Windows通过WIFI网卡上网，通过有线网卡跟Ubuntu和开发板互连。所以使用Ubuntu和开发板是<font color="#dd0000">无法上网</font>的，请记住这点。

&emsp;&emsp;在本章的网络连接中，你需要手工设置Ubuntu的网卡IP。

&emsp;&emsp;<font color="#dd0000">注意</font>，本例中把IP设置为192.168.<font color="#dd0000">2</font>.100。

&emsp;&emsp;方法如下(下面第1个图是给root用户设置密码，只需做1次)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_013.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_014.png) 
[//]: # (800px)
##### 设置开发板IP   
&emsp;&emsp;开发板运行UBOOT时、运行LINUX时，它们的IP需要分别设置，这两个阶段的IP没有联系。这两个阶段不会同时运行，所以它们的IP可以相同。

&emsp;&emsp;如果开发板正在运行UBOOT，执行以下命令设置IP：

 set  ipaddr  192.168.2.123   
 save

&emsp;&emsp;如果开发板正在运行LINUX，你可以执行以下命令设置手工IP：

 echo  “ifconfig   eth0  192.168.<font color="#dd0000">2</font>.123”  >> /etc/init.d/rcS   
 reboot   
   
#### 验证   
&emsp;&emsp;① 验证Windows和Ubuntu互通：

&emsp;&emsp;&emsp;&emsp;在Windows命令行执行：

 ping  192.168.2.100   // ping Ubuntu，如果有数据返回就表示通了，如下图：   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_015.png)

&emsp;&emsp;&emsp;&emsp;注意：UBOOT不回应PING数据，所以你是PING不通UBOOT的，

&emsp;&emsp;&emsp;&emsp;只能在UBOOT去PING别的电脑。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_016.png)

&emsp;&emsp;③ 验证Windows和“正在运行Linux的开发板”互通(<font color="#dd0000">第②③步，只要做一个就可以</font>)：

&emsp;&emsp;&emsp;&emsp;类似第①步，在Windows命令行下PING开发板的IP。

&emsp;&emsp;&emsp;&emsp;注意，开发板必须启动进入了Linux，它才能被Windows PING通。

   
### Windows不使用WIFI网卡   
#### 连接网线   
##### Windows通过有线网卡接路由器上网，开发板离路由器很近   
&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和Ubuntu互通</font>：不需要接开发板网线。

&emsp;&emsp;如果<font color="#dd0000">你只想让Ubuntu能上网</font>：不需要接开发板网线。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_017.png)
##### Windows通过有线网卡接路由器上网，开发板离路由器很远   
&emsp;&emsp;<font color="#dd0000">强烈建议</font>买一个网络集线器，很便宜的，20块钱之内；土壕可以买一个路由器代替集线器。

&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和Ubuntu互通</font>，请看3.1.1节：不需要接开发板网线，不需要买集线器，但是电脑的网线必须接到路由器(这样网卡才不是断开状态，才可使用)。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_018.png)
##### 不上网，Windows电脑和开发板的网线，都接到集线器或路由器   
&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和Ubuntu互通</font>：不需要接开发板网线，但是电脑的网线必须接到路由器或集线器(这样网卡才不是断开状态，才可使用)。

&emsp;&emsp;开发板和电脑，都使用网线连接到集线器或路由器上：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_019.png)
##### 电脑和开发板用网线直连(不建议)   
&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和Ubuntu互通，但是又没有集线器或路由器</font>：仍需要用网线连接电脑和开发板(这样网卡才不是断开状态，才可使用)。

&emsp;&emsp;实在不想再买网络集线器了(强烈建议不要这样做，否则使用过程中麻烦时不时发生)，可以照下图接线：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_020.png)

&emsp;&emsp;这是要特殊对待的情况！

&emsp;&emsp;如果电脑和开发板用网线直连，开发板上的程序，<font color="#dd0000">必须</font>使能网卡！

&emsp;&emsp;否则，Windows里看到的有线网卡就有一个红叉。

&emsp;&emsp;红叉表示"断开",

&emsp;&emsp;都"断开"了你别再问我为什么PING不通。

&emsp;&emsp;所以，

&emsp;&emsp;如果电脑和开发板用网线直连，开发板上的程序，<font color="#dd0000">必须</font>使能网卡：

&emsp;&emsp;a. 如果你要在开发板上玩UBOOT：

&emsp;&emsp;&emsp;&emsp;原生UBOOT是个奇葩，

&emsp;&emsp;&emsp;&emsp;它平时不使能网卡，

&emsp;&emsp;&emsp;&emsp;只有在使用网络命令那一小会，才使能网卡。

&emsp;&emsp;&emsp;&emsp;如果一定要直连，必须更换为"全程使能网卡的UBOOT"，

&emsp;&emsp;&emsp;&emsp;我们提供的JZ2440的uboot，已经全程使能网卡了，

&emsp;&emsp;&emsp;&emsp;你可以在它的前2行打印信息里看到：enable Ethernet alltime。

&emsp;&emsp;&emsp;&emsp;对于其他开发板所用的uboot，它并没有全程使能网卡，

&emsp;&emsp;&emsp;&emsp;对于这种情况，强烈建议不要用网线直接连接开发板和电脑。

	   
&emsp;&emsp;b. 如果你要在开发板上玩LINUX，

&emsp;&emsp;&emsp;&emsp;这个LINUX必须有网卡驱动，必须配置了网卡，

&emsp;&emsp;&emsp;&emsp;可以在开发板里执行类似这样的命令：

 ifconfig eth0 192.168.1.123    
   
#### VMWare里选择有线网卡   
&emsp;&emsp;如果你<font color="#dd0000">只想让Windows和开发板互通</font>，可以不启动VMWare，当然也就不用去设置VMWare和Ubuntu。

&emsp;&emsp;很多电脑有多网卡，比如WIFI网卡、有线网卡。

&emsp;&emsp;在本章所描述的连接情况下，VMWare<font color="#dd0000">必须选择桥</font>接模式、<font color="#dd0000">必须选择</font>有线网卡。

&emsp;&emsp;VMWare选择桥接模式，方法如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_021.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_022.png) 
[//]: # (800px)
#### 设置IP   
&emsp;&emsp;Windows的有线网卡IP、ubuntu的IP、开发板的IP，<font color="#dd0000">三个IP必须处于同一网段</font>。

   
##### 设置Windows IP   
&emsp;&emsp;Windows的IP一般都是自动分配的，在命令行执行：ipconfig，查看它的IP，假设为<font color="#dd0000">192.168.1.10</font>。

如果想修改IP，可以手工修改，具体方法请自行百度搜：“windows 修改 IP”。   
   
##### 设置Ubuntu IP   
&emsp;&emsp;如果只是想让Windows、Ubuntu、开发板三者网络互通，只需要设置Ubuntu的IP。如果想让Ubuntu能上网，还需要设置它的网关、DNS。

&emsp;&emsp;一般来说无需手工设置Ubuntu的网络，<font color="#dd0000">如果、万一</font>Ubuntu的网络不好使，就需要手工设置。

&emsp;&emsp;方法如下(下面第1个图是给root用户设置密码，只需做1次)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_023.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_024.png) 
[//]: # (800px)
##### 设置开发板IP   
&emsp;&emsp;开发板运行UBOOT时、运行LINUX时，它们的IP需要分别设置，这两个阶段的IP没有联系。这两个阶段不会同时运行，所以它们的IP可以相同。

&emsp;&emsp;如果开发板正在运行UBOOT，执行以下命令设置IP：

 set  ipaddr  192.168.2.123   
 save

&emsp;&emsp;如果开发板正在运行LINUX，你可以执行以下命令设置手工IP：

 echo  “ifconfig   eth0  192.168.<font color="#dd0000">2</font>.123”  >> /etc/init.d/rcS   
 reboot   
   
#### 验证   
&emsp;&emsp;① 验证Windows和Ubuntu互通：

&emsp;&emsp;在Windows命令行执行：

 ping  192.168.2.100   // ping Ubuntu，如果有数据返回就表示通了，如下图：   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_025.png)

&emsp;&emsp;&emsp;&emsp;注意：UBOOT不回应PING数据，所以你是PING不通UBOOT的，

&emsp;&emsp;&emsp;&emsp;只能在UBOOT去PING别的电脑。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_026.png)

&emsp;&emsp;③ 验证Windows和“正在运行Linux的开发板”互通(<font color="#dd0000">第②③步，只要做一个就可以</font>)：

&emsp;&emsp;&emsp;&emsp;类似第①步，在Windows命令行下PING开发板的IP。

&emsp;&emsp;&emsp;&emsp;注意，开发板必须启动进入了Linux，它才能被Windows PING通。

   
### 只想让Ubuntu能上网、能跟Windows互联(校园网必看)   
#### VMWare的3种网络模式简介   
&emsp;&emsp;VMWare的网络有3种模式：

&emsp;&emsp;① 桥接模式：

&emsp;&emsp;&emsp;&emsp;在这种模式下，Windows主操作系统、VMWare上运行的Ubuntu操作系统，就相当于2台独立的电脑。

&emsp;&emsp;&emsp;&emsp;如果Windows需要拔号才能上网，那么Ubuntu也需要拔号才能上网。但是一般来说学校、单位没有Ubuntu下的拔号软件。这时，Ubuntu想上网就不能使用桥接模式。

&emsp;&emsp;② NAT模式：

&emsp;&emsp;&emsp;&emsp;VMWare上运行的Ubuntu操作系统，它对外访问时，会使用Windows的IP，这称为共享主机Windows的IP。

&emsp;&emsp;&emsp;&emsp;在这种模式下，只要Windows能上网，Ubuntu就可以上网，无需对Ubuntu进行复杂的设置。

&emsp;&emsp;&emsp;&emsp;但是这种模式下，开发板无法访问Ubuntu，所以不适合进行后续的学习、开发。

&emsp;&emsp;③ 仅主机模式：

&emsp;&emsp;&emsp;&emsp;不太了解，用不上。

   
#### NAT模式适用情况   
&emsp;&emsp;有些校园网客户，或者在有些限制上网的公司里，想上网时必须申请，要拨号、要输入用户名密码。

&emsp;&emsp;有些学员，并不需要使用开发板，不需要三者互通：Windows、VMWare上的Ubuntu、开发板。他只需要两者互通：Windows、VMWare上的Ubuntu。

&emsp;&emsp;有些学员，三者互通的情况下Ubuntu是无法上网的，比如下图的连接方式中Ubuntu就无法上网。但是他需要临时使用Ubuntu上网，比如在Ubuntu中使用atp-get命令安装软件。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_027.png)

&emsp;&emsp;**那么可以设置VMWare使用NAT方式，设置Ubuntu自动获取IP**。

#### NAT模式的使用：VMWare和Ubuntu的设置   
&emsp;&emsp;VMWare选择NAT模式，方法如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_028.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_029.png) 
[//]: # (800px)
#### 测试   
&emsp;&emsp;如果Windows可以上网，那么可以按下图来测试：

&emsp;&emsp;&emsp;&emsp;1. 执行ifconfig命令确定已经自动获取了IP

&emsp;&emsp;&emsp;&emsp;2. 用ping命令连接外网

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_030.png) 
[//]: # (800px)

&emsp;&emsp;&emsp;&emsp;1. 执行ifconfig命令确定已经自动获取了IP

&emsp;&emsp;&emsp;&emsp;2. 用ping命令连接Windows（本例中Windows IP为192.168.1.10）

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_031.png) 
[//]: # (800px)
### Windows和Ubuntu之间远程操作   
&emsp;&emsp;Windows和Ubuntu之间网络互通之后，就可以在Windows远程登录Ubuntu并执行各种Linux命令了，还可以使用FileZilla在两者之间传输文件。

&emsp;&emsp;能执行本文操作的前提是Ubuntu上已经安装好了ssh服务。

&emsp;&emsp;如果你使用的是我们提供的Ubuntu系统，ssh服务已经安装好了；

&emsp;&emsp;否则你需要确保Ubuntu能上网，然后执行下面命令来安装ssh服务：

 sudo apt-get install openssh-server   
#### 使用MobaXterm登录Ubuntu   
&emsp;&emsp;**注意：**本章假设Ubuntu的IP为192.168.1.100，你可以登录Ubuntu桌面系统后启动terminal，执行ifconfig命令查看该IP。

&emsp;&emsp;&emsp;&emsp;1)打开MobaXterm，点击左上角的**Session**（会话控制），在弹出的窗口中选择SSH，如下图所示。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_032.png) 
[//]: # (800px)

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_033.png) 
[//]: # (800px)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;你可以把Windows下的文件直接拖到左边显示的Ubuntu目录中，也可以从里面把文件拖出来。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_034.png) 
[//]: # (800px)
#### 使用FileZilla与Ubuntu传输文件   
&emsp;&emsp;安装、运行FileZilla后，如下图依次输入Ubuntu的IP（192.168.1.100）、用户名（book）、端口号（<font color="#dd0000">22</font>），单击“快速链接”即可。

&emsp;&emsp;注意：必须输入端口号<font color="#dd0000">22</font>，这对应SFTP。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_035.png) 
[//]: # (800px)
### 开发板和Ubuntu之间传文件   
&emsp;&emsp;开发板和Ubuntu之间网络互通之后，就可以在两者之间传输文件了。

   
#### 开发板通过NFS挂载Ubuntu的目录   
&emsp;&emsp;开发板上不一定安装有FTP服务、SSH等服务，所以不一定能使用FTP等工具登录开发板。

&emsp;&emsp;但是开发板的系统一般都自带mount命令，并且支持NFS文件系统。所以可以在开发板上执行mount命令挂载ubuntu的某个目录。这样就可以在开发板和Ubuntu之间传文件了。

&emsp;&emsp;开发板使用NFS挂载Ubuntu的前提是：Ubuntu中已经安装了NFS服务，并且在/etc/exports中配置了某个目录供挂载。

##### 在Ubuntu上安装、配置NFS服务   
&emsp;&emsp;如果你使用的是我们提供的Ubuntu，那么已经安装好了NFS服务。

&emsp;&emsp;如果你的Ubuntu未安装NFS服务，那么在确保Ubuntu可以上网的前提下，执行以下命令：

 sudo apt-get install nfs-kernel-server   
&emsp;&emsp;然后，还得修改<font color="#dd0000">/etc/exports</font>，添加类似以下的内容，下面的例子里允许开发板通过NFS访问Ubuntu的/home/book、/work两个目录：

 /home/book   *(rw,nohide,insecure,no_subtree_check,async,no_root_squash)   
 /work       *(rw,nohide,insecure,no_subtree_check,async,no_root_squash)   
&emsp;&emsp;最后，重启NFS服务，在Ubuntu上执行以下命令：

 sudo /etc/init.d/nfs-kernel-server restart   
&emsp;&emsp;可以在Ubuntu上通过NFS挂载自己，验证一下NFS可用：

 sudo  mount  -t  nfs  -o  nolock,vers=3  127.0.0.1:/home/book   /mnt   
 ls  /mnt   
##### 在开发板上挂载Ubuntu的NFS文件系统   
&emsp;&emsp;确保开发板可以ping通Ubuntu后，就可以通过NFS挂载Ubuntu中的某个目录。

&emsp;&emsp;哪些目录呢？请查看Ubutnu的/etc/exports文件。

&emsp;&emsp;假设Ubuntu的IP为：192.168.1.100，在开发板上可以执行下面的命令挂载Ubuntu的/home/book目录到开发板的/mnt目录：

  mount  -t  nfs  -o  nolock,vers=2  192.168.1.100:/home/book   /mnt   
  // 如果不成功，就把vers=2改为vers=3或vers=4   
  mount  -t  nfs  -o  nolock,vers=3  192.168.1.100:/home/book   /mnt   
&emsp;&emsp;如果一切正常，你可以在Ubuntu上把文件放到/home/book目录中，在开发板上可以从/mnt目录中访问该文件。

   
### 常见问题   
&emsp;&emsp;**1. Windows上有多个网卡时，三者(Windows/Ubuntu/开发板)无法互通：**


&emsp;&emsp;&emsp;&emsp;<font color="#dd0000">答：</font>**可能原因有两个。


&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① VMWare中要选择桥接模式，并且桥接模式下要选择正确的网卡。选择哪一个网卡，请看第1、2、3章中的各种情况。


&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② Windows上安装了杀毒软件，或是启用了防火墙。在这种情况下，只要Windows能ping通Ubuntu、ping通开发板就可以了。不必强求Ubuntu和开发板都能ping通Windows，这不影响使用。


&emsp;&emsp;**2. vmnetcfg中没有桥接模式，如下图：**


![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_036.png) 
[//]: # (800px)

&emsp;&emsp;**3. VMWare中无法选择NAT模式：**


![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_037.png) 
[//]: # (800px)


![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/VMwareAndUbuntuNetworkSetupGuide_038.png) 
[//]: # (800px)
&emsp;&emsp;**4. 开发板和电脑用网线直连，问题多**


&emsp;&emsp;&emsp;&emsp;<font color="#dd0000">答：</font>不要直连，中间接一个HUB或路由器


   
## 开发工具的使用   
&emsp;&emsp;在开发过程中，我们会用到如下工具：

&emsp;&emsp;&emsp;&emsp;1. MobaXterm：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;可以用来远程登录Ubuntu，可以操作串口。

&emsp;&emsp;&emsp;&emsp;2. FileZilla：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;Windows和Ubuntu之间转文件的工具。

&emsp;&emsp;&emsp;&emsp;3. SourceInsight：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;源码阅读、编辑工具，非常好用。特别是在分析大型软件时，它能提供函数、变量的快速查找、跳转功能。

&emsp;&emsp;&emsp;&emsp;4. Notepad++：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;文本编辑工具，比记事本好用。

   
### MobaXterm的使用   
&emsp;&emsp;&emsp;&emsp;MobaXterm是您远程计算的终极工具箱。在单个Windows应用程序中，它提供了大量功能，这些功能是为程序员、网站管理员、IT管理员以及需要以更简单的方式处理远程作业的所有用户量身定制。

&emsp;&emsp;&emsp;&emsp;MobaXterm向Windows桌面提供所有重要的远程网络工具（SSH，X11，RDP，VNC，FTP，MOSH ......）和Unix命令（bash，ls，cat，sed，grep，awk，rsync）等，仅仅需要一个exe文件即可。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;MobaXterm官网    [https://mobaxterm.mobatek.net/](https://mobaxterm.mobatek.net/)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;MobaXterm使用文档 [https://mobaxterm.mobatek.net/documentation.html](https://mobaxterm.mobatek.net/documentation.html)

   
#### 安装及获取ubuntuIP地址   
&emsp;&emsp;解压文件“MobaXterm_Portable_v10.4.zip”即可。第一次打开会自解压比较慢，后续就正常了。

&emsp;&emsp;使用MobaXterm是为了远程登录Vmware里的Ubuntu，所以需要先知道Ubuntu的IP。方法如下图所示。点击Ubuntu桌面左上角图标，输入“term”可以得到图中蓝框中的“Terminal”程序，运行它；然后执行“ifconfig”命令即可查看Ubuntu的IP。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_028.png) 
<600px>
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_029.png) 
<600px>

&emsp;&emsp;在terminal即终端中，输入ifconfig命令查看IP(如果未设置IP，请参考前面章节《虚拟机和Ubuntu的网络设置》)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_030.png) 
<600px>

&emsp;&emsp;**注意**：本文档假设Ubuntu的IP为192.168.1.132，在上图中网卡为ens33(**你的电脑上网卡名也许不一样**)。

   
#### 新建SSH连接   
&emsp;&emsp;打开MobaXterm，点击左上角的Session（会话控制），在弹出的窗口中选择SSH，如面两个图所示。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_031.png) 
<400px>
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_032.png) 
<400px>
&emsp;&emsp;然后在弹出操作框里输入Ubuntu的IP和端口号（默认是22），点击“OK”，如下图：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_033.png) 
<600px>
    
&emsp;&emsp;最后，在新窗口中输入账号“book”和密码“123456”，再按下键盘“回车键”登陆Ubuntu。

&emsp;&emsp;此时界面分为两块，左边的是主机的文件，右边是终端。勾选左下角的“Follow terminal folder”可以让它们的工作路径保持一致，如下图所示。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_034.png) 
<600px>
   
#### 新建串口连接   
&emsp;&emsp;在后面的操作里，都是通过串口与板子进行“交流”。串口是串行接口的简称，是指数据一位一位地顺序传送，其特点是通信线路简单。

&emsp;&emsp;在电脑上安装好MobaXterm后，接上USB串口模块，并跟开发板连好线。

&emsp;&emsp;在MobaXterm里敲打键盘，就会通过USB 串口模块，将数据经过TTL延长线传给板子，板子就能接收到我们在电脑上发送的数据。

&emsp;&emsp;反过来，板子发送的数据首先经过TTL延长线到USB串口模块，MobaXterm读取数据后显示出来。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_035.png) 
<600px>
   
##### 安装USB串口模块驱动   
&emsp;&emsp;将USB转RS232/TTL串口模块插在电脑USB上，此时Windows会自动安装驱动(安装可能比较慢，等一分钟左右)。打开电脑的“设备管理器”，在“端口 (COM和LPT)”项下，可以看到如图2.2.4.2中的“USB Serial Port(COM3)”。这里的“COM3”可能与你电脑上的不一样，记住你电脑显示的数字。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_036.png) 
<600px>
如果电脑没有显示出端口号，就需要手动安装驱动，从驱动精灵官网（[www.drivergenius.com www.drivergenius.com]）下载一个驱动精灵，安装、运行、检测，会自动安装上串口驱动。   
   
   
##### 连接串口线   
&emsp;&emsp;以RK3399开发板为例，对于其他开发板，请看对应开发板的高级用户使用手册。

&emsp;&emsp;首先如下图所示将串口模块与电脑、板子连接。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_037.png) 
<600px>

&emsp;&emsp;其中特别需要注意的几点：

&emsp;&emsp;&emsp;&emsp;a)	串口模块的电平选择开关拨到如图所示的右边，表示切换到TTL电平；

&emsp;&emsp;&emsp;&emsp;b)	串口模块附赠两条排线，使用较宽的那根(2.54mm间距)连接串口模块最中间接口；

&emsp;&emsp;&emsp;&emsp;c)	排线的另一端插在ROC-RK3399-PC如图所示位置，注意黑线(GND)在图中最右边；

&emsp;&emsp;&emsp;&emsp;d)	该处没有eMMC，在该处背面的TF卡槽里，需要插上烧写好系统的TF卡；

&emsp;&emsp;&emsp;&emsp;e)	板子如图所示准备好配套的电源，注意是在图中的左边Type C接口，因为没有电源开关，插上就自动启动了，所以暂时先不插电源；

   
##### 设置使用MobaXterm   
&emsp;&emsp;打开MobaXterm，点击左上角的“Session”，在弹出的界面选中“Serial”，如下图所示选择端口号（前面设备管理器显示的端口号COM3）、波特率（Speed 115200）、流控（Flow Control: none）,最后点击“OK”即可。

&emsp;&emsp;注意：流控（Flow Control）一定要选择none，否则你将无法在MobaXterm中向串口输入数据。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_038.png) 
<600px>

&emsp;&emsp;随后显示一个黑色的窗口， 此时打开板子的电源开关，将收到板子串口发过来的数据，如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_039.png) 
<600px>
   
### 使用FileZilla在Windows和Ubuntu之间传文件   
&emsp;&emsp;MobaXterm支持FTP和SFTP连接。FTP安全性没有SFTP好，但速度比SFTP快，可根据自己需求选择适当的协议。MobaXterm的FTP、SFTP有时不稳定，我们推荐使用另一个软件FileZilla来传输文件。

&emsp;&emsp;注意：我们提供的ubuntu中没有安装FTP服务，你只能使用SFTP服务；后文提到的TFTP服务，跟FTP服务是不一样的，U-Boot可以使用TFTP从Ubuntu下载文件。

&emsp;&emsp;安装、运行FileZilla后，如下图依次输入Ubuntu的IP（192.168.1.132）、用户名（book）、端口号（22），单击“快速链接”即可。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_040.png) 
<800px>

&emsp;&emsp;成功连接Ubuntu后，即可在FileZilla到左、右两边的窗口中，互相传输文件了。

   
### 使用SourceInsight阅读、编辑源码   
&emsp;&emsp;Source Insight是Source Dynamics 的源代码编辑器。Source Insight提供语法突出显示，代码导航和可自定义的键盘快捷键。它不仅仅是一个编辑器，而是一个理解大型源代码库的工具，因此被称为“程序编辑器和分析器”。它灵活轻便，提供有用的功能，如关系，上下文和符号窗口。它还可以显示引用树，类继承图和调用树，因为它在自解析源时构建了符号信息的内部数据库。它的最大好处是加快了对不熟悉项目的代码理解。

&emsp;&emsp;&emsp;&emsp;官网主页 [https://www.sourceinsight.com/](https://www.sourceinsight.com/)

&emsp;&emsp;&emsp;&emsp;软件下载页面 [https://www.sourceinsight.com/trial/](https://www.sourceinsight.com/trial/)

&emsp;&emsp;&emsp;&emsp;用户使用教程 [https://www.sourceinsight.com/doc/v4/userguide/index.html](https://www.sourceinsight.com/doc/v4/userguide/index.html)

   
#### 安装并新建项目   
&emsp;&emsp;安装并运行(资料光盘\ 01_tools)中的sourceinsight4098-setup.exe文件。

&emsp;&emsp;这里我们以新建一个linux kernel的sourceInsight为例，对sourceInsight使用进行说明。

&emsp;&emsp;&emsp;&emsp;1)	在桌面上找到sourceInsight双击运行，点击Project->New Project,如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_041.png) 
<800px>

&emsp;&emsp;&emsp;&emsp;2)	在弹出的New Project对话框中设置New project name(项目的名称)让后设置Where do you want to store the project data file? (项目文件保存位置)，点击Browse…选择所在保存位置，设置好以后点击OK 如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_042.png) 
<800px>

&emsp;&emsp;&emsp;&emsp;3)	指定源文件所在目录：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;源文件的目录就是内核源码所在目录，点击红框左边 … 选择源码目录，点击OK：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_043.png) 
<800px>

&emsp;&emsp;&emsp;&emsp;4)	添加源文件

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;点击Add或Add All; 其中Add是手动选择需要添加的文件，而Add All是添加所有文件。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;一般我们选择Add All,在弹出的提示框中选中Recursively add lower sub-directories(递归添加下级的子目录)点击OK。同样的Remove File,Remove All是移除单个文件或者移除所有文件。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_044.png) 
<800px>
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_045.png) 
<400px>
&emsp;&emsp;&emsp;&emsp;5)	添加文件完成后点击Close，此时界面会返回到主界面

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_046.png) 
<800px>

&emsp;&emsp;&emsp;&emsp;6)	接下来我们需要点击Synchronize File(同步文件)		

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_047.png) 
<800px>

&emsp;&emsp;&emsp;&emsp;7)	强制解析所有文件：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;在弹出的Synchronize File对话框中 选中Force all files to be re-parsed ()，这一步会生成所有代码之间的调用关系等,等待一段时间后会返回到主页面，此时就可以阅读代码了。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_048.png) 
<800px>
   
   
#### Source Insight操作示例   
&emsp;&emsp;1) 点击“P”图标，可以在列表中输入想打开的文件，双击打开文件：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_049.png) 
<800px>

&emsp;&emsp;2) 鼠标放在函数、变量上"ctrl+鼠标点击" 跳到定义的位置。

&emsp;&emsp;3) 双击函数, 键盘按住"ctrl +/"即可弹出窗口，这是查找引用：
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterTwo_050png.png) 
<800px>  
   
#### Source Insight快捷键   
&emsp;&emsp;双击某个函数或变量，点击右键，即可看到很多快捷键。

|   快捷键   | 说明 <br>
|-----------|-----------<br>
| Alt + , 	| 退回上一步操作的位置<br>
| Alt + . 	| 前进到下一步操作的位置<br>
| F5 		| 显示行号<br>
| F8 		| 高亮显示代码或变量，再次按F8取消高亮显示<br>
| Ctrl + = 	| 跳到所选中的函数或变量的定义位置<br>
| Ctrl + / 	| 查找所选中的函数或变量的引用<br>
  

