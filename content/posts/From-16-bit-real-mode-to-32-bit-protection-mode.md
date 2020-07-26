---
title: "Change! From 16 bit real mode to 32-bit protection mode"
date: 2019-12-27T18:56:05+08:00
categories: ["OS"]
tags: ["Linux kernel"]
draft: false
---

## 回顾

从上文得知，在BIOS将Linux的磁盘引导程序`bootsect`加载到`0x07C00`之后，bootsect开始执行，其先是将自己移动到了`0x90000`处，然后设置了段寄存器ds、es、ss，后将setup、system程序加载至了指定位置，并确认了根设备号，最终通过段间跳转指令将CPU控制权交给了setup程序。

至此，操作系统的内核程序已经加载完成，但是计算机依旧运行在16位的实模式下，也就意味着只能利用20根地址总线(即`0 ~ 19`号地址线)，寻址空间仅1MB，也就是寻址范围为`0 ~ 0xFFFFF`。实模式下的特征是在1MB寻址空间内可以直接软件访问BIOS及周边硬件，但是没有硬件支持的分页机制和实时多任务概念。对于一个现代操作系统来说，这显然是不合适的。因此，从setup开始，做的至关重要的一件事情就是从实模式转变到保护模式下，成为一个真正的现代操作系统。

## 一、从BIOS中获取系统数据

setup做的第一件事就是从BIOS中获取系统数据，并将其保存到`0x90000 - 0x901FF`的位置。`0x90000`是bootsect的始址，并不超过`0x90200`，但是由于bootsect已经完成了任务，所以这段空间可以直接覆盖掉。

先是读取光标位置，通过BIOS中断`0x10`的`0x03`号功能来实现，代码如下

```shell
! ok, the read went well so we get current cursor position and save it for
! posterity.

    mov ax,#INITSEG ! this is done in bootsect already, but...
    mov ds,ax
    mov ah,#0x03    ! read cursor pos
    xor bh,bh
    int 0x10        ! save it in known place, con_init fetches
    mov [0],dx      ! it from 0x90000.
```

该功能号的入口参数为页号码，通过寄存器`bx`的高八位`bh`来传递，这里传入的是0，即通过`xor`运算将`bh`寄存器清零。

返回的参数包括

+ `ch`，扫描开始线；
+ `cl`，扫描结束线；
+ `dh`，行号(`0x00`是顶端)；
+ `dl`，列号(`0x00`是左边)。

`dx`寄存器总共两个字节，从`0x90000`开始保存，即占用`0x90000`和`0x90001`两个字节。

接下来获取拓展内存大小(即RAM中高于1MB的部分)，调用`0x15`中断的`0x88`功能号实现，代码如下

```shell
! Get memory size (extended mem, kB)

    mov ah,#0x88
    int 0x15
    mov [2],ax
```

返回值保存在`ax`寄存器中，共两个字节，保存在`0x90002`和`0x90003`中。

接下来读取显卡数据，通过`0x10`中断的`0x0f`功能实现，代码如下

```shell
! Get video-card data:

    mov ah,#0x0f
    int 0x10
    mov [4],bx      ! bh = display page
    mov [6],ax      ! al = video mode, ah = window width
```

返回参数为

+ `ah`，字符列数；
+ `al`，显示模式；
+ `bh`，当前显示页。

然后分别保存在偏移为`4`和`6`的位置。

接下来检查显示方式并取参数，分别通过`0x10`中断的`0x12`及`0x10`功能来实现，代码如下

```shell
! check for EGA/VGA and some config parameters

    mov ah,#0x12
    mov bl,#0x10
    int 0x10
    mov [8],ax
    mov [10],bx
    mov [12],cx
```

返回参数为

+ `bh`，显示状态(`0x00`-彩色模式, I/O端口 = `0x3dX`；`0x01`-单色模式, I/O端口=`0x3bX`)；
+ `bl`，安装的显示内存；
+ `cx`，显示卡特性参数。

然后分别保存在偏移为`8`, `10`, `12`的位置。

接着读取第一个硬盘的信息。需要注意的是，第一个硬盘参数表的首地址是中断向量`0x41`的向量值，该参数表的长度为16字节，保存的地址始址为`0x90080`，连续16个字节(`0x10`)。代码如下

