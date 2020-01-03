# 第五篇. 嵌入式Linux驱动开发基础知识   
## <font color="#dd0000">嵌入式后Linux驱动开发基础知识</font>的引导与说明   
### 打算讲什么、怎么讲？   
&emsp;&emsp;以几个简单的驱动程序，讲解嵌入式Linux驱动的框架，了解驱动开发的流程、方法，掌握从APP到驱动的调用流程。

&emsp;&emsp;会涉及很多种开发板，让你明白“Linux驱动 = 软件框架 + 硬件操作”，让你“一通百通”，掌握了普适性的原理之后，在工作中很容易在其他板子使用这些知识。

&emsp;&emsp;以LED驱动为例，会如下讲解：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_001.png) 
<600px>
   
### 需要做什么准备工作   
&emsp;&emsp;驱动程序依赖于Linux内核，你为开发板A开发驱动，那就先在Ubuntu中得到、配置、编译开发板A所使用的Linux内核。

&emsp;&emsp;请使用git下载本教程的文档、源码，查看如下目录中你所用开发板的高级用户使用手册(有些开发板的手册我们还没编写完，持续更新)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_002.png) 
<600px>

&emsp;&emsp;根据手册完成下面操作：

&emsp;&emsp;&emsp;&emsp;硬件部分：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 开发板接线：串口线、电源线、网线

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② 开发板烧写系统

&emsp;&emsp;&emsp;&emsp;软件部分：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 下载Linux内核，Windows和Ubuntu下各放一份

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② Windows下：使用Source Insight创建内核源码的工程，这是用来浏览内核、编辑驱动

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;③ Ubuntu下：安装工具链，配置、编译Linux内核

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<font color="# dd0000">注意：</font>git的使用方法请参考http://wiki.100ask.net中的“初学者学习路线”：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_003.png) 
<400px>
   
   
## Hello驱动(不涉及硬件操作)   
&emsp;&emsp;我们选用的内核都是4.x版本，操作都是类似的：

```bash
	rk3399   linux 4.4.154   
	rk3288   linux 4.4.154   
	imx6ul   linux 4.9.88   
	am3358  linux 4.9.168   
```
   
### APP打开的文件在内核中如何表示   
&emsp;&emsp;APP打开文件时，可以得到一个整数，这个整数被称为文件句柄。对于APP的每一个文件句柄，在内核里面都有一个“struct file”与之对应。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_004.png) 
<400px>

&emsp;&emsp;可以猜测，我们使用open打开文件时，传入的flags、mode等参数会被记录在内核中对应的struct file结构体里(f_flags、f_mode)：

```bash
	int open(const char *pathname, int flags, mode_t mode);   
```
   
&emsp;&emsp;去读写文件时，文件的当前偏移地址也会保存在struct file结构体的f_pos成员里。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_005.png) 
<400px>
   
   
### 打开字符设备节点时，内核中也有对应的struct file   
&emsp;&emsp;注意这个结构体中的结构体：struct file_operations *f_op，这是由驱动程序提供的。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_006.png) 
<400px>
   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_007.png) 
<600px>

&emsp;&emsp;结构体struct file_operations的定义如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_008.png) 
<600px>
   
   
### 请猜猜怎么编写驱动程序   
&emsp;&emsp;① 确定主设备号，也可以让内核分配

&emsp;&emsp;② 定义自己的file_operations结构体

&emsp;&emsp;③ 实现对应的drv_open/drv_read/drv_write等函数，填入file_operations结构体

&emsp;&emsp;④ 把file_operations结构体告诉内核：register_chrdev

&emsp;&emsp;⑤ 谁来注册驱动程序啊？得有一个入口函数：安装驱动程序时，就会去调用这个入口函数

&emsp;&emsp;⑥ 有入口函数就应该有出口函数：卸载驱动程序时，出口函数调用unregister_chrdev

&emsp;&emsp;⑦ 其他完善：提供设备信息，自动创建设备节点：class_create, device_create

   
### 请不要啰嗦，表演你的代码吧   
#### 写驱动程序   
&emsp;&emsp;参考driver/char中的程序，包含头文件，写框架，传输数据：

&emsp;&emsp;&emsp;&emsp;A. 驱动中实现open, read, write, release，APP调用这些函数时，都打印内核信息

&emsp;&emsp;&emsp;&emsp;B. APP调用write函数时，传入的数据保存在驱动中

&emsp;&emsp;&emsp;&emsp;C. APP调用read函数时，把驱动中保存的数据返回给APP

&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
		01_all_series_quickstart\04_快速入门(正式开始)\   
			02_嵌入式Linux驱动开发基础知识\source\01_hello_drv\hello_drv.c   
```
   
&emsp;&emsp;hello_drv.c源码如下：

``` C
	# include <linux/module.h>   
   
	# include <linux/fs.h>   
	# include <linux/errno.h>   
	# include <linux/miscdevice.h>   
	# include <linux/kernel.h>   
	# include <linux/major.h>   
	# include <linux/mutex.h>   
	# include <linux/proc_fs.h>   
	# include <linux/seq_file.h>   
	# include <linux/stat.h>   
	# include <linux/init.h>   
	# include <linux/device.h>   
	# include <linux/tty.h>   
	# include <linux/kmod.h>   
	# include <linux/gfp.h>   
   
	/* 1. 确定主设备号 */   
	static int major = 0;   
	static char kernel_buf[1024];   
	static struct class *hello_class;   
   
   
	# define MIN(a, b) (a < b ? a : b)   
   
	/* 3. 实现对应的open/read/write等函数，填入file_operations结构体 */   
	static ssize_t hello_drv_read (struct file *file, char __user *buf, size_t size, loff_t *offset)   
	{   
	    int err;   
	    printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	    err = copy_to_user(buf, kernel_buf, MIN(1024, size));   
	    return MIN(1024, size);   
	}   
   
	static ssize_t hello_drv_write (struct file *file, const char __user *buf, size_t size, loff_t *offset)   
	{   
	    int err;   
	    printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	    err = copy_from_user(kernel_buf, buf, MIN(1024, size));   
	    return MIN(1024, size);   
	}   
   
	static int hello_drv_open (struct inode *node, struct file *file)   
	{   
	    printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	    return 0;   
	}   
   
	static int hello_drv_close (struct inode *node, struct file *file)   
	{   
	    printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	    return 0;   
	}   
   
	/* 2. 定义自己的file_operations结构体 */   
	static struct file_operations hello_drv = {   
	    .owner   = THIS_MODULE,   
	    .open   = hello_drv_open,   
	    .read   = hello_drv_read,   
	    .write   = hello_drv_write,   
	    .release = hello_drv_close,   
	};   
   
	/* 4. 把file_operations结构体告诉内核：注册驱动程序 */   
	/* 5. 谁来注册驱动程序啊？得有一个入口函数：安装驱动程序时，就会去调用这个入口函数 */   
	static int __init hello_init(void)   
	{   
	    int err;   
   
	    printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	    major = register_chrdev(0, "hello", &hello_drv);  /* /dev/hello */   
   
   
	    hello_class = class_create(THIS_MODULE, "hello_class");   
	    err = PTR_ERR(hello_class);   
	    if (IS_ERR(hello_class)) {   
	          printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	          unregister_chrdev(major, "hello");   
	          return -1;   
	    }   
   
	    device_create(hello_class, NULL, MKDEV(major, 0), NULL, "hello"); /* /dev/hello */   
   
	    return 0;   
	}   
   
	/* 6. 有入口函数就有出口函数：卸载驱动程序时就会去调用这个出口函数 */   
	static void __exit hello_exit(void)   
	{   
	    printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	    device_destroy(hello_class, MKDEV(major, 0));   
	    class_destroy(hello_class);   
	    unregister_chrdev(major, "hello");   
	}   
   
   
	/* 7. 其他完善：提供设备信息，自动创建设备节点 */   
   
	module_init(hello_init);   
	module_exit(hello_exit);   
   
	MODULE_LICENSE("GPL");   
```
	   
&emsp;&emsp;阅读一个驱动程序，从它的入口函数开始，第66行就是入口函数。它的主要工作就是第71行，向内核注册一个file_operations结构体：hello_drv，这就是字符设备驱动程序的核心。

&emsp;&emsp;file_operations结构体hello_drv在第56行定义，里面提供了open/read/write/release成员，应用程序调用open/read/write/close时就会导致这些成员函数被调用。

&emsp;&emsp;file_operations结构体hello_drv中的成员函数都比较简单，大多数只是打印而已。要注意的是，驱动程序和应用程序之间传递数据要使用copy_from_user/copy_to_user函数。

#### 写测试程序   
&emsp;&emsp;测试程序要实现写、读功能：

```bash
	A.  ./hello_drv_test  -w  wiki.100ask.net  // 把字符串“wiki.100ask.net”发给驱动程序   
	B.  ./hello_drv_test  -r              // 把驱动中保存的字符串读回来   
```
   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\01_hello_drv\hello_drv_test.c   
```

&emsp;&emsp;hello_drv_test.c源码如下：

``` C
	# include <sys/types.h>   
	# include <sys/stat.h>   
	# include <fcntl.h>   
	# include <unistd.h>   
	# include <stdio.h>   
	# include <string.h>   
	   
	/*   
	 * ./hello_drv_test -w abc   
	 * ./hello_drv_test -r   
	 */   
	int main(int argc, char **argv)   
	{   
	    int fd;   
	    char buf[1024];   
	    int len;   
	   
	    /* 1. 判断参数 */   
	    if (argc < 2)   
	    {   
	          printf("Usage: %s -w <string>\n", argv[0]);   
	          printf("      %s -r\n", argv[0]);   
	          return -1;   
	    }   
	   
	    /* 2. 打开文件 */   
	    fd = open("/dev/hello", O_RDWR);   
	    if (fd == -1)   
	    {   
	          printf("can not open file /dev/hello\n");   
	          return -1;   
	    }   
	   
	    /* 3. 写文件或读文件 */   
	    if ((0 == strcmp(argv[1], "-w")) && (argc == 3))   
	    {   
	          len = strlen(argv[2]) + 1;   
	          len = len < 1024 ? len : 1024;   
	          write(fd, argv[2], len);   
	    }   
	    else   
	    {   
	          len = read(fd, buf, 1024);   
	          buf[1023] = '\0';   
	          printf("APP read : %s\n", buf);   
	    }   
	   
	    close(fd);   
	   
	    return 0;   
	}   
```

  
#### 测试
&emsp;&emsp;A.  编写驱动程序的Makefile

&emsp;&emsp;&emsp;&emsp;驱动程序中包含了很多头文件，这些头文件来自内核，不同的ARM板它的某些头文件可能不同。所以编译驱动程序时，需要指定板子所用的内核的源码路径。

&emsp;&emsp;&emsp;&emsp;要编译哪个文件？这也需要指定，设置obj-m变量即可

&emsp;&emsp;&emsp;&emsp;怎么把.c文件编译为驱动程序.ko？这要借助内核的顶层Makefile。

&emsp;&emsp;&emsp;&emsp;本驱动程序的Makefile内容如下： 

``` C
	01   
	02 #  1. 使用不同的开发板内核时, 一定要修改KERN_DIR   
	03 #  2. KERN_DIR中的内核要事先配置、编译, 为了能编译内核, 要先设置下列环境变量:   
	04 #  2.1 ARCH,        比如: export ARCH=arm64   
	05 #  2.2 CROSS_COMPILE, 比如: export CROSS_COMPILE=aarch64-linux-gnu-   
	06 #  2.3 PATH,        比如: export PATH=$PATH:/home/book/100ask_roc-rk3399-pc/ToolChain-6.3.1/gcc-linaro-6.3.1-2017.05-x86_64_aarch64-linux-gnu/bin   
	07 #  注意: 不同的开发板不同的编译器上述3个环境变量不一定相同,   
	08 #      请参考各开发板的高级用户使用手册   
	09   
	10 KERN_DIR = /home/book/100ask_roc-rk3399-pc/linux-4.4   
	11   
	12 all:   
	13     make -C $(KERN_DIR) M=`pwd` modules   
	14     $(CROSS_COMPILE)gcc -o hello_drv_test hello_drv_test.c   
	15   
	16 clean:   
	17     make -C $(KERN_DIR) M=`pwd` modules clean   
	18     rm -rf modules.order   
	19     rm -f hello_drv_test   
	20   
	21 obj-m      += hello_drv.o   
```
								   
&emsp;&emsp;先设置好交叉编译工具链，编译好你的板子所用的内核，然后修改Makefile指定内核源码路径，最后即可执行make命令编译驱动程序和测试程序。

&emsp;&emsp;B.  上机实验

&emsp;&emsp;&emsp;&emsp;<font color="# dd0000">{{redtext|注意：</font>}}我们是在Ubuntu中编译程序，但是需要在ARM板子上测试。所以需要把程序放到ARM板子上。

&emsp;&emsp;&emsp;&emsp;启动单板后，可以通过NFS挂载Ubuntu的某个目录，访问该目录中的程序。

&emsp;&emsp;&emsp;&emsp;测试示例：

&emsp;&emsp;① 在Ubuntu上编译好驱动，并它复制到NFS目录：

```bash
	$ cp *.ko hello_drv_test ~/nfs_rootfs/   
```
   
&emsp;&emsp;② 在ARM板上测试：

```bash
	#  echo "7 4 1 7" > /proc/sys/kernel/printk  // 打开内核的打印信息，有些板子默认打开了   
	#  ifconfig eth0 192.168.1.100   // 配置ARM板IP，下面是挂载NFS文件系统   
	#  mount -t nfs -o nolock,vers=3  192.168.1.137:/home/book/nfs_rootfs  /mnt   
	#  cd  /mnt   
	#  insmod hello_drv.ko   // 安装驱动程序   
	[  293.594910] hello_drv: loading out-of-tree module taints kernel.   
	[  293.616051] /home/book/source/01_hello_drv/hello_drv.c hello_init line 70   
	#  ls /dev/hello -l      // 驱动程序会生成设备节点   
	crw-------   1 root    root     236,   0 Jan 18 08:55 /dev/hello   
	#  ./hello_drv_test      // 查看测试程序的用法   
	Usage: ./hello_drv_test -w <string>   
	      ./hello_drv_test -r   
	#  ./hello_drv_test -w wiki.100ask.net   // 往驱动程序中写入字符串   
	[  318.360800] /home/book/source/01_hello_drv/hello_drv.c hello_drv_open line 45   
	[  318.372570] /home/book/source/01_hello_drv/hello_drv.c hello_drv_write line 38   
	[  318.382854] /home/book/source/01_hello_drv/hello_drv.c hello_drv_close line 51   
	#  ./hello_drv_test -r              // 从驱动程序中读出字符串   
	[  326.177890] /home/book/source/01_hello_drv/hello_drv.c hello_drv_open line 45   
	[  326.198304] /home/book/source/01_hello_drv/hello_drv.c hello_drv_read line 30   
	APP read : wiki.100ask.net   
	[  326.214782] /home/book/source/01_hello_drv/hello_drv.c hello_drv_close line 51   
   
```
   
&emsp;&emsp;<font color="# dd0000">注意：</font>如果安装驱动时提示version magic不匹配，请看本文档最后的“常见问题”。

### Hello驱动中的一些补充知识   
#### module_init/module_exit的实现   
#### register_chrdev的内部实现   
#### class_destroy/device_create浅析   

## 硬件知识_LED原理图

&emsp;&emsp;当我们学习C语言的时候，我们会写个Hello程序。

&emsp;&emsp;那当我们写ARM程序，也该有一个简单的程序引领我们入门，这个程序就是点亮LED。

&emsp;&emsp;我们怎样去点亮一个LED呢？

&emsp;&emsp;分为三步：

&emsp;&emsp;1.看原理图，确定控制LED的引脚;

&emsp;&emsp;2.看主芯片的芯片手册，确定如何设置控制这个引脚;

&emsp;&emsp;3.写程序;


### <font color="#dd0000">先来讲讲怎么看原理图</font>   
&emsp;&emsp;LED样子有很多种，像插脚的，贴片的。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_009.png) 
<400px>

&emsp;&emsp;它们长得完全不一样，因此我们在原理图中将它抽象出来。

&emsp;&emsp;点亮LED需要通电源，同时为了保护LED，加个电阻减小电流。

&emsp;&emsp;控制LED灯的亮灭，可以手动开关LED，但在电子系统中，不可能让人来控制开关，通过编程，利用芯片的引脚去控制开关。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_010.png) 
<400px>


&emsp;&emsp;LED的驱动方式，常见的有四种。

&emsp;&emsp;&emsp;&emsp;方式1：使用引脚输出3.3V点亮LED，输出0V熄灭LED。

&emsp;&emsp;&emsp;&emsp;方式2：使用引脚拉低到0V点亮LED，输出3.3V熄灭LED。

&emsp;&emsp;有的芯片为了省电等原因，其引脚驱动能力不足，这时可以使用三极管驱动。

&emsp;&emsp;&emsp;&emsp;方式3：使用引脚输出1.2V点亮LED，输出0V熄灭LED。

&emsp;&emsp;&emsp;&emsp;方式4：使用引脚输出0V点亮LED，输出1.2V熄灭LED。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_011.png) 
<600px>

&emsp;&emsp;由此，主芯片引脚输出高电平/低电平，即可改变LED状态，而无需关注GPIO引脚输出的是3.3V还是1.2V。

&emsp;&emsp;所以简称输出1或0：

&emsp;&emsp;&emsp;&emsp;逻辑1-->高电平

&emsp;&emsp;&emsp;&emsp;逻辑0-->低电平

 
## 普适的GPIO引脚操作方法==   
&emsp;&emsp;GPIO: General-purpose input/output，通用的输入输出口

### GPIO模块一般结构   
&emsp;&emsp;a.有多组GPIO，每组有多个GPIO

&emsp;&emsp;b.使能：电源/时钟

&emsp;&emsp;c.模式(Mode)：引脚可用于GPIO或其他功能

&emsp;&emsp;d.方向：引脚Mode设置为GPIO时，可以继续设置它是输出引脚，还是输入引脚

&emsp;&emsp;e.数值：对于输出引脚，可以设置寄存器让它输出高、低电平

&emsp;&emsp;&emsp;&emsp;对于输入引脚，可以读取寄存器得到引脚的当前电平


### GPIO寄存器操作   
&emsp;&emsp;a.芯片手册一般有相关章节，用来介绍：power/clock

&emsp;&emsp;&emsp;&emsp;可以设置对应寄存器使能某个GPIO模块(Module)

&emsp;&emsp;&emsp;&emsp;有些芯片的GPIO是没有使能开关的，即它总是使能的

&emsp;&emsp;b.一个引脚可以用于GPIO、串口、USB或其他功能，

&emsp;&emsp;&emsp;&emsp;有对应的寄存器来选择引脚的功能

&emsp;&emsp;c.对于已经设置为GPIO功能的引脚，有方向寄存器用来设置它的方向：输出、输入

&emsp;&emsp;d.对于已经设置为GPIO功能的引脚，有数据寄存器用来写、读引脚电平状态

&emsp;&emsp;&emsp;&emsp;GPIO寄存器的2种操作方法：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;原则：不能影响到其他位

&emsp;&emsp;a.直接读写：读出、修改对应位、写入

&emsp;&emsp;&emsp;&emsp;要设置bit n：

```bash
		val = data_reg;   
		val = val | (1<<n);   
		data_reg = val;   
```
   
&emsp;&emsp;&emsp;&emsp;要清除bit n：

```bash
		val = data_reg;   
		val = val & ~(1<<n);   
		data_reg = val;   
```
   
&emsp;&emsp;b.set-and-clear protocol：

&emsp;&emsp;&emsp;&emsp;set_reg, clr_reg, data_reg 三个寄存器对应的是同一个物理寄存器,

&emsp;&emsp;&emsp;&emsp;要设置bit n：set_reg = (1<<n);

&emsp;&emsp;&emsp;&emsp;要清除bit n：clr_reg = (1<<n);

### GPIO的其他功能：防抖动、中断、唤醒   
&emsp;&emsp;后续章节再介绍

   
## 具体单板的GPIO操作方法   
&emsp;&emsp;请使用GIT下载文档后，看下图红框所示目录中各板子对应的文档及图片。

&emsp;&emsp;网盘中相同名字的目录下也有对应的视频。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_012.png)

&emsp;&emsp;为方便学习，在本文档中也把上述GIT目录中的文档添加进来了。

   
### AM335X的GPIO操作方法

&emsp;&emsp;GPIO: General-purpose input/output，通用的输入输出口

&emsp;&emsp;PRCM: Power, Reset, and Clock Management (电源、复位、时钟管理器)

&emsp;&emsp;CM: Control Module(控制模块)  或 Clock Module (时钟模块)

&emsp;&emsp;PRM_PER: Power Reset Module Peripheral Registers(电源/复位模块中关于外设的寄存器)

&emsp;&emsp;CM_PER: Clock Module Peripheral Registers (时钟模块中关于外设的寄存器)

    
#### AM335X的GPIO模块结构   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_013.png) 
<800px>

&emsp;&emsp;有4组GPIO（GPIO0～3），每组有32个GPIO。

&emsp;&emsp;GPIO的控制涉及3大模块：PRCM、Control Module、GPIO模块本身。

&emsp;&emsp;① PRCM用于使能模块：

&emsp;&emsp;&emsp;&emsp;GPIO0永远都是使能的，GPIO1～3可单独控制。

&emsp;&emsp;&emsp;&emsp;PRCM模块给GPIO模块常供电，只需要使能GPIO模块的时钟。

&emsp;&emsp;② Control Module用于设置模式(Mode)：

&emsp;&emsp;&emsp;&emsp;设置引脚的Mode(即选择功能)、上下拉电阻等；

&emsp;&emsp;&emsp;&emsp;每一个GPIO引脚在Control Module中都有一个寄存器，怎么找到这个寄存器？

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. 根据pin number确定pin name

&emsp;&emsp;&emsp;&emsp;b. 根据pin name在Control Module中确定寄存器

&emsp;&emsp;③ GPIO模块内部：

&emsp;&emsp;&emsp;&emsp;方向：引脚Mode设置为GPIO时，可以继续设置它是输出引脚，还是输入引脚。

&emsp;&emsp;&emsp;&emsp;数值：对于输出引脚，可以设置寄存器让它输出高、低电平；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;对于输入引脚，可以读取寄存器得到引脚的当前电平。

   
#### AM335X的GPIO相关寄存器   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_014.png) 
<600px>

#### set-and-clear协议   
&emsp;&emsp;假设某个GPIO被设置为输出，怎么设置它的输出电平呢？AM335X中对于每个GPIO模块有一个GPIO_DATAOUT寄存器，其中的每一位对应一个引脚，如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_015.png) 
<600px>

&emsp;&emsp;要设置某一位时，不能影响到其他位，操作方法是：读出原来的值，修改某一位，把新值写回去。需要3个步骤才可以设置某一位的值，这效率太低了！

&emsp;&emsp;使用set-and-clear可以只用一个步骤即可修改某一位的值。

&emsp;&emsp;当想设置某一位为1时，往DATA_SETDATAOUT寄存器中某位写入1即可，芯片内部会把对应引脚的电平设置为1，并且不会影响其他引脚：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_016.png) 
<600px>


&emsp;&emsp;当想清除某一位为0时，往DATA_CLEARDATAOUT寄存器中某位写入1即可，芯片内部会把对应引脚的电平设置为0，并且不会影响其他引脚：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_017.png) 
<600px>


&emsp;&emsp;并非所有的芯片都有set-and-clear功能，TI的AM335X系列芯片有这功能。

### RK3288的GPIO操作方法   
&emsp;&emsp;GPIO: General-purpose input/output，通用的输入输出口

&emsp;&emsp;CRU: Clock & Reset Unit (时钟和复位单元)

&emsp;&emsp;PMU: Power Managerment Unit (电源管理单元)

&emsp;&emsp;GRF: General Register Files (通用寄存器文件)

   
#### RK3288的GPIO模块结构   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_018.png) 
<800px>
&emsp;&emsp;有9组GPIO（GPIO0～8），每组分为最多4个小组port A/B/C/D，每小组最多8个GPIO。理论上每组GPIO的引脚有32个，但是实际上并没有那么多。比如GPIO0只有GPIO0_A0～A7、GPIO0_B0～B7、GPIO0_C0～C2这些引脚。

&emsp;&emsp;GPIO的控制涉及4大模块：CRU、PMU、GRF、GPIO模块本身。

&emsp;&emsp;① CRU用于设置是否向GPIO模块提供时钟：

&emsp;&emsp;&emsp;&emsp;CRU的内部结构如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_019.png) 
<800px>

&emsp;&emsp;&emsp;&emsp;可以设置寄存器使能GPIOx的时钟：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. CRU_CLKGATE17_CON用于控制GPIO0；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. CRU_CLKGATE14_CON用于控制GPIO1～8

&emsp;&emsp;② PMU用于控制电源：

&emsp;&emsp;&emsp;&emsp;电源管理单元里，有多个电源域(power domain，简称为PM)，在一个域下有多个设备。

&emsp;&emsp;&emsp;&emsp;比如PD_ALIVE，它下面有这些设备：CRU、GRF、GPIO 1~8、TIMER或WDT。

&emsp;&emsp;&emsp;&emsp;比如PD_PMU，它下面有这些设备：PMU、SRAM(4K)、Secure GRF、GPIO0。

&emsp;&emsp;&emsp;&emsp;可见，GPIO0、GPIO1~8分属不同的PM。

&emsp;&emsp;&emsp;&emsp;GPIO0、GPIO1～8都是常供电的，它们是否工作取决于其时钟是否使能。

&emsp;&emsp;③ 设置引脚的模式(Mode、功能)：

&emsp;&emsp;&emsp;&emsp;GPIO0比较特殊，为了让其引脚用于GPIO功能，要设置PMU里的相关寄存器。

&emsp;&emsp;&emsp;&emsp;GPIO1～8类似，为了让其引脚用于GPIO功能，要设置GRF里的相关寄存器。

&emsp;&emsp;④ GPIO模块内部：

&emsp;&emsp;&emsp;&emsp;方向：引脚设置为GPIO时，可以继续设置寄存器GPIO_SWPORTA_DDR确定它是输出引脚，还是输入引脚。

&emsp;&emsp;&emsp;&emsp;数值：对于输出引脚，可以设置寄存器GPIO_SWPORTA_DR让它输出高、低电平；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;对于输入引脚，可以读取寄存器GPIO_EXT_PORTA得到引脚的当前电平。

#### RK3288的GPIO相关寄存器   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_020.png) 
<600px>
### RK3399的GPIO操作方法   
&emsp;&emsp;GPIO: General-purpose input/output，通用的输入输出口

&emsp;&emsp;CRU: Clock & Reset Unit (时钟和复位单元)

&emsp;&emsp;PMU: Power Managerment Unit (电源管理单元)

&emsp;&emsp;GRF: General Register Files (通用寄存器文件)

#### RK3399的GPIO模块结构   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_021.png) 
<600px>
&emsp;&emsp;有5组GPIO（GPIO0～4），每组分为最多4个小组port A/B/C/D，每小组最多8个GPIO。理论上每组GPIO的引脚有32个，但是实际上并没有那么多。比如GPIO0只有GPIO0_A0～A7、GPIO0_B0～B5这些引脚。

&emsp;&emsp;GPIO的控制涉及4大模块：CRU、PMU、GRF、GPIO模块本身。

&emsp;&emsp;① CRU用于设置是否向GPIO模块提供时钟

&emsp;&emsp;&emsp;&emsp;a. PMUCRU_CLKGATE_CON1用于控制GPIO0～1；

&emsp;&emsp;&emsp;&emsp;b. CRU_CLKGATE_CON31用于控制GPIO2～4

&emsp;&emsp;② PMU用于控制电源：

&emsp;&emsp;&emsp;&emsp;电源管理单元里，有多个电源域(power domain，简称为PM)，在一个域下有多个设备。

&emsp;&emsp;&emsp;&emsp;比如PD_ALIVE，它下面有这些设备：CRU、GRF、GPIO 1~4、TIMER或WDT。

&emsp;&emsp;&emsp;&emsp;比如PD_PMU，它下面有这些设备：cm0、PMU、SRAM(8K)、Secure GRF、GPIO0、PVTM、I2C。

