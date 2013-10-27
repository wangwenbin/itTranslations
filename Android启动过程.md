#In Depth : Android Boot Sequence / Process
>#深度解析：Android启动顺序与进程

------------------------------------------


What happened when I press power on button in my Android device ?
>当我按下我的Android设备的电源键时发生了什么？

What is Android boot sequence ?
>什么事Android启动序列？

What is linux kernel ?
>什么是linux内核？

What is different between desktop linux kernel and Android linux kernel ?
>Android的linux内核与桌面linux的内核有什么不同？

What is bootloader ?
>什么bootloader（启动引导）？

What is Zygote ?
>什么是Zygote？

What is x86 and ARM linux ?
>x86和ARM分别是什么？

What is init.rc ?
>什么是init.rc？

What is System Server ?
>什么是系统服务器？


 Many questions pop-up in mind when we think about Android boot sequence.
>当我们思考android启动顺序的时候，很多问题都会在我们的脑海浮现。

Here I am explaining Android boot process. I hope you will find answer of above questions.
>在这里我解释一下android的启动过程。我希望你能从中找到上面问题的答案。



Android is linux based open source operating system, x86 (x86 is a series of computer microprocessor instruction set architectures based on the Intel 8086 CPU.) is most likely system where linux kernel is deployed however all Android devices are running on ARM process (ARM (formerly Advanced RISC Machine, which was formerly Acorn RISC Machine)) except Intel’s Xolo device (http://xolo.in/xolo-x900-features). Xolo comes with Atom 1.6 GHz x86 processor. Android boot sequence or I can say embedded device or ARM based linux has minor difference compare to desktop version.  In this article I am going to explain boot sequence for Android only. Inside the linux boot process is good article for desktop based linux boot sequence.
>Android是一个基于linux的开源的操作系统，x86（x86是一系列的基于Intel 8086 CPU的计算机微处理器指令）最受linux内核欢迎的部署环境，然后几乎所有的（译注：原文意思为所有的）Android设备都运行在ARM(ARM(以前叫Advanced RISC Machine，更早以前叫Acorn RISC Machine))处理器上，除了Intel的Xolo（ http://xolo.in/xolo-x900-features ）设备，Xolo带有1.6GHz的x86处理器。Android启动顺序或者我称之为嵌入式设备或者基于linux的ARM设备的启动顺序和桌面版本linux相比有一点不同。在这片文章中，我将只解释Android的启动顺序。其中linux启动过程对基于linux启动顺序的桌面系统是好的文章。

Android device execute following steps when you press power switch
>当按下电源开关的时候Android设备执行以下步骤

Android Boot Sequence / Process
>Android启动顺序和过程

Step 1 : Power On and System Startup 
>第一步：机器上电，系统启动。

When power start Boot ROM code start execution from pre defined location which is hardwired on ROM. It load Bootloader into RAM and start execution
>当上电时开始Boot ROM代码，开始从硬刻录在ROM上的定义的地址执行。它加载Bootloader到RAM并且开始执行。

Step 2 : Bootloader
>第二步：Bootloader

Bootloader is small program which runs before Android operating system running. Bootloader is first program to run so It is specific for board and processor. Device manufacturer either use popular bootloaders like redboot,uboot, qi bootloader or they develop own bootloaders, It’s not part of Android Operating System. bootloader is the place where OEMs and Carriers put there locks and restrictions. 
>Bootloader 是一个在Android操作系统前运行的小程序。Bootloader是第一个要运行的程序，所以它是对主板和处理器区分的。设备制造商或者采用像redboot，uboot，qi bootloader等流行的bootloader或者他们自己开发自己的bootloader，它并不是Android操作系统的一部分。bootloader是OEM们和运营商放置他们枷锁和限制的地方。

Bootloader perform execution in two stages, first stage It to detect external RAM and load program which helps in second stage, In second stage bootloader setup network, memory, etc. which requires to run kernel, bootloader is able to provide configuration parameters or inputs to the kernel for specific purpose.  
>Bootloader的执行分为两个阶段。第一阶段，它检测扩展RAM和加载程序，他们对第二阶段有帮助。在第二阶段Bootloader设置网络、内存、等等。这些都需要启动内核。bootloader能够为特殊的目的提供设置参数或者内核输入。

Android bootloader can be found at 
>Android的bootloader可以在如下地方找到

<Android Source>\bootable\bootloader\legacy\usbloaderlegacy loader contain two important files that need to address here.
><Android源代码>\bootable\bootloader\legacy\usbloaderlegac 加载程序包括两个需要放在这里的重要的文件。

1. init.s - Initializes stacks, zeros the BSS segments, call _main() in main.c
>1. init.s --初始化栈，用0初始化BSS段，启动main.c中的_main()。

2. main.c - Initializes hardware (clocks, board, keypad, console), creates Linux tags
>2. main.c --初始化硬件（时钟，主板，键盘，控制台），创建Linux标签。

Refer this link to know more about Android bootloader :
>参考这个链接来学习更多关于Android bootloader的知识：

https://motorola-global-portal.custhelp.com/app/answers/detail/a_id/86208/~/bootloader-frequently-asked-questions

>[阅读](https://motorola-global-portal.custhelp.com/app/answers/detail/a_id/86208/~/bootloader-frequently-asked-questions)

Step 3: Kernel
>第三步：内核

Android kernel start similar way as desktop linux kernel starts, as kernel launch it start setup cache, protected memory, scheduling, loads drivers. When kernel finish system setup first thing it look for “init” in system files and launch root process or first process of system.  
>Android内核的启动和桌面Linux的内核启动相似，当内核被加载后它开始逐步设置缓存、保护内存、设置计划、加载驱动。当内核完成系统设置后，做的第一件事情就是查找系统文件中的init并且加载root进程或者系统的第一个进程。

Step 4: init process
>第四步：init进程（初始化进程）

init it very first process, we can say it is root process or grandmother of all processes. init process has two responsibilities 1. mount directories like /sys, /dev, /proc and 2. run init.rc script.
>init是最优先的进程，我们可以称之为root进程或者所有进程的祖进程（祖母进程）。init进程有两个责任：1.加载像/sys, /dev, /proc这样的目录。2.执行init.rc脚本。

 init process can be found at init : <android source>/system/core/init
 >init进程可以在如下目录找到：<Android源代码>/system/core/init

init.rc file can be found in source tree at <android source>/system/core/rootdir/init.rc
>init.rc文件可以在代码树种这一位置找到：<Android源代码>/system/core/rootdir/init.rc

readme.txt file can be found in source tree at <andorid source>/system/core/init/readme.txt
>readme.txt文件可以在代码树的这个位置找到：<Android源代码>/system/core/init/readme.txt

Android has specific format and rules for init.rc files. In Android we call it as “Android Init Language” 
>对于init.rc文件来说Android有特殊的格式和规则。在Android中我们称之为“Android初始化语言（Android Init Language）”。

The Android Init Language consists of four broad classes of statements,which are Actions, Commands, Services, and Options.
>Android初始化语言有四个类的语句，它们分别是动作（Actions），命令（Commands），服务（Services）和选项（Options）。

Action : Actions are named sequences of commands.  Actions have a trigger which is used to determine when the action should occur.
>动作：动作是一个命名了的命令序列。动作有一个触发器用来决定什么时候这个动作应该发生。

>写法：
`Syntax 
on <trigger>
   <command>
   <command>
   <command>
   `

Service :  Services are programs which init launches and (optionally) restarts when they exit. 
>服务：服务是一个init加载的可选存在则重启的软件。` Syntax

service <name> <pathname> [ <argument> ]*

   <option>

   <option>

   ...
`

Options : Options are modifiers to services.  They affect how and when init runs the service.
>选项：选项是服务的修饰符。它们影响init怎样和在什么时候启动对应的服务。

Let’s take a look of default init.rc file. Here I have listed only major events and services.
>让我们看一下缺省的init.rc文件。这里我只列出主要的事件和服务。

Action / Service	Description
on early-init	Set init and its forked children's oom_adj.
Set the security context for the init process.
on init	setup the global environment
Create cgroup mount point for cpu accounting
and many
on fs	mount mtd partitions
on post-fs	change permissions of system directories
on post-fs-data	change permission of /data folders and sub folders
on boot	basic network init ,Memory Management ,etc
service servicemanager	start system manager to manage all native services like location, audio, shared preference etc..
service zygote	start zygote as app_process

At this stage you can see “Android” logo on device screen.

Step 5: Zygote and Dalvik

In a Java, We know that separate Virtual Machine(VMs) instance will popup in memory for separate per app, In case of Android app should launch as quick as possible, If Android os launch different instance of Dalvik VM for every app then it consume lots of memory and time. so, to overcome this problem Android OS as system named “Zygote”. Zygote enable shared code across Dalvik VM, lower memory footprint and minimal startup time. Zygote is a VM process that starts at system boot time as we know in previous step. Zygote preloads and initialize core library classes.  Normally there core classes are read-only and part of Android SDK or Core frameworks. In Java VM each instance has it’s own copy of core library class files and heap objects. 

Zygote loading process

1. Load ZygoteInit class, 
Source Code :<Android Source> /frameworks/base/core/java/com/android/internal/os/ZygoteInit.java
2. registerZygoteSocket() -  Registers a server socket for zygote command connections
3. preloadClasses() - “preloaded-classes” is simple text file contains list of classes that need to be preloaded, you cna find “preloaded-classes” file at <Android Source>/frameworks/base
4. preloadResources() - preloadReaources means native themes and layouts, everything that include android.R file will be load using this method.

At this time you can see bootanimation

Step 6: System  Service or Services

After complete above steps, runtime request Zygote to launch system servers. System Servers are written in native and java both, System servers we can consider as process, The same system server is available as System Services in Android SDK. System server contain all system services. 

Zygote fork new process to launch system services. You can find source code in ZygoteInit class and “startSystemServer” method.

Core Services:
1.     Starting Power Manager
2.     Creating Activity Manager
3.     Starting Telephony Registry
4.     Starting Package Manager
5.     Set Activity Manager Service as System Process
6.     Starting Context Manager
7.     Starting System Context Providers
8.     Starting Battery Service
9.     Starting Alarm Manager
10.   Starting Sensor Service
11.   Starting Window Manager
12.   Starting Bluetooth Service
13.   Starting Mount Service

Other services
1.    Starting Status Bar Service
2.     Starting Hardware Service
3.     Starting NetStat Service
4.     Starting Connectivity Service
5.     Starting Notification Manager
6.     Starting DeviceStorageMonitor Service
7.     Starting Location Manager
8.     Starting Search Service
9.     Starting Clipboard Service
10.   Starting Checkin Service
11.   Starting Wallpaper Service
12.   Starting Audio Service
13.   Starting HeadsetObserver
14.   Starting AdbSettingsObserver
 
Step 7 : Boot Completed

Once System Services up and running in memory, Android has completed booting process, At this time “ACTION_BOOT_COMPLETED” standard broadcast action will fire.