```shell
! Get hd0 data

    mov ax,#0x0000
    mov ds,ax
    lds si,[4*0x41]
    mov ax,#INITSEG
    mov es,ax
    mov di,#0x0080
    mov cx,#0x10
    rep
    movsb
```

和前面不一样，这里使用`es:di`来指向传输的目的地址，而`ds:si`则指向参数表的地址，即源地址。

接下来要读取第二个硬盘的信息，代码逻辑和上述一致，其参数表的地址是中断向量`0x46`的地址值，由于第一个硬盘参数表刚好保存到`0x9008F`的位置，那么第二个硬盘的参数表就是从`0x90090`开始，连续16个字节(`0x10`)，代码如下

```shell
! Get hd1 data

    mov ax,#0x0000
    mov ds,ax
    lds si,[4*0x46]
    mov ax,#INITSEG
    mov es,ax
    mov di,#0x0090
    mov cx,#0x10
    rep
    movsb
```

最后要做的是检查系统是否存在第二个硬盘，如果不存在则将上述保存的第二个硬盘参数表清零，代码如下

```shell
! Check that there IS a hd1 :-)

    mov	ax,#0x01500
    mov	dl,#0x81
    int	0x13
    jc	no_disk1
    cmp	ah,#3
    je	is_disk1
no_disk1:
    mov	ax,#INITSEG
    mov	es,ax
    mov	di,#0x0090
    mov	cx,#0x10
    mov	ax,#0x00
    rep
    stosb
```

这个过程通过调用中断`0x13`的`0x15`号功能来实现。入口参数为`dl=驱动器号`，其中`0x8X`表示硬盘：`0x80`表示第一个硬盘、`0x81`表示第二个硬盘，那么自然这里必然是`0x81`。其出口参数为`ah=类型码`，`00`表示不存在此盘，并将`CF`置位；`01`表示软驱，没有`change-line`支持；`02`表示软驱或其它可移动设备，有`change-line`支持；`03`表示硬盘。

通过`jc`指令检查`CF`是否置位，如果置位即不存在第二个硬盘，那么就跳转至`no_disk1`处清零第二个参数表。如果存在第二个硬盘，那么`jc`指令自然不满足则执行`cmp`指令判断设备是否为硬盘，如果是则将标志寄存器`ZF`置位(即`ah == 03`)。再通过`je`指令判断`ZF`是否置位，如果置位那么代表设备为硬盘，那么就跳转至`is_disk1`处继续执行。即

```shell
is_disk1:
```

## 二、关中断！并将system移动至0x00000处

`is_disk1`第一句代码就是关中断，如下

```shell
is_disk1:

! now we want to move to protected mode ...

    cli         ! no interrupts allowed !
```

`cli`指令将CPU标志寄存器中中断允许标志`IF`置零，即不允许中断。关中断是16位实模式进入32位保护模式的标志性动作，这意味着接下来就可以废除16位实模式下的中断向量表，并初步打开32位寻址空间、建立保护模式下的中断响应机制等，这些都是与32位保护模式相配套的。


作为转变的开始，已经关闭了中断，那么接下来系统将不会响应中断，以便一心一意向保护模式转变。现在开始将system移动至内存始址处，代码如下

```shell
! first we move the system to it's rightful place

    mov	ax,#0x0000
    cld             ! 'direction'=0, movs moves forward
do_move:
    mov	es,ax       ! destination segment
    add	ax,#0x1000
    cmp	ax,#0x9000
    jz	end_move
    mov	ds,ax       ! source segment
    sub	di,di
    sub	si,si
    mov cx,#0x8000
    rep
    movsw
    jmp	do_move
```

其中`es:di`指向目的地址`0x0000:0x0`处，`ds:si`指向源地址`0x10000:0x0`处，由于起初假设system模块不会超过`0x80000`，即`512KB`，那么就不会超过`0x90000`,即system最初不会覆盖bootsect。那么这段程序就是将`[0x10000, 0x10000 + 512KB)`的内存数据块移动`[0x00000, 0x00000 + 512KB)`处，移动的数据块长度为`0x8000`节，即`512KB`。也就是说将每个源地址字节向内存低端移动`0x10000`个位置最终到达目标位置，上述汇编代码可以用如下伪码描述

```shell
ax = 0x0000
while truees = ax
    ax += 0x1000
    ds = ax
    di = si = 0x0
    ds:si to es:di, moving one seg continuously, i.e. 64KB
    if ax + 0x1000 == 0x9000
        break
```