&emsp;&emsp;&emsp;&emsp;可见，GPIO0、GPIO1~4分属不同的PM。

&emsp;&emsp;&emsp;&emsp;GPIO0、GPIO1～4都是常供电的。

&emsp;&emsp;③ 设置引脚的模式(Mode、功能)：

&emsp;&emsp;&emsp;&emsp;GPIO0～1比较特殊，为了让其引脚用于GPIO功能，要设置PMU里的相关寄存器。

&emsp;&emsp;&emsp;&emsp;GPIO2～4类似，为了让其引脚用于GPIO功能，要设置GRF里的相关寄存器。

&emsp;&emsp;④ GPIO模块内部：

&emsp;&emsp;&emsp;&emsp;方向：引脚设置为GPIO时，可以继续设置寄存器GPIO_SWPORTA_DDR确定它是输出引脚，还是输入引脚。

&emsp;&emsp;&emsp;&emsp;数值：对于输出引脚，可以设置寄存器GPIO_SWPORTA_DR让它输出高、低电平；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;对于输入引脚，可以读取寄存器GPIO_EXT_PORTA得到引脚的当前电平。

   
#### RK3399的GPIO相关寄存器   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_022.png) 
<600px>
### IMX6ULL的GPIO操作方法   
&emsp;&emsp;CCM: Clock Controller Module (时钟控制模块)

&emsp;&emsp;IOMUXC : IOMUX Controller，IO复用控制器

&emsp;&emsp;GPIO: General-purpose input/output，通用的输入输出口

#### IMX6ULL的GPIO模块结构   
&emsp;&emsp;参考资料：芯片手册《Chapter 26: General Purpose Input/Output (GPIO)》

&emsp;&emsp;有5组GPIO（GPIO1～GPIO5），每组引脚最多有32个，但是可能实际上并没有那么多。

&emsp;&emsp;GPIO1有32个引脚：GPIO1_IO0~GPIO1_IO31；

&emsp;&emsp;GPIO2有22个引脚：GPIO2_IO0~GPIO2_IO21；

&emsp;&emsp;GPIO3有29个引脚：GPIO3_IO0~GPIO3_IO28；

&emsp;&emsp;GPIO4有29个引脚：GPIO4_IO0~GPIO4_IO28；

&emsp;&emsp;GPIO5有12个引脚：GPIO5_IO0~GPIO5_IO11；

&emsp;&emsp;GPIO的控制涉及4大模块：CCM、IOMUXC、GPIO模块本身，框图如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_023.png) 
<600px>
   
#### CCM用于设置是否向GPIO模块提供时钟   
&emsp;&emsp;参考资料：芯片手册《Chapter 18: Clock Controller Module (CCM)》

&emsp;&emsp;GPIOx要用CCM_CCGRy寄存器中的2位来决定该组GPIO是否使能。哪组GPIO用哪个CCM_CCGR寄存器来设置，请看上图红框部分。

&emsp;&emsp;CCM_CCGR寄存器中某2位的取值含义如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_024.png) 
<400px>

&emsp;&emsp;① 00：该GPIO模块全程被关闭

&emsp;&emsp;② 01：该GPIO模块在CPU run mode情况下是使能的；在WAIT或STOP模式下，关闭

&emsp;&emsp;③ 10：保留

&emsp;&emsp;④ 11：该GPIO模块全程使能

&emsp;&emsp;&emsp;&emsp;GPIO2时钟控制：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_025.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;GPIO1、GPIO5时钟控制：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_026.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;GPIO3时钟控制：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_027.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;GPIO4时钟控制：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_028.png) 
<600px>
   
   
#### IOMUXC：引脚的模式(Mode、功能)   
&emsp;&emsp;参考资料：芯片手册《Chapter 32: IOMUX Controller (IOMUXC)》。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_029.png) 
<100px>

&emsp;&emsp;对于某个/某组引脚，IOMUXC中有2个寄存器用来设置它：

&emsp;&emsp;① 选择功能：

&emsp;&emsp;&emsp;&emsp;IOMUXC_SW_<font color="# dd0000">MUX</font>_CTL_<font color="#dd0000">PAD</font>_<<font color="#dd0000">PADNAME</font>> ：Mux pad xxx，选择某个pad的功能

&emsp;&emsp;&emsp;&emsp;IOMUXC_SW_<font color="# dd0000">MUX</font>_CTL_<font color="#dd0000">GRP</font>_<<font color="#dd0000">GROUP NAME</font>>：Mux grp xxx，选择某组引脚的功能

&emsp;&emsp;&emsp;&emsp;某个引脚，或是某组预设的引脚，都有8个可选的模式(alternate (ALT) MUX_MODE)。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_030.png) 
<400px>


&emsp;&emsp;&emsp;&emsp;比如：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_031.png) 
<600px>


&emsp;&emsp;② 设置上下拉电阻等参数：

&emsp;&emsp;&emsp;&emsp;IOMUXC_SW_<font color="# dd0000">PAD</font>_CTL_<font color="#dd0000">PAD</font>_<<font color="#dd0000">PADNAME</font>> ： pad pad xxx，设置某个pad的参数

&emsp;&emsp;&emsp;&emsp;IOMUXC_SW_<font color="# dd0000">PAD</font>_CTL_<font color="#dd0000">GRP</font>_<<font color="#dd0000">GROUP NAME</font>>：pad grp xxx，设置某组引脚的参数

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_032.png) 
<400px>

&emsp;&emsp;&emsp;&emsp;比如：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_033.png) 
<600px>
   
#### GPIO模块内部   
&emsp;&emsp;框图如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_034.png) 
<600px>


&emsp;&emsp;我们暂时只需要关心3个寄存器：

&emsp;&emsp;&emsp;&emsp;① GPIOx_GDIR：设置引脚方向，每位对应一个引脚，1-output，0-input

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_035.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;② GPIOx_GDIR：设置输出引脚的电平，每位对应一个引脚，1-高电平，0-低电平

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_036.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;③ GPIOx_PSR：读取引脚的电平，每位对应一个引脚，1-高电平，0-低电平

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_037.png) 
<600px>
   
#### 怎么编程   
##### 读GPIO   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_038.png) 
<600px>
&emsp;&emsp;翻译一下：

&emsp;&emsp;&emsp;&emsp;① 设置CCM_CCGRx寄存器中某位使能对应的GPIO模块 // 默认是使能的，上图省略了

&emsp;&emsp;&emsp;&emsp;② 设置IOMUX来选择引脚用于GPIO

&emsp;&emsp;&emsp;&emsp;③ 设置GPIOx_GDIR中某位为0，把该引脚设置为输入功能

&emsp;&emsp;&emsp;&emsp;④ 读GPIOx_DR或GPIOx_PSR得到某位的值（读GPIOx_DR返回的是GPIOx_PSR的值）

   
##### 写GPIO   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_039.png) 
<600px>
&emsp;&emsp;翻译一下：

&emsp;&emsp;&emsp;&emsp;① 设置CCM_CCGRx寄存器中某位使能对应的GPIO模块 // 默认是使能的，上图省略了

&emsp;&emsp;&emsp;&emsp;② 设置IOMUX来选择引脚用于GPIO

&emsp;&emsp;&emsp;&emsp;③ 设置GPIOx_GDIR中某位为1，把该引脚设置为输出功能

&emsp;&emsp;&emsp;&emsp;④ 写GPIOx_DR某位的值

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;需要注意的是，你可以设置该引脚的loopback功能，这样就可以从GPIOx_PSR中读到引脚的有实电平；你从GPIOx_DR中读回的只是上次设置的值，它并不能反应引脚的真实电平，比如可能因为硬件故障导致该引脚跟地短路了，你通过设置GPIOx_DR让它输出高电平并不会起效果。

   
## LED驱动程序框架   
&emsp;&emsp;<font color="# dd0000">注意：</font>如果做实验安装驱动时提示version magic不匹配，请看本文档最后的“常见问题”。

   
### 回顾字符设备驱动程序框架   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_040.png) 
<600px>
### 对于LED驱动，我们想要什么样的接口？   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_041.png) 
<600px>
### LED驱动要怎么写，才能支持多个板子？分层。   
&emsp;&emsp;1. 把驱动拆分为通用的框架(leddrv.c)、具体的硬件操作(board_X.c)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_042.png) 
<600px>

&emsp;&emsp;2. 以面向对象的思想，改进代码：

&emsp;&emsp;&emsp;&emsp;抽象出一个结构体：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_043.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;每个单板相关的board_X.c实现自己的led_operations结构体，供上层的leddrv.c调用：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_044.png) 
<600px>
   
### 写代码   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\02_led_drv\01_led_drv_template   
```
   
#### 驱动程序   
&emsp;&emsp;驱动程序分为上下两层：leddrv.c、board_demo.c。

&emsp;&emsp;leddrv.c负责注册file_operations结构体，它的open/write成员会调用board_demo.c中提供的硬件led_opr中的对应函数。

#### 把LED的操作抽象出一个led_operations结构体   
&emsp;&emsp;首先看看led_opr.h，它定义了一个led_operations结构体，把LED的操作抽象为这个结构体：

``` C
	# ifndef _LED_OPR_H   
	# define _LED_OPR_H   
   
	struct led_operations {   
	    int (*init) (int which); /* 初始化LED, which-哪个LED */   
	    int (*ctl) (int which, char status); /* 控制LED, which-哪个LED, status:1-亮,0-灭 */   
	};   
   
	struct led_operations *get_board_led_opr(void);   
   
   
	# endif   
```
   
	   
#### 驱动程序的上层：file_operations结构体   
&emsp;&emsp;上层是leddrv.c，它的核心是file_operations结构体，首先看看入口函数：

``` C
	80 /* 4. 把file_operations结构体告诉内核：注册驱动程序 */   
	81 /* 5. 谁来注册驱动程序啊？得有一个入口函数：安装驱动程序时，就会去调用这个入口函数 */   
	82 static int __init led_init(void)   
	83 {   
	84     int err;   
	85     int i;   
	86   
	87     printk(“%s %s line %d\n”, __FILE__, __FUNCTION__, __LINE__);   
	88     major = register_chrdev(0, “100ask_led”, &led_drv);  /* /dev/led */   
	89   
	90   
	91     led_class = class_create(THIS_MODULE, “100ask_led_class”);   
	92     err = PTR_ERR(led_class);   
	93     if (IS_ERR(led_class)) {   
	94           printk(“%s %s line %d\n”, __FILE__, __FUNCTION__, __LINE__);   
	95           unregister_chrdev(major, “led”);   
	96           return -1;   
	97     }   
	98   
	99     for (i = 0; i < LED_NUM; i++)   
	100          device_create(led_class, NULL, MKDEV(major, i), NULL, “100ask_led%d”, i); /* /dev/100ask_led0,1,… */   
	101   
	102    p_led_opr = get_board_led_opr();   
	103   
	104    return 0;   
	105 }   
   
```
	   
&emsp;&emsp;第88行向内核注册一个file_operations结构体。

&emsp;&emsp;第102行从底层硬件相关的代码board_demo.c中获得led_operaions结构体。

&emsp;&emsp;再来看看leddrv.c中file_operations结构体的成员函数：

``` C
	37 /* write(fd, &val, 1); */   
	38 static ssize_t led_drv_write (struct file *file, const char __user *buf, size_t size, loff_t *offset)   
	39 {   
	40     int err;   
	41     char status;   
	42     struct inode *inode = file_inode(file);   
	43     int minor = iminor(inode);   
	44   
	45     printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	46     err = copy_from_user(&status, buf, 1);   
	47   
	48     /* 根据次设备号和status控制LED */   
	49     p_led_opr->ctl(minor, status);   
	50   
	51     return 1;   
	52 }   
	53   
	54 static int led_drv_open (struct inode *node, struct file *file)   
	55 {   
	56     int minor = iminor(node);   
	57   
	58     printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	59     /* 根据次设备号初始化LED */   
	60     p_led_opr->init(minor);   
	61   
	62     return 0;   
	63 }   
	64   
	65 static int led_drv_close (struct inode *node, struct file *file)   
	66 {   
	67     printk("%s %s line %d\n", __FILE__, __FUNCTION__, __LINE__);   
	68     return 0;   
	69 }   
	70   
	71 /* 2. 定义自己的file_operations结构体 */   
	72 static struct file_operations led_drv = {   
	73     .owner   = THIS_MODULE,   
	74     .open   = led_drv_open,   
	75     .read   = led_drv_read,   
	76     .write   = led_drv_write,   
	77     .release = led_drv_close,   
	78 };   
   
```
   
&emsp;&emsp;第49行、第60行，会调用led_operations结构体中对应的函数。

#### 测试程序
&emsp;&emsp;测试程序为ledtest.c：

``` C
	# include <sys/types.h>   
	# include <sys/stat.h>   
	# include <fcntl.h>   
	# include <unistd.h>   
	# include <stdio.h>   
	# include <string.h>   
   
	/*   
	 * ./ledtest /dev/100ask_led0 on   
	 * ./ledtest /dev/100ask_led0 off   
	 */   
	int main(int argc, char **argv)   
	{   
	    int fd;   
	    char status;   
   
	    /* 1. 判断参数 */   
	    if (argc != 3)   
	    {   
	          printf("Usage: %s <dev> <on | off>\n", argv[0]);   
	          return -1;   
	    }   
   
	    /* 2. 打开文件 */   
	    fd = open(argv[1], O_RDWR);   
	    if (fd == -1)   
	    {   
	          printf("can not open file %s\n", argv[1]);   
	          return -1;   
	    }   
   
	    /* 3. 写文件 */   
	    if (0 == strcmp(argv[2], "on"))   
	    {   
	          status = 1;   
	          write(fd, &status, 1);   
	    }   
	    else   
	    {   
	          status = 0;   
	          write(fd, &status, 1);   
	    }   
   
	    close(fd);   
   
	    return 0;   
	}   
```

&emsp;&emsp;第26行打开设备节点。

&emsp;&emsp;如果用户想点亮LED，第37行会把值“1”通过write函数写入驱动程序。

&emsp;&emsp;如果用户想熄灭LED，第42行会把值“0”通过write函数写入驱动程序。


#### 上机测试   
&emsp;&emsp;这只是一个示例程序，还没有真正操作硬件。测试程序操作驱动程序时，只会导致驱动程序中打印信息。

&emsp;&emsp;首先设置交叉工具链，修改驱动Makefile中内核的源码路径，编译驱动和测试程序。

&emsp;&emsp;启动开发板后，通过NFS访问编译好驱动程序、测试程序，就可以在开发板上如下操作了： 

```bash
	#  insmod 100ask_led.ko   // 装载驱动程序   
	[13449.134044] /home/book/source/02_led_drv/01_led_drv_template/leddrv.c led_init line 87   
	#  ls /dev/100ask_led* -l   // 可以得到2个设备节点   
	crw-------   1 root    root     235,   0 Jan 18 12:34 /dev/100ask_led0   
	crw-------   1 root    root     235,   1 Jan 18 12:34 /dev/100ask_led1   
	#  ./ledtest /dev/100ask_led0 on   // 点亮LED   
	[13463.176987] /home/book/source/02_led_drv/01_led_drv_template/leddrv.c led_drv_open line 58   
	[13463.197877] /home/book/source/02_led_drv/01_led_drv_template/board_demo.c board_demo_led_init line 22, led 0   
	[13463.216232] /home/book/source/02_led_drv/01_led_drv_template/leddrv.c led_drv_write line 45   
	[13463.232889] /home/book/source/02_led_drv/01_led_drv_template/board_demo.c board_demo_led_ctl line 28, led 0, on   // 可以看到这句“on”打印   
	[13463.247977] /home/book/source/02_led_drv/01_led_drv_template/leddrv.c led_drv_close line 67   
	#  ./ledtest /dev/100ask_led0 off    // 熄灭LED   
	[13464.540637] /home/book/source/02_led_drv/01_led_drv_template/leddrv.c led_drv_open line 58   
	[13464.554380] /home/book/source/02_led_drv/01_led_drv_template/board_demo.c board_demo_led_init line 22, led 0   
	[13464.569671] /home/book/source/02_led_drv/01_led_drv_template/leddrv.c led_drv_write line 45   
	[13464.580615] /home/book/source/02_led_drv/01_led_drv_template/board_demo.c board_demo_led_ctl line 28, led 0, off   // 可以看到这句“off”打印   
	[13464.593397] /home/book/source/02_led_drv/01_led_drv_template/leddrv.c led_drv_close line 67   
```
   
### 课后作业   
&emsp;&emsp;实现读LED状态的功能：涉及APP和驱动。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_045.png) 
<600px>
   
   
## 具体单板的LED驱动程序   
&emsp;&emsp;我们选用的内核都是4.x版本，操作都是类似的：

```bash
	rk3399   linux 4.4.154   
	rk3288   linux 4.4.154   
	imx6ul   linux 4.9.88   
	am3358  linux 4.9.168   
```
   
&emsp;&emsp;录制视频时，我的source insight里总是使用某个版本的内核。这没有关系，驱动程序中调用的内核函数，在这些4.x版本的内核里都是一样的。

   
### 怎么写LED驱动程序？   
&emsp;&emsp;详细步骤如下：

&emsp;&emsp;&emsp;&emsp;① 看原理图确定引脚，确定引脚输出什么电平才能点亮/熄灭LED

&emsp;&emsp;&emsp;&emsp;② 看主芯片手册，确定寄存器操作方法：哪些寄存器？哪些位？地址是？

&emsp;&emsp;&emsp;&emsp;③ 编写驱动：先写框架，再写硬件操作的代码

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<font color="# dd0000">注意：</font>在芯片手册中确定的寄存器地址被称为<font color="#dd0000">物理地址</font>，在Linux内核中无法直接使用。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;需要使用内核提供的ioremap把物理地址映射为虚拟地址，使用<font color="# dd0000">虚拟地址</font>。

&emsp;&emsp;ioremap函数的使用：

&emsp;&emsp;&emsp;&emsp;① 函数原型：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_046.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;使用时，要包含头文件：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_047.png) 
<200px>


&emsp;&emsp;&emsp;&emsp;② 它的作用：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;把物理地址phys_addr开始的一段空间(大小为size)，映射为虚拟地址；返回值是该段虚拟地址的首地址。

``` C
	virt_addr  = ioremap(phys_addr, size);   
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;实际上，它是按页(4096字节)进行映射的，是整页整页地映射的。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;假设phys_addr = 0x10002，size=4，ioremap的内部实现是：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. phys_addr按页取整，得到地址0x10000

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. size按页取整，得到4096

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. 把起始地址0x10000，大小为4096的这一块物理地址空间，映射到虚拟地址空间，

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;假设得到的虚拟空间起始地址为0xf0010000

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;d. 那么phys_addr = 0x10002对应的virt_addr = 0xf0010002

&emsp;&emsp;&emsp;&emsp;③ 不再使用该段虚拟地址时，要iounmap(virt_addr)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_048.png) 
<400px>


&emsp;&emsp;volatile的使用：

&emsp;&emsp;&emsp;&emsp;① 编译器很聪明，会帮我们做些优化，比如：

``` C
	int   a;   
	a = 0;   // 这句话可以优化掉，不影响a的结果   
	a = 1;   
```
   
&emsp;&emsp;&emsp;&emsp;② 有时候编译器会自作聪明，比如：

``` C
	int *p = ioremap(xxxx, 4);  // GPIO寄存器的地址   
	*p = 0;   // 点灯，但是这句话被优化掉了   
	*p = 1;   // 灭灯   
```
   
&emsp;&emsp;&emsp;&emsp;③ 对于上面的情况，为了避免编译器自动优化，需要加上volatile，告诉它“这是容易出错的，别乱优化”：

``` C
	volatile  int *p = ioremap(xxxx, 4);  // GPIO寄存器的地址   
	*p = 0;   // 点灯，这句话不会被优化掉   
	*p = 1;   // 灭灯   
```
   
### AM335X的LED驱动程序   
#### 原理图   
&emsp;&emsp;100ask_AM335X开发板结构为：底板+核心板，其中一个LED原理图如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_049.png) 
<1200px>


&emsp;&emsp;它使用GPIO1_16这个引脚，当它输出低电平时，LED被点亮；当它输出高电平时，LED被熄灭。

#### 所涉及的寄存器操作   
&emsp;&emsp;a. 使能GPIO1

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_050.png) 
<600px>
   
``` C
	/* set PRCM to enalbe GPIO1   
	 * set CM_PER_GPIO1_CLKCTRL (0x44E00000 + 0xAC)   
	 * val: (1<<18) | 0x2   
	 */   
```
   
&emsp;&emsp;b. 设置GPIO1_16的功能，让它工作于GPIO模式

&emsp;&emsp;&emsp;&emsp;根据原理图可以找到GPIO1_16这个引脚接到AM3358的R13引脚，根据下图知道pin name为GPMC_A0，并且知道要设置这个引脚为Mode 7。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_051.png) 
<600px>


&emsp;&emsp;&emsp;&emsp;在芯片手册中搜“conf_gpmc_a0”，可得：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_052.png) 
<600px>
   
``` C
	/* set Control Module to set GPIO1_16 (R13) used as GPIO   
	 * conf_gpmc_a0 as mode 7   
	 * addr : 0x44E10000 + 0x840   
	 * val  : 7   
	 */   
```
   
&emsp;&emsp;c. 设置GPIO1_16的方向，让它作为输出引脚

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_053.png) 
<600px>
   
``` C
	/* set GPIO1's registers , to set GPIO1_16'S dir (output)   
	 * GPIO_OE   
	 * addr : 0x4804C000 + 0x134   
	 * clear bit 16   
	 */   
```
   
&emsp;&emsp;d. 设置GPIO1_16的数据，让它输出高电平

&emsp;&emsp;&emsp;&emsp;AM335X芯片支持set-and-clear protocol，设置GPIO_SETDATAOUT的bit 16为1即可让引脚输出1：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_054.png) 
<600px>
   
``` C
	/* set GPIO1_16's registers , to output 1   
	 * GPIO_SETDATAOUT   
	 * addr : 0x4804C000 + 0x194   
	 */   
```
	   
&emsp;&emsp;e. 清除GPIO1_16的数据，让它输出低电平

&emsp;&emsp;&emsp;&emsp;AM335X芯片支持set-and-clear protocol，设置GPIO_CLEARDATAOUT的bit 16为1即可让引脚输出0：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_055.png) 
<600px>
   
``` C
	/* set GPIO1_16's registers , to output 0   
	 * GPIO_CLEARDATAOUT   
	 * addr : 0x4804C000 + 0x190   
	 */   
```
   
#### 写程序   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\02_led_drv\   
	  	02_led_drv_for_boards\am335x_src_bin   
```
   
	   
&emsp;&emsp;硬件相关的文件是board_am335x.c，其他文件跟LED框架驱动程序完全一样。

&emsp;&emsp;它首先构造了一个led_operations结构体，用来表示LED的硬件操作：

``` C
	100 static struct led_operations board_demo_led_opr = {   
	101    .num  = 1,   
	102    .init = board_demo_led_init,   
	103    .ctl  = board_demo_led_ctl,   
	104 };   
	105   
```
   
&emsp;&emsp;led_operations结构体中有init函数指针，它指向board_demo_led_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。

&emsp;&emsp;值得关注的是第33～37行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器。

``` C
	19 # include "led_opr.h"   
	20   
	21 static volatile unsigned int *CM_PER_GPIO1_CLKCTRL;   
	22 static volatile unsigned int *conf_gpmc_a0;   
	23 static volatile unsigned int *GPIO1_OE;   
	24 static volatile unsigned int *GPIO1_CLEARDATAOUT;   
	25 static volatile unsigned int *GPIO1_SETDATAOUT;   
	26   
	27 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */   
	28 {   
	29    if (which == 0)   
	30    {   
	31       if (!CM_PER_GPIO1_CLKCTRL)   
	32       {   
	33          CM_PER_GPIO1_CLKCTRL = ioremap(0x44E00000 + 0xAC, 4);   
	34          conf_gpmc_a0 = ioremap(0x44E10000 + 0x840, 4);   
	35          GPIO1_OE = ioremap(0x4804C000 + 0x134, 4);   
	36          GPIO1_CLEARDATAOUT = ioremap(0x4804C000 + 0x190, 4);   
	37          GPIO1_SETDATAOUT = ioremap(0x4804C000 + 0x194, 4);   
	38       }   
	39   
	40       //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	41       /* a. 使能GPIO1   
	42        * set PRCM to enalbe GPIO1   
	43        * set CM_PER_GPIO1_CLKCTRL (0x44E00000 + 0xAC)   
	44        * val: (1<<18) | 0x2   
	45        */   
	46       *CM_PER_GPIO1_CLKCTRL = (1<<18) | 0x2;   
	47   
	48       /* b. 设置GPIO1_16的功能，让它工作于GPIO模式   
	49        * set Control Module to set GPIO1_16 (R13) used as GPIO   
	50        * conf_gpmc_ad0 as mode 7   
	51        * addr : 0x44E10000 + 0x800   
	52        * val  : 7   
	53        */   
	54       *conf_gpmc_a0 = 7;   
	55   
	56       /* c. 设置GPIO1_16的方向，让它作为输出引脚   
	57        * set GPIO1's registers , to set GPIO1_16'S dir (output)   
	58        * GPIO_OE   
	59        * addr : 0x4804C000 + 0x134   
	60        * clear bit 16   
	61        */   
	62   
	63       *GPIO1_OE &= ~(1<<16);   
	64    }   
	65   
	66    return 0;   
	67 }   
	68   
   
```
   
&emsp;&emsp;led_operations结构体中有ctl函数指针，它指向board_demo_led_ctl函数，在里面将会根据参数设置LED引脚的输出电平：

``` C
	69 static int board_demo_led_ctl (int which, char status) /* 控制LED, which-哪个LED, status:1-亮,0-灭 */   
	70 {   
	71    //printk("%s %s line %d, led %d, %s\n", __FILE__, __FUNCTION__, __LINE__, which, status ? "on" : "off");   
	72   
	73    if (which == 0)   
	74    {   
	75       if (status) /* on: output 0 */   
	76       {   
	77          /* e. 清除GPIO1_16的数据，让它输出低电平   
	78           * AM335X芯片支持set-and-clear protocol，设置GPIO_CLEARDATAOUT的bit 16为1即可让引脚输出0：   
	79           * set GPIO1_16's registers , to output 0   
	80           * GPIO_CLEARDATAOUT   
	81           * addr : 0x4804C000 + 0x190   
	82           */   
	83          *GPIO1_CLEARDATAOUT = (1<<16);   
	84       }   
	85       else   
	86       {   
	87          /* d. 设置GPIO1_16的数据，让它输出高电平   
	88           * AM335X芯片支持set-and-clear protocol，设置GPIO_SETDATAOUT的bit 16为1即可让引脚输出1：   
	89           * set GPIO1_16's registers , to output 1   
	90           * GPIO_SETDATAOUT   
	91           * addr : 0x4804C000 + 0x194   
	92           */   
	93          *GPIO1_SETDATAOUT = (1<<16);   
	94       }   
	95    }   
	96   
	97    return 0;   
	98 }   
	99   
```
   
&emsp;&emsp;下面的get_board_led_opr函数供上层调用，给上层提供led_operations结构体：

``` C
	106 struct led_operations *get_board_led_opr(void)   
	107 {   
	108    return &board_demo_led_opr;   
	109 }   
	110   
```
   
#### 配置内核去掉原有LED驱动   
&emsp;&emsp;不需要重新配置内核，只需要在开发板上执行以下3条命令关闭内核对LED的使用即可：

```bash
	#  echo none > /sys/class/leds/am335x:green:cpu0/trigger   
	#  echo none > /sys/class/leds/am335x:green:mmc0/trigger   
	#  echo none > /sys/class/leds/am335x:green:nand/trigger   
