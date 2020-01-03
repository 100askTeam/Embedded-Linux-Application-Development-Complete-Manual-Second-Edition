# 第一篇. 韦东山全系列视频介绍及资料下载   
## 全系列视频介绍   
### 嵌入式Linux发展迅速，学习方法要与时俱进   
#### 嵌入式Linux变化巨大   
&emsp;&emsp;200x年左右，嵌入式 Linux 在全世界、在中国刚刚兴起。<br>

&emsp;&emsp;我记得我2005年进入中兴时，全部门的人正在努力学习Linux。<br>

&emsp;&emsp;在 2008 年，我写了一本书《嵌入式Linux应用开发完全手册》。<br>

&emsp;&emsp;它的大概内容是：裸机、U-boot、Linux内核、Linux设备驱动。<br>

&emsp;&emsp;那时还没有这样讲解整个系统的书，<br>

&emsp;&emsp;芯片厂家 Linux 开发包也还不完善，从 bootloader 到内核，再到设备驱动都不完善。<br>

&emsp;&emsp;有全系统开发能力的人也很少。<br>

&emsp;&emsp;于是这书也就恰逢其时，变成了畅销书。<br>

&emsp;&emsp;我也根据这个思路录制了视频：裸机、U-boot、Linux内核、Linux设备驱动。<br>

&emsp;&emsp;收获些许名声，带领很多人进入Linux世界。

&emsp;&emsp;11年过去了，嵌入式 Linux 世界发生了翻天覆地的变化：<br>

##### 基本系统能用   
&emsp;&emsp;芯片厂家都会提供完整的 U-boot 、 Linux 内核、芯片上硬件资源的驱动。<br>

&emsp;&emsp;方案厂家会做一些定制，比如加上某个 WIFI 模块，会添加这个 WIFI 模块的驱动。<br>

&emsp;&emsp;你可以使用厂家的原始方案，或是使用/借鉴方案商的方案，做出一个“能用”的产品。<br>

##### 基础驱动弱化；高级驱动专业化   
&emsp;&emsp;基础的驱动，比如GPIO、UART、SPI、I2C、LCD、MMC等，有了太多的书籍、视频、示例代码，修修改改总是可以用的。<br>

&emsp;&emsp;很多所谓的驱动工程师，实际上就是“调参工程师”。<br>

&emsp;&emsp;我们群里有名的火哥，提出了一个概念：这些驱动就起一个 <font color="#dd0000">“hardware enable”</font> 的作用。

&emsp;&emsp;高级的驱动，比如USB、PCIE、HDMI、MIPI、GPU、WIFI、蓝牙、摄像头、声卡。<br>

&emsp;&emsp;体系非常复杂，很少有人能讲清楚，很多时候只是一笔带过。<br>

&emsp;&emsp;配置一下应用层工具就了事，能用就成。<br>

&emsp;&emsp;这些高级驱动，工作中需要专门的人来负责，非常专业。<br>

&emsp;&emsp;他们是某一块的专家，比如摄像头专家、音频专家。<br>

   
##### 项目为王   
&emsp;&emsp;你到一个公司，目的是把产品做出来，会涉及 APP 到内核到驱动全流程。<br>

&emsp;&emsp;中小公司玩不起华为中兴的配置，需要的是全面手。<br>

&emsp;&emsp;大公司里，只负责很小很小一块的镙丝钉，位置也不太稳固啊。<br>

&emsp;&emsp;所以，如果你不是立志成为某方面的专家，那就做一个全栈工程师吧。<br>

##### 调试很重要   
&emsp;&emsp;都说代码是 3 分写 7 分调，各种调试调优技术，可以为你的升职加薪加一把火。<br>

#### 嵌入式 Linux 的学习方法要与时俱进   
&emsp;&emsp;基于上述4点，<br>

&emsp;&emsp;再从裸机、 U-boot 、内核、驱动这样的路线学习就不适合了，时间就拉得太长了。<br>

&emsp;&emsp;录制的全新视频将有这些特点：<br>

&emsp;&emsp;&emsp;&emsp;① 快速入门，<br>

&emsp;&emsp;&emsp;&emsp;② 实战项目，<br>

&emsp;&emsp;&emsp;&emsp;③ 驱动大全，<br>

&emsp;&emsp;&emsp;&emsp;④ 专题，<br>

&emsp;&emsp;&emsp;&emsp;⑤ 授人以渔，<br>

&emsp;&emsp;&emsp;&emsp;⑥ 要做任务<br>

&emsp;&emsp;另外，我们会使用多款芯片同时录制，先讲通用的原理，再单独讲各个板子的操作。<br>

&emsp;&emsp;这些芯片涵盖主流芯片公司的主流芯片，让你学习工作无缝对接。<br>

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterOne_001.png)
<800px>
   
### 快速入门   
&emsp;&emsp;入门讲究的是快速，入门之后再慢慢深入，<br>

&emsp;&emsp;特别是对于急着找工作的学生，对于业余时间挑灯夜读的工作了的人，一定要快！<br>