再进一步地说明，上述伪码中各参数变动情况如下所示(注：下述`while`循环用来解释上述中的`ds:si to es:di, moving one seg continuously, i.e. 64KB`)

```shell
The first cycle is as follows:
  es = 0x0000, ax = 0x1000, ds = 0x1000, di = 0x0, si = 0x0, cx = 0x8000
    while cx != 0x0000
      ds:si to es:di, one word at a time, i.e. movsw
      cx -= 0x0001

The second cycle is as follows:
  es = 0x1000, ax = 0x2000, ds = 0x2000, di = 0x0, si = 0x0, cx = 0x8000
    while cx != 0x0000
      ds:si to es:di, one word at a time, i.e. movsw
      cx -= 0x0001
...
```

这就很容易理解了。那么该段汇编就可以整体上用如下伪码描述

```shell
ax = 0x0000
while truees = ax
    ax += 0x1000
    ds = ax
    di = si = 0x0
    cx = 0x8000
    while cx != 0x0000
        ds:si to es:di, one word at a time, i.e. movsw
        cx -= 0x0001
    if ax + 0x1000 == 0x9000
        break
```

至此，就完成了对system模块的移动。对system模块的移动起到了如下的效果

+ 废除了BIOS中断向量表，等同于废除了BIOS所提供的实模式下的中断服务程序；
+ 回收已经无用的内存空间，因为要向保护模式转变，BIOS中断向量表所占空间自然无用，应当回收；
+ 让system模块占据内存物理地址最天然、有利的位置。

## 三、设置IDT与GDT

IDT，即中断描述符表(Interrupt Descriptor Table)，其保存的是所有中断服务程序的入口地址，就类似于实模式下的中断向量表，这也是构建保护模式下中断机制的开始。实模式下终端向量表的始址在`0x00000`处，这个位置是固定的，而保护模式下IDT的位置是不固定的，可以在任何位置，那么为了找到IDT就要将IDT的入口地址保存在一个寄存器当中，这个寄存器就是IDTR(Interrupt Descriptor Table Register, IDT基址寄存器)，该寄存器共48位，即3个字长，第一个字是限长，剩余的两个字是基地址，结构如下

```shell
47----15-----0
 base | limit 
```

将IDT入口地址传递给IDTR的过程就是设置IDTR，代码如下

```shell
    lidt    idt_48      ! load idt with 0,0
```

那么这里标号`idt_48`对应的就是IDT的入口地址，`idt_48`的内容如下

```shell
idt_48:
    .word	0           ! idt limit=0
    .word	0,0         ! idt base=0L
```

按照上面的解释，这就很容易理解了。第一个字为限长，这里为0，剩余两个字为基址，也是0，即基址就是`0x00000`处。这里基址用两个字描述的初衷在于第三个字是段基址，第二个字就是偏移，那么整个地址`0x00000`就是`0x00000 = 0x0000 * 16 + 0x0000`。即这里的IDT依旧是放在内存开始处。下面的GDTR结构的解读也是同理。

与实模式不同的是，保护模式下的段寻址是通过GDT(Global Descriptor Table, 全局描述符表)完成的，GDT中存放的是段寄存器内容，其数据结构为数组。GDT在操作系统进程切换中意义重大，其中存放了每个任务的LDT(Local Descriptor Table, 局部描述符表)地址和TSS(Task Structure Segment, 任务状态段)地址，以完成进程中各段的寻址、现场保护与现场恢复。

GDT的初始内容已经写在了setup程序中，如下

```shell
gdt:
    .word   0,0,0,0     ! dummy

    .word   0x07FF      ! 8Mb - limit=2047 (2048*4096=8Mb)
    .word   0x0000      ! base address=0
    .word   0x9A00      ! code read/exec
    .word   0x00C0      ! granularity=4096, 386

    .word   0x07FF      ! 8Mb - limit=2047 (2048*4096=8Mb)
    .word   0x0000      ! base address=0
    .word   0x9200      ! data read/write
    .word   0x00C0      ! granularity=4096, 386
```

可以整理为如下一张表

```shell
 index |    GDT
------------------
   2   | 00C0 9200
       | 0000 07FF
------------------
   1   | 00C0 9A00
       | 0000 07FF
------------------
   0   | 0000 0000
       | 0000 0000
```