```
   
&emsp;&emsp;然后就可以去安装驱动程序，执行测试程序了，操作过程跟LED框架驱动程序的测试是一样的。

#### 课后作业   
&emsp;&emsp;a. 在board_am335x.c里有ioremap，什么时候执行iounmap？请完善程序

&emsp;&emsp;b. 视频里我们只实现了点一个LED，请修改代码实现操作4个LED

   
### RK3288和RK3399的LED驱动程序   
#### 原理图   
##### fireflye RK3288的LED原理图   
&emsp;&emsp;RK3288开发板上有2个LED，原理图如下，其中的WORK_LED使用引脚GPIO8_A1：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_056.png) 
<800px>

&emsp;&emsp;这些LED引脚输出低电平时，LED被点亮；输出高电平时，LED被熄灭。

   
##### firefly RK3399的LED原理图   
&emsp;&emsp;RK3399开发板上有3个LED，原理图如下，其中的WORK_LED使用引脚GPIO2_D3：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_057.png) 
<1200px>


&emsp;&emsp;这些LED引脚输出低电平时，LED被点亮；输出高电平时，LED被熄灭。

   
   
#### 所涉及的寄存器操作   
&emsp;&emsp;截图便于对比，后面有文字便于复制：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_058.png) 
<800px>
   
##### RK3288的GPIO8_A1引脚   
&emsp;&emsp;a. 使能GPIO8

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_059.png) 
<600px>

&emsp;&emsp;设置CRU_CLKGATE14_CON的b[8]为0使能GPIO8，要修改b[8]的前提是把b[24]设置为1。

``` C
	/* rk3288 GPIO8_A1 */   
	/* a. 使能GPIO8   
	 * set CRU to enable GPIO8   
	 * CRU_CLKGATE14_CON 0xFF760000 + 0x198   
	 * (1<<(8+16)) | (0<<8)   
	 */   
```
   
&emsp;&emsp;b. 设置GPIO8_A1用于GPIO

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_060.png) 
<600px>

&emsp;&emsp;设置GRF_GPIO8A_IOMUX的b[3:2]为0b00把GPIO8_A1用作GPIO，要修改b[3:2]的前提是把b[19:18]设置为0b11。

``` C
	/* b. 设置GPIO8_A1用于GPIO   
	 * set PMU/GRF to configure GPIO8_A1 as GPIO   
	 * GRF_GPIO8A_IOMUX 0xFF770000 + 0x0080   
	 * bit[3:2] = 0b00   
	 * (3<<(2+16)) | (0<<2)   
	 */   
```	

&emsp;&emsp;c. 设置GPIO8_A1作为output引脚

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_061.png) 
<600px>

&emsp;&emsp;设置GPIO_SWPORTA_DDR 寄存器b[1]为1，把GPIO8_A1设置为输出引脚。

&emsp;&emsp;<font color="# dd0000">注意：</font>

&emsp;&emsp;&emsp;&emsp;GPIO_A0~A7 对应bit0~bit7；GPIO_B0~B7 对应bit8~bit15；

&emsp;&emsp;&emsp;&emsp;GPIO_C0~C7 对应bit16~bit23；GPIO_D0~D7 对应bit24~bit31

``` C
/* c. 设置GPIO8_A1作为output引脚   
 * set GPIO_SWPORTA_DDR to configure GPIO8_A1 as output   
 * GPIO_SWPORTA_DDR 0xFF7F0000 + 0x0004   
 * bit[1] = 0b1   
 */   
```
   
&emsp;&emsp;d. 设置GPIO8_A1输出高电平

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_062.png) 
<600px>

&emsp;&emsp;设置GPIO_SWPORTA_DR 寄存器b[1]为1，让GPIO8_A1输出高电平。

&emsp;&emsp;<font color="# dd0000">注意：</font>

&emsp;&emsp;&emsp;&emsp;GPIO_A0~A7 对应bit0~bit7；GPIO_B0~B7 对应bit8~bit15；

&emsp;&emsp;&emsp;&emsp;GPIO_C0~C7 对应bit16~bit23；GPIO_D0~D7 对应bit24~bit31

``` C
	/* d. 设置GPIO8_A1输出高电平   
	 * set GPIO_SWPORTA_DR to configure GPIO8_A1 output 1   
	 * GPIO_SWPORTA_DR 0xFF7F0000 + 0x0000   
	 * bit[1] = 0b1   
	 */   
```
   
&emsp;&emsp;e. 设置GPIO8_A1输出低电平

&emsp;&emsp;&emsp;&emsp;同样是设置GPIO_SWPORTA_DR 寄存器，把b[1]设为0，让GPIO8_A1输出低电平。

``` C
	/* e. 设置GPIO8_A1输出低电平   
	 * set GPIO_SWPORTA_DR to configure GPIO8_A1 output 0   
	 * GPIO_SWPORTA_DR 0xFF7F0000 + 0x0000   
	 * bit[1] = 0b0   
	 */   
```
   
##### RK3399的GPIO2_D3引脚   
&emsp;&emsp;a. 使能GPIO2

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_063.png) 
<600px>

&emsp;&emsp;设置CRU_CLKGATE_CON31的b[3]为0使能GPIO2，要修改b[3]的前提是把b[19]设置为1。

``` C
	/* rk3399 GPIO2_D3 */   
	/* a. 使能GPIO2   
	 * set CRU to enable GPIO2   
	 * CRU_CLKGATE_CON31 0xFF760000 + 0x037c   
	 * (1<<(3+16)) | (0<<3)   
	 */   
```
   
&emsp;&emsp;b. 设置GPIO2_D3用于GPIO

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_064.png) 
<600px>

&emsp;&emsp;设置GRF_GPIO2D_IOMUX的b[7:6]为0b00把GPIO2_D3用作GPIO，要修改b[7:6]的前提是把b[23:22]设置为0b11。

``` C
	/* b. 设置GPIO2_D3用于GPIO   
	 * set PMU/GRF to configure GPIO2_D3 as GPIO   
	 * GRF_GPIO2D_IOMUX 0xFF770000 + 0x0e00c   
	 * bit[7:6] = 0b00   
	 * (3<<(6+16)) | (0<<6)   
	 */   
```
   
&emsp;&emsp;c. 设置GPIO2_D3作为output引脚

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_065.png) 
<600px>

&emsp;&emsp;设置GPIO_SWPORTA_DDR 寄存器b[27]为1，把GPIO2_D3设置为输出引脚。

&emsp;&emsp;<font color="# dd0000">注意：</font>

&emsp;&emsp;&emsp;&emsp;GPIO_A0~A7 对应bit0~bit7；GPIO_B0~B7 对应bit8~bit15；

&emsp;&emsp;&emsp;&emsp;GPIO_C0~C7 对应bit16~bit23；GPIO_D0~D7 对应bit24~bit31

``` C
	/* c. 设置GPIO2_D3作为output引脚   
	 * set GPIO_SWPORTA_DDR to configure GPIO2_D3 as output   
	 * GPIO_SWPORTA_DDR 0xFF780000 + 0x0004   
	 * bit[27] = 0b1   
	 */   
```
   
&emsp;&emsp;d. 设置GPIO2_D3输出高电平

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_066.png) 
<600px>

&emsp;&emsp;设置GPIO_SWPORTA_DR 寄存器b[27]为1，让GPIO2_D3输出高电平。

&emsp;&emsp;<font color="# dd0000">注意：</font>

&emsp;&emsp;&emsp;&emsp;GPIO_A0~A7 对应bit0~bit7；GPIO_B0~B7 对应bit8~bit15；

&emsp;&emsp;&emsp;&emsp;GPIO_C0~C7 对应bit16~bit23；GPIO_D0~D7 对应bit24~bit31

``` C
	/* d. 设置GPIO2_D3输出高电平   
	 * set GPIO_SWPORTA_DR to configure GPIO2_D3 output 1   
	 * GPIO_SWPORTA_DR 0xFF780000 + 0x0000   
	 * bit[27] = 0b1   
	 */   
```
   
&emsp;&emsp;e. 设置GPIO2_D3输出低电平

&emsp;&emsp;&emsp;&emsp;同样是设置GPIO_SWPORTA_DR 寄存器，把b[27]设为0，让GPIO2_D3输出低电平。

``` C
	/* e. 设置GPIO2_D3输出低电平   
	 * set GPIO_SWPORTA_DR to configure GPIO2_D3 output 0   
	 * GPIO_SWPORTA_DR 0xFF780000 + 0x0000   
	 * bit[27] = 0b0   
	 */   
```
   
#### 写程序   
##### RK3288   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\02_led_drv\   
	  	02_led_drv_for_boards\rk3288_src_bin   
```
   
&emsp;&emsp;硬件相关的文件是board_rk3288.c，其他文件跟LED框架驱动程序完全一样。

&emsp;&emsp;它首先构造了一个led_operations结构体，用来表示LED的硬件操作：

``` C
	91 static struct led_operations board_demo_led_opr = {   
	92     .num  = 1,   
	93     .init = board_demo_led_init,   
	94     .ctl  = board_demo_led_ctl,   
	95 };   
	96   
```
   
&emsp;&emsp;led_operations结构体中有init函数指针，它指向board_demo_led_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。

&emsp;&emsp;值得关注的是第32～35行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器：

``` C
	20 static volatile unsigned int *CRU_CLKGATE14_CON;   
	21 static volatile unsigned int *GRF_GPIO8A_IOMUX ;   
	22 static volatile unsigned int *GPIO8_SWPORTA_DDR;   
	23 static volatile unsigned int *GPIO8_SWPORTA_DR ;   
	24   
	25 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */      
	26 {   
	27     //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	28     if (which == 0)   
	29     {   
	30           if (!CRU_CLKGATE14_CON)   
	31           {   
	32                 CRU_CLKGATE14_CON = ioremap(0xFF760000 + 0x0198, 4);   
	33                 GRF_GPIO8A_IOMUX  = ioremap(0xFF770000 + 0x0080, 4);   
	34                 GPIO8_SWPORTA_DDR = ioremap(0xFF7F0000 + 0x0004, 4);   
	35                 GPIO8_SWPORTA_DR  = ioremap(0xFF7F0000 + 0x0000, 4);   
	36           }   
	37   
	38           /* rk3288 GPIO8_A1 */   
	39           /* a. 使能GPIO8   
	40            * set CRU to enable GPIO8   
	41            * CRU_CLKGATE14_CON 0xFF760000 + 0x198   
	42            * (1<<(8+16)) | (0<<8)   
	43            */   
	44           *CRU_CLKGATE14_CON = (1<<(8+16)) | (0<<8);   
	45   
	46           /* b. 设置GPIO8_A1用于GPIO   
	47            * set PMU/GRF to configure GPIO8_A1 as GPIO   
	48            * GRF_GPIO8A_IOMUX 0xFF770000 + 0x0080   
	49            * bit[3:2] = 0b00   
	50            * (3<<(2+16)) | (0<<2)   
	51            */   
	52           *GRF_GPIO8A_IOMUX =(3<<(2+16)) | (0<<2);   
	53   
	54           /* c. 设置GPIO8_A1作为output引脚   
	55            * set GPIO_SWPORTA_DDR to configure GPIO8_A1 as output   
	56            * GPIO_SWPORTA_DDR 0xFF7F0000 + 0x0004   
	57            * bit[1] = 0b1   
	58            */   
	59           *GPIO8_SWPORTA_DDR |= (1<<1);   
	60     }   
	61           return 0;   
	62 }   
	63   
```
   
&emsp;&emsp;led_operations结构体中有ctl函数指针，它指向board_demo_led_ctl函数，在里面将会根据参数设置LED引脚的输出电平：

``` C
	64 static int board_demo_led_ctl (int which, char status) /* 控制LED, which-哪个LED, status:1-亮, 0-灭*/   
	65 {   
	66     //printk("%s %s line %d, led %d, %s\n", __FILE__, __FUNCTION__, __LINE__, which, status ? "on" : "off");   
	67     if (which == 0)   
	68     {   
	69           if (status) /* on: output 0 */   
	70           {   
	71                 /* e. 设置GPIO8_A1输出低电平   
	72                  * set GPIO_SWPORTA_DR to configure GPIO8_A1 output 0   
	73                  * GPIO_SWPORTA_DR 0xFF7F0000 + 0x0000   
	74                  * bit[1] = 0b0   
	75                  */   
	76                 *GPIO8_SWPORTA_DR &= ~(1<<1);   
	77           }   
	78           else /* off: output 1 */   
	79           {   
	80                 /* d. 设置GPIO8_A1输出高电平   
	81                  * set GPIO_SWPORTA_DR to configure GPIO8_A1 output 1   
	82                  * GPIO_SWPORTA_DR 0xFF7F0000 + 0x0000   
	83                  * bit[1] = 0b1   
	84                  */   
	85                 *GPIO8_SWPORTA_DR |= (1<<1);   
	86           }   
	87     }   
	88     return 0;   
	89 }   
	90   
```
   
&emsp;&emsp;下面的get_board_led_opr函数供上层调用，给上层提供led_operations结构体：

``` C
	97 struct led_operations *get_board_led_opr(void)   
	98 {   
	99     return &board_demo_led_opr;   
	100 }   
	101   
```
   
##### RK3399   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\02_led_drv\   
	  	02_led_drv_for_boards\rk3399_src_bin   
```
   
&emsp;&emsp;硬件相关的文件是board_rk3399.c，其他文件跟LED框架驱动程序完全一样。

&emsp;&emsp;它首先构造了一个led_operations结构体，用来表示LED的硬件操作：

```bash
	91 static struct led_operations board_demo_led_opr = {   
	92    .num  = 1,   
	93    .init = board_demo_led_init,   
	94    .ctl  = board_demo_led_ctl,   
	95 };   
	96   
```
   
&emsp;&emsp;led_operations结构体中有init函数指针，它指向board_demo_led_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。

&emsp;&emsp;值得关注的是第32～35行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器：

```bash
	20 static volatile unsigned int *CRU_CLKGATE_CON31;   
	21 static volatile unsigned int *GRF_GPIO2D_IOMUX ;   
	22 static volatile unsigned int *GPIO2_SWPORTA_DDR;   
	23 static volatile unsigned int *GPIO2_SWPORTA_DR ;   
	24   
	25 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */      
	26 {   
	27    //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	28    if (which == 0)   
	29    {   
	30       if (!CRU_CLKGATE_CON31)   
	31       {   
	32          CRU_CLKGATE_CON31 = ioremap(0xFF760000 + 0x037c, 4);   
	33          GRF_GPIO2D_IOMUX  = ioremap(0xFF770000 + 0x0e00c, 4);   
	34          GPIO2_SWPORTA_DDR = ioremap(0xFF780000 + 0x0004, 4);   
	35          GPIO2_SWPORTA_DR  = ioremap(0xFF780000 + 0x0000, 4);   
	36       }   
	37   
	38       /* rk3399 GPIO2_D3 */   
	39       /* a. 使能GPIO2   
	40        * set CRU to enable GPIO2   
	41        * CRU_CLKGATE_CON31 0xFF760000 + 0x037c   
	42        * (1<<(3+16)) | (0<<3)   
	43        */   
	44       *CRU_CLKGATE_CON31 = (1<<(3+16)) | (0<<3);   
	45   
	46       /* b. 设置GPIO2_D3用于GPIO   
	47        * set PMU/GRF to configure GPIO2_D3 as GPIO   
	48        * GRF_GPIO2D_IOMUX 0xFF770000 + 0x0e00c   
	49        * bit[7:6] = 0b00   
	50        * (3<<(6+16)) | (0<<6)   
	51        */   
	52       *GRF_GPIO2D_IOMUX = (3<<(6+16)) | (0<<6);   
	53   
	54       /* c. 设置GPIO2_D3作为output引脚   
	55        * set GPIO_SWPORTA_DDR to configure GPIO2_D3 as output   
	56        * GPIO_SWPORTA_DDR 0xFF780000 + 0x0004   
	57        * bit[27] = 0b1   
	58        */   
	59       *GPIO2_SWPORTA_DDR |= (1<<27);   
	60    }   
	61    return 0;   
	62 }   
	63   
```
   
&emsp;&emsp;led_operations结构体中有ctl函数指针，它指向board_demo_led_ctl函数，在里面将会根据参数设置LED引脚的输出电平：

```bash
	64 static int board_demo_led_ctl (int which, char status) /* 控制LED, which-哪个LED, status:1-亮, 0-灭*/   
	65 {   
	66    //printk("%s %s line %d, led %d, %s\n", __FILE__, __FUNCTION__, __LINE__, which, status ? "on" : "off");   
	67    if (which == 0)   
	68    {   
	69       if (status) /* on: output 1 */   
	70       {   
	71          /* d. 设置GPIO2_D3输出高电平   
	72           * set GPIO_SWPORTA_DR to configure GPIO2_D3 output 1   
	73           * GPIO_SWPORTA_DR 0xFF780000 + 0x0000   
	74           * bit[27] = 0b1   
	75           */   
	76          *GPIO2_SWPORTA_DR |= (1<<27);   
	77       }   
	78       else /* off : output 0 */   
	79       {   
	80          /* e. 设置GPIO2_D3输出低电平   
	81           * set GPIO_SWPORTA_DR to configure GPIO2_D3 output 0   
	82           * GPIO_SWPORTA_DR 0xFF780000 + 0x0000   
	83           * bit[27] = 0b0   
	84           */   
	85          *GPIO2_SWPORTA_DR &= ~(1<<27);   
	86       }   
	87    }   
	88    return 0;   
	89 }   
	90   
```
   
&emsp;&emsp;下面的get_board_led_opr函数供上层调用，给上层提供led_operations结构体：

```bash
	97 struct led_operations *get_board_led_opr(void)   
	98 {   
	99    return &board_demo_led_opr;   
	100 }   
	101   
```
	   
#### 上机实验   
&emsp;&emsp;首先设置工具链，然后修改驱动程序Makefile指定内核源码路径，就可以编译驱动程序和测试程序了。

&emsp;&emsp;启动开发板，挂载NFS文件系统，这样就可以访问到Ubuntu中的文件。

&emsp;&emsp;最后，就可以在开发板上进行下列测试。

##### RK3288   
```bash
	#  insmod  100ask_led.ko   
	#  ./ledtest  /dev/100ask_led0  on   
	#  ./ledtest  /dev/100ask_led0  off   
```
   
##### RK3399   
&emsp;&emsp;要先禁止内核中原来的LED驱动，把“heatbeat”功能关闭，执行以下命令即可：

```bash
	#  echo none > /sys/class/leds/firefly\:yellow\:heartbeat/trigger   
	#  echo none > /sys/class/leds/firefly\:yellow\:user/trigger   
	#  echo none > /sys/class/leds/firefly\:red\:power/trigger   
```
   
&emsp;&emsp;这样就可以使用我们的驱动程序做实验了：

```bash
	#  insmod  100ask_led.ko   
	#  ./ledtest  /dev/100ask_led0  on   
	#  ./ledtest  /dev/100ask_led0  off   
```
   
&emsp;&emsp;如果想恢复原来的心跳功能，可以执行：

```bash
	#  echo heartbeat > /sys/class/leds/firefly\:yellow\:heartbeat/trigger   
	#  echo heartbeat > /sys/class/leds/firefly\:yellow\:user/trigger   
	#  echo heartbeat > /sys/class/leds/firefly\:red\:power/trigger   
```
   
#### 课后作业   
&emsp;&emsp;a. 在驱动里有ioremap，什么时候执行iounmap？请完善程序

&emsp;&emsp;b. 视频里我们只实现了点一个LED，请修改代码实现操作所有LED

### 野火/正点原子IMX6ULL的LED驱动程序   
&emsp;&emsp;野火、正点原子用的内核版本是<font color="# dd0000">4.1.15</font>，

&emsp;&emsp;我们用的内核版本是 <font color="# dd0000">linux 4.9.88</font>，

&emsp;&emsp;都是4.x版本，在学习上<font color="# dd0000">没有任何差别</font>。

&emsp;&emsp;你拿到板子后，可以使用他们出厂的系统，

&emsp;&emsp;也可以根据我们提供的高级用户手册更改为我们的系统。

#### 原理图   
##### 野火fire_imx6ull-pro开发板   
&emsp;&emsp;LED原理图如下，它使用GPIO5_IO03，引脚输出低电平时LED被点亮，输出高电平时LED被熄灭：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_067.png) 
<400px>
   
##### 正点原子Atk_imx6ull-alpha开发板   
&emsp;&emsp;LED原理图如下，它使用GPIO1_IO03，引脚输出低电平时LED被点亮，输出高电平时LED被熄灭：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_068.png) 
<800px>
   
#### 所涉及的寄存器操作   
&emsp;&emsp;GPIO模块图如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_023.png) 
<600px>

&emsp;&emsp;代码中对硬件的操作截图如下，截图便于对比，后面有文字便于复制：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_069.png) 
<800px>
   
   
##### <font color="#dd0000">野火fire_imx6ull-pro</font> 开发板   
&emsp;&emsp;步骤1：使能GPIO5

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_070.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置b[31:30]就可以使能GPIO5，设置为什么值呢？

&emsp;&emsp;&emsp;&emsp;看下图，设置为0b11：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_024.png) 
<400px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 00：该GPIO模块全程被关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② 01：该GPIO模块在CPU run mode情况下是使能的；在WAIT或STOP模式下，关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;③ 10：保留

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;④ 11：该GPIO模块全程使能

``` C
	/* GPIO5_IO03 */   
	/* a. 使能GPIO5   
	 * set CCM to enable GPIO5   
	 * CCM_CCGR1[CG15] 0x20C406C   
	 * bit[31:30] = 0b11   
	 */   
```
   
&emsp;&emsp;步骤2：设置GPIO5_IO03为GPIO模式

&emsp;&emsp;&emsp;&emsp;设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_071.png) 
<600px>
   
``` C
	/* b. 设置GPIO5_IO03用于GPIO   
	 * set IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3   
	 *		to configure GPIO5_IO03 as GPIO   
	 * IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3	0x2290014   
	 * bit[3:0] = 0b0101 alt5   
	 */   
```
   
&emsp;&emsp;步骤3：设置GPIO5_IO03为输出引脚，设置其输出电平

&emsp;&emsp;&emsp;&emsp;寄存器地址为：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_072.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置方向寄存器，把引脚设置为输出引脚：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_073.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置数据寄存器，设置引脚的输出电平：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_074.png) 
<600px>
   
``` C
	/* c. 设置GPIO5_IO03作为output引脚   
	 * set GPIO5_GDIR to configure GPIO5_IO03 as output   
	 * GPIO5_GDIR  0x020AC000 + 0x4   
	 * bit[3] = 0b1   
	 */   
   
	/* d. 设置GPIO5_DR输出低电平   
	 * set GPIO5_DR to configure GPIO5_IO03 output 0   
	 * GPIO5_DR 0x020AC000 + 0   
	 * bit[3] = 0b0   
	 */   
	   
	/* e. 设置GPIO5_IO3输出高电平   
	 * set GPIO5_DR to configure GPIO5_IO03 output 1   
	 * GPIO5_DR 0x020AC000 + 0   
	 * bit[3] = 0b1   
	 */   
```
   
##### <font color="#dd0000">正点原子Atk_imx6ull-alpha</font>开发板   
&emsp;&emsp;步骤1：使能GPIO1

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_075.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置b[27:26]就可以使能GPIO1，设置为什么值呢？

&emsp;&emsp;&emsp;&emsp;看下图，设置为0b11：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_024.png) 
<400px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 00：该GPIO模块全程被关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② 01：该GPIO模块在CPU run mode情况下是使能的；在WAIT或STOP模式下，关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;③ 10：保留

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;④ 11：该GPIO模块全程使能

``` C
	/* GPIO1_IO03 */   
	/* a. 使能GPIO1   
	 * set CCM to enable GPIO1   
	 * CCM_CCGR1[CG13] 0x20C406C   
	 * bit[27:26] = 0b11   
	 */   
```
   
&emsp;&emsp;步骤2：设置GPIO1_IO03为GPIO模式

&emsp;&emsp;&emsp;&emsp;设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_076.png) 
<600px>
   
``` C
	/* b. 设置GPIO1_IO03用于GPIO   
	 * set IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03   
	 *		to configure GPIO1_IO03 as GPIO   
	 * IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03  0x20E0068   
	 * bit[3:0] = 0b0101 alt5   
	 */   
```
   
&emsp;&emsp;步骤3：设置GPIO1_IO03为输出引脚，设置其输出电平

&emsp;&emsp;&emsp;&emsp;寄存器地址为：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_072.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置方向寄存器，把引脚设置为输出引脚：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_073.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置数据寄存器，设置引脚的输出电平：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_074.png) 
<600px>
   
``` C
	/* c. 设置GPIO1_IO03作为output引脚   
	 * set GPIO1_GDIR to configure GPIO1_IO03 as output   
	 * GPIO1_GDIR  0x0209C000 + 0x4   
	 * bit[3] = 0b1   
	 */   
	   
	/* d. 设置GPIO1_DR输出低电平   
	 * set GPIO1_DR to configure GPIO1_IO03 output 0   
	 * GPIO1_DR 0x0209C000 + 0   
	 * bit[3] = 0b0   
	 */   
	   
	/* e. 设置GPIO1_IO03输出高电平   
	 * set GPIO1_DR to configure GPIO1_IO03 output 1   
	 * GPIO1_DR 0x0209C000 + 0   
	 * bit[3] = 0b1   
	 */   
```
	   
#### 写程序   
##### <font color="#dd0000">野火fire_imx6ull-pro</font>开发板   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\02_led_drv\   
	  	02_led_drv_for_boards\fire_imx6ull-pro_src_bin   
```
   
&emsp;&emsp;硬件相关的文件是board_fire_imx6ull-pro.c，其他文件跟LED框架驱动程序完全一样。

&emsp;&emsp;它首先构造了一个led_operations结构体，用来表示LED的硬件操作：

``` C
	100 static struct led_operations board_demo_led_opr = {   
	101    .num  = 1,   
	102    .init = board_demo_led_init,   
	103    .ctl  = board_demo_led_ctl,   
	104 };   
	105   
```
   
&emsp;&emsp;led_operations结构体中有init函数指针，它指向board_demo_led_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。

&emsp;&emsp;值得关注的是第35～38行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器：

``` C
	21 static volatile unsigned int *CCM_CCGR1                       ;   
	22 static volatile unsigned int *IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3;   
	23 static volatile unsigned int *GPIO5_GDIR                      ;   
	24 static volatile unsigned int *GPIO5_DR                        ;   
	25   
	26 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */      
	27 {   
	28    unsigned int val;   
	29   
	30    //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	31    if (which == 0)   
	32    {   
	33       if (!CCM_CCGR1)   
	34       {   
	35          CCM_CCGR1  = ioremap(0x20C406C, 4);   
	36       IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3 = ioremap(0x2290014, 4);   
	37          GPIO5_GDIR  = ioremap(0x020AC000 + 0x4, 4);   
	38          GPIO5_DR   = ioremap(0x020AC000 + 0, 4);   
	39       }   
	40   
	41       /* GPIO5_IO03 */   
	42       /* a. 使能GPIO5   
	43        * set CCM to enable GPIO5   
	44        * CCM_CCGR1[CG15] 0x20C406C   
	45        * bit[31:30] = 0b11   
	46        */   
	47       *CCM_CCGR1 |= (3<<30);   
	48   
	49       /* b. 设置GPIO5_IO03用于GPIO   
	50        * set IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3   
	51        *     to configure GPIO5_IO03 as GPIO   
	52        * IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3  0x2290014   
	53        * bit[3:0] = 0b0101 alt5   
	54        */   
	55       val = *IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3;   
	56       val &= ~(0xf);   
	57       val |= (5);   
	58       *IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3 = val;   
	59   
	60   
	61       /* b. 设置GPIO5_IO03作为output引脚   
	62        * set GPIO5_GDIR to configure GPIO5_IO03 as output   
	63        * GPIO5_GDIR  0x020AC000 + 0x4   
	64        * bit[3] = 0b1   
	65        */   
	66       *GPIO5_GDIR |= (1<<3);   
	67    }   
	68   
	69    return 0;   
	70 }   
	71   
```
   
&emsp;&emsp;led_operations结构体中有ctl函数指针，它指向board_demo_led_ctl函数，在里面将会根据参数设置LED引脚的输出电平：

