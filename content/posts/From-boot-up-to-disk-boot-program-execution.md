---
title: "From power up to disk boot program execution"
date: 2019-09-13T16:01:05+08:00
categories: ["OS"]
tags: ["Linux kernel"]
draft: false
---

本系列文章基于Linux 0.11内核源代码，共计14000+行左右，基本上都是Linux系统的精髓；而最新版Linux内核已经发展到了5.2.14已上千万行，是不利于学习Linux内核的。

## 引言

我们知道，计算机的运行主要是以RAM为对象的CPU运算，即程序执行的前提是被调入RAM当中。操作系统作为底层软件，理所当然地需要被加载至RAM中通过CPU运算构建整个操作系统体系以接管、分配和调度系统资源。

由于CPU无法直接与外设进行数据交互，那么位于软盘/硬盘中的操作系统程序就必须首先调入RAM中。但是现在RAM中空空如也，既没有数据，也没有指令，那么是谁通过CPU将操作系统程序调入RAM中的呢？

答案是`BIOS`！

## 一、BIOS的启动原理

既然是BIOS将操作系统程序调入RAM当中，那么问题来了，BIOS又是如何启动的？

答案就在于`0xFFFF0`地址处！

PC机加电伊始，基于80x86结构的CPU将会自动进入实模式(实模式下CPU的地址总线为20根，其寻址能力为2<sup>20</sup>即1MB，这是因为80x86CPU是采取一种用两个16位地址合成一个20位地址的方法给出物理地址的，即物理地址=段地址×16+偏移地址)，并且CPU将会强制将CS寄存器的值设置为`0xF000`、IP寄存器的值设置为`0xFFF0`，即CPU将会指向`0xFFFF0`地址处，这个地址通常是ROM-BIOS的入口地址，即BIOS程序的第一条指令就在这个位置。等等，不是说RAM中没有任何数据和指令吗？那么CPU指向这个位置不也是空的吗？有什么意义？要注意的是CPU实际指向的是由主存地址空间(RAM)、显存地址空间(RAM)、显卡BIOS地址空间(ROM)、网卡BIOS地址空间(ROM)、系统BIOS地址空间(ROM)等物理存储器编址而成的逻辑存储器，被称为内存地址空间(以下简称内存)。所以CPU此时指向的地址是装有系统BIOS程序的构成内存地址空间的ROM地址空间，而非空无一物的主存地址空间。BIOS程序被固化在ROM芯片上，由于ROM的非易失性使得其内部并非空无一物。80x86 PC机内存地址空间图示如下

