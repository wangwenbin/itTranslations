#In Depth : Android Boot Sequence / Process
>深度解析：Android启动序列与进程

================================================================
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


Here I am explaining Android boot process. I hope you will find answer of above questions.




Android is linux based open source operating system, x86 (x86 is a series of computer microprocessor instruction set architectures based on the Intel 8086 CPU.) is most likely system where linux kernel is deployed however all Android devices are running on ARM process (ARM (formerly Advanced RISC Machine, which was formerly Acorn RISC Machine)) except Intel’s Xolo device (http://xolo.in/xolo-x900-features). Xolo comes with Atom 1.6 GHz x86 processor. Android boot sequence or I can say embedded device or ARM based linux has minor difference compare to desktop version.  In this article I am going to explain boot sequence for Android only. Inside the linux boot process is good article for desktop based linux boot sequence.

Android device execute following steps when you press power switch

Android Boot Sequence / Process

Step 1 : Power On and System Startup 

When power start Boot ROM code start execution from pre defined location which is hardwired on ROM. It load Bootloader into RAM and start execution

Step 2 : Bootloader

Bootloader is small program which runs before Android operating system running. Bootloader is first program to run so It is specific for board and processor. Device manufacturer either use popular bootloaders like redboot,uboot, qi bootloader or they develop own bootloaders, It’s not part of Android Operating System. bootloader is the place where OEMs and Carriers put there locks and restrictions. 

Bootloader perform execution in two stages, first stage It to detect external RAM and load program which helps in second stage, In second stage bootloader setup network, memory, etc. which requires to run kernel, bootloader is able to provide configuration parameters or inputs to the kernel for specific purpose.  

Android bootloader can be found at 

<Android Source>\bootable\bootloader\legacy\usbloaderlegacy loader contain two important files that need to address here.

1. init.s - Initializes stacks, zeros the BSS segments, call _main() in main.c
2. main.c - Initializes hardware (clocks, board, keypad, console), creates Linux tags

Refer this link to know more about Android bootloader :
https://motorola-global-portal.custhelp.com/app/answers/detail/a_id/86208/~/bootloader-frequently-asked-questions

Step 3: Kernel

Android kernel start similar way as desktop linux kernel starts, as kernel launch it start setup cache, protected memory, scheduling, loads drivers. When kernel finish system setup first thing it look for “init” in system files and launch root process or first process of system.  

Step 4: init process

init it very first process, we can say it is root process or grandmother of all processes. init process has two responsibilities 1. mount directories like /sys, /dev, /proc and 2. run init.rc script.


 init process can be found at init : <android source>/system/core/init
init.rc file can be found in source tree at <android source>/system/core/rootdir/init.rc
readme.txt file can be found in source tree at <andorid source>/system/core/init/readme.txt


Android has specific format and rules for init.rc files. In Android we call it as “Android Init Language” 

The Android Init Language consists of four broad classes of statements,which are Actions, Commands, Services, and Options.

Action : Actions are named sequences of commands.  Actions have a trigger which is used to determine when the action should occur.

Syntax 
on <trigger>
   <command>
   <command>
   <command>

Service :  Services are programs which init launches and (optionally) restarts when they exit.  Syntax

service <name> <pathname> [ <argument> ]*
   <option>
   <option>
   ...

Options : Options are modifiers to services.  They affect how and when init runs the service.

Let’s take a look of default init.rc file. Here I have listed only major events and services.


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