``` C
	72 static int board_demo_led_ctl (int which, char status) /* 控制LED, which-哪个LED, status:1-亮,0-灭 */   
	73 {   
	74    //printk("%s %s line %d, led %d, %s\n", __FILE__, __FUNCTION__, __LINE__, which, status ? "on" : "off");   
	75    if (which == 0)   
	76    {   
	77       if (status) /* on: output 0*/   
	78       {   
	79          /* d. 设置GPIO5_DR输出低电平   
	80           * set GPIO5_DR to configure GPIO5_IO03 output 0   
	81           * GPIO5_DR 0x020AC000 + 0   
	82           * bit[3] = 0b0   
	83           */   
	84          *GPIO5_DR &= ~(1<<3);   
	85       }   
	86       else  /* off: output 1*/   
	87       {   
	88          /* e. 设置GPIO5_IO3输出高电平   
	89           * set GPIO5_DR to configure GPIO5_IO03 output 1   
	90           * GPIO5_DR 0x020AC000 + 0   
	91           * bit[3] = 0b1   
	92           */   
	93          *GPIO5_DR |= (1<<3);   
	94       }   
	95   
	96    }   
	97    return 0;   
	98 }   
	99   
```
   
&emsp;&emsp;下面的get_board_led_opr函数供上层调用，给上层提供led_operations结构体：

``` C
	106 struct led_operations *get_board_led_opr(void)   
	107 {   
	108    return &board_demo_led_opr;   
	109 }   
	110   
```
   
##### <font color="#dd0000">正点原子Atk_imx6ull-alpha</font>开发板   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\02_led_drv\   
	     02_led_drv_for_boards\atk_imx6ull-alpha_src_bin   
```
   
&emsp;&emsp;硬件相关的文件是board_atk_imx6ull-alpha.c，其他文件跟LED框架驱动程序完全一样。

&emsp;&emsp;它首先构造了一个led_operations结构体，用来表示LED的硬件操作：

``` C
	100 static struct led_operations board_demo_led_opr = {   
	101    .num  = 1,   
	102    .init = board_demo_led_init,   
	103    .ctl  = board_demo_led_ctl,   
	104 };   
	105   
```
   
&emsp;&emsp;led_operations结构体中有init函数指针，它指向board_demo_led_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。

&emsp;&emsp;值得关注的是第35～38行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器：

``` C
	26 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */   
	27 {   
	28    unsigned int val;   
	29   
	30    //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	31    if (which == 0)   
	32    {   
	33       if (!CCM_CCGR1)   
	34       {   
	35          CCM_CCGR1 = ioremap(0x20C406C, 4);   
	36          IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03 = ioremap(0x20E0068, 4);   
	37          GPIO1_GDIR = ioremap(0x0209C000 + 0x4, 4);   
	38          GPIO1_DR  = ioremap(0x0209C000 + 0, 4);   
	39       }   
	40   
	41       /* GPIO1_IO03 */   
	42       /* a. 使能GPIO1   
	43        * set CCM to enable GPIO1   
	44        * CCM_CCGR1[CG13] 0x20C406C   
	45        * bit[27:26] = 0b11   
	46        */   
	47       *CCM_CCGR1 |= (3<<26);   
	48   
	49       /* b. 设置GPIO1_IO03用于GPIO   
	50        * set IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03   
	51        *     to configure GPIO1_IO03 as GPIO   
	52        * IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03  0x20E0068   
	53        * bit[3:0] = 0b0101 alt5   
	54        */   
	55       val = *IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03;   
	56       val &= ~(0xf);   
	57       val |= (5);   
	58       *IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03 = val;   
	59   
	60   
	61       /* c. 设置GPIO1_IO03作为output引脚   
	62        * set GPIO1_GDIR to configure GPIO1_IO03 as output   
	63        * GPIO1_GDIR  0x0209C000 + 0x4   
	64        * bit[3] = 0b1   
	65        */   
	66       *GPIO1_GDIR |= (1<<3);   
	67    }   
	68   
	69    return 0;   
	70 }   
	71   
```
   
&emsp;&emsp;led_operations结构体中有ctl函数指针，它指向board_demo_led_ctl函数，在里面将会根据参数设置LED引脚的输出电平：

``` C
	72 static int board_demo_led_ctl (int which, char status) /* 控制LED, which-哪个LED, status:1-亮,0-灭 */   
	73 {   
	74    //printk("%s %s line %d, led %d, %s\n", __FILE__, __FUNCTION__, __LINE__, which, status ? "on" : "off");   
	75    if (which == 0)   
	76    {   
	77       if (status) /* on: output 0*/   
	78       {   
	79          /* d. 设置GPIO1_DR输出低电平   
	80           * set GPIO1_DR to configure GPIO1_IO03 output 0   
	81           * GPIO1_DR 0x0209C000 + 0   
	82           * bit[3] = 0b0   
	83           */   
	84          *GPIO1_DR &= ~(1<<3);   
	85       }   
	86       else  /* off: output 1*/   
	87       {   
	88          /* e. 设置GPIO1_IO03输出高电平   
	89           * set GPIO1_DR to configure GPIO1_IO03 output 1   
	90           * GPIO1_DR 0x0209C000 + 0   
	91           * bit[3] = 0b1   
	92           */   
	93          *GPIO1_DR |= (1<<3);   
	94       }   
	95   
	96    }   
	97    return 0;   
	98 }   
	99   
```
   
&emsp;&emsp;下面的get_board_led_opr函数供上层调用，给上层提供led_operations结构体：

``` C
	06 struct led_operations *get_board_led_opr(void)   
	07 {   
	08    return &board_demo_led_opr;   
	09 }   
	10   
```
   
#### 上机实验   
&emsp;&emsp;首先设置工具链，然后修改驱动程序Makefile指定内核源码路径，就可以编译驱动程序和测试程序了。

&emsp;&emsp;启动开发板，挂载NFS文件系统，这样就可以访问到Ubuntu中的文件。

&emsp;&emsp;最后，就可以在开发板上进行下列测试。

##### <font color="#dd0000">野火fire_imx6ull-pro</font> 开发板   
&emsp;&emsp;<font color="# dd0000">注意：</font>如果要使用板子自带的系统，关闭原有LED驱动的方法是类似的，也是进入开发板/sys/class/leds/目录，对于每一个LED在该目录下都有一个子目录，假设某个子目录名为XXX，则执行如下命令：

```bash
	#  echo none  >  /sys/class/leds/XXX/trigger   
```
   
&emsp;&emsp;使用我们的系统时，按如下操作。

&emsp;&emsp;要先禁止内核中原来的LED驱动，把“heatbeat”功能关闭，执行以下命令即可：

```bash
	#  echo none > /sys/class/leds/cpu/trigger   
```
   
&emsp;&emsp;这样就可以使用我们的驱动程序做实验了：

```bash
	#  insmod  100ask_led.ko   
	# ./ledtest  /dev/100ask_led0  on   
	# ./ledtest  /dev/100ask_led0  off   
```
   
&emsp;&emsp;如果想恢复原来的心跳功能，可以执行：

```bash
	#  echo heartbeat > /sys/class/leds/cpu/trigger   
```
##### <font color="#dd0000">正点原子Atk_imx6ull-alpha</font>开发板   
&emsp;&emsp;<font color="# dd0000">注意：</font>如果要使用板子自带的系统，关闭原有LED驱动的方法是类似的，也是进入开发板/sys/class/leds/目录，对于每一个LED在该目录下都有一个子目录，假设某个子目录名为XXX，则执行如下命令：

```bash
	#  echo none  >  /sys/class/leds/XXX/trigger   
```
   
&emsp;&emsp;使用我们的系统时，按如下操作。

&emsp;&emsp;要先禁止内核中原来的LED驱动，把“heatbeat”功能关闭，执行以下命令即可：

```bash
	#  echo none > /sys/class/leds/sys-led/trigger   
```
   
&emsp;&emsp;这样就可以使用我们的驱动程序做实验了：

```bash
	#  insmod  100ask_led.ko   
	#  ./ledtest  /dev/100ask_led0  on   
	#  ./ledtest  /dev/100ask_led0  off   
```
   
&emsp;&emsp;如果想恢复原来的心跳功能，可以执行：

```bash
	#  echo heartbeat > /sys/class/leds/sys-led/trigger   
```
#### 课后作业   
&emsp;&emsp;a. 在驱动里有ioremap，什么时候执行iounmap？请完善程序

&emsp;&emsp;b. 视频里我们只实现了点一个LED，开发板上也只有一个LED，

&emsp;&emsp;所以，请修改代码操作蜂鸣器。

   
### 百问网IMX6ULL-QEMU的LED驱动程序   
&emsp;&emsp;使用QEMU模拟的硬件，它的硬件资源可以随意扩展。

&emsp;&emsp;在IMX6ULL QEMU 虚拟开发板上，我们为它设计了4个 LED。

#### 看原理图确定引脚及操作方法   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_077.png) 
<400px>
&emsp;&emsp;从上图可知，这4个 LED 用到了GPIO5_3、GPIO1_3、GPIO1_5、GPIO1_6 共4个引脚。

&emsp;&emsp;在芯片手册里，这些引脚的名字是：GPIO5_IO03、GPIO1_IO03、GPIO1_IO05、GPIO1_IO06。可以根据名字搜到对应的寄存器。

&emsp;&emsp;当这些引脚输出低电平时，对应的LED被点亮；输出高电平时，LED熄灭。

   
#### 所涉及的寄存器操作   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_023.png) 
<600px>
&emsp;&emsp;步骤1：使能GPIO1、GPIO5

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_078.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置b[31:30]、b[27:26]就可以使能GPIO5、GPIO1，设置为什么值呢？

&emsp;&emsp;&emsp;&emsp;看下图，设置为0b11：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_024.png) 
<400px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 00：该GPIO模块全程被关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② 01：该GPIO模块在CPU run mode情况下是使能的；在WAIT或STOP模式下，关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;③ 10：保留

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;④ 11：该GPIO模块全程使能

&emsp;&emsp;步骤2：设置GPIO5_IO03、GPIO1_IO03、GPIO1_IO05、GPIO1_IO06为GPIO模式

&emsp;&emsp;&emsp;&emsp;① 对于GPIO5_IO03，设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_071.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;② 对于GPIO1_IO03，设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_076.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;③ 对于GPIO1_IO05，设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_079.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;④ 对于GPIO1_IO06，设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_080.png) 
<600px>


&emsp;&emsp;步骤3：设置GPIO5_IO03、GPIO1_IO03、GPIO1_IO05、GPIO1_IO06为输出引脚，设置其输出电平

&emsp;&emsp;&emsp;&emsp;寄存器地址为：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_072.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置方向寄存器，把引脚设置为输出引脚：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_073.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置数据寄存器，设置引脚的输出电平：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_074.png) 
<600px>
   
   
#### 写程序   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

	01_all_series_quickstart\04_快速入门(正式开始)\   
	02_嵌入式Linux驱动开发基础知识\source\02_led_drv\   
	     02_led_drv_for_boards\100ask_imx6ull-qemu_src_bin

&emsp;&emsp;硬件相关的文件是board_100ask_imx6ull-qemu.c，其他文件跟LED框架驱动程序完全一样。

&emsp;&emsp;涉及的寄存器挺多，一个一个去执行ioremap效率太低。

&emsp;&emsp;先定义结构体，然后对结构体指针进行ioremap，这些结构体在。

&emsp;&emsp;对于IOMUXC，可以如下定义：

``` C
	struct iomux {   
		volatile unsigned int unnames[23];   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO00;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO01;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO02;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO04;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO05;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO06;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO07;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO08;   
		volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO09;   
	};   
   
	struct iomux  *iomux = ioremap(0x20e0000,  sizeof(struct iomux));   
```
   
&emsp;&emsp;对于GPIO，可以如下定义：

``` C
	struct imx6ull_gpio {   
		volatile unsigned int dr;   
		volatile unsigned int gdir;   
		volatile unsigned int psr;   
		volatile unsigned int icr1;   
		volatile unsigned int icr2;   
		volatile unsigned int imr;   
		volatile unsigned int isr;   
		volatile unsigned int edge_sel;   
	};   
	struct imx6ull_gpio *gpio1 = ioremap(0x209C000,  sizeof(struct imx6ull_gpio));   
	struct imx6ull_gpio *gpio5 = ioremap(0x20AC000,  sizeof(struct imx6ull_gpio));   
```


&emsp;&emsp;开始详细分析board_100ask_imx6ull-qemu.c。

&emsp;&emsp;它首先构造了一个led_operations结构体，用来表示LED的硬件操作：

``` C
	176 static struct led_operations board_demo_led_opr = {   
	177    .num  = 4,   
	178    .init = board_demo_led_init,   
	179    .ctl  = board_demo_led_ctl,   
	180 };   
	181   
```
   
&emsp;&emsp;led_operations结构体中有init函数指针，它指向board_demo_led_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。

&emsp;&emsp;值得关注的是第61～66行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器：

``` C
	57 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */   
	58 {   
	59    if (!CCM_CCGR1)   
	60    {   
	61       CCM_CCGR1 = ioremap(0x20C406C, 4);   
	62       IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3 = ioremap(0x2290014, 4);   
	63   
	64       iomux = ioremap(0x20e0000, sizeof(struct iomux));   
	65       gpio1 = ioremap(0x209C000, sizeof(struct imx6ull_gpio));   
	66       gpio5 = ioremap(0x20AC000, sizeof(struct imx6ull_gpio));   
	67    }   
	68   
	69    if (which == 0)   
	70    {   
	71       /* 1. enable GPIO5   
	72        * CG15, b[31:30] = 0b11   
	73        */   
	74       *CCM_CCGR1 |= (3<<30);   
	75   
	76       /* 2. set GPIO5_IO03 as GPIO   
	77        * MUX_MODE, b[3:0] = 0b101   
	78        */   
	79       *IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER3 = 5;   
	80   
	81       /* 3. set GPIO5_IO03 as output   
	82        * GPIO5 GDIR, b[3] = 0b1   
	83        */   
	84       gpio5->gdir |= (1<<3);   
	85    }   
	86    else if(which == 1)   
	87    {   
	88       /* 1. enable GPIO1   
	89        * CG13, b[27:26] = 0b11   
	90        */   
	91       *CCM_CCGR1 |= (3<<26);   
	92   
	93       /* 2. set GPIO1_IO03 as GPIO   
	94        * MUX_MODE, b[3:0] = 0b101   
	95        */   
	96       iomux->IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03 = 5;   
	97   
	98       /* 3. set GPIO1_IO03 as output   
	99        * GPIO1 GDIR, b[3] = 0b1   
	100        */   
	101       gpio1->gdir |= (1<<3);   
	102    }   
	103    else if(which == 2)   
	104    {   
	105       /* 1. enable GPIO1   
	106        * CG13, b[27:26] = 0b11   
	107        */   
	108       *CCM_CCGR1 |= (3<<26);   
	109   
	110       /* 2. set GPIO1_IO05 as GPIO   
	111        * MUX_MODE, b[3:0] = 0b101   
	112        */   
	113       iomux->IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO05 = 5;   
	114   
	115       /* 3. set GPIO1_IO05 as output   
	116        * GPIO1 GDIR, b[5] = 0b1   
	117        */   
	118       gpio1->gdir |= (1<<5);   
	119    }   
	120    else if(which == 3)   
	121    {   
	122       /* 1. enable GPIO1   
	123        * CG13, b[27:26] = 0b11   
	124        */   
	125       *CCM_CCGR1 |= (3<<26);   
	126   
	127       /* 2. set GPIO1_IO06 as GPIO   
	128        * MUX_MODE, b[3:0] = 0b101   
	129        */   
	130       iomux->IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO06 = 5;   
	131   
	132       /* 3. set GPIO1_IO06 as output   
	133        * GPIO1 GDIR, b[6] = 0b1   
	134        */   
	135       gpio1->gdir |= (1<<6);   
	136    }   
	137   
	138    //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	139    return 0;   
	140 }   
	141   
```
   
&emsp;&emsp;led_operations结构体中有ctl函数指针，它指向board_demo_led_ctl函数，在里面将会根据参数设置LED引脚的输出电平：

``` C
	142 static int board_demo_led_ctl (int which, char status) /* 控制LED, which-哪个LED, status:1-亮,0-灭 */   
	143 {   
	144    //printk("%s %s line %d, led %d, %s\n", __FILE__, __FUNCTION__, __LINE__, which, status ? "on" : "off");   
	145    if (which == 0)   
	146    {   
	147       if (status)  /* on : output 0 */   
	148          gpio5->dr &= ~(1<<3);   
	149       else       /* on : output 1 */   
	150          gpio5->dr |= (1<<3);   
	151    }   
	152    else if (which == 1)   
	153    {   
	154       if (status)  /* on : output 0 */   
	155          gpio1->dr &= ~(1<<3);   
	156       else       /* on : output 1 */   
	157          gpio1->dr |= (1<<3);   
	158    }   
	159    else if (which == 2)   
	160    {   
	161       if (status)  /* on : output 0 */   
	162          gpio1->dr &= ~(1<<5);   
	163       else       /* on : output 1 */   
	164          gpio1->dr |= (1<<5);   
	165    }   
	166    else if (which == 3)   
	167    {   
	168       if (status)  /* on : output 0 */   
	169          gpio1->dr &= ~(1<<6);   
	170       else       /* on : output 1 */   
	171          gpio1->dr |= (1<<6);   
	172    }   
	173    return 0;   
	174 }   
	175   
```
   
&emsp;&emsp;下面的get_board_led_opr函数供上层调用，给上层提供led_operations结构体：

``` C
	182 struct led_operations *get_board_led_opr(void)   
	183 {   
	184    return &board_demo_led_opr;   
	185 }   
	186   
```
   
#### 上机实验   
&emsp;&emsp;先启动IMX6ULL QEMU模拟器，挂载NFS文件系统。

&emsp;&emsp;运行QEMU时，

&emsp;&emsp;QEMU内部为主机虚拟出一个网卡, IP为 10.0.2.2，

&emsp;&emsp;IMX6ULL有一个网卡, IP为 10.0.2.15，

&emsp;&emsp;它连接到主机的虚拟网卡。

&emsp;&emsp;这样IMX6ULL就可以通过10.0.2.2去访问Ubuntu了。

&emsp;&emsp;然后执行以下命令安装驱动、执行测试程序：

```bash
	#  insmod  100ask_led.ko   
	#  ./ledtest  /dev/100ask_led0  on   
	#  ./ledtest  /dev/100ask_led0  off   
```
   
   
#### 课后作业   
&emsp;&emsp;a. 在驱动里有ioremap，什么时候执行iounmap？请完善程序

&emsp;&emsp;b. 驱动程序中有太多的if判断，请优化程序减少if的使用

   
## 驱动设计的思想：面向对象/分层/分离   
### 面向对象   
&emsp;&emsp;字符设备驱动程序抽象出一个file_operations结构体；

&emsp;&emsp;我们写的程序针对硬件部分抽象出led_operations结构体。

   
### 分层   
&emsp;&emsp;上下分层，比如我们前面写的LED驱动程序就分为2层：

&emsp;&emsp;&emsp;&emsp;① 上层实现硬件无关的操作，比如注册字符设备驱动：leddrv.c

&emsp;&emsp;&emsp;&emsp;② 下层实现硬件相关的操作，比如board_A.c实现单板A的LED操作

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_081.png) 
<400px>
   
### 分离   
&emsp;&emsp;还能不能改进？<font color="# dd0000">分离</font>。

&emsp;&emsp;在board_A.c中，实现了一个led_operations，为LED引脚实现了初始化函数、控制函数：

``` C
	static struct led_operations board_demo_led_opr = {   
		.num  = 1,   
		.init = board_demo_led_init,   
		.ctl  = board_demo_led_ctl,   
	};   
```
   
&emsp;&emsp;如果硬件上更换一个引脚来控制LED怎么办？你要去修改上面结构体中的init、ctl函数。

&emsp;&emsp;实际情况是，每一款芯片它的GPIO操作都是类似的。比如：GPIO1_3、GPIO5_4这2个引脚接到LED：

&emsp;&emsp;&emsp;&emsp;① GPIO1_3属于第1组，即GPIO1。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;有方向寄存器DIR、数据寄存器DR等，基础地址是addr_base_addr_gpio1。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;设置为output引脚：修改GPIO1的DIR寄存器的bit3。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;设置输出电平：修改GPIO1的DR寄存器的bit3。

&emsp;&emsp;&emsp;&emsp;② GPIO5_4属于第5组，即GPIO5。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;有方向寄存器DIR、数据寄存器DR等，基础地址是addr_base_addr_gpio5。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;设置为output引脚：修改GPIO5的DIR寄存器的bit4。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;设置输出电平：修改GPIO5的DR寄存器的bit4。



&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;既然引脚操作那么有规律，并且这是跟主芯片相关的，那可以针对该芯片写出比较通用的硬件操作代码。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;比如board_A.c使用芯片chipY，那就可以写出：chipY_gpio.c，它实现芯片Y的GPIO操作，适用于芯片Y的所有GPIO引脚。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;使用时，我们只需要在board_A_led.c中指定使用哪一个引脚即可。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;程序结构如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_082.png) 
<600px>


&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;以面向对象的思想，在board_A_led.c中实现led_resouce结构体，它定义“资源”──要用哪一个引脚。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;在chipY_gpio.c中仍是实现led_operations结构体，它要写得更完善，支持所有GPIO。

   
   
### 写示例代码   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\02_led_drv\03_led_drv_template_seperate   
```
	   
&emsp;&emsp;程序仍分为上下结构：上层leddrv.c向内核注册file_operations结构体；下层chip_demo_gpio.c提供led_operations结构体来操作硬件。

&emsp;&emsp;下层的代码分为2个：chip_demo_gpio.c实现通用的GPIO操作，board_A_led.c指定使用哪个GPIO，即“资源”。

&emsp;&emsp;led_resource.h中定义了led_resource结构体，用来描述GPIO：

``` C
	04 /* GPIO3_0 */   
	05 /* bit[31:16] = group */   
	06 /* bit[15:0]  = which pin */   
	07 # define GROUP(x) (x>>16)   
	08 # define PIN(x)   (x&0xFFFF)   
	09 # define GROUP_PIN(g,p) ((g<<16) | (p))   
	10   
	11 struct led_resource {   
	12     int pin;   
	13 };   
	14   
	15 struct led_resource *get_led_resouce(void);   
	16   
```
   
&emsp;&emsp;board_A_led.c指定使用哪个GPIO，它实现一个led_resource结构体，并提供访问函数：

``` C
	02 # include "led_resource.h"   
	03   
	04 static struct led_resource board_A_led = {   
	05     .pin = GROUP_PIN(3,1),   
	06 };   
	07   
	08 struct led_resource *get_led_resouce(void)   
	09 {   
	10     return &board_A_led;   
	11 }   
	12   
```


&emsp;&emsp;chip_demo_gpio.c中，首先获得board_A_led.c实现的led_resource结构体，然后再进行其他操作，请看下面第26行：

``` C
	20 static struct led_resource *led_rsc;   
	21 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */   
	22 {   
	23     //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	24     if (!led_rsc)   
	25     {   
	26           led_rsc = get_led_resouce();   
	27     }   
	28   
```
   
   
### 课后作业   
&emsp;&emsp;使用“分离”的思想，去改造前面写的LED驱动程序：实现led_resouce，在里面可以指定要使用哪一个LED；改造led_operations，让它能支持更多GPIO。

&emsp;&emsp;<font color="# dd0000">注意：</font>作为练习，led_operations结构体不需要写得很完善，不需要支持所有GPIO，你可以只支持若干个GPIO即可。

   
## 驱动进化之路：总线设备驱动模型   
&emsp;&emsp;示例：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_083.png) 
<800px>
   
### 驱动编写的3种方法   
&emsp;&emsp;以LED驱动为例：

#### 传统写法   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_084.png) 
<600px>
&emsp;&emsp;使用哪个引脚，怎么操作引脚，都写死在代码中。

&emsp;&emsp;最简单，不考虑扩展性，可以快速实现功能。

&emsp;&emsp;修改引脚时，需要重新编译。

#### 总线设备驱动模型   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_085.png) 
<600px>
&emsp;&emsp;引入platform_device/platform_driver，将“资源”与“驱动”分离开来。

&emsp;&emsp;代码稍微复杂，但是易于扩展。

&emsp;&emsp;冗余代码太多，修改引脚时设备端的代码需要重新编译。

&emsp;&emsp;更换引脚时，上图中的led_drv.c基本不用改，但是需要修改led_dev.c

   
#### 设备树   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_086.png) 
<600px>
&emsp;&emsp;通过配置文件──设备树来定义“资源”。

&emsp;&emsp;代码稍微复杂，但是易于扩展。

&emsp;&emsp;无冗余代码，修改引脚时只需要修改dts文件并编译得到dtb文件，把它传给内核。

&emsp;&emsp;无需重新编译内核/驱动。

   
### 在Linux中实现“分离”：Bus/Dev/Drv模型   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_087.png) 
<1200px>
### 匹配规则   
#### 最先比较：platform_device. driver_override和platform_driver.driver.name   
&emsp;&emsp;可以设置platform_device的driver_override，强制选择某个platform_driver。

#### 然后比较：platform_device. name和platform_driver.id_table[i].name   
&emsp;&emsp;Platform_driver.id_table是“platform_device_id”指针，表示该drv支持若干个device，它里面列出了各个device的<font color="# dd0000">{.name, .driver_data</font>}，其中的“name”表示该drv支持的设备的名字，driver_data是些提供给该device的私有数据。

   
#### 最后比较：platform_device.name和platform_driver.driver.name   
&emsp;&emsp;platform_driver.id_table可能为空，

&emsp;&emsp;这时可以根据platform_driver.driver.name来寻找同名的platform_device。

#### 函数调用关系   
``` C
platform_device_register   
	platform_device_add   
		device_add   
			bus_add_device // 放入链表   
			bus_probe_device  // probe枚举设备，即找到匹配的(dev, drv)   
				device_initial_probe   
					__device_attach   
						bus_for_each_drv(...,__device_attach_driver,...)   
							__device_attach_driver   
								driver_match_device(drv, dev) // 是否匹配   
								driver_probe_device       // 调用drv的probe   
   
platform_driver_register   
	__platform_driver_register   
		driver_register   
			bus_add_driver // 放入链表   
				driver_attach(drv)   
						bus_for_each_dev(drv->bus, NULL, drv, __driver_attach);   
							__driver_attach   
								driver_match_device(drv, dev) // 是否匹配   
								driver_probe_device       // 调用drv的probe   
```
   
### 常用函数   
&emsp;&emsp;这些函数可查看内核源码：drivers/base/platform.c，根据函数名即可知道其含义。

&emsp;&emsp;下面摘取常用的几个函数。

#### 注册/反注册   
&emsp;&emsp;platform_device_register/ platform_device_unregister

&emsp;&emsp;platform_driver_register/ platform_driver_unregister

&emsp;&emsp;platform_add_devices // 注册多个device

#### 获得资源   
&emsp;&emsp;返回该dev中某类型(type)资源中的第几个(num)：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_088.png) 
<600px>

&emsp;&emsp;返回该dev所用的第几个(num)中断：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_089.png) 
<600px>


&emsp;&emsp;通过名字(name)返回该dev的某类型(type)资源：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_090.png) 
<600px>


&emsp;&emsp;通过名字(name)返回该dev的中断号：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_091.png) 
<600px>
   
### 怎么写程序   
#### 分配/设置/注册platform_device结构体   
&emsp;&emsp;在里面定义所用资源，指定设备名字。

#### 分配/设置/注册platform_driver结构体   
&emsp;&emsp;在其中的probe函数里，分配/设置/注册file_operations结构体，

&emsp;&emsp;并从platform_device中确实所用硬件资源。

&emsp;&emsp;指定platform_driver的名字。

   
### 课后作业   
&emsp;&emsp;在内核源码中搜索platform_device_register可以得到很多驱动，选择一个作为例子：

&emsp;&emsp;&emsp;&emsp;① 确定它的名字

&emsp;&emsp;&emsp;&emsp;② 根据它的名字找到对应的platform_driver

&emsp;&emsp;&emsp;&emsp;③ 进入platform_device_register/platform_driver_register内部，分析dev和drv的匹配过程

   
## LED模板驱动程序的改造：总线设备驱动模型   
### 原来的框架   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_092.png) 
<800px>
### 要实现的框架===   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_093.png) 
<800px>
   
### 写代码   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

	01_all_series_quickstart\04_快速入门(正式开始)\   
	02_嵌入式Linux驱动开发基础知识\source\   
	02_led_drv\04_led_drv_template_bus_dev_drv   
   