![80x86PC机内存地址空间示例](https://feily.tech/pic/images/article/memory-address-space.png)

言归正传，那么此刻CPU已经指向`0xFFFF0`的位置，意味着BIOS开始启动，紧接着将是BIOS程序在CPU中执行，屏幕上会显示出显卡信息、内存信息等，这说明CPU在进行自检，期间一项非常重要的工作就是BIOS在内存中构建中断向量表和中断处理程序。BIOS会在内存(主存RAM)的开始位置(`0x00000`)用1KB的内存空间(`0x00000` ～ `0x003FF`)构建BIOS中断向量表，在紧挨着中断向量表的位置用256字节内存空间构建BIOS数据区(`0x00400` ～ `0x004FF`)，并在大约57KB以后的位置(`0x0E05B`)加载大约8KB的与中断向量表对应的若干中断处理程序。如下图所示

![构建BIOS之后的内存布局](https://feily.tech/pic/images/article/ram-bios.png)

中断向量表中有256个中断向量，每个中断向量占4字节，刚好是`1KB(256 × 4Byte = 1KB)`。其中两个字节是该中断向量对应的处理程序的CS的值，剩下的两个字节是对应IP的值(所以段地址和偏移地址都是16位，可以用来合成20位物理地址，达到实模式下1MB寻址范围)。

BIOS构建完中断服务体系之后，CPU会接收到一个`int 0x19`中断(该中断的作用是把可启动设备的第一个扇区的程序加载至内存的指定位置)，然后CPU在中断向量表中查找该中断对应的中断处理程序地址，随后执行该中断的中断处理程序将启动设备(这里是软盘)的第一个扇区中的程序加载到内存的`0x07C00(31KB)`处。

那么软盘第一个扇区中的程序是什么呢？就是Linux的磁盘引导程序`bootsect`。

## 二、bootsect对内存的规划

bootsect是完全用汇编语言写成的，其首先要对实模式下可寻址(最大范围`1MB`,即`0x00000 ～ 0xFFFFF`)的内存进行规划。代码如下所示

```shell
SYSSIZE  = 0x3000
SETUPLEN = 4                    ! nr of setup-sectors
BOOTSEG  = 0x07c0               ! original address of boot-sector
INITSEG  = 0x9000               ! we move boot here - out of the way
SETUPSEG = 0x9020               ! setup starts here
SYSSEG   = 0x1000               ! system loaded at 0x10000 (65536).
ENDSEG   = SYSSEG + SYSSIZE     ! where to stop loading
...
```

其中`SYSSIZE`是system模块的节数，由于16字节为1节，所以system模块共`0x30000Byte`(对应的十进制为`196608Byte`)，即`192KB (196608 / 1024)`;`SETUPLEN`为setup程序的扇区数，即4个扇区，由于bootsect为第1扇区，那么setup必然就是第2扇区到第5扇区;`BOOTSEG`为bootsect程序段的初始位置，也就是BIOS加在到内存中的位置，即`0x07c0`;`INITSEG`为bootsect将要移动的新位置，为`0x9000`;`SETUPSEG`为setup程序的始址，由于bootsect大小为1个扇区，且新始址为`0x9000`，也就是在`576KB`处(`0x9000`节，即`0x90000Byte`，转化为十进制为`589824Byte`，即`589824 / 1024 = 576KB`),由于第一扇区大小为`512Byte`,即`0.5KB`，那么bootsect的末端就在`576.5KB`处，即十进制`590336Byte`处，对应的十六进制为`0x90200Byte`，也就是`0x9020`(节)处，也就是setup程序紧跟着bootsect，可见Linus对内存的精妙控制;`SYSSEG`为system模块的段始址，即地址`0x10000`处，也就是64KB(对应十进制`65536KB / 1024`)处;`ENDSEG`自然为system模块的地址末端。

反映到实模式下的内存上，如下图所示

![bootsect设置的内存布局](https://feily.tech/pic/images/article/memory-bootsect.png)

接下来bootsect将会按照上述内存布局进行相应的操作。

## 三、bootsect对自身的复制

bootsect先将自身的全部内容(1个扇区)从内存`0x07C00`处(段地址BOOTSEG)复制到内存`0x90000`处(段地址INITSEG),对应的汇编代码如下

```shell
entry _start
_start:
    mov ax,#BOOTSEG
    mov ds,ax
    mov ax,#INITSEG
    mov es,ax
    mov cx,#256
    sub si,si
    sub di,di
    rep
    movw
```

数据段寄存器ds与源变址寄存器si构成bootsect的源地址`0x07c00`，即`ds:si = 0x07c0:0x0000`;而附加段寄存器es与目的变址寄存器di构成了bootsect的目的地址`0x9000`，即`es:di = 0x9000:0x0000`。通过计数寄存器cx控制循环的次数，这里`cx = 256`,由于cx为16位寄存器，即这里代表256个字，也就是`512Byte`，刚好是一个扇区的数目。然后通过`rep movw`循环进行复制操作。

这段代码执行完毕后，整个内存空间将含有两段bootsect的数据，即以BOOTSEG开始的`512Byte`数据和以INITSEG开始的`512Byte`数据。接下来通过执行段间跳转指令跳转到INITSEG段的go代码块处继续执行bootsect，如下

```shell
	jmpi	go,INITSEG
```

## 四、设置段寄存器ds、es、ss

进入go代码块中，要设置ds、es、ss寄存器，代码如下

```shell
go:	mov	ax,cs
	mov	ds,ax
	mov	es,ax
! put stack at 0x9ff00.
	mov	ss,ax
	mov	sp,#0xFF00		! arbitrary value >>512
```

由于cs此时指向的是bootsect的段地址，即0x9000，那么上述代码就是将数据段寄存器ds、附加段寄存器es以及栈基址寄存器ss设置成与代码段寄存器cs相同的位置，然后将栈顶指针sp指向偏移地址`0xFF00`处。其内存映像如下所示

![对ds、es、ss、sp的设置](https://feily.tech/pic/images/article/memory-bootsect-ds-es-ss-sp.png)

至此，由于对ss、sp寄存器的设置，此后程序便可以基于栈进行复杂数据运算了。

## 五、加载setup程序

接下来bootsect需要将setup程序加载至SETUPSEG处，setup位于软盘/磁盘第2扇区到第5扇区连续四个扇区，共`2KB(512Byte * 4 = 2048Byte = 2KB)`,代码如下

```shell
! load the setup-sectors directly after the bootblock.
! Note that 'es' is already set up.

load_setup:
	mov	dx,#0x0000		! drive 0, head 0
	mov	cx,#0x0002		! sector 2, track 0
	mov	bx,#0x0200		! address = 512, in INITSEG
	mov	ax,#0x0200+SETUPLEN	! service 2, nr of sectors
	int	0x13			! read it
	jnc	ok_load_setup		! ok - continue
	mov	dx,#0x0000
	mov	ax,#0x0000		! reset the diskette
	int	0x13
	j	load_setup
```

与BIOS加载bootsect不同的是，bootsect加载setup用的是`int 0x13`中断而不是`int 0x19`中断，系统通过几个通用寄存器来给BIOS中断处理程序传参，含义如下

+ dx寄存器中，高8位dh为磁头号，低8位dl为驱动器号；
+ cx寄存器中，高8位ch为磁道(柱面)号的低8位，低8位cl为开始扇区；
+ bx寄存器，配合es段寄存器指向内存缓冲区，即SETUPSEG的位置；
+ ax寄存器中，高8位`ah = 0x02`代表的是读磁盘扇区到内存中；低8位al代表要读出的扇区数量。

根据上述解释，这段程序指明通过驱动器0(dl)的0号磁头(dh)对应盘面的0磁道(ch)第2扇区(cl)开始的连续4个扇区(al)读到内存中(ah)`es:bx`指向的位置`0x9000:0200`，即`0x90200`处。

BIOS对文件、磁盘等IO的操作是通过检测CF位来判断是否成功的，若`CF = 0`表示成功，`jnc`指令通过判断`CF = 0`来实现跳转，即读取成功则跳转至`ok_load_setup`处继续执行。代码如下

```shell
ok_load_setup:

! Get disk drive parameters, specifically nr of sectors/track

	mov	dl,#0x00
	mov	ax,#0x0800		! AH=8 is get drive parameters
	int	0x13
    seg cs
	mov	sectors,cx
	mov	ax,#INITSEG
	mov	es,ax
...
sectors:
	.word 0
```

加载setup成功之后，获取磁盘驱动器的参数，特别是每道扇区数量。

仍然是通过`int 0x13`中断实现的，其中dl代表驱动器号，这里是0号，ax的高8位`ah = 8`代表获取驱动器参数(上面`ah = 2`代表读磁盘扇区到内存)，然后调用`0x13`中断的处理程序获取数据，这里会返回ah = 0, al = 0, bl = 驱动器类型(AT/PS2),ch = 最大磁道号的低8位,cl = 每磁道最大扇区数(第0到第5位)最大磁道号高2位(第6到第7位),dh = 最大磁头数,dl = 驱动器数量。如果读取出错，则CF位置为1，同时ah为状态码。而`es:di`指向软驱磁盘参数表。

然后将获取到的每磁道扇区数保存起来，供确认根设备号时使用，即`mov sectors,cx`。由于上述`es:di`指向了软驱磁盘参数表，因此这里需要将es复位，即`mov es,ax`。

若setup加载失败，即`CF = 1`,那么不断重试！

加载成功后执行到ok_load_setup获取磁盘参数表之后，就要为加载system模块做准备了。

## 六、加载system模块

由于system模块多达240扇区，因此加载起来需要一定的时间，所以需要打印一些提示字符串(Loading system ...)告诉用户并未死机，代码如下

```shell
! Print some inane message

	mov	ah,#0x03		! read cursor pos
	xor	bh,bh
	int	0x10
	
	mov	cx,#24
	mov	bx,#0x0007		! page 0, attribute 7 (normal)
	mov	bp,#msg1
	mov	ax,#0x1301		! write string, move cursor
	int	0x10
...
msg1:
	.byte 13,10
	.ascii "Loading system ..."
	.byte 13,10,13,10
```

先看msg1段，可以算出来刚好是24个字符。

通过`int 0x10`中断的功能号为03H的子功能(ah = 0x03)先获取光标位置，以下参数将传递给`int 0x10`中断的功能号为13H的子功能(ax寄存器的高8位，即13)，如下

+ cx寄存器指明共24个字符即循环24次
+ bh指明页码，`bh = 0`，即第0页；bl为属性值，这里bl = 7；
+ `es:bp`指向要打印的字符串，即msg1。由于msg1在bootsect中，因此段地址es适用，bp为msg1的段内偏移。

然后调用`int 0x10`中断的功能号为13H的子功能打印字符串。

已经提示用户正在加载系统了，那么接下来就是正式加载system模块，如下

```shell
! ok, we've written the message, now
! we want to load the system (at 0x10000)

	mov	ax,#SYSSEG
	mov	es,ax		! segment of 0x010000
	call	read_it
...
```

bootsect加载system模块是通过read_it子程序来完成的，子程序的输入参数为es，即`es = SYSSEG`。read_it仍然是通过`int 0x13`中断来加载system模块的。为第6扇区开始的连续约240个扇区。

system加载完成之后，整个操作系统的内核代码都已经进入内存了，最后要做的一件事情就是再次确定一下根设备号。

## 七、确定根设备号

首先查看根设备号是否已经定义，如果已经定义(即!=0)那么将检查过的根设备号保存起来，即`mov root_dev,ax`;否则根据BIOS报告的每磁道扇区数来确定使用`/dev/PS0(2,28)`还是`/dev/at0(2,8)`。这里两个设备文件的含义为，在Linux中软驱的主设备号是2，次设备号 = type × 4 + nr，其中nr为0 - 3，分别对应软驱A、B、C或D；type是软驱的类型，1.2MB的驱动器为2,1.44MB的驱动器为7。所以1.2MB的A驱动器的次设备号为2 × 4 + 0 = 8，所以`/dev/at0(2,8)`指的是1.2MB A驱动器；而1.44MB A驱动器的次设备号为7 × 4 + 0 = 28，所以`/dev/PS0(2,28)`指的是1.44MB A驱动器。

其次，设备号的计算公式为：设备号 = 主设备号 × 256 + 次设备号。那么1.44MB A驱动器的设备号就是2 × 256 + 28 = 540，对应的16进制就是0x021c；同理计算1.2MB A驱动器的设备号为0x0208。

```shell
! After that we check which root-device to use. If the device is
! defined (!= 0), nothing is done and the given device is used.
! Otherwise, either /dev/PS0 (2,28) or /dev/at0 (2,8), depending
! on the number of sectors that the BIOS reports currently.

	seg cs
	mov	ax,root_dev
	cmp	ax,#0
	jne	root_defined
	seg cs
	mov	bx,sectors
	mov	ax,#0x0208		! /dev/ps0 - 1.2Mb
	cmp	bx,#15
	je	root_defined
	mov	ax,#0x021c		! /dev/PS0 - 1.44Mb
	cmp	bx,#18
	je	root_defined
undef_root:
	jmp undef_root
root_defined:
	seg cs
	mov	root_dev,ax
...
root_dev:
	.word ROOT_DEV
```

先将上述获取到的每磁道扇区数传送给通用寄存器bx，如果`bx = 15`，即每磁道扇区数为15,那么说明就是1.2MB的驱动器,如果为18，那么就是1.44MB的软驱，然后跳转到`root_defined`中将通用寄存器ax中定义的根设备号保存起来。如果获取到的每道磁盘扇区数既不为15也不为18那么就会跳过`je`指令直接进入`undef_root`段中，进入死循环。

## 八.跳转至setup

以上bootsect的任务几乎全部完成了，最后需要执行的指令就是

```shell
! after that (everyting loaded), we jump to
! the setup-routine loaded directly after
! the bootblock:

	jmpi	0,SETUPSEG
```

即跳转到setup程序段的偏移地址为0处(也就是setup段首)继续执行，至此bootsect完成了CPU控制权的交接！