其中每个GDT表项共64位，即8字节/4字，结构如下

```shell
31------------------------------16-15-----------------------0
             Base 0:15            |         Limit 0:15
63--------56-55---52-51---------48-47---------40-39--------32
 Base 24:31 | Flags | Limit 16:19 | Access Byte | Base 16:23
```

Access Byte的结构如下

```shell
8---7-------5---4----3----2----1---0
 Pr | Privl | 1 | Ex | Dc | RW | Ac
```

Flags的结构如下

```shell
8---7----6---5--4
 Gr | Sz | 0 | 0
```

特别说明的`Privl`为特权级，如果为`00`表示内核特权级，如果为`11`，则表明用户特权级；`Gr`标志位为颗粒度标志，如果为1，那么段限长的单位为4KB，如果为0，那么就是1B。

显而易见，GDT将段基址与段限长拆分保存在不连续的bit位中。这是为了兼容286架构。那么现在GDT表项就很容易理解了。

以第一项GDT为例，其内容为

```shell
00C0 9A00
0000 07FF
```

第`0-15bit`与`48-51bit`构成段限长，内容为

```shell
007FF
```

将其转换为10进制就是2047bit，即2KB；再看一下颗粒度标志，其包含在最后一个字节，即

```shell
00C0    // 00000000 11000000
```

可见，颗粒度标志位的值为1，那么也就意味着段限长实际上为`0x007FF * 4KB = 8MB`。确定了段限长，我们再确定一下段基址，`16-31bit`、`32-39bit`、`56-63bit`构成了段基址，那么合起来就是

```shell
0x0000000
```

即内存始址。

现在我们知道了GDT的内容与含义，那么设置GDT就是将GDT表的始址保存在GDTR(Global Descriptor Table Register, GDT基地址寄存器)中，通过下述指令完成

```shell
    lgdt    gdt_48      ! load gdt with whatever appropriate
```

也就是GDT的始址信息保存在`gdt_48`标号处，其内容为

```shell
gdt_48:
    .word   0x800       ! gdt limit=2048, 256 GDT entries
    .word   512+gdt,0x9 ! gdt base = 0X9xxxx
```

GDTR与IDTR的结构一致，那么也就是说第一个字`0x800`为限长，即十进制`2048bit 或 2KB`，由于每`8Byte`构成一个段描述符，所以GDT中共有256项；第三个字也就是GDT的基地址，即`0x9000`；第二个字当中，`512`为一扇区，也就是`0x00200`，`gdt`为setup程序中`GDT`表的偏移，那么`512+gdt`实际上就是指向了`0x9000`段中偏移为`gdt`的位置处。所以整个地址就可以计算为`0x9000 * 16 + (512 + gdt)`。

综上，总结一下，IDTR与GDTR分别说明了IDT与GDT的入口地址在哪(base)，也说明了IDT和GDT中最多有多少个表项(limit)。

## 四、打开A20，实现32位寻址

这是进入保护模式的关键，因为保护模式下必须突破16位寻址以实现32位寻址，就是通过打开A20地址线实现的。

IBM公司最初的PC机使用的是Intel 8088处理器。该微机中地址线只有20根(`A0-A19`)，当是RAM只有几百KB不到1MB，这20根地址线是完全够用的，所能寻址的最高地址为`0xffff:0xffff`，即1MB处(`0xfffff = 0xffff * 16 + 0xffff`)，那么对于超过1MB的寻址地址将环绕到内存始址处(注意这个细节，可以利用这个特点来检测A20地址线是否打开)。当1985年引入AT机时使用的Intel 80286处理器具有24根地址线，最高寻址16MB，并且有一个与8088完全兼容的实模式运行方式，但是，在寻址值超过1MB时，80286却无法像8088那样实现地址寻址环绕。为了完全实现兼容，IBM最终使用了一个被称之为A20的信号，当A20为0时，那么比特20及以上的地址线都会被清除，从而实现兼容。

机器启动时，A20是默认关闭的，所以只能实现实模式下1MB寻址，那么要进入保护模式就需要打开A20，实现32位寻址。代码如下

```shell
! that was painless, now we enable A20

    call  empty_8042
    mov   al,#0xD1      ! command write
    out   #0x64,al
    call  empty_8042
    mov   al,#0xDF      ! A20 on
    out   #0x60,al
    call  empty_8042
```