#### 注意事项   
&emsp;&emsp;① 如果platform_device中不提供release函数，如下图所示不提供红框部分的函数：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_094.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;则在调用platform_device_unregister时会出现警告，如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_095.png) 
<600px>


&emsp;&emsp;&emsp;&emsp;你可以提供一个release函数，如果实在无事可做，把这函数写为空。

&emsp;&emsp;② EXPORT_SYMBOL

&emsp;&emsp;&emsp;&emsp;a.c编译为a.ko，里面定义了func_a；如果它想让b.ko使用该函数，那么a.c里需要导出此函数(如果a.c, b.c都编进内核，则无需导出)：

&emsp;&emsp;&emsp;&emsp;EXPORT_SYMBOL(led_device_create);

&emsp;&emsp;&emsp;&emsp;并且，使用时要先加载a.ko。

&emsp;&emsp;&emsp;&emsp;如果先加载b.ko，会有类似如下“Unknown symbol”的提示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_096.png) 
<600px>
   
   
   
#### 实现platform_device结构体   
&emsp;&emsp;board_A.c作为一个可加载模块，里面也有入口函数、出口函数。在入口函数中注册platform_device结构体，在platform_device结构体中指定使用哪个GPIO引脚。

&emsp;&emsp;首先看入口函数，它调用platform_device_register函数，向内核注册board_A_led_dev结构体：

``` C
	50 static int __init led_dev_init(void)   
	51 {   
	52    int err;   
	53   
	54    err = platform_device_register(&board_A_led_dev);   
	55   
	56    return 0;   
	57 }   
	58   
```
	   
&emsp;&emsp;board_A_led_dev结构体定义如下。

&emsp;&emsp;在resouces数组中指定了2个引脚(第27～38行)；

&emsp;&emsp;我们还提供了一个空函数led_dev_release(第23～25行)，它被赋给board_A_led_dev结构体(第46行)，这个函数在卸载platform_device时会被调用，如果不提供的话内核会打印警告信息。

``` C
	23 static void led_dev_release(struct device *dev)   
	24 {   
	25 }   
	26   
	27 static struct resource resources[] = {   
	28       {   
	29             .start = GROUP_PIN(3,1),   
	30             .flags = IORESOURCE_IRQ,   
	31             .name = "100ask_led_pin",   
	32       },   
	33       {   
	34             .start = GROUP_PIN(5,8),   
	35             .flags = IORESOURCE_IRQ,   
	36             .name = "100ask_led_pin",   
	37       },   
	38 };   
	39   
	40   
	41 static struct platform_device board_A_led_dev = {   
	42       .name = "100ask_led",   
	43       .num_resources = ARRAY_SIZE(resources),   
	44       .resource = resources,   
	45       .dev = {   
	46             .release = led_dev_release,   
	47        },   
	48 };   
	49   
```
   
   
#### 实现platform_driver结构体   
&emsp;&emsp;chip_demo_gpio.c中注册platform_driver结构体，它使用Bus/Dev/Drv模型，当有匹配的platform_device时，它的probe函数就会被调用。

&emsp;&emsp;在probe函数中所做的事情跟之前的代码没有差别。

&emsp;&emsp;先看入口函数。

&emsp;&emsp;第150行向内核注册一个platform_driver结构体；

&emsp;&emsp;这个结构体的核心在于第140行的chip_demo_gpio_probe函数。

``` C
	138 static struct platform_driver chip_demo_gpio_driver = {   
	139    .probe     = chip_demo_gpio_probe,   
	140    .remove    = chip_demo_gpio_remove,   
	141    .driver    = {   
	142       .name   = "100ask_led",   
	143    },   
	144 };   
	145   
	146 static int __init chip_demo_gpio_drv_init(void)   
	147 {   
	148    int err;   
	149   
	150    err = platform_driver_register(&chip_demo_gpio_driver);   
	151    register_led_operations(&board_demo_led_opr);   
	152   
	153    return 0;   
	154 }   
	155   
```
   
&emsp;&emsp;chip_demo_gpio_probe函数代码如下。

&emsp;&emsp;第107行：从匹配的platform_device中获取资源，确定GPIO引脚。

&emsp;&emsp;第111行：把引脚记录下来，在操作硬件时要用。

&emsp;&emsp;第112行：新发现了一个GPIO引脚，就调用上层驱动的代码创建设备节点。

``` C
	100 static int chip_demo_gpio_probe(struct platform_device *pdev)   
	101 {   
	102    struct resource *res;   
	103    int i = 0;   
	104   
	105    while (1)   
	106    {   
	107       res = platform_get_resource(pdev, IORESOURCE_IRQ, i++);   
	108       if (!res)   
	109          break;   
	110   
	111       g_ledpins[g_ledcnt] = res->start;   
	112       led_class_create_device(g_ledcnt);   
	113       g_ledcnt++;   
	114    }   
	115    return 0;   
	116   
	117 }   
	118   
```
   
&emsp;&emsp;操作硬件的代码如下，第31、63行的代码里用到了数组g_ledpins，里面的值来自platform_device，在probe函数中根据platform_device的资源确定了引脚：

``` C
	23 static int g_ledpins[100];   
	24 static int g_ledcnt = 0;   
	25   
	26 static int board_demo_led_init (int which) /* 初始化LED, which-哪个LED */   
	27 {   
	28    //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
	29   
	30   printk("init gpio: group %d, pin %d\n", GROUP(g_ledpins[which]), PIN(g_ledpins[which]));   
	31    switch(GROUP(g_ledpins[which]))   
	32    {   
	33       case 0:   
	34       {   
	35          printk("init pin of group 0 ...\n");   
	36          break;   
	37       }   
	38       case 1:   
	39       {   
	40          printk("init pin of group 1 ...\n");   
	41          break;   
	42       }   
	43       case 2:   
	44       {   
	45          printk("init pin of group 2 ...\n");   
	46          break;   
	47       }   
	48       case 3:   
	49       {   
	50          printk("init pin of group 3 ...\n");   
	51          break;   
	52       }   
	53    }   
	54   
	55    return 0;   
	56 }   
	57   
	58 static int board_demo_led_ctl (int which, char status) /* 控制LED, which-哪个LED, status:1-亮,0-灭 */   
	59 {   
	60    //printk("%s %s line %d, led %d, %s\n", __FILE__, __FUNCTION__, __LINE__, which, status ? "on" : "off");   
	61   printk("set led %s: group %d, pin %d\n", status ? "on" : "off", GROUP(g_ledpins[which]), PIN(g_ledpins[which]));   
	62   
	63    switch(GROUP(g_ledpins[which]))   
	64    {   
	65       case 0:   
	66       {   
	67          printk("set pin of group 0 ...\n");   
	68          break;   
	69       }   
	70       case 1:   
	71       {   
	72          printk("set pin of group 1 ...\n");   
	73          break;   
	74       }   
	75       case 2:   
	76       {   
	77          printk("set pin of group 2 ...\n");   
	78          break;   
	79       }   
	80       case 3:   
	81       {   
	82          printk("set pin of group 3 ...\n");   
	83          break;   
	84       }   
	85    }   
	86   
	87    return 0;   
	88 }   
	89   
	90 static struct led_operations board_demo_led_opr = {   
	91    .init = board_demo_led_init,   
	92    .ctl  = board_demo_led_ctl,   
	93 };   
	94   
	95 struct led_operations *get_board_led_opr(void)   
	96 {   
	97    return &board_demo_led_opr;   
	98 }   
	99   
```
   
### 课后作业   
&emsp;&emsp;完善半成品程序：04_led_drv_template_bus_dev_drv_unfinished。

&emsp;&emsp;请仿照本节提供的程序(位于04_led_drv_template_bus_dev_drv目录)，改造你所用的单板的LED驱动程序。

## 驱动进化之路：设备树的引入及简明教程   
&emsp;&emsp;官方文档(可以下载到devicetree-specification-v0.2.pdf): [](https://www.devicetree.org/specifications/ https://www.devicetree.org/specifications/)

&emsp;&emsp;内核文档:

```bash
	Documentation/devicetree/booting-without-of.txt   
```
   
&emsp;&emsp;我录制“设备树视频”时写的文档：设备树详细分析.txt

&emsp;&emsp;这个txt文件也同步上传到wiki了：[](http://wiki.100ask.org/Linux_devicetree http://wiki.100ask.org/Linux_devicetree)

&emsp;&emsp;&emsp;&emsp;我录制的设备树视频，它是基于s3c2440的，用的是linux 4.19；需要深入研究的可以看该视频(收费)。

&emsp;&emsp;&emsp;&emsp;注意，如果只是想入门，看本文档及视频即可。

### 设备树的引入与作用   
&emsp;&emsp;以LED驱动为例，如果你要更换LED所用的GPIO引脚，需要修改驱动程序源码、重新编译驱动、重新加载驱动。

&emsp;&emsp;在内核中，使用同一个芯片的板子，它们所用的外设资源不一样，比如A板用GPIO A，B板用GPIO B。而GPIO的驱动程序既支持GPIO A也支持GPIO B，你需要指定使用哪一个引脚，怎么指定？在c代码中指定。

&emsp;&emsp;随着ARM芯片的流行，内核中针对这些ARM板保存有大量的、没有技术含量的文件。

&emsp;&emsp;Linus大发雷霆："this whole ARM thing is a f*cking pain in the ass"。

&emsp;&emsp;于是，Linux内核开始引入设备树。

&emsp;&emsp;设备树并不是重新发明出来的，在Linux内核中其他平台如PowerPC，早就使用设备树来描述硬件了。

&emsp;&emsp;Linus发火之后，内核开始全面使用设备树来改造，神人就神人。

&emsp;&emsp;有一种错误的观点，说“新驱动都是用设备树来写了”。

&emsp;&emsp;<font color="# dd0000">设备树不可能用来写驱动</font>。

&emsp;&emsp;请想想，要操作硬件就需要去操作复杂的寄存器，如果设备树可以操作寄存器，那么它就是“驱动”，它就一样很复杂。

&emsp;&emsp;设备树只是用来给内核里的驱动程序，<font color="# dd0000">指定硬件的信息</font>。比如LED驱动，在内核的驱动程序里去操作寄存器，但是操作哪一个引脚？这由设备树指定。

&emsp;&emsp;你可以事先体验一下设备树，板子启动后执行下面的命令：

```bash
	#  ls /sys/firmware/   
	devicetree  fdt   
```
   
&emsp;&emsp;/sys/firmware/devicetree目录下是以目录结构程现的dtb文件, 根节点对应base目录, 每一个节点对应一个目录, 每一个属性对应一个文件。

&emsp;&emsp;这些属性的值如果是字符串，可以使用cat命令把它打印出来；对于数值，可以用hexdump把它打印出来。

&emsp;&emsp;一个单板启动时，u-boot先运行，它的作用是启动内核。U-boot会把内核和设备树文件都读入内存，然后启动内核。在启动内核时会把设备树在内存中的地址告诉内核。

### 设备树的语法   
&emsp;&emsp;为什么叫“树”？

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_097.png) 
<600px>


&emsp;&emsp;怎么描述这棵树？

&emsp;&emsp;我们需要编写设备树文件(dts: device tree source)，它需要编译为dtb(device tree blob)文件，内核使用的是dtb文件。

&emsp;&emsp;dts文件是根本，它的语法很简单。

&emsp;&emsp;下面是一个设备树示例：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_098.png) 
<800px>

&emsp;&emsp;它对应的dts文件如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_099.png) 
<400px>
   
#### Devicetree格式   
##### DTS文件的格式   
&emsp;&emsp;DTS文件布局(layout):

```bash
	/dts-v1/;            // 表示版本   
	[memory reservations]   // 格式为: /memreserve/ <address> <length>;   
	/ {   
	   [property definitions]   
	   [child nodes]   
	};   
```
   
##### node的格式   
&emsp;&emsp;设备树中的基本单元，被称为“node”，其格式为：

``` C
	[label:] node-name[@unit-address] {   
	   [properties definitions]   
	   [child nodes]   
	};   
```


&emsp;&emsp;label是标号，可以省略。label的作用是为了方便地引用node，比如：

``` C
	/dts-v1/;   
	/ {   
		uart0: uart@fe001000 {   
	      compatible="ns16550";   
	      reg=<0xfe001000 0x100>;   
		};   
	};   
```
   
&emsp;&emsp;可以使用下面2种方法来修改uart@fe001000这个node：

``` C
	// 在根节点之外使用label引用node：   
	&uart0 {   
	   status = “disabled”;   
	};   
```
&emsp;&emsp;或在根节点之外使用全路径：

``` C
		&{/uart@fe001000}  {   
		   status = “disabled”;   
		};   
```
   
   
##### properties的格式   
&emsp;&emsp;简单地说，properties就是“name=value”，value有多种取值方式。

&emsp;&emsp;**Property格式1:**

``` C
	[label:] property-name = value;   
```


&emsp;&emsp;**Property格式2(没有值):**

``` C
	[label:] property-name;   
```
   
&emsp;&emsp;Property取值只有3种

```bash
		arrays of cells(1个或多个32位数据, 64位数据使用2个32位数据表示),   
		string(字符串),   
		bytestring(1个或多个字节)   
```
   
&emsp;&emsp;**示例**

&emsp;&emsp;a. Arrays of cells : cell就是一个32位的数据，用<font color="# dd0000">尖括号</font>包围起来

``` C
	interrupts = <17 0xc>;   
```
   
&emsp;&emsp;b. 64bit数据使用2个cell来表示，用<font color="# dd0000">尖括号</font>包围起来:

``` C
	clock-frequency = <0x00000001 0x00000000>;   
```
   
&emsp;&emsp;c. A null-terminated string (有结束符的字符串)，用双引号包围起来:

``` C
	compatible = "simple-bus";   
```
   
&emsp;&emsp;d. A bytestring(字节序列) ，用<font color="# dd0000">中括号</font>包围起来:

``` C
	local-mac-address = [00 00 12 34 56 78];  // 每个byte使用2个16进制数来表示   
	local-mac-address = [000012345678];      // 每个byte使用2个16进制数来表示   
```
   
&emsp;&emsp;e. 可以是各种值的组合, 用<font color="# dd0000">逗号隔开</font>:

``` C
	compatible = "ns16550", "ns8250";   
	example = <0xf00f0000 19>, "a strange property format";   
```
   
#### dts文件包含dtsi文件   
&emsp;&emsp;设备树文件不需要我们从零写出来，内核支持了某款芯片比如imx6ull，在内核的arch/arm/boot/dts目录下就有了能用的设备树模板，一般命名为xxxx.dtsi。“i”表示“include”，被别的文件引用的。

&emsp;&emsp;我们使用某款芯片制作出了自己的单板，所用资源跟xxxx.dtsi是大部分相同，小部分不同，所以需要引脚xxxx.dtsi并修改。

&emsp;&emsp;dtsi文件跟dts文件的语法是完全一样的。



&emsp;&emsp;dts中可以包含.h头文件，也可以包含dtsi文件，在.h头文件中可以定义一些宏。

&emsp;&emsp;示例：

``` C
	/dts-v1/;   
	   
	# include <dt-bindings/input/input.h>   
	# include "imx6ull.dtsi"   
	   
	/ {   
	……   
	};   
```
   
   
#### 常用的属性   
###### address-cells、#size-cells   
&emsp;&emsp;cell指一个32位的数值，

``` C
		address-cells：address要用多少个32位数来表示；   
		size-cells：size要用多少个32位数来表示。   
```
   
&emsp;&emsp;比如一段内存，怎么描述它的起始地址和大小？

&emsp;&emsp;下例中，address-cells为1，所以reg中用1个数来表示地址，即用0x80000000来表示地址；size-cells为1，所以reg中用1个数来表示大小，即用0x20000000表示大小：

``` C
		/ {   
		# address-cells = <1>;   
		# size-cells = <1>;   
		memory {   
		reg = <0x80000000 0x20000000>;   
		   };   
		};   
```
   
##### compatible   
&emsp;&emsp;“compatible”表示“兼容”，对于某个LED，内核中可能有A、B、C三个驱动都支持它，那可以这样写：

``` C
		led {   
		compatible = “A”, “B”, “C”;   
		};   
```
&emsp;&emsp;内核启动时，就会为这个LED按这样的优先顺序为它找到驱动程序：A、B、C。

&emsp;&emsp;根节点下也有compatible属性，用来选择哪一个“machine desc”：一个内核可以支持machine A，也支持machine B，内核启动后会根据根节点的compatible属性找到对应的machine desc结构体，执行其中的初始化函数。

&emsp;&emsp;compatible的值，建议取这样的形式："manufacturer,model"，即“厂家名,模块名”。

&emsp;&emsp;<font color="# dd0000">注意：</font>machine desc的意思就是“机器描述”，学到内核启动流程时才涉及。

   
##### model   
&emsp;&emsp;model属性与compatible属性有些类似，但是有差别。

&emsp;&emsp;compatible属性是一个字符串列表，表示可以你的硬件兼容A、B、C等驱动；

&emsp;&emsp;model用来准确地定义这个硬件是什么。

&emsp;&emsp;比如根节点中可以这样写：

``` C
		/ {   
			compatible = "samsung,smdk2440", "samsung,mini2440";   
			model = "jz2440_v3";   
		};   
```
&emsp;&emsp;它表示这个单板，可以兼容内核中的“smdk2440”，也兼容“mini2440”。

&emsp;&emsp;从compatible属性中可以知道它兼容哪些板，但是它到底是什么板？用model属性来明确。

   
##### status   
&emsp;&emsp;dtsi文件中定义了很多设备，但是在你的板子上某些设备是没有的。这时你可以给这个设备节点添加一个status属性，设置为“disabled”：

``` C
		&uart1 {   
		     status = "disabled";   
		};   
```
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_100.png) 
<800px>
   
   
##### reg   
&emsp;&emsp;<font color="# dd0000">reg的本意是register，用来表示寄</font>存器地址。

&emsp;&emsp;但是在设备树里，它可以用来描述一段空间。反正对于ARM系统，寄存器和内存是统一编址的，即访问寄存器时用某块地址，访问内存时用某块地址，在访问方法上没有区别。

&emsp;&emsp;reg属性的值，是一系列的“address  size”，用多少个32位的数来表示address和size，由其父节点的# address-cells、#size-cells决定。

&emsp;&emsp;示例：

``` C
		/dts-v1/;   
		/ {   
				# address-cells = <1>;   
				# size-cells = <1>;   
				memory {   
				reg = <0x80000000 0x20000000>;   
			};   
		};   
```
   
##### name(过时了，建议不用)   
&emsp;&emsp;它的值是字符串，用来表示节点的名字。在跟platform_driver匹配时，优先级最低。

&emsp;&emsp;compatible属性在匹配过程中，优先级最高。

##### device_type(过时了，建议不用)   
&emsp;&emsp;它的值是字符串，用来表示节点的类型。在跟platform_driver匹配时，优先级为中。

&emsp;&emsp;compatible属性在匹配过程中，优先级最高。

   
   
#### 常用的节点(node)   
##### 根节点   
&emsp;&emsp;dts文件中必须有一个根节点：

``` C
	/dts-v1/;   
	/ {   
			model = "SMDK24440";   
			compatible = "samsung,smdk2440";   
   
			# address-cells = <1>;   
			# size-cells = <1>;   
	};   
   
```
   
&emsp;&emsp;根节点中必须有这些属性：

```bash
		# address-cells // 在它的子节点的reg属性中, 使用多少个u32整数来描述地址(address)   
		# size-cells   // 在它的子节点的reg属性中, 使用多少个u32整数来描述大小(size)   
		compatible   // 定义一系列的字符串, 用来指定内核中哪个machine_desc可以支持本设备   
		         // 即这个板子兼容哪些平台   
		         // uImage : smdk2410 smdk2440 mini2440    ==> machine_desc         
		               
		model      // 咱这个板子是什么   
		         // 比如有2款板子配置基本一致, 它们的compatible是一样的   
		         // 那么就通过model来分辨这2款板子   
```
##### CPU节点   
&emsp;&emsp;一般不需要我们设置，在dtsi文件中都定义好了：

``` C
		cpus {   
				# address-cells = <1>;   
				# size-cells = <0>;   
		   
				cpu0: cpu@0 {   
				   .......   
		      }   
		};   
```
   
##### memory节点   
&emsp;&emsp;芯片厂家不可能事先确定你的板子使用多大的内存，所以memory节点需要板厂设置，比如：

``` C
		memory {   
			reg = <0x80000000 0x20000000>;   
		};   
```
##### chosen节点   
&emsp;&emsp;我们可以通过设备树文件给内核传入一些参数，这要在chosen节点中设置bootargs属性：

``` C
		chosen {   
			bootargs = "noinitrd root=/dev/mtdblock4 rw init=/linuxrc console=ttySAC0,115200";   
		};   
```
   
   
### 编译、更换设备树   
&emsp;&emsp;我们一般不会从零写dts文件，而是修改。程序员水平有高有低，改得对不对？需要编译一下。并且内核直接使用dts文件的话，就太低效了，它也需要使用二进制格式的dtb文件。

   
#### 在内核中直接make   
&emsp;&emsp;设置ARCH、CROSS_COMPILE、PATH这三个环境变量后，进入ubuntu上板子内核源码的目录，执行如下命令即可编译dtb文件：

	make  dtbs  V=1   
&emsp;&emsp;这些操作步骤在各个开发板的高级用户使用手册，或是http://wiki.100ask.net中各个板子的页面里，都有说明。

&emsp;&emsp;以野火的IMX6UL为例，可以看到如下输出：

``` C
		mkdir -p arch/arm/boot/dts/ ;   
		arm-linux-gnueabihf-gcc -E   
		  -Wp,-MD,arch/arm/boot/dts/.imx6ull-14x14-ebf-mini.dtb.d.pre.tmp   
		  -nostdinc   
		  -I./arch/arm/boot/dts   
		  -I./arch/arm/boot/dts/include   
		  -I./drivers/of/testcase-data   
		  -undef -D__DTS__ -x assembler-with-cpp   
		  -o arch/arm/boot/dts/.imx6ull-14x14-ebf-mini.dtb.dts.tmp   
		  arch/arm/boot/dts/imx6ull-14x14-ebf-mini.dts ;   
		    
		./scripts/dtc/dtc -O dtb   
		  -o arch/arm/boot/dts/imx6ull-14x14-ebf-mini.dtb   
		  -b 0 -i arch/arm/boot/dts/ -Wno-unit_address_vs_reg    
		  -d arch/arm/boot/dts/.imx6ull-14x14-ebf-mini.dtb.d.dtc.tmp   
		  arch/arm/boot/dts/.imx6ull-14x14-ebf-mini.dtb.dts.tmp ;   
```
   
&emsp;&emsp;它首先用arm-linux-gnueabihf-gcc预处理dts文件，把其中的.h头文件包含进来，把宏展开。

&emsp;&emsp;然后使用scripts/dtc/dtc生成dtb文件。

&emsp;&emsp;可见，dts文件之所以支持“# include”语法，是因为arm-linux-gnueabihf-gcc帮忙。

&emsp;&emsp;如果只用dtc工具，它是不支持”# include”语法的，只支持“/include”语法。

   
#### 手工编译   
&emsp;&emsp;除非你对设备树比较了解，否则不建议手工使用dtc工具直接编译。

&emsp;&emsp;内核目录下scripts/dtc/dtc是设备树的编译工具，直接使用它的话，包含其他文件时不能使用“# include”，而必须使用“/incldue”。

&emsp;&emsp;编译、反编译的示例命令如下，“-I”指定输入格式，“-O”指定输出格式，“-o”指定输出文件：

```bash
		./scripts/dtc/dtc -I dts -O dtb -o tmp.dtb arch/arm/boot/dts/xxx.dts  // 编译dts为dtb   
		./scripts/dtc/dtc -I dtb -O dts -o tmp.dts arch/arm/boot/dts/xxx.dtb  // 反编译dtb为dts   
```
   
#### 给开发板更换设备树文件   
&emsp;&emsp;怎么给各个单板编译出设备树文件，它们的设备树文件是哪一个？

&emsp;&emsp;这些操作步骤在各个开发板的高级用户使用手册，或是[](http://wiki.100ask.net http://wiki.100ask.net)中各个板子的页面里，都有说明。

&emsp;&emsp;基本方法都是：设置ARCH、CROSS_COMPILE、PATH这三个环境变量后，在内核源码目录中执行：

```bash
	make  dtbs   
```
   
##### 对于100ask-am335x 单板   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/100ask-am335x.dtb

&emsp;&emsp;要更换板子上的设备树文件，启动板子后，更换这个文件：/boot/mx6ull-14x14-ebf.dtb

   
#####  对于firefly-rk3288   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/rk3288-firefly.dtb

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/rk3288-firefly.dtb

   
##### 对于firefly的roc-rk3399-pc   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm64/boot/dts/rk3399-roc-pc.dtb

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/ rk3399-roc-pc.dtb

##### 对于百问网使用QEMU模拟的IMX6ULL板子   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/100ask_imx6ul_qemu.dtb

&emsp;&emsp;它是执行qemu时直接在命令行中指定设备树文件的，你可以打开脚本文件qemu-imx6ul-gui.sh找到dtb文件的位置，然后使用新编译出来的dtb去覆盖老文件。

##### 对于野火imx6ull-pro   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/imx6ull-14x14-ebf.dtb

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/imx6ull-14x14-ebf.dtb

##### 对于正点原子imx6ull-alpha   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/imx6ull-14x14-alpha.dtb

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/arch/arm/boot/dts/imx6ull-14x14-alpha.dtb

#### 板子启动后查看设备树   
&emsp;&emsp;板子启动后执行下面的命令：

```bash
		#  ls /sys/firmware/   
		devicetree  fdt   
```
   
&emsp;&emsp;/sys/firmware/devicetree目录下是以目录结构程现的dtb文件, 根节点对应base目录, 每一个节点对应一个目录, 每一个属性对应一个文件。

&emsp;&emsp;这些属性的值如果是字符串，可以使用cat命令把它打印出来；对于数值，可以用hexdump把它打印出来。

&emsp;&emsp;还可以看到/sys/firmware/fdt文件，它就是dtb格式的设备树文件，可以把它复制出来放到ubuntu上，执行下面的命令反编译出来(-I dtb：输入格式是dtb，-O dts：输出格式是dts)：

```bash
		cd  板子所用的内核源码目录   
		./scripts/dtc/dtc  -I  dtb  -O  dts   /从板子上/复制出来的/fdt  -o   tmp.dts   
```
   
### 内核对设备树的处理   
&emsp;&emsp;从源代码文件dts文件开始，设备树的处理过程为：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_101.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;① dts在PC机上被编译为dtb文件；

&emsp;&emsp;&emsp;&emsp;② u-boot把dtb文件传给内核；

&emsp;&emsp;&emsp;&emsp;③ 内核解析dtb文件，把每一个节点都转换为device_node结构体；

&emsp;&emsp;&emsp;&emsp;④ 对于某些device_node结构体，会被转换为platform_device结构体。

#### dtb中每一个节点都被转换为device_node结构体   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_102.png) 
<600px>
&emsp;&emsp;根节点被保存在全局变量of_root中，从of_root开始可以访问到任意节点。

#### 哪些设备树节点会被转换为platform_device   
&emsp;&emsp;A. 根节点下含有compatile属性的<font color="# dd0000">子节点</font>

&emsp;&emsp;B. 含有特定compatile属性的节点的<font color="# dd0000">子节点</font>

&emsp;&emsp;&emsp;&emsp;如果一个节点的compatile属性，它的值是这4者之一："simple-bus","simple-mfd","isa","arm,amba-bus",

&emsp;&emsp;&emsp;&emsp;那么它的<font color="# dd0000">子结点</font>(需含compatile属性)也可以转换为platform_device。

&emsp;&emsp;C. 总线I2C、SPI节点下的子节点：<font color="# dd0000">不转换</font>为platform_device

&emsp;&emsp;&emsp;&emsp;某个总线下到子节点，应该交给对应的总线驱动程序来处理, 它们不应该被转换为platform_device。

&emsp;&emsp;&emsp;&emsp;比如以下的节点中：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;/mytest会被转换为platform_device, 因为它兼容"simple-bus";

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;它的子节点/mytest/mytest@0 也会被转换为platform_device

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;/i2c节点一般表示i2c控制器, 它会被转换为platform_device, 在内核中有对应的platform_driver;

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;/i2c/at24c02节点不会被转换为platform_device, 它被如何处理完全由父节点的platform_driver决定, 一般是被创建为一个i2c_client。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;类似的也有/spi节点, 它一般也是用来表示SPI控制器, 它会被转换为platform_device, 在内核中有对应的platform_driver;

&emsp;&emsp;/spi/flash@0节点不会被转换为platform_device, 它被如何处理完全由父节点的platform_driver决定, 一般是被创建为一个spi_device。   

``` C
			/ {   
				  mytest {   
					  compatile = "mytest", "simple-bus";   
					  mytest@0 {   
							compatile = "mytest_0";   
					  };   
				  };   
				    
				  i2c {   
					  compatile = "samsung,i2c";   
					  at24c02 {   
							compatile = "at24c02";                   
					  };   
				  };   
			   
				  spi {   
					  compatile = "samsung,spi";             
					  flash@0 {   
							compatible = "winbond,w25q32dw";   
							spi-max-frequency = <25000000>;   
							reg = <0>;   
						  };   
				  };   
			  };   
```   
   
#### 怎么转换为platform_device   
&emsp;&emsp;内核处理设备树的函数调用过程，这里不去分析；我们只需要得到如下结论：

&emsp;&emsp;&emsp;&emsp;A. platform_device中含有resource数组, 它来自device_node的reg, interrupts属性;

&emsp;&emsp;&emsp;&emsp;B. platform_device.dev.of_node指向device_node, 可以通过它获得其他属性

### platform_device如何与platform_driver配对   
&emsp;&emsp;从设备树转换得来的platform_device会被注册进内核里，以后当我们每注册一个platform_driver时，它们就会两两确定能否配对，如果能配对成功就调用platform_driver的probe函数。

&emsp;&emsp;套路是一样的。

&emsp;&emsp;我们需要将前面讲过的“匹配规则”再完善一下：

&emsp;&emsp;先贴源码：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_103.png) 
<600px>
   
   
#### 最先比较：是否强制选择某个driver   
&emsp;&emsp;比较platform_device. driver_override和platform_driver.driver.name