&emsp;&emsp;再从裸机、 U-boot 、内核、驱动这样的路线学习就不适合了，时间就拉得太长了。<br>

&emsp;&emsp;搞不好学了后面忘了前面。<br>

&emsp;&emsp;并且实际工作中并不需要你去弄懂 U-boot ，会用就行： U-boot 比驱动还复杂。<br>

   
#### 讲哪些内容？   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterOne_002.png)
<800px>

#### 怎么讲呢？   
&emsp;&emsp;<font color="#dd0000">① 混着讲：</font><br>

&emsp;&emsp;&emsp;&emsp;比如先讲 LED APP，知道 APP 怎么调用驱动，再讲LED硬件原理和裸机，最后讲驱动的编写。<br>

&emsp;&emsp;&emsp;&emsp;这样可以快速掌握嵌入式Linux的整套开发流程，<br>

&emsp;&emsp;&emsp;&emsp;不必像以前那样光学习裸机就花上1、2个月。

&emsp;&emsp;&emsp;&emsp;而里面的裸机课程，也会让你在掌握硬件操作的同时，把单片机也学会了。<br>

&emsp;&emsp;② 讲<font color="#dd0000">基础技能：</font><br>

&emsp;&emsp;&emsp;&emsp;中断、休眠-唤醒、异步通知、阻塞、内存映射等等机制，会配合驱动和APP来讲解。<br>

&emsp;&emsp;&emsp;&emsp;这些技能是嵌入式 Linux 开发的基础。<br>

&emsp;&emsp;&emsp;&emsp;而这些驱动，只会涉及LED、按键、LCD等几个驱动。<br>

&emsp;&emsp;&emsp;&emsp;掌握了这些输入、输出的驱动和对应的APP后，你已经具备基本的开发能力了。

&emsp;&emsp;③ 讲<font color="#dd0000">配置</font><br>

&emsp;&emsp;&emsp;&emsp;我们从厂家、从方案公司基本上都可以拿到一套完整的开发环境，怎么去配置它？<br>

&emsp;&emsp;&emsp;&emsp;需要懂shell和python等配置脚本。

&emsp;&emsp;<font color="#dd0000">④ 效果效率优先</font><br>

&emsp;&emsp;&emsp;&emsp;以前我都是现场写代码、现场写文档，字写得慢，降低了学习效率。<br>

&emsp;&emsp;&emsp;&emsp;这次，效果与效率统一考虑，<font color="#dd0000">不再追求所有东西都现场写</font>。<br>

&emsp;&emsp;&emsp;&emsp;容易的地方可先写好代码文档，难的地方现场写。<br>

   
### 实战项目   
&emsp;&emsp;会讲解这样的项目(不限于，请多提建议)：<br>

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterOne_003.png) 
<600px> 

&emsp;&emsp;定位为：快速掌握项目开发经验，丰满简历。

&emsp;&emsp;涉及的每一部分都会讲，比如如果涉及蓝牙，在这里只会讲怎么使用，让你能写出程序；如果要深入，可以看后面的蓝牙专题。<br>

   
### 驱动大全   
&emsp;&emsp;包括基础驱动、高级驱动。<br>

&emsp;&emsp;这些驱动都是独立成章，深入讲解。

&emsp;&emsp;虽然基础驱动弱化了，但是作为 Linux 系统开发人员，这是<font color="#dd0000">必备技能</font>，并且<font color="#dd0000">从驱动去理解内核是一个好方法</font>。<br>

&emsp;&emsp;在讲解这些驱动时，会把驱动的运行环境，比如内核调度，进程线程等概念也讲出来，这样就可以搭建一个知识体系。<br>

&emsp;&emsp;没有这些知识体系的话，对驱动的理解就太肤浅了，等于在 Linux 框架下写裸机，一叶障目，不见泰山。

&emsp;&emsp;定位为：工具、字典，用到再学习。<br>

### 专题   
&emsp;&emsp;想深入学习的任何内容，都可独立为专题。<br>