选通A20之后，Linux 0.11就可以实现32位寻址，其线性寻址空间就是4GB!

## 五、对8259A中断控制器进行重编程

由于CPU在保护模式下，`int 0x00 - int 0x1F`被Intel保留为内部不可屏蔽中断和异常中断。如果不对8259A进行重新编程的话，那么也就意味着`int 0x00 - int 0x1F`会被保护模式下的Intel保留中断所覆盖掉，因此，必须重新编程，其本质就是重新建立映射关系。代码如下

```shell
! well, that went ok, I hope. Now we have to reprogram the interrupts :-(
! we put them right after the intel-reserved hardware interrupts, at
! int 0x20-0x2F. There they won't mess up anything. Sadly IBM really
! messed this up with the original PC, and they haven't been able to
! rectify it afterwards. Thus the bios puts interrupts at 0x08-0x0f,
! which is used for the internal hardware interrupts as well. We just
! have to reprogram the 8259's, and it isn't fun.

    mov al,#0x11        ! initialization sequence
    out	#0x20,al        ! send it to 8259A-1
    .word   0x00eb,0x00eb       ! jmp $+2, jmp $+2
    out	#0xA0,al        ! and to 8259A-2
    .word   0x00eb,0x00eb
    mov	al,#0x20        ! start of hardware int's (0x20)
    out	#0x21,al
    .word   0x00eb,0x00eb
    mov	al,#0x28        ! start of hardware int's 2 (0x28)
    out	#0xA1,al
    .word   0x00eb,0x00eb
    mov	al,#0x04        ! 8259-1 is master
    out	#0x21,al
    .word   0x00eb,0x00eb
    mov	al,#0x02        ! 8259-2 is slave
    out	#0xA1,al
    .word   0x00eb,0x00eb
    mov	al,#0x01        ! 8086 mode for both
    out	#0x21,al
    .word   0x00eb,0x00eb
    out	#0xA1,al
    .word   0x00eb,0x00eb
    mov	al,#0xFF        ! mask off all interrupts for now
    out	#0x21,al
    .word   0x00eb,0x00eb
    out	#0xA1,al
```

重新编程前后的中断请求号与中断号之间的对应关系如下

```shell
    before       |        after
-----------------------------------
IRQ0  -> 0X00    |    IRQ0  -> 0X20
IRQ1  -> 0X01    |    IRQ1  -> 0X21
...
IRQ14 -> 0X0E    |    IRQ14 -> 0X2E
IRQ15 -> 0X0F    |    IRQ15 -> 0X2F
```

## 六、进入32位保护模式

setup执行以来，从关中断到对8259A重编程，都是在为进入保护模式做准备，下面两行代码直接设置CPU进入32位保护模式运行。

```shell
    mov	ax,#0x0001  ! protected mode (PE) bit
    lmsw    ax      ! This is it!
```

`lmsw`指令的作用是加载机器状态字，即load Machine Status Word，也称之为控制寄存器`CR0`，共32位，存放系统控制标志，其第0位为`PE(Protected Mode)`，若为1则表明设置处理器工作方式为保护模式。

`#0x0001`就是总共16位，最低位为1，上述代码加在`CR0`之后就可以通过ax寄存器将`PE`位置1，自此，CPU正式进入保护模式。

## 七、跳转！进入system执行head.s

CPU进入保护模式工作后，最明显的特征就是要根据GDT决定后续执行程序所在段(即，要执行的程序在哪)。setup向head.s跳转也是同理，如下

```shell
    jmpi    0,8     ! jmp offset 0 of segment 8 (cs)
```

通过段间跳转指令`jmpi`跳转至`8:0`处执行，这里的8如何理解呢？

如果将8转为二进制就容易了，即`1000`，这里的每个比特位都有含义，第一第二位表示内核特权级，前面说过，`00`表示GDT表项为内核特权级，`11`则为用户特权级；第三位用于区分GDT和LDT，若为`0`则表示GDT，`1`表示LDT；第四位`1`表示GDT的第二项，即索引为1的那一项，我们前面刚好分析过这一项。那么上述代码就是跳转到以第二项GDT的base为段地址，以0位偏移的内存处。上面分析过，第二项GDT的base为`0x0000000`，即内存始址，那么再加上偏移0还是内存始址，这里是什么呢？就是system模块的head.s程序。

本文完！