&emsp;&emsp;可以设置platform_device的driver_override，强制选择某个platform_driver。

#### 然后比较：设备树信息   
&emsp;&emsp;比较：platform_device. dev.of_node和platform_driver.driver.of_match_table。

&emsp;&emsp;由设备树节点转换得来的platform_device中，含有一个结构体：of_node。

&emsp;&emsp;它的类型如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_104.png) 
<400px>

&emsp;&emsp;如果一个platform_driver支持设备树，它的platform_driver.driver.of_match_table是一个数组，类型如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_105.png) 
<200px>

&emsp;&emsp;使用设备树信息来判断dev和drv是否配对时，

&emsp;&emsp;<font color="# dd0000">首先</font>，如果of_match_table中含有compatible值，就跟dev的compatile属性比较，若一致则成功，否则返回失败；

&emsp;&emsp;<font color="# dd0000">其次</font>，如果of_match_table中含有type值，就跟dev的device_type属性比较，若一致则成功，否则返回失败；

&emsp;&emsp;<font color="# dd0000">最后</font>，如果of_match_table中含有name值，就跟dev的name属性比较，若一致则成功，否则返回失败。

&emsp;&emsp;而设备树中建议不再使用devcie_type和name属性，所以基本上只使用设备节点的compatible属性来寻找匹配的platform_driver。

#### 接下来比较：platform_device_id   
&emsp;&emsp;比较platform_device. name和platform_driver.id_table[i].name，id_table中可能有多项。

&emsp;&emsp;platform_driver.id_table是“platform_device_id”指针，表示该drv支持若干个device，它里面列出了各个device的{.name, .driver_data}，其中的“name”表示该drv支持的设备的名字，driver_data是些提供给该device的私有数据。

   
#### 最后比较：platform_device.name和platform_driver.driver.name   
&emsp;&emsp;platform_driver.id_table可能为空，

&emsp;&emsp;这时可以根据platform_driver.driver.name来寻找同名的platform_device。

#### 一个图概括所有的配对过程   
&emsp;&emsp;概括出了这个图：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_106.png) 
<1200px>
   
### 没有转换为platform_device的节点，如何使用   
&emsp;&emsp;任意驱动程序里，都可以直接访问设备树。

&emsp;&emsp;你可以使用“11.7”节中介绍的函数找到节点，读出里面的值。

   
### 内核里操作设备树的常用函数   
&emsp;&emsp;内核源码中include/linux/目录下有很多of开头的头文件，of表示“open firmware”即开放固件。

#### 内核中设备树相关的头文件介绍   
&emsp;&emsp;内核源码中include/linux/目录下有很多of开头的头文件，of表示“open firmware”即开放固件。

&emsp;&emsp;设备树的处理过程是：dtb -> device_node -> platform_device

##### 处理DTB   
``` C
		of_fdt.h         // dtb文件的相关操作函数, 我们一般用不到,   
		// 因为dtb文件在内核中已经被转换为device_node树(它更易于使用)   
```
   
##### 处理device_node   
``` C
		of.h            // 提供设备树的一般处理函数,   
		// 比如 of_property_read_u32(读取某个属性的u32值),   
		// of_get_child_count(获取某个device_node的子节点数)   
		of_address.h      // 地址相关的函数,   
		// 比如 of_get_address(获得reg属性中的addr, size值)   
		// of_match_device (从matches数组中取出与当前设备最匹配的一项)   
		of_dma.h         // 设备树中DMA相关属性的函数   
		of_gpio.h        // GPIO相关的函数   
		of_graph.h       // GPU相关驱动中用到的函数, 从设备树中获得GPU信息   
		of_iommu.h       // 很少用到   
		of_irq.h         // 中断相关的函数   
		of_mdio.h        // MDIO (Ethernet PHY) API   
		of_net.h         // OF helpers for network devices.   
		of_pci.h         // PCI相关函数   
		of_pdt.h         // 很少用到   
		of_reserved_mem.h  // reserved_mem的相关函数   
```
   
##### 处理 platform_device   
``` C
		of_platform.h     // 把device_node转换为platform_device时用到的函数,   
		               // 比如of_device_alloc(根据device_node分配设置platform_device),   
		               // of_find_device_by_node (根据device_node查找到platform_device),   
		               //   of_platform_bus_probe (处理device_node及它的子节点)   
		of_device.h      // 设备相关的函数, 比如 of_match_device   
```
#### platform_device相关的函数   
&emsp;&emsp;of_platform.h中声明了很多函数，但是作为驱动开发者，我们只使用其中的1、2个。其他的都是给内核自己使用的，内核使用它们来处理设备树，转换得到platform_device。

   
##### of_find_device_by_node   
&emsp;&emsp;函数原型为：

``` C
		extern struct platform_device *of_find_device_by_node(struct device_node *np);   
```
   
&emsp;&emsp;设备树中的每一个节点，在内核里都有一个device_node；你可以使用device_node去找到对应的platform_device。

   
##### platform_get_resource   
&emsp;&emsp;这个函数跟设备树没什么关系，但是设备树中的节点被转换为platform_device后，设备树中的reg属性、interrupts属性也会被转换为“resource”。

&emsp;&emsp;这时，你可以使用这个函数取出这些资源。

&emsp;&emsp;函数原型为：

``` C
			/**   
			 * platform_get_resource - get a resource for a device   
			 * @dev: platform device   
			 * @type: resource type   // 取哪类资源？IORESOURCE_MEM、IORESOURCE_REG   
			*                 // IORESOURCE_IRQ等   
			 * @num: resource index  // 这类资源中的哪一个？   
			 */   
			struct resource *platform_get_resource(struct platform_device *dev,   
							      unsigned int type, unsigned int num);   
```
   
&emsp;&emsp;对于设备树节点中的reg属性，它属性IORESOURCE_MEM类型的资源；

&emsp;&emsp;对于设备树节点中的interrupts属性，它属性IORESOURCE_IRQ类型的资源。

#### 有些节点不会生成platform_device，怎么访问它们   
&emsp;&emsp;内核会把dtb文件解析出一系列的device_node结构体，我们可以直接访问这些device_node。

&emsp;&emsp;内核源码incldue/linux/of.h中声明了device_node和属性property的操作函数，device_node和property的结构体定义如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_102.png) 
<800px>
   
##### 找到节点   
&emsp;&emsp;a. of_find_node_by

&emsp;&emsp;&emsp;&emsp;根据路径找到节点，比如“/”就对应根节点，“/memory”对应memory节点。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
	static inline struct device_node *of_find_node_by_path(const char *path);   
```
   
&emsp;&emsp;b. of_find_node_by_name

&emsp;&emsp;&emsp;&emsp;根据名字找到节点，节点如果定义了name属性，那我们可以根据名字找到它。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		 extern struct device_node *of_find_node_by_name(struct device_node *from,   
				const char *name);   
```
   
&emsp;&emsp;&emsp;&emsp;参数from表示从哪一个节点开始寻找，传入NULL表示从根节点开始寻找。

&emsp;&emsp;&emsp;&emsp;但是在设备树的官方规范中不建议使用“name”属性，所以这函数也不建议使用。

&emsp;&emsp;c. of_find_node_by_type

&emsp;&emsp;&emsp;&emsp;根据类型找到节点，节点如果定义了device_type属性，那我们可以根据类型找到它。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		 extern struct device_node *of_find_node_by_type(struct device_node *from,   
		 	const char *type);   
```
   
&emsp;&emsp;&emsp;&emsp;参数from表示从哪一个节点开始寻找，传入NULL表示从根节点开始寻找。

&emsp;&emsp;&emsp;&emsp;但是在设备树的官方规范中不建议使用“device_type”属性，所以这函数也不建议使用。



&emsp;&emsp;d. of_find_compatible_node

&emsp;&emsp;&emsp;&emsp;根据compatible找到节点，节点如果定义了compatible属性，那我们可以根据compatible属性找到它。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		extern struct device_node *of_find_compatible_node(struct device_node *from,   
			const char *type, const char *compat);   
```
   
&emsp;&emsp;&emsp;&emsp;参数from表示从哪一个节点开始寻找，传入NULL表示从根节点开始寻找。

&emsp;&emsp;&emsp;&emsp;参数compat是一个字符串，用来指定compatible属性的值；

&emsp;&emsp;&emsp;&emsp;参数type是一个字符串，用来指定device_type属性的值，可以传入NULL。

&emsp;&emsp;e. of_find_node_by_phandle

&emsp;&emsp;&emsp;&emsp;根据phandle找到节点。

&emsp;&emsp;&emsp;&emsp;dts文件被编译为dtb文件时，每一个节点都有一个数字ID，这些数字ID彼此不同。可以使用数字ID来找到device_node。这些数字ID就是phandle。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		extern struct device_node *of_find_node_by_phandle(phandle handle);   
```
   
&emsp;&emsp;&emsp;&emsp;参数from表示从哪一个节点开始寻找，传入NULL表示从根节点开始寻找。

&emsp;&emsp;f. of_get_parent

&emsp;&emsp;&emsp;&emsp;找到device_node的父节点。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		extern struct device_node *of_get_parent(const struct device_node *node);   
```
   
&emsp;&emsp;&emsp;&emsp;参数from表示从哪一个节点开始寻找，传入NULL表示从根节点开始寻找。

&emsp;&emsp;g. of_get_next_parent

&emsp;&emsp;&emsp;&emsp;这个函数名比较奇怪，怎么可能有“next parent”？

&emsp;&emsp;&emsp;&emsp;它实际上也是找到device_node的父节点，跟of_get_parent的返回结果是一样的。

&emsp;&emsp;&emsp;&emsp;差别在于它多调用下列函数，把node节点的引用计数减少了1。这意味着调用of_get_next_parent之后，你不再需要调用of_node_put释放node节点。

``` C
		of_node_put(node);   
```
&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		extern struct device_node *of_get_next_parent(struct device_node *node);   
```
   
&emsp;&emsp;&emsp;&emsp;参数from表示从哪一个节点开始寻找，传入NULL表示从根节点开始寻找。



&emsp;&emsp;h. of_get_next_child

&emsp;&emsp;&emsp;&emsp;取出下一个子节点。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
			extern struct device_node *of_get_next_child(const struct device_node *node,   
								    struct device_node *prev);   
```
   
&emsp;&emsp;&emsp;&emsp;参数node表示父节点；

&emsp;&emsp;&emsp;&emsp;prev表示上一个子节点，设为NULL时表示想找到第1个子节点。

&emsp;&emsp;&emsp;&emsp;不断调用of_get_next_child时，不断更新pre参数，就可以得到所有的子节点。

&emsp;&emsp;i. of_get_next_available_child

&emsp;&emsp;&emsp;&emsp;取出下一个“可用”的子节点，有些节点的status是“disabled”，那就会跳过这些节点。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		 struct device_node *of_get_next_available_child(const struct device_node *node,   
		 	struct device_node *prev);   
```
   
&emsp;&emsp;&emsp;&emsp;参数node表示父节点；

&emsp;&emsp;&emsp;&emsp;prev表示上一个子节点，设为NULL时表示想找到第1个子节点。

&emsp;&emsp;j. of_get_child_by_name

&emsp;&emsp;&emsp;&emsp;根据名字取出子节点。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
			extern struct device_node *of_get_child_by_name(const struct device_node *node,   
								const char *name);   
```
   
&emsp;&emsp;&emsp;&emsp;参数node表示父节点；

&emsp;&emsp;&emsp;&emsp;name表示子节点的名字。

   
##### 找到属性   
&emsp;&emsp;内核源码incldue/linux/of.h中声明了device_node的操作函数，当然也包括属性的操作函数。

&emsp;&emsp;a. of_find_property

&emsp;&emsp;&emsp;&emsp;找到节点中的属性。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		extern struct property *of_find_property(const struct device_node *np,   
							 const char *name,   
							 int *lenp);   
```
   
&emsp;&emsp;&emsp;&emsp;参数np表示节点，我们要在这个节点中找到名为name的属性。

&emsp;&emsp;&emsp;&emsp;lenp用来保存这个属性的长度，即它的值的长度。

&emsp;&emsp;&emsp;&emsp;在设备树中，节点大概是这样：

``` C
	xxx_node {   
	   xxx_pp_name = “hello”;   
	};   
```
&emsp;&emsp;&emsp;&emsp;上述节点中，“xxx_pp_name”就是属性的名字，值的长度是6。

   
##### 获取属性的值   
&emsp;&emsp;a. of_get_property

&emsp;&emsp;&emsp;&emsp;根据名字找到节点的属性，并且返回它的值。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		/*   
		 * Find a property with a given name for a given node   
		 * and return the value.   
		 */   
		const void *of_get_property(const struct device_node *np, const char *name,   
					   int *lenp)   
```
   
&emsp;&emsp;&emsp;&emsp;参数np表示节点，我们要在这个节点中找到名为name的属性，然后返回它的值。

&emsp;&emsp;&emsp;&emsp;lenp用来保存这个属性的长度，即它的值的长度。

&emsp;&emsp;b. of_property_count_elems_of_size

&emsp;&emsp;&emsp;&emsp;根据名字找到节点的属性，确定它的值有多少个元素(elem)。

&emsp;&emsp;&emsp;&emsp;函数原型：

``` C
		/* of_property_count_elems_of_size - Count the number of elements in a property   
		 *   
		 * @np:		device node from which the property value is to be read.   
		 * @propname:	name of the property to be searched.   
		 * @elem_size:	size of the individual element   
		 *   
		 * Search for a property in a device node and count the number of elements of   
		 * size elem_size in it. Returns number of elements on sucess, -EINVAL if the   
		 * property does not exist or its length does not match a multiple of elem_size   
		 * and -ENODATA if the property does not have a value.   
		 */   
		int of_property_count_elems_of_size(const struct device_node *np,   
						const char *propname, int elem_size)   
```
   
&emsp;&emsp;&emsp;&emsp;参数np表示节点，我们要在这个节点中找到名为propname的属性，然后返回下列结果：

``` C
		return prop->length / elem_size;   
```
   
&emsp;&emsp;&emsp;&emsp;在设备树中，节点大概是这样：

``` C
		xxx_node {   
		   xxx_pp_name = <0x50000000 1024>  <0x60000000  2048>;   
		};   
```
&emsp;&emsp;&emsp;&emsp;调用of_property_count_elems_of_size(np, “xxx_pp_name”, 8)时，返回值是2；

&emsp;&emsp;&emsp;&emsp;调用of_property_count_elems_of_size(np, “xxx_pp_name”, 4)时，返回值是4。

&emsp;&emsp;c. 读整数u32/u64

&emsp;&emsp;&emsp;&emsp;函数原型为：

``` C
		static inline int of_property_read_u32(const struct device_node *np,   
						      const char *propname,   
						      u32 *out_value);   
   
		extern int of_property_read_u64(const struct device_node *np,   
	 				const char *propname, u64 *out_value);   
```
&emsp;&emsp;&emsp;&emsp;在设备树中，节点大概是这样：

``` C
	xxx_node {   
	   name1 = <0x50000000>;   
	   name2 = <0x50000000  0x60000000>;   
	};   
```
&emsp;&emsp;&emsp;&emsp;调用of_property_read_u32 (np, “name1”, &val)时，val将得到值0x50000000；

&emsp;&emsp;&emsp;&emsp;调用of_property_read_u64 (np, “name2”, &val)时，val将得到值0x0x6000000050000000。

&emsp;&emsp;d. 读某个整数u32/u64

&emsp;&emsp;&emsp;&emsp;函数原型为：

``` C
		extern int of_property_read_u32_index(const struct device_node *np,   
						      const char *propname,   
						      u32 index, u32 *out_value);   
```
   
&emsp;&emsp;&emsp;&emsp;在设备树中，节点大概是这样：

``` C
		xxx_node {   
		   name2 = <0x50000000  0x60000000>;   
		};   
```
&emsp;&emsp;&emsp;&emsp;调用of_property_read_u32 (np, “name2”, 1, &val)时，val将得到值0x0x60000000。

&emsp;&emsp;e. 读数组

&emsp;&emsp;&emsp;&emsp;函数原型为：

``` C
		int of_property_read_variable_u8_array(const struct device_node *np,   
							const char *propname, u8 *out_values,   
							size_t sz_min, size_t sz_max);   
		   
		int of_property_read_variable_u16_array(const struct device_node *np,   
							const char *propname, u16 *out_values,   
							size_t sz_min, size_t sz_max);   
		   
		int of_property_read_variable_u32_array(const struct device_node *np,   
					      const char *propname, u32 *out_values,   
					      size_t sz_min, size_t sz_max);   
		   
		int of_property_read_variable_u64_array(const struct device_node *np,   
					      const char *propname, u64 *out_values,   
					      size_t sz_min, size_t sz_max);   
```
   
&emsp;&emsp;&emsp;&emsp;在设备树中，节点大概是这样：

``` C
		xxx_node {   
		   name2 = <0x50000012  0x60000034>;   
		};   
```
&emsp;&emsp;上述例子中属性name2的值，长度为8。

&emsp;&emsp;调用of_property_read_variable_u8_array (np, “name2”, out_values, 1, 10)时，out_values中将会保存这8个字节： 0x12,0x00,0x00,0x50,0x34,0x00,0x00,0x60。

&emsp;&emsp;调用of_property_read_variable_u16_array (np, “name2”, out_values, 1, 10)时，out_values中将会保存这4个16位数值： 0x0012, 0x5000,0x0034,0x6000。

&emsp;&emsp;总之，这些函数要么能取到全部的数值，要么一个数值都取不到；

&emsp;&emsp;如果值的长度在sz_min和sz_max之间，就返回<font color="# dd0000">全部的数值</font>；否则一个数值都不返回。

&emsp;&emsp;f. 读字符串

&emsp;&emsp;函数原型为：

``` C
		 int of_property_read_string(const struct device_node *np, const char *propname,   
		 				const char **out_string);   
```
   
&emsp;&emsp;&emsp;&emsp;返回节点np的属性(名为propname)的值，(*out_string)指向这个值，把它当作字符串。

   
### 怎么修改设备树文件   
&emsp;&emsp;一个写得好的驱动程序, 它会尽量确定所用资源。

&emsp;&emsp;只把不能确定的资源留给设备树, 让设备树来指定。

&emsp;&emsp;根据原理图确定"驱动程序无法确定的硬件资源", 再在设备树文件中填写对应内容。

&emsp;&emsp;那么, 所填写内容的格式是什么?

#### 使用芯片厂家提供的工具   
&emsp;&emsp;有些芯片，厂家提供了对应的设备树生成工具，可以选择某个引脚用于某些功能，就可以自动生成设备树节点。

&emsp;&emsp;你再把这些节点复制到内核的设备树文件里即可。

#### 看绑定文档   
&emsp;&emsp;内核文档 Documentation/devicetree/bindings/

&emsp;&emsp;做得好的厂家也会提供设备树的说明文档

####  参考同类型单板的设备树文件   
#### 网上搜索   
#### 实在没办法时, 只能去研究驱动源码   
   
## LED模板驱动程序的改造：设备树   
### 总结3种写驱动程序的方法   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_107.png) 
<600px>
&emsp;&emsp;核心永远是file_operations结构体。

&emsp;&emsp;上述三种方法，只是指定“硬件资源”的方式不一样。

&emsp;&emsp;从上图可以知道，platform_device/platform_driver只是编程的技巧，不涉及驱动的核心。

### 怎么使用设备树写驱动程序   
#### 设备树节点要与platform_driver能匹配   
&emsp;&emsp;在我们的工作中，驱动要求设备树节点提供什么，我们就得按这要求去编写设备树。

&emsp;&emsp;但是，匹配过程所要求的东西是固定的：

&emsp;&emsp;&emsp;&emsp;① 设备树要有compatible属性，它的值是一个字符串

&emsp;&emsp;&emsp;&emsp;② platform_driver中要有of_match_table，其中一项的.compatible成员设置为一个字符串

&emsp;&emsp;&emsp;&emsp;③ 上述2个字符串要一致。

&emsp;&emsp;&emsp;&emsp;示例如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_108.png) 
<800px>
   
#### 设备树节点指定资源，platform_driver获得资源   
&emsp;&emsp;如果在设备树节点里使用reg属性，那么内核生成对应的platform_device时会用reg属性来设置IORESOURCE_MEM类型的资源。

&emsp;&emsp;如果在设备树节点里使用interrupts属性，那么内核生成对应的platform_device时会用reg属性来设置IORESOURCE_IRQ类型的资源。对于interrupts属性，内核会检查它的有效性，所以不建议在设备树里使用该属性来表示其他资源。

&emsp;&emsp;在我们的工作中，驱动要求设备树节点提供什么，我们就得按这要求去编写设备树。驱动程序中根据pin属性来确定引脚，那么我们就在设备树节点中添加pin属性。

&emsp;&emsp;设备树节点中：

``` C
		# define GROUP_PIN(g,p) ((g<<16) | (p))   
		100ask_led0 {   
		   compatible = “100ask,led”;   
		   pin = <GROUP_PIN(5, 3)>;   
		};   
```
   
&emsp;&emsp;驱动程序中，可以从platform_device中得到device_node，再用of_property_read_u32得到属性的值：

``` C
			struct  device_node* np = pdev->dev. of_node;   
			int led_pin;   
			int err = of_property_read_u32(np, “pin”, &led_pin);   
```
   
### 开始编程   
#### 修改设备树添加led设备节点   
&emsp;&emsp;在本实验中，需要添加的设备节点代码是一样的，你需要找到你的单板所用的设备树文件，在它的根节点下添加如下内容：

``` C
			# define GROUP_PIN(g,p) ((g<<16) | (p))   
			100ask_led@0 {   
				compatible = "100as,leddrv";   
				pin = <GROUP_PIN(3, 1)>;   
			};   
   
			100ask_led@1 {   
				compatible = "100as,leddrv";   
				pin = <GROUP_PIN(5, 8)>;   
			};   
```
##### 对于100ask-am335x 单板   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/100ask-am335x.dts

&emsp;&emsp;修改、编译后得到arch/arm/boot/dts/100ask-am335x.dtb文件。

&emsp;&emsp;要更换板子上的设备树文件，启动板子后，更换这个文件：/boot/mx6ull-14x14-ebf.dtb

   
##### 对于firefly-rk3288   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/rk3288-firefly.dts

&emsp;&emsp;修改、编译后得到arch/arm/boot/dts/rk3288-firefly.dtb文件。

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/rk3288-firefly.dtb

   
   
##### 对于firefly的roc-rk3399-pc   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm64/boot/dts/rk3399-roc-pc.dts

&emsp;&emsp;修改、编译后得到arch/arm64/boot/dts/rk3399-roc-pc.dtb文件。

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/ rk3399-roc-pc.dtb

##### 对于百问网使用QEMU模拟的IMX6ULL板子   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/100ask_imx6ul_qemu.dts

&emsp;&emsp;修改、编译后得到arch/arm/boot/dts/100ask_imx6ul_qemu.dtb文件。

&emsp;&emsp;它是执行qemu时直接在命令行中指定设备树文件的，你可以打开脚本文件qemu-imx6ul-gui.sh找到dtb文件的位置，然后使用新编译出来的dtb去覆盖老文件。

##### 对于野火imx6ull-pro   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/imx6ull-14x14-ebf.dts

&emsp;&emsp;修改、编译后得到arch/arm/boot/dts/imx6ull-14x14-ebf.dtb文件。

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/imx6ull-14x14-ebf.dtb

##### 对于正点原子imx6ull-alpha   
&emsp;&emsp;设备树文件是：内核源码目录中arch/arm/boot/dts/imx6ull-14x14-alpha.dts

&emsp;&emsp;修改、编译后得到arch/arm/boot/dts/imx6ull-14x14-alpha.dtb文件。

&emsp;&emsp;对于这款板子，本教程中我们使用SD卡上的系统。

&emsp;&emsp;要更换板上的设备树文件，你可以使用SD卡启动开发板后，更换这个文件：/boot/arch/arm/boot/dts/imx6ull-14x14-alpha.dtb

#### 修改platform_driver的源码   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

		01_all_series_quickstart\04_快速入门(正式开始)\   
			02_嵌入式Linux驱动开发基础知识\source\   
				02_led_drv\05_led_drv_template_device_tree

&emsp;&emsp;关键代码在chip_demo_gpio.c，主要看里面的platform_driver，代码如下。

&emsp;&emsp;第166行指定了of_match_table，它是用来跟设备树节点匹配的，如果设备树节点中有compatile属性，并且其值等于第157行的“100as,leddrv”，就会导致第162行的probe函数被调用。

``` C
		156 static const struct of_device_id ask100_leds[] = {   
		157    { .compatible = "100as,leddrv" },   
		158    { },   
		159 };   
		160   
		161 static struct platform_driver chip_demo_gpio_driver = {   
		162    .probe     = chip_demo_gpio_probe,   
		163    .remove    = chip_demo_gpio_remove,   
		164    .driver    = {   
		165       .name   = "100ask_led",   
		166       .of_match_table = ask100_leds,   
		167    },   
		168 };   
		169   
		170 static int __init chip_demo_gpio_drv_init(void)   
		171 {   
		172    int err;   
		173   
		174    err = platform_driver_register(&chip_demo_gpio_driver);   
		175    register_led_operations(&board_demo_led_opr);   
		176   
		177    return 0;   
		178 }   
		179   
```
   
### 上机实验   
&emsp;&emsp;1．使用新的设备树dtb文件启动单板，查看/sys/firmware/devicetree/base下有无节点

&emsp;&emsp;2. 查看/sys/devices/platform目录下有无对应的platform_device

&emsp;&emsp;3. 加载驱动：

```bash
		#  insmod  leddrv.ko   
		#  insmod  chip_demo_gpio.ko   
```
   
&emsp;&emsp;4. 测试驱动：

```bash
		#  ./ledtest   /dev/100ask_led0  on   
		#  ./ledtest   /dev/100ask_led0  off   
```
### 调试技巧   
&emsp;&emsp;/sys目录下有很多内核、驱动的信息：

#### 设备树的信息   
&emsp;&emsp;以下目录对应设备树的根节点，可以从此进去找到自己定义的节点。