&emsp;&emsp;比如 U-boot 专题、内核内存管理专题、 systemtap 调试专题。<br>

   
## 资源下载方法   
### GIT使用简明教程   
#### 安装git   
&emsp;&emsp;Windows下的 git 名为 msysGit ，从 [http://msysgit.github.io/](http://msysgit.github.io/) 上下载安装文件，双击安装即可。

&emsp;&emsp;在Ubuntu下，执行以下命令即可，它会从网上下载安装 git （在我们发布的 Ubuntu 虚拟机里，已经安装有 git ，无需再次安装）：<br>

```bash
   $ sudo apt-get install git   
```
&emsp;&emsp;对于 Windows 或 Linux ，它们的命令行用法相似；对于Windows，进入Git命令行的方法是在“开始”->“所有程序”->“Git”下启动 Git Bash 。<br>

&emsp;&emsp;Git Bash 的命令用法跟 Linux 完全一样，比如 cd 、 ls 等命令。<br>

   
#### 使用示例：获得全部源码   
&emsp;&emsp;使用 git 下载资料，需要先知道 git 仓库的地址。比如下面 2 个地址都保存有“韦东山全系列视频第1季快速入门”的文档及源码，分别是国外、国内的网站：<br>

```bash
   https://github.com/100askTeam/01_all_series_quickstart.git   
   https://git.dev.tencent.com/weidongshan/01_all_series_quickstart.git   
```
&emsp;&emsp;要获取本季视频对应的资料，可以执行以下命令，这称为“克隆”，这会得到一个名为 01_all_series_quickstart 的目录：<br>

```bash
   git clone https://git.dev.tencent.com/weidongshan/01_all_series_quickstart.git   
```
&emsp;&emsp;如果在你“克隆”之后，我们又更新了源码，你可以先进入该目录，然后更新。<br>

&emsp;&emsp;启动 git bash 后，使用 cd 命令可以切换目录。假设要进入 D:\abc\01_all_series_quickstart 目录，可以执行以下命令：<br>

```bash
   cd  /D   
   cd abc   
   cd 01_all_series_quickstart   
```   
&emsp;&emsp;也可以执行一个命令直接进入该目录，注意目录分隔符是“/”而非“\”：<br>

```bash
   cd  /D/abc/01_all_series_quickstart   
```
   
&emsp;&emsp;在 01_all_series_quickstart 目录下，执行以下命令获得最新版本：<br>

```bash
   git pull origin   
```
### 百度网盘使用教程   
&emsp;&emsp;注意：千万不要在浏览器上直接下载。<br>

&emsp;&emsp;正确的使用方法是：先转存到自己的网盘，再用客户端下载。<br>

#### 注册   
&emsp;&emsp;a. 注册百度网盘帐号<br>

&emsp;&emsp;b. 也许现在还可以免费获赠大空间，在手机上下载百度网盘APP、登录试试<br>

#### 转存   
&emsp;&emsp;a. 在电脑上，使用浏览器打开pan.baidu.com，并登录你的网盘帐号<br>

&emsp;&emsp;b. 在浏览器中，打开我们提供的网盘链接，选择你要下载到文件夹，转存到到你的网盘<br>

#### 下载   
&emsp;&emsp;a. 在电脑上安装百度网盘客户端，在客户端中登录，在客户端中找到文件夹并下载<br>

### 本教程所有资料介绍   
&emsp;&emsp;本教程的资料有三部分：<br>

#### 录制视频过程中编写的文档、源码   
&emsp;&emsp;&emsp;&emsp;存放在github和腾迅云中，使用git命令下载、更新。<br>

&emsp;&emsp;&emsp;&emsp;下载方法请参考下面第4节，内容如下图所示：<br>

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterOne_004.png)
<400px>

&emsp;&emsp;&emsp;&emsp;01_arduino_manual ：各开发板的Arduino手册<br>

&emsp;&emsp;&emsp;&emsp;02_basic_operation_for_linux_and_tools ：“Linux基本操作与开发工具使用”视频对应的文档<br>

&emsp;&emsp;&emsp;&emsp;03_development_manual ：各开发板的高级用户使用手册<br>

&emsp;&emsp;&emsp;&emsp;04_quickstart_doc_src ：全系列视频第1季快速入门，对应的文档、源码<br>

   
#### 录制的视频 和 开发板的 BSP 包   
&emsp;&emsp;这 2 部分资料都存放在百度网盘中，链接地址：[http://wiki.100ask.org/Download](http://wiki.100ask.org/Download)<br>

&emsp;&emsp;打开上述链接地址后，可以找到这2项：<br>

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterOne_005.png) 
(400px)   

&emsp;&emsp;上图中，第1项是录制的视频，应该全部下载来学习。第2项是各个开发板的BSP包，里面含有原理图、芯片手册、源代码、各种工具、VMWare的Ubuntu映象文件等，你使用哪个开发板就下载对应的BSP包。

&emsp;&emsp;每一个开发板都有对应的配套资料，你只需要下载自己所用板子对应的资料。<br>

&emsp;&emsp;<font color="#dd0000">注意</font>，对于 RK3288 、 RK3399 ，我们也提供了 firefly 的 BSP 包，仅供熟练开发者使用，初学者不需要下载。<br>

&emsp;&emsp;每一种开发板需要下载的配套资料在下图中用红框圈起来了：<br>

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterOne_006.png) 
(400px)   
   
   
### 手册、文档、源码的下载与更新   
&emsp;&emsp;在Windows下启动git bash，执行下述命令：<br>

&emsp;&emsp;&emsp;&emsp;a. 第1次下载：<br>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;执行以下命令之一，哪个速度快你使用哪一个：<br>

```bash
         git clone https://github.com/100askTeam/01_all_series_quickstart.git   
         git clone https://git.dev.tencent.com/weidongshan/01_all_series_quickstart.git   
```
   
&emsp;&emsp;&emsp;&emsp;b. 更新：<br>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;随着视频的录制，会发布更多的文档、源码，可以使用进入01_all_series_quickstart目录，执行下面的命令获得最新版本。假设该目录绝对路径为D:\abc\01_all_series_quickstart：<br>

```bash
         cd  /D/abc/01_all_series_quickstart   
         git pull origin   
```

