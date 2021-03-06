# 存储管理的功能

#### 地址转换

又称重定位即把逻辑地址转换成绝对地址

**静态重定位**

在程序装入内存时进行地址转换。

由装入程序执行，早期小型OS使用

**动态重定位**

在CPU执行程序时进行地址转换。

该功能依赖硬件地址转换机构

#### 主存储器空间的分配与去配

**分配**

进程装入主存时，存储管理软件进行具体的主存分配操作，并设置一个表格记录主存空间的分配情况

**去配**

当某个进程撤离或主动归还主存资源时，存储管理软件要收回它所占用的全部或者部分存储空间，调整主存分配表信息。

#### 主存储器空间的共享

**多个进程共享主存储器资源**

多道程序设计技术使若干个程序同时进入主存储器，各自占用一定数量的存储空间，共同使用一个主存储器。

**多个进程共享主存储器的某些区域**

若干个协作进程有共同的主存程序块或者主存数据块。

#### 存储保护

为避免主存中的多个进程相互干扰，必须对主存中的程序和数据进行保护。

* 私有主存区中的信息
  * 可读可写
* 公共区中的共享信息
  * 根据授权
* 非本进程信息
  * 不可读写

这一功能需要软硬件协同完成。CPU检查是否允许访问，不允许则产生地址保护异常，由OS进行相应处理。

#### 主存储器空间的扩充

**存储扩充**

把磁盘作为主存扩充，只把部分进程或进程部分内容装入内存

* 对换技术
  * 把部分不运行的进程调出
* 虚拟技术
  * 只调入进程的部分内容

这一工作需要软硬件写作完成

* 对换进程决定对换，硬件机构调入
* CPU处理到不在主存的地址，发出虚拟地址异常，OS将其调入，重执指令