```bash
		cd /sys/firmware/devicetree/base/   
```
   
&emsp;&emsp;节点是目录，属性是文件。

&emsp;&emsp;属性值是字符串时，用cat命令可以打印出来；属性值是数值时，用hexdump命令可以打印出来。

   
#### platform_device的信息   
&emsp;&emsp;以下目录含有注册进内核的所有platform_device：

```bash
		/sys/devices/platform   
```
&emsp;&emsp;一个设备对应一个目录，进入某个目录后，如果它有“driver”子目录，就表示这个platform_device跟某个platform_driver配对了。

&emsp;&emsp;比如下面的结果中，平台设备“ff8a0000.i2s”已经跟平台驱动“<font color="# dd0000">rockchip-i2s</font>”配对了：

```bash
		/sys/devices/platform/ff8a0000.i2s]#  ls driver -ld   
		lrwxrwxrwx   1 root    root          0 Jan 18 16:28 driver -> ../../../bus/platform/drivers/rockchip-i2s   
```
   
#### platform_driver的信息   
&emsp;&emsp;以下目录含有注册进内核的所有platform_driver：

```bash
		/sys/bus/platform/drivers   
```
   
&emsp;&emsp;一个driver对应一个目录，进入某个目录后，如果它有配对的设备，可以直接看到。

&emsp;&emsp;比如下面的结果中，平台驱动“<font color="# dd0000">rockchip-i2s</font>”跟2个平台设备“平台设备“ff890000.i2s”、“ff8a0000.i2s”配对了：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_109.png) 
<600px>

&emsp;&emsp;<font color="# dd0000">注意：</font>一个平台设备只能配对一个平台驱动，一个平台驱动可以配对多个平台设备。

   
### 课后作业   
&emsp;&emsp;请仿照本节提供的程序(位于05_led_drv_template_device_tree目录)，改造你所用的单板的LED驱动程序。

## APP怎么读取按键值   
&emsp;&emsp;APP读取按键值，需要有按键驱动程序。

&emsp;&emsp;<font color="# dd0000">为什么要讲按键驱动程序？</font>

&emsp;&emsp;APP去读按键的方法有4种：

&emsp;&emsp;&emsp;&emsp;① 查询方式

&emsp;&emsp;&emsp;&emsp;② 休眠-唤醒方式

&emsp;&emsp;&emsp;&emsp;③ poll方式

&emsp;&emsp;&emsp;&emsp;④ 异步通知方式

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;通过这4种方式的学习，我们可以掌握如下知识：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 驱动的基本技能：中断、休眠、唤醒、poll等机制。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;这些基本技能是驱动开发的基础，其他大型驱动复杂的地方是它的框架及设计思想，但是基本技术就这些。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② APP开发的基本技能：阻塞 、非阻塞、休眠、poll、异步通知。

   
### 妈妈怎么知道孩子醒了   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_110.png) 
<400px>

&emsp;&emsp;妈妈怎么知道卧室里小孩醒了？

&emsp;&emsp;&emsp;&emsp;① 时时进房间看一下：<font color="# dd0000">查询方式</font>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;简单，但是累

&emsp;&emsp;&emsp;&emsp;② 进去房间陪小孩一起睡觉，小孩醒了会吵醒她：<font color="# dd0000">休眠-唤醒</font>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;不累，但是妈妈干不了活了

&emsp;&emsp;&emsp;&emsp;③ 妈妈要干很多活，但是可以陪小孩睡一会，定个闹钟：<font color="# dd0000">poll方式</font>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;要浪费点时间，但是可以继续干活。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;妈妈要么是被小孩吵醒，要么是被闹钟吵醒。

&emsp;&emsp;&emsp;&emsp;④ 妈妈在客厅干活，小孩醒了他会自己走出房门告诉妈妈：<font color="# dd0000">异步通知</font>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;妈妈、小孩互不耽误。

&emsp;&emsp;这4种方法没有优劣之分，在不同的场合使用不同的方法。

### APP读取按键的4种方法   
&emsp;&emsp;跟上述生活场景类似，APP去读取按键也有4种方法：

&emsp;&emsp;&emsp;&emsp;① 查询方式

&emsp;&emsp;&emsp;&emsp;② 休眠-唤醒方式

&emsp;&emsp;&emsp;&emsp;③ poll方式

&emsp;&emsp;&emsp;&emsp;④ 异步通知方式

&emsp;&emsp;第2、3、4种方法，都涉及中断服务程序。中断，就像小孩醒了会哭闹一样，中断不经意间到来，它会做某些事情：唤醒APP、向APP发信号。

&emsp;&emsp;所以，在按键驱动程序中，中断是核心。

&emsp;&emsp;实际上，中断无论是在单片机还是在Linux中都很重要。在Linux中，中断的知识还涉及进程、线程等。

   
#### 查询方式   
&emsp;&emsp;这种方法最简单：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_111.png) 
<400px>


&emsp;&emsp;驱动程序中构造、注册一个file_operations结构体，里面提供有对应的open,read函数。APP调用open时，导致驱动中对应的open函数被调用，在里面配置GPIO为输入引脚。APP调用read时，导致驱动中对应的read函数被调用，它读取寄存器，把引脚状态直接返回给APP。

   
#### 休眠-唤醒方式   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_112.png) 
<600px>
&emsp;&emsp;驱动程序中构造、注册一个file_operations结构体，里面提供有对应的open,read函数。

&emsp;&emsp;APP调用open时，导致驱动中对应的open函数被调用，在里面配置GPIO为输入引脚；并且注册GPIO的中断处理函数。

&emsp;&emsp;APP调用read时，导致驱动中对应的read函数被调用，如果有按键数据则直接返回给APP；否则APP在内核态休眠。

&emsp;&emsp;当用户按下按键时，GPIO中断被触发，导致驱动程序之前注册的中断服务程序被执行。它会记录按键数据，并唤醒休眠中的APP。

&emsp;&emsp;APP被唤醒后继续在内核态运行，即继续执行驱动代码，把按键数据返回给APP(的用户空间)。

   
   
#### poll方式   
&emsp;&emsp;上面的休眠-唤醒方式有个缺点：如果用户一直没操作按键，那么APP就会永远休眠。

&emsp;&emsp;我们可以给APP定个闹钟，这就是poll方式。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_113.png) 
<800px>


&emsp;&emsp;驱动程序中构造、注册一个file_operations结构体，里面提供有对应的open,read,poll函数。

&emsp;&emsp;APP调用open时，导致驱动中对应的open函数被调用，在里面配置GPIO为输入引脚；并且注册GPIO的中断处理函数。

&emsp;&emsp;APP调用poll或select函数，意图是“查询”是否有数据，这2个函数都可以指定一个超时时间，即在这段时间内没有数据的话就返回错误。这会导致驱动中对应的poll函数被调用，如果有按键数据则直接返回给APP；否则APP在内核态休眠一段时间。

&emsp;&emsp;当用户按下按键时，GPIO中断被触发，导致驱动程序之前注册的中断服务程序被执行。它会记录按键数据，并唤醒休眠中的APP。

&emsp;&emsp;如果用户没按下按键，但是超时时间到了，内核也会唤醒APP。

&emsp;&emsp;所以APP被唤醒有2种原因：用户操作了按键，超时。被唤醒的APP在内核态继续运行，即继续执行驱动代码，把“状态”返回给APP(的用户空间)。

&emsp;&emsp;APP得到poll/select函数的返回结果后，如果确认是有数据的，则再调用read函数，这会导致驱动中的read函数被调用，这时驱动程序中含有数据，会直接返回数据。

   
#### 异步通知方式   
##### 异步通知的原理：发信号=====   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_114.png) 
<800px>
&emsp;&emsp;异步通知的实现原理是：内核给APP发信号。信号有很多种，这里发的是SIGIO。

&emsp;&emsp;驱动程序中构造、注册一个file_operations结构体，里面提供有对应的open,read,fasync函数。

&emsp;&emsp;APP调用open时，导致驱动中对应的open函数被调用，在里面配置GPIO为输入引脚；并且注册GPIO的中断处理函数。

&emsp;&emsp;APP给信号SIGIO注册自己的处理函数：my_signal_fun。

&emsp;&emsp;APP调用fcntl函数，把驱动程序的flag改为FASYNC，这会导致驱动程序的fasync函数被调用，它只是简单记录进程PID。

&emsp;&emsp;当用户按下按键时，GPIO中断被触发，导致驱动程序之前注册的中断服务程序被执行。它会记录按键数据，然后给进程PID发送SIGIO信号。

&emsp;&emsp;APP收到信号后会被打断，先执行信号处理函数：在信号处理函数中可以去调用read函数读取按键值。

&emsp;&emsp;信号处理函数返回后，APP会继续执行原先被打断的代码。

   
   
##### 应用程序之间发信号示例代码   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\03_signal_example   
```
   
&emsp;&emsp;代码并不复杂，如下。

&emsp;&emsp;第13行注册信号处理函数，第15行就是一个无限循环。在它运行期间，你可以用另一个APP发信号给它。

``` C
	01 # include <stdio.h>   
	02 # include <unistd.h>   
	03 # include <signal.h>   
	04 void my_sig_func(int signo)   
	05 {   
	06    printf("get a signal : %d\n", signo);   
	07 }   
	08   
	09 int main(int argc, char **argv)   
	10 {   
	11    int i = 0;   
	12   
	13    signal(SIGIO, my_sig_func);   
	14   
	15    while (1)   
	16    {   
	17       printf("Hello, world %d!\n", i++);   
	18       sleep(2);   
	19    }   
	20   
	21    return 0;   
	22 }   
```
   
&emsp;&emsp;在Ubuntu上的测试方法：

```bash
		$ gcc   -o   signal  signal.c   // 编译程序   
		$ ./signal &               // 后台运行   
		$ ps  -A | grep signal        // 查看进程ID，假设是9527   
		$ kill  -SIGIO  9527         // 给这个进程发信号   
```
#### 驱动程序提供能力，不提供策略   
&emsp;&emsp;我们的驱动程序可以实现上述4种提供按键的方法，但是驱动程序不应该限制APP使用哪种方法。

&emsp;&emsp;这就是驱动设计的一个原理：提供能力，不提供策略。就是说，你想用哪种方法都行，驱动程序都可以提供；但是驱动程序不能限制你使用哪种方法。

   
   
## 查询方式的按键驱动程序_编写框架   
### LED驱动回顾   
&emsp;&emsp;对于LED，APP调用open函数导致驱动程序的led_open函数被调用。在里面，把GPIO配置为输出引脚。安装驱动程序后并不意味着会使用对应的硬件，而APP要使用对应的硬件，必须先调用open函数。所以建议在驱动程序的open函数中去设置引脚。

&emsp;&emsp;APP继续调用write函数传入数值，在驱动程序的led_write函数根据该数值去设置GPIO的数据寄存器，从而控制GPIO的输出电平。

&emsp;&emsp;怎么操作寄存器？从芯片手册得到对应寄存器的物理地址，在驱动程序中使用ioremap函数映射得到虚拟地址。驱动程序中使用虚拟地址去访问寄存器。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_045.png) 
<600px>
   
### 按键驱动编写思路   
&emsp;&emsp;GPIO按键的原理图一般有如下2种：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_115.png) 
<400px>

&emsp;&emsp;按键没被按下时，上图中左边的GPIO电平为高，右边的GPIO电平为低。

&emsp;&emsp;按键被按下后，上图中左边的GPIO电平为低，右边的GPIO电平为高。

&emsp;&emsp;编写按键驱动程序最简单的方法如下图所示：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_116.png) 
<600px>

&emsp;&emsp;回顾一下编写驱动程序的套路：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_040.png) 
<600px>

&emsp;&emsp;对于使用查询方式的按键驱动程序，我们只需要实现button_open、button_read。

   
### 编程：先写框架   
&emsp;&emsp;我们的目的写出一个容易扩展到各种芯片、各种板子的按键驱动程序，所以驱动程序分为上下两层：

&emsp;&emsp;&emsp;&emsp;① button_drv.c分配/设置/注册file_operations结构体

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;起承上启下的作用，向上提供button_open,button_read供APP调用。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;而这2个函数又会调用底层硬件提供的p_button_opr中的init、read函数操作硬件。

&emsp;&emsp;&emsp;&emsp;② board_xxx.c分配/设置/注册button_operations结构体

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;这个结构体是我们自己抽象出来的，里面定义单板xxx的按键操作函数。

&emsp;&emsp;这样的结构易于扩展，对于不同的单板，只需要替换board_xxx.c提供自己的button_operations结构体即可。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_117.png) 
<800px>


&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	01_all_series_quickstart\04_快速入门(正式开始)\   
		02_嵌入式Linux驱动开发基础知识\source\   
			04_button_drv\01_button_drv_template   
```
   
#### 把按键的操作抽象出一个button_operations结构体   
&emsp;&emsp;首先看看button_drv.h，它定义了一个button_operations结构体，把按键的操作抽象为这个结构体：

``` C
		04 struct button_operations {   
		05    int count;   
		06    void (*init) (int which);   
		07    int (*read) (int which);   
		08 };   
		09   
		10 void register_button_operations(struct button_operations *opr);   
		11 void unregister_button_operations(void);   
		12   
```
   
&emsp;&emsp;再看看board_xxx.c，它实现了一个button_operations结构体，代码如下。

&emsp;&emsp;第 45 行调用register_button_operations函数，把这个结构体注册到上层驱动中。

``` C
		37 static struct button_operations my_buttons_ops ={   
		38    .count = 2,   
		39    .init  = board_xxx_button_init_gpio,   
		40    .read  = board_xxx_button_read_gpio,   
		41 };   
		42   
		43 int board_xxx_button_init(void)   
		44 {   
		45    register_button_operations(&my_buttons_ops);   
		46    return 0;   
		47 }   
		48   
```
#### 驱动程序的上层：file_operations结构体   
&emsp;&emsp;上层是button_drv.c，它的核心是file_operations结构体，首先看看入口函数，代码如下。

&emsp;&emsp;第 83 行向内核注册一个file_operations结构体。

&emsp;&emsp;第 85 行创建一个class，但是该class下还没有device，在后面获得底层硬件的信息时再在class下创建device：这只是用来创建设备节点，它不是驱动程序的核心。

``` C
		81 int button_init(void)   
		82 {   
		83    major = register_chrdev(0, "100ask_button", &button_fops);   
		84   
		85    button_class = class_create(THIS_MODULE, "100ask_button");   
		86    if (IS_ERR(button_class))   
		87       return -1;   
		88   
		89    return 0;   
		90 }   
		91   
```
   
&emsp;&emsp;再来看看button_drv.c中file_operations结构体的成员函数，代码如下。

&emsp;&emsp;第 34 、 44 行都用到一个button_operations指针，它是从何而来？

``` C
		28 static struct button_operations *p_button_opr;   
		29 static struct class *button_class;   
		30   
		31 static int button_open (struct inode *inode, struct file *file)   
		32 {   
		33    int minor = iminor(inode);   
		34    p_button_opr->init(minor);   
		35    return 0;   
		36 }   
		37   
		38 static ssize_t button_read (struct file *file, char __user *buf, size_t size, loff_t *off)   
		39 {   
		40    unsigned int minor = iminor(file_inode(file));   
		41    char level;   
		42    int err;   
		43   
		44    level = p_button_opr->read(minor);   
		45    err = copy_to_user(buf, &level, 1);   
		46    return 1;   
		47 }   
		48   
		49   
		50 static struct file_operations button_fops = {   
		51    .open = button_open,   
		52    .read = button_read,   
		53 };   
```
   
&emsp;&emsp;上面第34、44行都用到一个button_operations指针，来自于底层硬件相关的代码。

&emsp;&emsp;底层代码调用register_button_operations函数，向上提供这个结构体指针。

&emsp;&emsp;register_button_operations函数代码如下，它还根据底层提供的button_operations调用device_create，这是创建设备节点(第62行)。

``` C
		55 void register_button_operations(struct button_operations *opr)   
		56 {   
		57    int i;   
		58   
		59    p_button_opr = opr;   
		60    for (i = 0; i < opr->count; i++)   
		61    {   
		62       device_create(button_class, NULL, MKDEV(major, i), NULL, "100ask_button%d", i);   
		63    }   
		64 }   
		65   
```
   
### 测试   
&emsp;&emsp;这只是一个示例程序，还没有真正操作硬件。测试程序操作驱动程序时，只会导致驱动程序中打印信息。

&emsp;&emsp;首先设置交叉工具链，修改驱动Makefile中内核的源码路径，编译驱动和测试程序。

&emsp;&emsp;启动开发板后，通过NFS访问编译好驱动程序、测试程序，就可以在开发板上如下操作了：

```bash
		#  insmod button_drv.ko   // 装载驱动程序   
		[  435.276713] button_drv: loading out-of-tree module taints kernel.   
		#  insmod board_xxx.ko   
		#  ls /dev/100ask_button* -l    // 查看设备节点   
		crw-------   1 root    root     236,   0 Jan 18 08:57 /dev/100ask_button0   
		crw-------   1 root    root     236,   1 Jan 18 08:57 /dev/100ask_button1   
		#  ./button_test /dev/100ask_button0   // 读按键   
		[  450.886180] /home/book/source/04_button_drv/01_button_drv_template/board_xxx.c board_xxx_button_init_gpio 28, init gpio for button 0   
		[  450.910915] /home/book/source/04_button_drv/01_button_drv_template/board_xxx.c board_xxx_button_read_gpio 33, read gpio for button 0   
		get button : 1   // 得到数据   
```
   
### 课后怎业   
&emsp;&emsp;合并LED、BUTTON框架驱动程序：01_led_drv_template、01_button_drv_template，合并为：gpio_drv_template

## 具体单板的按键驱动程序(查询方式)   
### GPIO操作回顾   
&emsp;&emsp;参考第4章《普适的GPIO引脚操作方法》、第5章《具体单板的GPIO操作方法》。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_118.png) 
<600px>
   
   
### AM335X的按键驱动程序(查询方式)   
#### 先看原理图确定引脚及操作方法   
&emsp;&emsp;AM335X是底板+核心板的结构，打开底板原理图100ask_am335x_v12_原理图.pdf，它有4个按键，本视频只操作一个按键，原理图如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_119.png) 
<400px>

&emsp;&emsp;平时按键电平为高，按下按键后电平为低。

&emsp;&emsp;按键引脚为GPIO1_25。

   
#### 再看芯片手册确定寄存器及操作方法   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_120.png) 
<600px>

&emsp;&emsp;步骤1：使能GPIO1模块

&emsp;&emsp;&emsp;&emsp;设置CM_PER_GPIO1_CLKCTRL寄存器的bit[18]为1，bit[1:0]为0x2，该寄存器地址为0x44E00000+0xAC。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_121.png) 
<600px>


&emsp;&emsp;步骤2：把GPIO1_25对应的引脚设置为GPIO模式

&emsp;&emsp;&emsp;&emsp;要用哪一个寄存器来把GPIO1_25对应的引脚设置为GPIO模式？

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 在核心板原理图ET-som335X原理图.pdf里搜“GPIO1_25”，可以看到下图，确定pin number为U16：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_122.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② 在芯片手册AM335x Sitara™ Processors.pdf里搜“U16”，可得下图，引脚名为GPMC_A9，用作GPIO时要设置为mode 7：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_123.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;③ 在芯片手册AM335x_datasheet_spruh73p.pdf中搜gpmc_a9，

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_124.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;所以，要把GPIO1_25对应的引脚设置为GPIO模式，要设置conf_gpmc_a9寄存器的bit[5]为1，bit[2:0]为7，这个寄存器的地址是 0x44E10000+0x864。

&emsp;&emsp;步骤3：设置GPIO1内部寄存器，把GPIO1_25设置为输入引脚，读数据寄存器

&emsp;&emsp;&emsp;&emsp;GPIO_OE寄存器：地址为0x4804C000+0x134，bit[25]设置为1。

&emsp;&emsp;&emsp;&emsp;GPIO_DATAIN寄存器：地址为0x4804C000+0x138，读其bit[25]。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_125.png) 
<600px>
   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_126.png) 
<600px>
   
#### 编程   
##### 程序框架   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
		01_all_series_quickstart\04_快速入门(正式开始)\   
			02_嵌入式Linux驱动开发基础知识\source\   
				04_button_drv\02_button_drv_for_boards\01_button_drv_for_am335x   
```
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_127.png) 
<800px>
   
   
##### 硬件相关的代码   
&emsp;&emsp;主要看board_am335x.c，先看它的入口函数，代码如下。

&emsp;&emsp;第84行向上层驱动注册一个button_operations结构体，该结构体在第76～80行定义。

``` C
		76 static struct button_operations my_buttons_ops = {   
		77    .count = 1,   
		78    .init = board_am335x_button_init,   
		79    .read = board_am335x_button_read,   
		80 };   
		81   
		82 int board_am335x_button_drv_init(void)   
		83 {   
		84    register_button_operations(&my_buttons_ops);   
		85    return 0;   
		86 }   
		87   
```
   
&emsp;&emsp;button_operations结构体中有init函数指针，它指向board_am335x_button_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。代码如下。

&emsp;&emsp;值得关注的是第32～35行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器。

``` C
		21 static volatile unsigned int *CM_PER_GPIO1_CLKCTRL;   
		22 static volatile unsigned int *conf_gpmc_a9;   
		23 static volatile unsigned int *GPIO1_OE;   
		24 static volatile unsigned int *GPIO1_DATAIN;   
		25   
		26 static void board_am335x_button_init (int which) /* 初始化button, which-哪个button */   
		27 {   
		28    if (which == 0)   
		29    {   
		30       if (!CM_PER_GPIO1_CLKCTRL)   
		31       {   
		32          CM_PER_GPIO1_CLKCTRL = ioremap(0x44E00000 + 0xAC, 4);   
		33          conf_gpmc_a9 = ioremap(0x44E10000 + 0x864, 4);   
		34          GPIO1_OE = ioremap(0x4804C000 + 0x134, 4);   
		35          GPIO1_DATAIN = ioremap(0x4804C000 + 0x138, 4);   
		36       }   
		37   
		38       //printk("%s %s line %d, led %d\n", __FILE__, __FUNCTION__, __LINE__, which);   
		39       /* a. 使能GPIO1   
		40        * set PRCM to enalbe GPIO1   
		41        * set CM_PER_GPIO1_CLKCTRL (0x44E00000 + 0xAC)   
		42        * val: (1<<18) | 0x2   
		43        */   
		44       *CM_PER_GPIO1_CLKCTRL = (1<<18) | 0x2;   
		45   
		46       /* b. 设置GPIO1_25的功能，让它工作于GPIO模式   
		47        * set Control Module to set GPIO1_25 (U16) used as GPIO   
		48        * conf_gpmc_a9 as mode 7   
		49        * addr : 0x44E10000 + 0x864   
		50        * bit[5]   : 1, Input enable value for the PAD   
		51        * bit[2:0] : mode 7   
		52        */   
		53       *conf_gpmc_a9 = (1<<5) | 7;   
		54   
		55       /* c. 设置GPIO1_25的方向，让它作为输入引脚   
		56        * set GPIO1's registers , to set 设置GPIO1_25的方向'S dir (input)   
		57        * GPIO_OE   
		58        * addr : 0x4804C000 + 0x134   
		59        * set bit 25   
		60        */   
		61   
		62       *GPIO1_OE |= (1<<25);   
		63    }   
		64   
		65 }   
		66   
```
   
&emsp;&emsp;button_operations结构体中还有有read函数指针，它指向board_am335x_button_read函数，在里面将会读取并返回按键引脚的电平。代码如下。

``` C
		67 static int board_am335x_button_read (int which) /* 读button, which-哪个 */   
		68 {   
		69    printk("%s %s line %d, button %d, 0x%x\n", __FILE__, __FUNCTION__, __LINE__, which, *GPIO1_DATAIN);   
		70    if (which == 0)   
		71       return (*GPIO1_DATAIN & (1<<25)) ? 1 : 0;   
		72    else   
		73       return 0;   
		74 }   
		75   
```
   
#### 测试   
&emsp;&emsp;安装驱动程序之后执行测试程序，观察它的返回值(执行测试程序的同时操作按键)：

```bash
		#  insmod button_drv.ko   
		#  insmod board_am335x.ko   
		#  ./button_test /dev/100ask_button0   
```
   
#### 课后作业   
&emsp;&emsp;① 修改board_am335x.c，增加更多按键

&emsp;&emsp;② 修改button_test.c，使用按键来点灯

### RK3288的按键驱动程序(查询方式)   
#### 先看原理图确定引脚及操作方法   
&emsp;&emsp;Firefly的RK3288开发板上没有按键，我们为它制作的扩展板上有1个按键。在扩展板原理图rk3288_extend_v12_0715.pdf中可以看到按键，如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_128.png) 
<400px>

&emsp;&emsp;平时按键电平为高，按下按键后电平为低。

&emsp;&emsp;按键引脚为GPIO7_B1。

   
   
#### 再看芯片手册确定寄存器及操作方法   
&emsp;&emsp;芯片手册为Rockchip_RK3288_TRM_V1.2_Part1-20170321.pdf，不过我们总结如下。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_129.png) 
<600px>


&emsp;&emsp;步骤1：使能GPIO7模块

&emsp;&emsp;&emsp;&emsp;设置CRU_CLKGATE14_CON寄存器的bit[7]为0。

&emsp;&emsp;&emsp;&emsp;要设置bit7，必须同时设置bit23为1。

&emsp;&emsp;&emsp;&emsp;该寄存器地址为0xFF760000+0x198。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_130.png) 
<600px>


&emsp;&emsp;步骤2：把GPIO7_B1对应的引脚设置为GPIO模式

&emsp;&emsp;&emsp;&emsp;设置GRF_GPIO7B_IOMUX寄存器的bit[3:2]为0b00。

&emsp;&emsp;&emsp;&emsp;要设置bit[3:2]，必须同时设置bit[19:18]为0b11。

&emsp;&emsp;&emsp;&emsp;该寄存器地址为0xFF770000+0x0070。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_131.png) 
<600px>


&emsp;&emsp;步骤3：设置GPIO7内部寄存器，把GPIO7_B1设置为输入引脚，读数据寄存器

&emsp;&emsp;&emsp;&emsp;GPIO_SWPORTA_DDR方向寄存器：地址为0xFF7E0000+ 0x0004，bit[9]设置为0。

&emsp;&emsp;&emsp;&emsp;GPIO_EXT_PORTA外部端口寄存器：地址为0xFF7E0000+ 0x0050，读其bit[9]。

&emsp;&emsp;&emsp;&emsp;<font color="# dd0000">注意：</font>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;GPIO_A0~A7 对应bit0~bit7；GPIO_B0~B7 对应bit8~bit15；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;GPIO_C0~C7 对应bit16~bit23；GPIO_D0~D7 对应bit24~bit31

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_132.png) 
<600px>
   
   
#### 编程   
##### 程序框架   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
		01_all_series_quickstart\04_快速入门(正式开始)\   
			02_嵌入式Linux驱动开发基础知识\source\   
				04_button_drv\02_button_drv_for_boards\02_button_drv_for_rk3288   
```
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_133.png) 
<800px>
   
   
##### 硬件相关的代码   
&emsp;&emsp;主要看board_rk3288.c，先看它的入口函数，代码如下。

&emsp;&emsp;第 81 行向上层驱动注册一个button_operations结构体，该结构体在第73～77行定义。

``` C
		73 static struct button_operations my_buttons_ops = {   
		74    .count = 1,   
		75    .init = board_rk3288_button_init,   
		76    .read = board_rk3288_button_read,   
		77 };   
		78   
		79 int board_rk3288_button_drv_init(void)   
		80 {   
		81    register_button_operations(&my_buttons_ops);   
		82    return 0;   
		83 }   
```
   
&emsp;&emsp;button_operations结构体中有init函数指针，它指向board_rk3288_button_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。代码如下。

&emsp;&emsp;值得关注的是第32～35行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器。

``` C
		21 static volatile unsigned int *CRU_CLKGATE14_CON;   
		22 static volatile unsigned int *GRF_GPIO7B_IOMUX ;   
		23 static volatile unsigned int *GPIO7_SWPORTA_DDR;   
		24 static volatile unsigned int *GPIO7_EXT_PORTA ;   
		25   
		26 static void board_rk3288_button_init (int which) /* 初始化button, which-哪个button */   
		27 {   
		28    if (which == 0)   
		29    {   
		30       if (!CRU_CLKGATE14_CON)   
		31       {   
		32          CRU_CLKGATE14_CON = ioremap(0xFF760000 + 0x0198, 4);   
		33          GRF_GPIO7B_IOMUX  = ioremap(0xFF770000 + 0x0070, 4);   
		34          GPIO7_SWPORTA_DDR = ioremap(0xFF7E0000 + 0x0004, 4);   
		35          GPIO7_EXT_PORTA   = ioremap(0xFF7E0000 + 0x0050, 4);   
		36       }   
		37   
		38       /* rk3288 GPIO7_B1 */   
		39       /* a. 使能GPIO7   
		40        * set CRU to enable GPIO7   
		41        * CRU_CLKGATE14_CON 0xFF760000 + 0x198   
		42        * (1<<(7+16)) | (0<<7)   
		43        */   
		44       *CRU_CLKGATE14_CON = (1<<(7+16)) | (0<<7);   
		45   
		46       /* b. 设置GPIO7_B1用于GPIO   
		47        * set PMU/GRF to configure GPIO7_B1 as GPIO   
		48        * GRF_GPIO7B_IOMUX 0xFF770000 + 0x0070   
		49        * bit[3:2] = 0b00   
		50        * (3<<(2+16)) | (0<<2)   
		51        */   
		52       *GRF_GPIO7B_IOMUX =(3<<(2+16)) | (0<<2);   
		53   
		54       /* c. 设置GPIO7_B1作为input引脚   
		55        * set GPIO_SWPORTA_DDR to configure GPIO7_B1 as input   
		56        * GPIO_SWPORTA_DDR 0xFF7E0000 + 0x0004   
		57        * bit[9] = 0b0   
		58        */   
		59       *GPIO7_SWPORTA_DDR &= ~(1<<9);   
		60    }   
		61   
		62 }   
```
   
&emsp;&emsp;button_operations结构体中还有有read函数指针，它指向board_rk3288_button_read函数，在里面将会读取并返回按键引脚的电平。代码如下。

``` C
		64 static int board_rk3288_button_read (int which) /* 读button, which-哪个 */   
		65 {   
		66    //printk("%s %s line %d, button %d, 0x%x\n", __FILE__, __FUNCTION__, __LINE__, which, *GPIO1_DATAIN);   
		67    if (which == 0)   
		68       return (*GPIO7_EXT_PORTA & (1<<9)) ? 1 : 0;   
		69    else   
		70       return 0;   
		71 }   
```
   
#### 测试   
&emsp;&emsp;安装驱动程序之后执行测试程序，观察它的返回值(执行测试程序的同时操作按键)：

```bash
		#  insmod button_drv.ko   
		#  insmod board_rk3288.ko   
		#  ./button_test /dev/100ask_button0   
```
#### 课后作业   
&emsp;&emsp;① 修改button_test.c，使用按键来点灯

   
   
### RK3399的按键驱动程序(查询方式)   
#### 先看原理图确定引脚及操作方法   
&emsp;&emsp;Firefly的RK3399开发板上没有按键，我们为它制作的扩展板上有3个按键。在扩展板原理图rk3399_extend_v12_0709final.pdf中可以看到按键，如下：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_134.png) 
<800px>

&emsp;&emsp;平时按键电平为高，按下按键后电平为低。

&emsp;&emsp;按键引脚为GPIO0_B1、GPIO0_B2、GPIO0_B4。

&emsp;&emsp;本视频中，只操作一个按键：GPIO0_B1。

   
   
#### 再看芯片手册确定寄存器及操作方法   
&emsp;&emsp;芯片手册为Rockchip RK3399TRM V1.3 Part1.pdf和Rockchip RK3399TRM V1.3 Part2.pdf，不过我们总结如下。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_135.png) 
<600px>


&emsp;&emsp;步骤1：使能GPIO0模块

&emsp;&emsp;&emsp;&emsp;设置PMUCRU_CLKGATE_CON1寄存器的bit[3]为0。

&emsp;&emsp;&emsp;&emsp;要设置bit3，必须同时设置bit19为1。

&emsp;&emsp;&emsp;&emsp;该寄存器地址为0xFF760000+ 0x0104。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_136.png) 
<600px>


&emsp;&emsp;步骤2：把GPIO0_B1对应的引脚设置为GPIO模式

&emsp;&emsp;&emsp;&emsp;设置PMUGRF_GPIO0B_IOMUX寄存器的bit[3:2]为0b00。

&emsp;&emsp;&emsp;&emsp;要设置bit[3:2]，必须同时设置bit[19:18]为0b11。

&emsp;&emsp;&emsp;&emsp;该寄存器地址为0xFF310000+0x0004。

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_137.png) 
<600px>


&emsp;&emsp;步骤3：设置GPIO0内部寄存器，把GPIO0_B1设置为输入引脚，读数据寄存器

&emsp;&emsp;&emsp;&emsp;这些寄存器的介绍在芯片手册Rockchip RK3399TRM V1.3 Part2.pdf中。

&emsp;&emsp;&emsp;&emsp;GPIO_SWPORTA_DDR方向寄存器：地址为0xFF720000+ 0x0004，bit[9]设置为0。

&emsp;&emsp;&emsp;&emsp;GPIO_EXT_PORTA外部端口寄存器：地址为0xFF720000+ 0x0050，读其bit[9]。

&emsp;&emsp;&emsp;&emsp;<font color="# dd0000">注意：</font>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;GPIO_A0~A7 对应bit0~bit7；GPIO_B0~B7 对应bit8~bit15；

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;GPIO_C0~C7 对应bit16~bit23；GPIO_D0~D7 对应bit24~bit31

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_138.png) 
<600px>
   
   
#### 编程   
##### 程序框架   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
		01_all_series_quickstart\04_快速入门(正式开始)\   
			02_嵌入式Linux驱动开发基础知识\source\   
				04_button_drv\02_button_drv_for_boards\03_button_drv_for_rk3399   
```
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_139.png) 
<600px>
   
   
##### 硬件相关的代码   
&emsp;&emsp;主要看board_rk3399.c，先看它的入口函数，代码如下。

&emsp;&emsp;第81行向上层驱动注册一个button_operations结构体，该结构体在第73～77行定义。

``` C
		73 static struct button_operations my_buttons_ops = {   
		74    .count = 1,   
		75    .init = board_rk3399_button_init,   
		76    .read = board_rk3399_button_read,   
		77 };   
		78   
		79 int board_rk3399_button_drv_init(void)   
		80 {   
		81    register_button_operations(&my_buttons_ops);   
		82    return 0;   
		83 }   
```
   
&emsp;&emsp;button_operations结构体中有init函数指针，它指向board_rk3399_button_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。代码如下。

&emsp;&emsp;值得关注的是第32～35行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器。

``` C
		21 static volatile unsigned int *PMUCRU_CLKGATE_CON1;   
		22 static volatile unsigned int *GRF_GPIO0B_IOMUX ;   
		23 static volatile unsigned int *GPIO0_SWPORTA_DDR;   
		24 static volatile unsigned int *GPIO0_EXT_PORTA ;   
		25   
		26 static void board_rk3399_button_init (int which) /* 初始化button, which-哪个button */   
		27 {   
		28    if (which == 0)   
		29    {   
		30       if (!PMUCRU_CLKGATE_CON1)   
		31       {   
		32          PMUCRU_CLKGATE_CON1 = ioremap(0xFF760000+ 0x0104, 4);   
		33          GRF_GPIO0B_IOMUX  = ioremap(0xFF310000+0x0004, 4);   
		34          GPIO0_SWPORTA_DDR = ioremap(0xFF720000 + 0x0004, 4);   
		35          GPIO0_EXT_PORTA   = ioremap(0xFF720000 + 0x0050, 4);   
		36       }   
		37   
		38       /* rk3399 GPIO0_B1 */   
		39       /* a. 使能GPIO0   
		40        * set CRU to enable GPIO0   
		41        * PMUCRU_CLKGATE_CON1 0xFF760000+ 0x0104   
		42        * (1<<(3+16)) | (0<<3)   
		43        */   
		44       *PMUCRU_CLKGATE_CON1 = (1<<(3+16)) | (0<<3);   
		45   
		46       /* b. 设置GPIO0_B1用于GPIO   
		47        * set PMU/GRF to configure GPIO0_B1 as GPIO   
		48        * GRF_GPIO0B_IOMUX 0xFF310000+0x0004   
		49        * bit[3:2] = 0b00   
		50        * (3<<(2+16)) | (0<<2)   
		51        */   
		52       *GRF_GPIO0B_IOMUX =(3<<(2+16)) | (0<<2);   
		53   
		54       /* c. 设置GPIO0_B1作为input引脚   
		55        * set GPIO_SWPORTA_DDR to configure GPIO0_B1 as input   
		56        * GPIO_SWPORTA_DDR 0xFF720000 + 0x0004   
		57        * bit[9] = 0b0   
		58        */   
		59       *GPIO0_SWPORTA_DDR &= ~(1<<9);   
		60    }   
		61   
		62 }   
```
   
&emsp;&emsp;button_operations结构体中还有有read函数指针，它指向board_rk3399_button_read函数，在里面将会读取并返回按键引脚的电平。代码如下。

``` C
		64 static int board_rk3399_button_read (int which) /* 读button, which-哪个 */   
		65 {   
		66   //printk("%s %s line %d, button %d, 0x%x\n", __FILE__, __FUNCTION__, __LINE__, which, *GPIO1_DATAIN);   
		67    if (which == 0)   
		68       return (*GPIO0_EXT_PORTA & (1<<9)) ? 1 : 0;   
		69    else   
		70       return 0;   
		71 }   
```
#### 测试   
&emsp;&emsp;安装驱动程序之后执行测试程序，观察它的返回值(执行测试程序的同时操作按键)：

```bash
		#  insmod button_drv.ko   
		#  insmod board_rk3399.ko   
		#  ./button_test /dev/100ask_button0   
```
   
#### 课后作业   
&emsp;&emsp;① 修改board_rk3399.c，增加更多按键

&emsp;&emsp;② 修改button_test.c，使用按键来点灯

   
### 百问网IMX6ULL-QEMU的按键驱动程序(查询方式)   
&emsp;&emsp;使用QEMU模拟的硬件，它的硬件资源可以随意扩展。

&emsp;&emsp;在IMX6ULL QEMU 虚拟开发板上，我们为它设计了2个 按键。在QEMU的GUI上有4个按键，右边的2个留待以后用于电源管理。

#### 先看原理图确定引脚及操作方法   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_140.png) 
<600px>

&emsp;&emsp;平时按键电平为低，按下按键后电平为高。

&emsp;&emsp;按键引脚为GPIO5_IO01、GPIO1_IO18。

#### 再看芯片手册确定寄存器及操作方法   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_023.png) 
<600px>
&emsp;&emsp;步骤1：使能GPIO1、GPIO5

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_078.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置b[31:30]、b[27:26]就可以使能GPIO5、GPIO1，设置为什么值呢？

&emsp;&emsp;&emsp;&emsp;看下图，设置为0b11：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_141.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;① 00：该GPIO模块全程被关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;② 01：该GPIO模块在CPU run mode情况下是使能的；在WAIT或STOP模式下，关闭

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;③ 10：保留

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;④ 11：该GPIO模块全程使能

&emsp;&emsp;步骤2：设置GPIO5_IO01、GPIO1_IO18为GPIO模式

&emsp;&emsp;&emsp;&emsp;① 对于GPIO5_IO01，设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_142.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;② 对于GPIO1_IO18，设置如下寄存器：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_001.png) 
<600px>

&emsp;&emsp;步骤3：设置GPIO5_IO01、GPIO1_IO18为输入引脚，读取引脚电平

&emsp;&emsp;&emsp;&emsp;寄存器地址为：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_072.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;设置方向寄存器，把引脚设置为输出引脚：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_073.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;读取引脚状态寄存器，得到引脚电平：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_143.png) 
<600px>
   
   
#### 编程   
##### 程序框架   
&emsp;&emsp;使用GIT下载所有源码后，本节源码位于如下目录：

```bash
	 01_all_series_quickstart\04_快速入门(正式开始)\   
	 		02_嵌入式Linux驱动开发基础知识\source\   
	 			04_button_drv\02_button_drv_for_boards\04_button_drv_for_100ask_imx6ull-qemu   
```
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_144.png) 
<800px>
   
   
##### 硬件相关的代码   
&emsp;&emsp;主要看board_100ask_imx6ull-qemu.c。

&emsp;&emsp;涉及的寄存器挺多，一个一个去执行ioremap效率太低。

&emsp;&emsp;先定义结构体，然后对结构体指针进行ioremap。

&emsp;&emsp;对于IOMUXC，可以如下定义：

``` C
		struct iomux {   
			volatile unsigned int unnames[23];   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO00;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO01;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO02;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO03;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO04;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO05;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO06;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO07;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO08;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_GPIO1_IO09;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_UART1_TX_DATA;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_UART1_RX_DATA;   
			volatile unsigned int IOMUXC_SW_MUX_CTL_PAD_UART1_CTS_B;   
		};   
```
   
&emsp;&emsp;struct iomux  *iomux = ioremap(0x20e0000,  sizeof(struct iomux));

&emsp;&emsp;对于GPIO，可以如下定义：

``` C
		struct imx6ull_gpio {   
			volatile unsigned int dr;   
			volatile unsigned int gdir;   
			volatile unsigned int psr;   
			volatile unsigned int icr1;   
			volatile unsigned int icr2;   
			volatile unsigned int imr;   
			volatile unsigned int isr;   
			volatile unsigned int edge_sel;   
		};   
		struct imx6ull_gpio *gpio1 = ioremap(0x209C000,  sizeof(struct imx6ull_gpio));   
		struct imx6ull_gpio *gpio5 = ioremap(0x20AC000,  sizeof(struct imx6ull_gpio));   
```
   
&emsp;&emsp;看一个驱动程序，先看它的入口函数，代码如下。

&emsp;&emsp;第127行向上层驱动注册一个button_operations结构体，该结构体在第119～123行定义。

``` C
		119 static struct button_operations my_buttons_ops = {   
		120    .count = 2,   
		121    .init = board_imx6ull_button_init,   
		122    .read = board_imx6ull_button_read,   
		123 };   
		124   
		125 int board_imx6ull_button_drv_init(void)   
		126 {   
		127    register_button_operations(&my_buttons_ops);   
		128    return 0;   
		129 }   
```
   
&emsp;&emsp;button_operations结构体中有init函数指针，它指向board_imx6ull_button_init函数，在里面将会初始化LED引脚：使能、设置为GPIO模式、设置为输出引脚。代码如下。

&emsp;&emsp;值得关注的是第65～70行，对于寄存器要先使用ioremap得到它的虚拟地址，以后使用虚拟地址访问寄存器。

``` C
		50 /* enable GPIO1,GPIO5 */   
		51 static volatile unsigned int *CCM_CCGR1;   
		52   
		53 /* set GPIO5_IO03 as GPIO */   
		54 static volatile unsigned int *IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER1;   
		55   
		56 static struct iomux *iomux;   
		57   
		58 static struct imx6ull_gpio *gpio1;   
		59 static struct imx6ull_gpio *gpio5;   
		60   
		61 static void board_imx6ull_button_init (int which) /* 初始化button, which-哪个button */   
		62 {   
		63    if (!CCM_CCGR1)   
		64    {   
		65       CCM_CCGR1 = ioremap(0x20C406C, 4);   
		66       IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER1 = ioremap(0x229000C, 4);   
		67   
		68       iomux = ioremap(0x20e0000, sizeof(struct iomux));   
		69       gpio1 = ioremap(0x209C000, sizeof(struct imx6ull_gpio));   
		70       gpio5 = ioremap(0x20AC000, sizeof(struct imx6ull_gpio));   
		71    }   
		72   
		73    if (which == 0)   
		74    {   
		75       /* 1. enable GPIO5   
		76        * CG15, b[31:30] = 0b11   
		77        */   
		78       *CCM_CCGR1 |= (3<<30);   
		79   
		80       /* 2. set GPIO5_IO01 as GPIO   
		81        * MUX_MODE, b[3:0] = 0b101   
		82        */   
		83       *IOMUXC_SNVS_SW_MUX_CTL_PAD_SNVS_TAMPER1 = 5;   
		84   
		85       /* 3. set GPIO5_IO01 as input   
		86        * GPIO5 GDIR, b[1] = 0b0   
		87        */   
		88       gpio5->gdir &= ~(1<<1);   
		89    }   
		90    else if(which == 1)   
		91    {   
		92       /* 1. enable GPIO1   
		93        * CG13, b[27:26] = 0b11   
		94        */   
		95       *CCM_CCGR1 |= (3<<26);   
		96   
		97       /* 2. set GPIO1_IO18 as GPIO   
		98        * MUX_MODE, b[3:0] = 0b101   
		99        */   
		100       iomux->IOMUXC_SW_MUX_CTL_PAD_UART1_CTS_B = 5;   
		101   
		102       /* 3. set GPIO1_IO18 as input   
		103        * GPIO1 GDIR, b[18] = 0b0   
		104        */   
		105       gpio1->gdir &= ~(1<<18);   
		106    }   
		107   
		108 }   
```
   
&emsp;&emsp;button_operations结构体中还有有read函数指针，它指向board_imx6ull_button_read函数，在里面将会读取并返回按键引脚的电平。代码如下。

``` C
		110 static int board_imx6ull_button_read (int which) /* 读button, which-哪个 */   
		111 {   
		112    //printk("%s %s line %d, button %d, 0x%x\n", __FILE__, __FUNCTION__, __LINE__, which, *GPIO1_DATAIN);   
		113    if (which == 0)   
		114       return (gpio5->psr & (1<<1)) ? 1 : 0;   
		115    else   
		116       return (gpio1->psr & (1<<18)) ? 1 : 0;   
		117 }   
```
   
#### 测试   
&emsp;&emsp;先启动IMX6ULL QEMU模拟器，挂载NFS文件系统。

&emsp;&emsp;运行QEMU时，

&emsp;&emsp;QEMU内部为主机虚拟出一个网卡, IP为 10.0.2.2，

&emsp;&emsp;IMX6ULL有一个网卡, IP为 10.0.2.15，

&emsp;&emsp;它连接到主机的虚拟网卡。

&emsp;&emsp;这样IMX6ULL就可以通过10.0.2.2去访问Ubuntu了。

&emsp;&emsp;安装驱动程序之后执行测试程序，观察它的返回值(执行测试程序的同时操作按键)：

```bash
	#  insmod button_drv.ko   
	#  insmod board_drv.ko   
	#  insmod board_100ask_imx6ull-qemu.ko   
	#  ./button_test  /dev/100ask_button0   
	#  ./button_test  /dev/100ask_button1   
```
   
#### 课后作业   
&emsp;&emsp;① 修改button_test.c，使用按键来点灯

   
   
## 异常与中断的概念及处理流程   
### 中断的引入   
#### 妈妈怎么知道孩子醒了   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_110.png) 
<400px>

&emsp;&emsp;妈妈怎么知道卧室里小孩醒了？

&emsp;&emsp;&emsp;&emsp;① 时时进房间看一下：查询方式

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;简单，但是累

&emsp;&emsp;&emsp;&emsp;② 进去房间陪小孩一起睡觉，小孩醒了会吵醒她：休眠-唤醒

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;不累，但是妈妈干不了活了

&emsp;&emsp;&emsp;&emsp;③ 妈妈要干很多活，但是可以陪小孩睡一会，定个闹钟：poll方式

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;要浪费点时间，但是可以继续干活。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;妈妈要么是被小孩吵醒，要么是被闹钟吵醒。

&emsp;&emsp;&emsp;&emsp;④ 妈妈在客厅干活，小孩醒了他会自己走出房门告诉妈妈：异步通知

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;妈妈、小孩互不耽误。

&emsp;&emsp;后面的3种方式，都需要“小孩来中断妈妈”：中断她的睡眠、中断她的工作。

&emsp;&emsp;实际上，能“中断”妈妈的事情可多了：

&emsp;&emsp;&emsp;&emsp;① 远处的猫叫：这可以被忽略

&emsp;&emsp;&emsp;&emsp;② 门铃、小孩哭声：妈妈的应对措施不一样

&emsp;&emsp;&emsp;&emsp;③ 身体不舒服：那要赶紧休息

&emsp;&emsp;&emsp;&emsp;④ 有蜘蛛掉下来了：赶紧跑啊，救命

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_145.png) 
<600px>


&emsp;&emsp;妈妈当前正在看书，被“中断”后她会怎么做？流程如下：

&emsp;&emsp;&emsp;&emsp;① 妈妈正在看书

&emsp;&emsp;&emsp;&emsp;② 发生了各种声音

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;可忽略的远处猫叫

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;快递员按门铃

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;卧室中小孩哭了

&emsp;&emsp;&emsp;&emsp;③ 妈妈怎么办？

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. 先在书中放入书签，合上书

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. 去处理

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;对于不同的情况，处理方法不同：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;对于门铃：开门取快递

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;对于哭声：照顾小孩

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. 回来继续看书

#### 嵌入系统中也有类似的情况   
![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_146.png) 
<600px>

&emsp;&emsp;CPU在运行的过程中，也会被各种“异常”打断。这些“异常”有：

&emsp;&emsp;&emsp;&emsp;① 指令未定义

&emsp;&emsp;&emsp;&emsp;② 指令、数据访问有问题

&emsp;&emsp;&emsp;&emsp;③ SWI(软中断)

&emsp;&emsp;&emsp;&emsp;④ 快中断

&emsp;&emsp;&emsp;&emsp;⑤ 中断

&emsp;&emsp;中断也属于一种“异常”，导致中断发生的情况有很多，比如：

&emsp;&emsp;&emsp;&emsp;① 按键

&emsp;&emsp;&emsp;&emsp;② 定时器

&emsp;&emsp;&emsp;&emsp;③ ADC转换完成

&emsp;&emsp;&emsp;&emsp;④ UART发送完数据、收到数据

&emsp;&emsp;&emsp;&emsp;⑤ 等等

&emsp;&emsp;这些众多的“中断源”，汇集到“中断控制器”，由“中断控制器”选择优先级最高的中断并通知CPU。

   
### 中断的处理流程   
&emsp;&emsp;arm对异常(中断)处理过程：

&emsp;&emsp;&emsp;&emsp;① 初始化：

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. 设置中断源，让它可以产生中断

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. 设置中断控制器(可以屏蔽某个中断，优先级)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. 设置CPU总开关(使能中断)

&emsp;&emsp;&emsp;&emsp;② 执行其他程序：正常程序

&emsp;&emsp;&emsp;&emsp;③ 产生中断：比如按下按键--->中断控制器--->CPU

&emsp;&emsp;&emsp;&emsp;④ CPU 每执行完一条指令都会检查有无中断/异常产生

&emsp;&emsp;&emsp;&emsp;⑤ CPU发现有中断/异常产生，开始处理。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;对于不同的异常，跳去不同的地址执行程序。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;这地址上，只是一条跳转指令，跳去执行某个函数(地址)，这个就是异常向量。

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<font color="# dd0000">③④⑤都是硬件做的。</font>

&emsp;&emsp;&emsp;&emsp;⑥ 这些函数做什么事情？

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;软件做的:

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;a. 保存现场(各种寄存器)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;b. 处理异常(中断):

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;分辨中断源，再调用不同的处理函数

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;c. 恢复现场

   
### 异常向量表   
&emsp;&emsp;u-boot或是Linux内核，都有类似如下的代码：

``` C
		 _start: b	reset   
		 	ldr	pc, _undefined_instruction   
		 	ldr	pc, _software_interrupt   
		 	ldr	pc, _prefetch_abort   
		 	ldr	pc, _data_abort   
		 	ldr	pc, _not_used   
		 	ldr	pc, _irq //发生中断时，CPU跳到这个地址执行该指令 **假设地址为0x18**   
		 	ldr	pc, _fiq   
```
   
&emsp;&emsp;这就是异常向量表，每一条指令对应一种异常。

&emsp;&emsp;发生复位时，CPU就去 执行第1条指令：b  reset。

&emsp;&emsp;发生中断时，CPU就去执行“ldr  pc, _irq”这条指令。

&emsp;&emsp;这些指令存放的位置是固定的，比如对于ARM9芯片中断向量的地址是0x18。

&emsp;&emsp;当发生中断时，CPU就强制跳去执行0x18处的代码。

&emsp;&emsp;在向量表里，一般都是放置一条跳转指令，发生该异常时，CPU就会执行向量表中的跳转指令，去调用更复杂的函数。

&emsp;&emsp;当然，向量表的位置并不总是从0地址开始，很多芯片可以设置某个vector base寄存器，指定向量表在其他位置，比如设置vector base为0x80000000，指定为DDR的某个地址。但是表中的各个异常向量的偏移地址，是固定的：复位向量偏移地址是0，中断是0x18。

### 参考资料   
&emsp;&emsp;对于ARM的中断控制器，述语上称之为GIC (Generic Interrupt Controller)，到目前已经更新到v4版本了。

&emsp;&emsp;各个版本的差别可以看这里：https://developer.arm.com/ip-products/system-ip/system-controllers/interrupt-controllers

&emsp;&emsp;简单地说，GIC v3/v4用于 ARMv8 架构，即64位ARM芯片。

&emsp;&emsp;而GIC v2用于ARMv7和其他更低的架构。

&emsp;&emsp;以后在驱动大全里讲解中断时，我们再深入分析，到时会涉及单核、多核等知识。

## 常见问题   
### 安装驱动时version magic不匹配   
&emsp;&emsp;要想彻底了解内核的LOCALVERSION信息，可以看这个贴子： https://blog.csdn.net/gatieme/article/details/78510497

&emsp;&emsp;总结一下：

&emsp;&emsp;&emsp;&emsp;① 开发板所用的内核版本：

&emsp;&emsp;&emsp;&emsp;&emsp;;在开发板上执行“uname  -r”命令，可以得到开发板所用内核的版本，比如：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_147.png) 
<200px>


&emsp;&emsp;&emsp;&emsp;② 在服务器中给开发板编译内核时，这个内核也有一个版本：

&emsp;&emsp;&emsp;&emsp;&emsp;;进入该内核源码目录，执行“make kernelrelease”命令，可以得到它的版本，比如：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_148.png) 
<800px>


&emsp;&emsp;&emsp;&emsp;③ 编译驱动时，会用到服务器中开发板的内核源码，会带有它的版本信息。

&emsp;&emsp;&emsp;&emsp;&emsp;如果①②③的版本信息不匹配，很可能导致驱动程序无法加载，比如：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_149.png) 
<800px>

&emsp;&emsp;有2个解决方法：

&emsp;&emsp;&emsp;&emsp;A. 在Ubuntu上重新编译内核，让开发板使用新的内核启动；重新编译驱动，加载新驱动：

&emsp;&emsp;&emsp;&emsp;&emsp;这样，①②③三者的内核版本就都一致了。

&emsp;&emsp;&emsp;&emsp;&emsp;但是，这种方法有时候不好用，比如开发板上的内核无法更改(出厂固化了)，或者你没有开发板上所用内核的全部源码无法编译出内核，这时就可以使用下面的方法。

&emsp;&emsp;&emsp;&emsp;B. 在Ubuntu上修改版本号，改为开发板上“uname -r”的结果，然后重新编译内核和驱动：

&emsp;&emsp;&emsp;&emsp;&emsp;开发板就可以继续使用原来的内核，并且可以加载编译出来的驱动了。

&emsp;&emsp;步骤如下：

&emsp;&emsp;&emsp;&emsp;b.1 修改Ubuntu上开发板内核源码顶层目录Makefile，如下图：

![](https://weidongshan.readthedocs.io/zh_CN/latest/_images/EmbeddedLinuxApplicationDevelopmentCompleteManualSecondEditionChapterFive_150.png) 
<600px>

&emsp;&emsp;&emsp;&emsp;b.2 重新编译内核，这会生成一些头文件，供驱动使用

&emsp;&emsp;&emsp;&emsp;b.3 重新编译驱动

   
   
   
   
