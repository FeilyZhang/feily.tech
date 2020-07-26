---
title: "Class file structure"
date: 2019-04-22T15:43:25+08:00
categories: ["Java"]
tags: ["JVM"]
draft: false
---

Class文件是一组以8位字节为基础单位的二进制流，各个数据项目严格按照顺序紧凑的排列在Class文件之中，中间没有任何分隔符，这使得整个Class文件中存储的内容几乎全部都是程序运行的必要数据，没有空隙存在。当遇到需要占用8为字节以上空间的数据项时，则会按照高位在前的方式分割成若干个8位字节进行存储。

根据Java虚拟机规范的规定，Class文件格式采用一种类似于C语言结构体的伪结构来存储，这种伪结构只有两种数据类型：无符号数和表。

无符号数属于基本数据类型，以u1、u2、u4、u8来分别代表1、2、4、8个字节的无符号数，无符号数可以描述数字、索引引用、数量值或者按照UTF-8编码构成的字符串值。

表是多个无符号数或其他表作为数据项构成的复合数据类型，所有表都习惯性地以“_info”结尾，表用于描述有层次关系的复合结构的数据，整个Class文件本质上就是一张表。Class文件由以下表项构成(需要注意的是，**数据类型只是指明了该名称表项占用的连续内存的多少**)

|类型|名称|数量|
|------|------|------|
|u4|magic|1|
|u2|minor_version|1|
|u2|major_version|1|
|u2|constant_pool_count|1|
|cp_info|constant_pool|constant_pool_count - 1|
|u2|access_flags|1|
|u2|this_class|1|
|u2|super_class|1|
|u2|interfaces_count|1|
|u2|interfaces|interfaces_count|
|u2|fields_count|1|
|field_info|fields|fields_count|
|u2|methods_count|1|
|method_info|methods|methods_count|
|u2|attributes_count|1|
|attribute_info|attributes|attributes_count|

无论是无符号数还是表，当需要描述同一类型但数量不定的多个数据时，经常会使用一个前置的容量计数器加若干个连续的数据项的形式，这时候称这一系列连续的某一类型的数据为某一类型的集合。

## 一、魔数与Class文件的版本

魔数的唯一作用是确定这个文件是否为一个能被虚拟机接受的Class文件。Java文件的魔数是十六进制的`CAFEBABE`，共占用四个字节，即名称为魔数的表项的类型为u4，那么从Class文件中开头连续数四个字节的数据就是魔数，如下

![](https://feily.tech/pic/images/article/class1.png)

紧接着魔数的四个字节分别是此版本号和主版本号，分别占两个字节，共4个字节，如下

![](https://feily.tech/pic/images/article/class2.png)

主版本号为52，说明是JDK1.8编译出来的。JDK1.1的主版本号为45，1.2的主版本号为46，1.7的主版本号为51，自然地1.8的主版本号为52.

## 二、常量池容量计数与常量池

常量池容量计数（constant_pool_count）是一个u2类型的数据，占用16个bit，即最多表示65536个(0 - 65535)个常量池项目，即常量池最多有65536个项目。

![](https://feily.tech/pic/images/article/class3.png)

可见，该Class文件中常量池容量计数值为19，即常量池中共有18项常量(该计数是从1开始的而非0，索引值为1 - 18)，也就意味着接下来的类型为`cp_info`的`constant_pool`会有`constant_pool_count - 1`项常量。

常量池`constant_pool`是Class文件表中的一张子表，主要存放两大类常量：字面量和符号引用。字面量包括文本字符串、被声明为final的常量值等。而符号引用则包括类和接口的全限定名、字段的名称和描述符、方法的名称和描述符。完整结构如下

![](https://feily.tech/pic/images/article/constant-pool.png)

由于常量池每一项的第一个子项均为tag，因此可以通过tag的数值区分该项常量为常量池中的哪个项目，再通过该项目子项的项目及其类型就可以确定下一个常量池的tag。

举个例子，在本例中常量池的第一个u1类型的tag为十进制10，如下

![](https://feily.tech/pic/images/article/class4.png)

即代表第一项常量为**类中方法的符号引用**，该项常量除tag外各有两个字节的index，第一个index值为

![](https://feily.tech/pic/images/article/class5.png)

即代表指向常量池中第4项常量，第二个index为

![](https://feily.tech/pic/images/article/class6.png)

即代表指向常量池中第15项常量。接下来第二项常量为

![](https://feily.tech/pic/images/article/class7.png)

tag为9，代表这项常量是**字段的符号引用**，两个u2类型的index的值分别为

![](https://feily.tech/pic/images/article/class8.png)

![](https://feily.tech/pic/images/article/class9.png)

即分别指向第3项和第16项常量。

剩余常量的分析与上述同理，即都是通过tag判断常量池中常量类型，然后tag后该常量池经过子项的数据类型代表的连续地址空间之后就是另一个常量的tag，继续重复即可。

剩余的常量我们借助Class文件字节码分析工具javap来完成，常量池中完整内容如下

```
C:\Users\Administrator>javap -verbose C:\Users\Administrator\Desktop\docs\TestCl
ass.class
Classfile /C:/Users/Administrator/Desktop/docs/TestClass.class
  Last modified 2019-4-22; size 295 bytes
  MD5 checksum 81f2ab948a7a3068839b61a8f91f634b
  Compiled from "TestClass.java"
public class org.fenixsoft.clazz.TestClass
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #4.#15         // java/lang/Object."<init>":()V
   #2 = Fieldref           #3.#16         // org/fenixsoft/clazz/TestClass.m:I
   #3 = Class              #17            // org/fenixsoft/clazz/TestClass
   #4 = Class              #18            // java/lang/Object
   #5 = Utf8               m
   #6 = Utf8               I
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               inc
  #12 = Utf8               ()I
  #13 = Utf8               SourceFile
  #14 = Utf8               TestClass.java
  #15 = NameAndType        #7:#8          // "<init>":()V
  #16 = NameAndType        #5:#6          // m:I
  #17 = Utf8               org/fenixsoft/clazz/TestClass
  #18 = Utf8               java/lang/Object
SourceFile: "TestClass.java"
```

可见前两项常量的分析我们是正确的，常量池中共有18项常量，第一项常量的具体子项分别指向第4和第15项常量，内容分别为`java/lang/Object`和`"<init>":()V`；

而第二项常量分别指向第3和16项常量，内容分别为`org/fenixsoft/clazz/TestClass`和`m:I`。

## 三、访问标志

常量池结束后的两个字节代表访问标志(access_flags)，该标志用于识别一些类或接口层次的访问信息，包括：这个Class是类还是接口；是否定义为public类型；是否定义为abstract类型；如果是的话，是否被声明为final，等等。具体的标志位以及含义见下表

|标志名称|标志值|含义|
|------|------|------|
|ACC_PUBLIC|0x0001|是否为public类型|
|ACC_FINAL|0x0010|是否被声明为final，只有类可设置|
|ACC_SUPER|0x0020|是否允许使用`invokespecial`字节码指令，JDK1.2之后编译出来的类的这个标志为真|
|ACC_INTERFACE|0x0200|标识这是一个接口|
|ACC_ABSTRACT|0x0400|是否为abstract类型，对于接口或者抽象类来说，此标志值为真，其它值为假|
|ACC_SYNTHETIC|0x1000|标识这个类并非由用户代码产生的|
|ACC_ANNOTATION|0x2000|标识这是一个注解|
|ACC_ENUM|0x4000|标识这是一个枚举|

access_flags共有16个标志位可用，当前只定义了8个，没有使用到的标志位要求一律为0。以本例为例，TestClass被public关键字修饰但是并没有被声明为final和abstract，并且使用了JDK1.2之后的编译器进行编译，因此access_flags的值应为：`0x0001 | 0x0020 = 0x0021`,如下图所示

![](https://feily.tech/pic/images/article/class10.png)

## 三、类索引、父类索引与接口索引集合

类索引(this_class)与父类索引(super_class)均为u2类型的数据，其值分别指向常量池中的某项常量，即符号引用。类索引用于确定这个类的全限定名，父类索引用于确定这个类的父类的全限定名。由于Java不允许多重继承，所以父类索引只有一个，除了java.lang.Object之外，所有的Java类都有父类，也就是说出了java.lang.Object之外，所有的Java类的父类索引(super_class)都不为0。在本例中如下图所示

![](https://feily.tech/pic/images/article/class11.png)

即类索引指向常量池中第3项常量，值为`org/fenixsoft/clazz/TestClass`，即该类的全限定名。

![](https://feily.tech/pic/images/article/class12.png)

父类的全限定名指向常量池第四项常量，为`java/lang/Object`。

接口索引集合的计数器`interfaces_count`为0，代表接口索引集合`interfaces`字段无值，直接跳过(如下图)。接口索引集合用来描述这个类实现了那些接口，这些被实现的接口按照implements的顺序从做左到右的顺序排列在接口索引集合中。接口索引集合的元素仍然是u2类型的数据，即指向常量池中的某项常量，该常量给出了接口的全限定名，接口索引集合计数器为多少就连续数多少个u2类型数据。如果当前类本身就是一个接口，那么接口索引集合后的值就是extends语句。

![](https://feily.tech/pic/images/article/class13.png)

## 四、字段表集合

字段表(field_info)用来描述接口或者类中声明的变量。字段(field)包括了类级变量或实例级变量，但不包括在方法内部声明的变量(这种变量包含在栈中的局部变量表中)。一个字段的描述信息都包括：字段的作用域(public、private、protected修饰符)、是实例变量还是类变量(static修饰符)、可变性(final)、并发可见性(volatile修饰符，是否强制从主内存读写)、可否序列化(transient修饰符)、字段数据类型(基本类型、对象、数组)、字段名称。

这些信息中，各个修饰符都是布尔值，即要么有要么没有，很适合用标志位来表示。而字段叫什么名字、字段被定义为什么数据类型是无法固定的，也就意味着会指向常量池中的某项常量。字段表是Class文件表的一张子表，其格式如下


|类型|名称|数量|描述|
|------|------|------|------|
|u2|access_flags|1|字段的修饰符，具体值见下表|
|u2|name_index|1|字段名称|
|u2|descriptor_index|1|字段数据类型修饰符,各数据类型的常量表示见下下表|
|u2|attributes_count|1|该字段与下一个字段描述字段表集合的额外信息|
|attribute_info|attributes|attributes_count|该字段与上一个字段描述字段表集合的额外信息|

字段访问标志access_flags的标志位与值如下表


|标志名称|标志值|含义|
|------|------|------|
|ACC_PUBLIC|0x0001|字段是否为public|
|ACC_PRIVATE|0x0002|字段是否为private|
|ACC_PROTECTED|0x0004|字段是否为protected|
|ACC_STATIC|0x0008|字段是否为static|
|ACC_FINAL|0x0010|字段是否为final|
|ACC_VOLATILE|0x0040|字段是否为volatile|
|ACC_TRANSIENT|0x0080|字段是否为transient|
|ACC_SYNTHETIC|0x1000|字段是否由编译器自动产生的|
|ACC_ENUM|0x4000|字段是否为enum|

常量池中描述符表示字符的含义为

|标识字符|含义|
|------|------|
|B|基本类型byte|
|C|基本类型char|
|D|基本类型double|
|F|基本类型float|
|I|基本类型int|
|J|基本类型long|
|S|基本类型short|
|Z|基本类型boolean|
|V|特殊类型void|
|L|对象类型，如Ljava/lang/Object;|

对于数组类型，每一维度将使用一个前置的`[`字符来描述，如一个定义为`java.lang.String[][]`类型的二维数组，将被记录为：`[[Ljava/lang/String;`，其中`[[`二维数组，`L`表示这是一个对象，`java/lang/String`表示这是一个String型。合起来就是一个String对象二维数组，那么一个整型数组`int[]`会被记录为`[I`。

题外话：而用描述符来描述方法时，按照先参数列表，后返回值的顺序描述，参数列表按照参数的严格顺序放在一组小括号`()`之内。如方法`void inc()`的描述符为`()V`，方法`java.lang.String toString()`的描述符为`()Ljava/lang/String;`,表示该方法无参，返回值为对象`(L)String`。而方法`int indexOf(char[] source, int sourceOffset, int sourceCount, char[] target, int targetOffset, int targetCount, int fromIndex)`的描述符为`([CII[CIII)I`,很好理解，不解释。

接下来对TestClass字段表集合的分析如下

![](https://feily.tech/pic/images/article/class14.png)

首先该字段表集合计数器fields_count为1，即字段集合表中只有一个实例变量或者类变量。那么进入字段集合表继续分析这一个变量。

![](https://feily.tech/pic/images/article/class15.png)

上图显示该字段的access_flags值为`0x0002`，查字段访问标志表得，该字段的访问修饰符为`private`。

![](https://feily.tech/pic/images/article/class16.png)

上图显示，`name_index`字段指向了常量池中第五项常量，内容为

```
#5 = Utf8               m
```

即该字段的名称为`m`。

![](https://feily.tech/pic/images/article/class17.png)

上图中descriptor_index值为6，该值仍然指向常量池，内容为

```
#6 = Utf8               I
```

那么综上所述就可以推断出该字段被定义为`private int m;`。其后的两个字节表示attributes_count，如下图

![](https://feily.tech/pic/images/article/class18.png)

值为0，代表attributes无值，可以直接跳过。

## 五、方法表集合

方法表集合与字段表集合的解释几乎一样，连结构也一样，如下表所示

|类型|名称|数量|描述|
|------|------|------|------|
|u2|access_flags|1|方法的修饰符，具体值见下表|
|u2|name_index|1|方法名称|
|u2|descriptor_index|1|方法返回值修饰符,同字段表返回值修饰符|
|u2|attributes_count|1|该字段与下一个字段描述方法表集合的额外信息|
|attribute_info|attributes|attributes_count|该字段与上一个字段描述方法表集合的额外信息|


方法访问标志access_flags如下表

|标志名称|标志值|含义|
|------|------|------|
|ACC_PUBLIC|0x0001|方法是否为public|
|ACC_PRIVATE|0x0002|方法是否为private|
|ACC_PROTECTED|0x0004|方法是否为protected|
|ACC_STATIC|0x0008|方法是否为static|
|ACC_FINAL|0x0010|方法是否为final|
|ACC_SYNCHRONIZED|0x0020|方法是否为synchronized|
|ACC_BRIDGE|0x0040|方法是否是由编译器产生的桥接方法|
|ACC_VARARGS|0x0080|方法是否接受不定参数|
|ACC_NATIVE|0x0100|方法是否为native|
|ACC_ABSTRACT|0x0400|方法是否为abstract|
|ACC_STRICT|0x0800|方法是否为strictfp|
|ACC_SYNTHETIC|0x1000|方法是否是由编译器自动产生的|

接下来分析TestClass

![](https://feily.tech/pic/images/article/class19.png)

methods_count为2，代表该类有两个方法(分别是编译器添加的构造器`<init>`和源码中的`inc()`方法)。先看第一个方法的access_flags

![](https://feily.tech/pic/images/article/class20.png)

值为0x0001，查表得该方法的修饰符为public，name_index如下图

![](https://feily.tech/pic/images/article/class21.png)

可见指向常量池中第7项常量，内容为

```
#7 = Utf8               <init>
```

可见该方法为构造器`<init>`，接下来看descriptor_index的值，如下图

![](https://feily.tech/pic/images/article/class22.png)

值为8，仍然是指向常量池，第八项常量为

```
#8 = Utf8               ()V
```

即无参无返回值。接下来看attributes_count，如下图

![](https://feily.tech/pic/images/article/class23.png)

值为1，代表方法表中的属性表集合中有一项属性。接下来看Class文件的最后一个项目——属性表集合(attribute_info)。

## 六、属性表集合

Class文件、字段表、方法表均可以携带自己的属性表集合，以用于描述某些场景专有的信息。与Class文件中其他的数据项目要求严格的顺序、长度和内容不同，属性表集合的限制稍微宽松了一些，不再要求各个属性表具有严格的顺序，并且只要不与已有的属性名重复，任何人实现的编译器都可以向属性表中写入自己定义的属性信息，Java虚拟机运行时会忽略掉它不认识的属性。为了能正确解析Class文件，《Java虚拟机规范（第2版）》中预定义了9项虚拟机应当能识别的属性，如下

|属性名称|使用位置|含义|
|------|------|------|
|Code|方法表|Java代码编译成的字节码指令|
|ConstantValue|字段表|final关键字定义的常量值|
|Deprecated|类、方法表、字段表|被声明为deprecated的方法和字段|
|Exceptions|方法表|方法抛出的异常|
|InnerClasses|类文件|内部类列表|
|LineNumberTable|上述Code属性中|Java源码的行号与字节码指令的对应关系|
|LocalVariableTable|上述Code属性中|方法的局部变量描述|
|SourceFile|类文件|源文件名称|
|Synthetic|类、方法表、字段表|标识方法或字段是否为编译器自动生成的|

对于每个属性，它的名称需要从常量池中引用一个CONSTANT_Utf8_info类型的常量来表示，而属性值的结构则是完全自定义的，只需要说明属性值所占用的位数长度即可。一个符合规则的属性表应该满足如下定义的结构(作用就是字段表和方法表中attributes_count指明了额外的属性信息后进入属性表集合中根据attribute_name_index查常量池从而确定为何种属性)

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u2|attribute_length|1|
|u1|info|attribute_length|

### 6.1 Code属性

对于方法表集合中的属性表集合，Code属性是其中的一个。Java程序方法体里面的代码经过javac编译器处理之后，最终会变成字节码指令存储在方法表集合的属性表集合的Code属性中，也就是说方法表集合属性表集合之前的一些子项只是方法名称、返回值、修饰符的一些描述，真正的方法代码就在方法表集合的属性表集合的Code属性中，Code属性的表结构如下

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|
|u2|max_stack|1|
|u2|max_locals|1|
|u4|code_length|1|
|u1|code|code_length(即每个字节码占用1个字节，code_length为多大，那么该方法就有多少个code，连续读)|
|u2|exception_table_length|1|
|exception_info|exception_table|exception_table_length|
|u2|attributes_count|1|
|attribute_info|attributes|attributes_count|

attribute_name_index指向CONSTANT_Utf8_info型常量的索引，常量值固定为`Code`，它代表了该属性的属性名称，attribute_length指示了属性**值**的长度，由于属性名称索引与属性长度共6个字节，所以属性值的长度固定为整个属性表的长度减去6个字节。

max_stack代表了操作数栈深度的最大值，在方法执行的任意时刻，操作数栈都不会超过这个深度。虚拟机在运行的时候需要根据这个值来分配栈帧(Frame)中的操作栈深度。

max_locals代表了局部变量表所需的存储空间，基本单位是Slot，Slot是虚拟机为局部变量分配内存所使用的最小单位。对于byte、char、float、int、short、boolean、reference和returnAddress等长度不超过32位的数据类型，每个局部变量占用1个Slot，而double和long这两种64位的数据类型则需要2个Slot来存放。**方法参数（包括实例方法中的隐藏参数this）、显式异常处理器的参数(Exception Handler Parameter,即try-catch语句中catch快所定义的异常)、方法体中定义的局部变量都需要使用局部变量表来存放。另外，并不是方法中用到了多少个局部变量就把局部变量所占的Slot之和作为max_locals的值，原因是局部变量表中的Slot可以重用，当代码执行超出一个局部变量的作用域时，这个局部变量所占的Slot就可以被其他局部变量所占用，编译器会根据变量的作用域来分类Slot并分配给各个变量使用，然后计算出max_locals的大小。**

code_length和code用来存储Java源程序编译后生成的字节码指令，code_length代表字节码的长度，code是用于存储字节码指令的一系列字节流。既然名为字节码指令，那么每个指令就是一个u1类型的单字节，当虚拟机读取到code中的一个字节码时，就可以相应地找出这个字节码代表什么指令，并且可以知道这条指令后面是否需要跟随参数以及参数应该如何理解。我们知道一个u1类型的取值范围为0 - 255，也就是一共可以表达256条指令。当前，Java虚拟机规范中定义了约200条编码值对应的指令含义。

关于code_length,虽然是一个u4类型的长度，理论上可以取值2<sup>32</sup> - 1,也就是说理论上一个方法可以有2<sup>32</sup> - 1条**指令**，但是虚拟机规范明确指出，一个方法不允许超过65535条**字节码指令**，如果超过了这个限制，javac编译器就会拒绝编译。

**Code属性是Class文件中最重要的一个属性，如果把一个Java程序中的信息分为代码(Code,方法体里面的Java代码)和元数据(Metadata，包括类、字段、方法定义及其他信息)两部分，那么在整个Class文件里，Code属性用于描述代码，所有的其他数据项目都用于描述元数据。**

继续以TestClass为例，说明方法表集合中属性表集合的Code属性

![](https://feily.tech/pic/images/article/class23.png)

由上图得知，方法表集合中第一个方法`<init>`attributes_count的值为1,即代表该方法包含一个额外的描述信息，进入属性表集合，根据属性表结构第一项为u2类型的attribute_name_index，那么先连续读2个字节确定该属性属于何种属性

![](https://feily.tech/pic/images/article/class24.png)

得知指向常量池第9项常量，内容为

```
#9 = Utf8               Code
```

那么就可以确定方法表集合中的属性表集合中的属性为Code属性，即方法体中经编译产生的字节码，那就查Code属性结构表继续分析，连续四个字节为attribute_length

![](https://feily.tech/pic/images/article/class25.png)

即长度为29。如下两图分别指出了max_stack与max_locals的值

![](https://feily.tech/pic/images/article/class26.png)

![](https://feily.tech/pic/images/article/class27.png)

分别为1。接下来连续四个字节为code_length,即字节码指令的长度，如下

![](https://feily.tech/pic/images/article/class28.png)

值为5，那么连续读5个字节就是字节码指令(code),分别如下

![](https://feily.tech/pic/images/article/class29.png)

此处的查表查的的编码值与字节码指令的对应表，这里无法列出。仅作说明

+ 读入2A，查表得0x2A所对应的指令为aload_0,含义是将第0个Slot中卫reference类型的本地变量推送至栈顶；
+ 读入B7，查表得0xB7所对应的指令为invokespecial，作用是以栈顶的reference类型的数据所指向的对象作为方法的接受者，调用此对象的实例构造器方法、private方法或其父类的方法。这个方法有一个u2类型的参数说明具体调用哪一个方法，它指向常量池中的一个CONSTANT_Methodref_info类型的常量，即此方法的方法符号引用；
+ 读入u2类型的数据00 01，这是invokespacial的参数，查常量池0x000A对应的常量为实例构造器`<init>`方法的符号引用；
+ 读入B1，查表得0xB1对应的指令为return，含义是返回此方法，并且返回值为void。这条指令执行后，当前方法结束。

从这里可以看出，方法的执行过程中的数据交换、方法调用等操作都是基于栈(操作栈)进行的。因此可以初步猜测：Java虚拟机执行字节码是基于栈的体系结构。

剩下个另一个方法通过javap命令来计算，如下

```
{
  public org.fenixsoft.clazz.TestClass();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>
":()V
         4: return
      LineNumberTable:
        line 3: 0

  public int inc();
    descriptor: ()I
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: getfield      #2                  // Field m:I
         4: iconst_1
         5: iadd
         6: ireturn
      LineNumberTable:
        line 8: 0
}
SourceFile: "TestClass.java"
```

这是直接计算出了方法表集合中的所有信息，包括描述符标识字符descriptor、方法名称等、访问标志还有属性表集合Code属性。

在字节码指令Code属性的code数据项之后是该方法的显式异常处理表集合，exception_table_length指明了该方法的异常数量，而exception_table数据项则指出了该方法每个异常类型为exception_info的exception_table_length个异常。exception_info的定义如下

|类型|名称|数量|
|------|------|------|
|u2|start_pc|1|
|u2|end_pc|1|
|u2|handler_pc|1|
|u2|catch_type|1|

整体含义为：**如果字节码指令从第start_pc行到第end_pc行(不含第end_pc行)出现了类型为catch_type或其子类的异常(catch_type指向一个CONSTANT_Class_info型常量的索引)，则转到第handler_pc行继续处理。当catch_type的值为0时，代表任何异常都由handler_pc处进行处理。**

这里的异常表不是必须的，比如TestClass没有显式的捕获异常，所以没有。

我们改动一下TestClass文件，如下

```
1 package org.fenixsoft.clazz;
2 
3 public class TestClass {
4 	
5     private int m;
6 	
7     public int inc() {
8         int x;
9         try {
10            x = 1;
11            return x;
12        } catch (Exception e) {
13            x = 2;
14            return x;
15        } finally {
16            x = 3;
17        }
18    }
19	
20 }
```

编译后的inc方法的相关内容为

```
public int inc();
  descriptor: ()I
  flags: ACC_PUBLIC
  Code:
    stack=1, locals=5, args_size=1
       0: iconst_1    // x = 1;
       1: istore_1
       2: iload_1    // return x;
       3: istore_2
       4: iconst_3    // x = 3;
       5: istore_1
       6: iload_2    // return x;
       7: ireturn
       8: astore_2
       9: iconst_2    // x = 2;
      10: istore_1
      11: iload_1    // return x;
      12: istore_3    // int x;
      13: iconst_3    // x = 3;
      14: istore_1    
      15: iload_3    // return x;
      16: ireturn    
      17: astore        4
      19: iconst_3
      20: istore_1
      21: aload         4
      23: athrow
    Exception table:
       from    to  target type
           0     4     8   Class java/lang/Exception
           0     4    17   any
           8    13    17   any
          17    19    17   any
    LineNumberTable:
      line 10: 0
      line 11: 2
      line 16: 4
      line 11: 6
      line 12: 8
      line 13: 9
      line 14: 11
      line 16: 13
      line 14: 15
      line 16: 17
      line 17: 21
    StackMapTable: number_of_entries = 2
      frame_type = 72 /* same_locals_1_stack_item */
        stack = [ class java/lang/Exception ]
      frame_type = 72 /* same_locals_1_stack_item */
        stack = [ class java/lang/Throwable ]
```

**需要说明的是，方法中的参数以及局部变量均存储在栈帧中的局部变量表中，而对局部变量表中参数与局部变量的操作是在栈帧的操作数栈中进行的。**

根据LineNumberTable指出的源码与字节码指令的对应关系得知

+ 第0条字节码指令`iconst_1`对应的是源码中的第10行`x = 1;`，含义是**将int型1推送至栈顶**；
  + 第1条字节码指令`istore_1`，含义是**将栈顶int数值存入本地变量表中的第二个Slot中**；
+ 第2条字节码指令`iload_1`对应源码中的第11行`return x;`，含义是**将第2个int型本地变量推送至栈顶**；有个疑问，int型1不是本来就在栈顶吗，怎么又推送一次？还出现了个**第二个**？由于第1条字节码指令的执行，原来栈顶的int型1已经被存入局部变量表中的第二个Slot中，所以已经不在栈顶了，所以第2条字节码指令需要将本地变量表中的第2个Slot的int型1值取出来推送至栈顶；
  + 第3条字节码指令`istore_2`，含义是**将栈顶int数值存入本地变量表中的第三个Slot中**；也就是将栈顶的int型1再次存入第三个Slot中；
+ 第4条字节码指令`iconst_3`对应源码中的第16行`x = 3;`，含义是**将int型3推送至栈顶**。
  + 第5条字节码指令`istore_1`的含义是**将栈顶int型数值存入局部变量表中的第二个Slot中**，由于int型1已经被从第二个Slot中取出推送至栈中后又被存入第三个Slot中，所以第二个Slot空出来了，任意一个int型数值都可以进入；这条字节码指令执行后，局部变量表中的第二个Slot中为int型3；
+ 第6条字节码指令`iload_2`对应源码中的第11行`return x;`。含义是**将局部变量表中的第三个Slot的值推送至栈顶**，也就是int型1被推送至栈顶。
  + 第7条字节码指令`ireturn`含义是**从当前方法返回int**。即将该方法的返回值返回给该方法的调用者(即虚拟机栈当前线程调用栈的在inc方法(栈帧)执行时位于其底部的栈帧)。返回值为1，**从这里也可以看出，方法的返回值仍然是从操作数栈中返回而不是局部变量表，所以没有返回3而是1。这里也揭示了try-finally块中返回值的原理。**

以上是try中没有发生异常的情况下字节码指令的执行方式，如果发生了类型为`Class java/lang/Exception`的异常，即字节码指令第0条到第3条(不包括第4条)，那么根据异常表的指示将会跳转到第8行处理。由于字节码指令第4条才会将3推送至栈顶，而第五条才会将3存入第2个Slot，也就意味着如果发生异常就不会执行到第4条和第五条字节码指令，也就意味着第2个Slot并没有值(根据第2条字节码指令，局部变量表中的第2个Slot中的1被推送至栈顶后又存放到了第3个Slot中，所以第二个Slot没有值)如下

+ 第8条字节码指令`astore_2`对应源码中的第12行`} catch (Exception e) {`，代表的是异常处理，继续看下一行，因为是一个整体；
+ 第9条字节码指令`iconst_2`对应源码中的第13行`x = 2;`，是对异常的处理，含义为**将int型2推送至栈顶**；
  + 第10条字节码指令`istore_1`一目了然，含义是将**将栈顶int数值2存入本地变量表中的第二个Slot中**；由于出现了异常，上面已经解释过了，第二个Slot无值，因此可以存放而不会覆盖别的值。
+ 第11条字节码指令`iload_1`对应源码中的第14行`return x;`，含义是**将局部变量表中第2个Slot的值2推送至栈顶**；
  + 第12条字节码指令`istore_3`的含义是将栈顶的值2存入局部变量表中的第四个Slot中，因为第三个Slot存放的是int型1，上面也解释过了；`由于是不管有没有发生异常，都会执行finally，所以接下来执行finally`；
+ 第13条字节码指令`iconst_3`对应的仍是源码第16行`x = 3;`，含义是**将int型3推送至栈顶**。(注意，发生异常后try中并未执行到这里)；含义是将int型3推送至栈顶；
  + 第14条字节码指令`istore_1 `的含义是**将栈顶的3存入局部变量表中的第2个Slot中**，由于第11条字节码指令`iload_1`将局部变量表第2个Slot中的数据2推送至了栈顶，紧接着第12条指令将栈顶的2存入了第四个Slot中，所以此时Slot无值，可以存入栈顶的3；
+ 第15条字节码指令`iload_3`对应源码的第14行`return x;`，含义是**将局部变量表第4个Slot中的值推送至栈顶，即将2推送至栈顶；**
  + 第16条字节码指令`ireturn`含义是**从当前方法返回int**。即将该方法的返回值返回给该方法的调用者(即虚拟机栈当前线程调用栈的在inc方法(栈帧)执行时位于其底部的栈帧)。返回值为2，**这里也揭示了try-catch-finally块中返回值的原理。**
  
以上是字节码指令第0条到第3条(不包括第4条)发生了类型为`Class java/lang/Exception`的异常的处理方式，如果发生了别的异常，即类型为`any`，那么将会跳转到第17条字节码指令处理。从方法表集合的属性表集合的Code属性的异常表集合得知，第8到12行字节码、17到18行字节码发生类型为`any`的异常，也会调到第17条字节码指令进行处理，所以统一看一下

+ 第17条字节码指令`astore    4`对应源码第16行`x = 3;`，该指令原型为`astore index`，含义是将栈顶树值存入局部变量表中索引为index(第index+1个Slot)的Slot中，此处不详细展开，因为类型为`any`对应的异常字节码有三段，所以也不知道具体是哪一段的哪一条字节码出现异常，但是无非两种情况，一种是栈顶存在操作数，一种是栈顶不存在操作数，不管存不存在操作数，那么都将其存入第5(索引为4)个Slot中，这样就清空了栈；
+ 第19条字节码指令`iconst_3`，含义是**将int型3推送至栈顶**。也就是finally中的x = 3；
+ 第20条字节码指令`istore_1`，含义是**将栈顶的操作数存入第2个Slot中(索引为1)**,也就是将3存入第二个Slot中，这里分析也比较复杂，因为类型为`any`对应的异常字节码有三段，所以也不知道具体是哪一段的哪一条字节码出现异常，但是无非两种情况，第二个Slot中有可能有值也有可能没有。这种情况已经不重要了，因为这种情况下并没有返回值会返回给上层方法调用者，而是直接将栈顶的异常抛出。即第23条字节码`athrow`。

解释完毕，可以看出，Java方法的执行确实是基于栈的，对参数以及变量(局部变量)的操作都是在操作数栈中进行的，局部变量表存储方法中的变量及参数，通过对局部变量表中变量的入栈参与指令运算和出栈暂存数值最终出栈栈顶元素实现返回结果！而对实例变量的访问是通过局部变量表中第1个Slot(索引为0)中存放的this来指向具体对象的地址，这样就可以通过地址引用到实例变量，也可以通过地址将对实例变量的修改写入堆中。

### 6.2 Exceptions属性

这里的Exceptions属性是在方法表中与Code属性平级的一项属性。Exceptions属性的作用是列举出方法中可能抛出的受检**异常**，也就是方法描述时在throws关键字后面列举的异常。结构如下表所示

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|
|u2|number_of_exceptions|1|
|u2|exception_index_table|number_of_exceptions|

仍然是通过attribute_name_index判断该属性属于属性表集合中的哪一个具体的属性，number_of_exceptions指明了该方法可能抛出的受检异常的个数，紧接着连续从常量池读取number_of_exceptions个数个具体常量就是受检异常的类型。

### 6.3 LineNumberTable属性

该属性用于描述Java源码行号与字节码行号之间的对应关系。并不是运行时的必须属性，但是会默认生成到Class文件之中，可以在javac中使用`-g:none`或者`-g:lines`选项忽略该属性。

如果选择不生成该属性，那么对程序主要的影响就是在抛出异常时，堆栈中将不会显示出错的行号，并且在调试程序的时候无法按照源码来设置断点。表结构如下

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|
|u2|line_number_table_length|1|
|line_number_info|line_number_table|line_number_table_length|

line_number_info中包括了`start_pc`和`line_number`两个u2类型的数据项，前者是字节码行号，后者是Java源码行号。

TestClass中的该属性被描述为

```
{
  public org.fenixsoft.clazz.TestClass();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>
":()V
         4: return
      LineNumberTable:
        line 3: 0

  public int inc();
    descriptor: ()I
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: getfield      #2                  // Field m:I
         4: iconst_1
         5: iadd
         6: ireturn
      LineNumberTable:
        line 8: 0
}
SourceFile: "TestClass.java"
```

### 6.4 localVariableTable属性

该属性用于描述栈帧中局部变量表中的变量与Java源码中定义的变量之间的关系，不是运行时必需的属性，默认也不会生成到Class文件中，可以通过在javac中使用`-g:none`或者`-g:vars`选项来忽略该属性信息。

如果没有生成该属性，那么最大的影响就是当其他人引用这个方法时，所有的参数名称将丢失，IDE可能会使用诸如arg0、arg1之类的占位符来代替原有的参数名。表结构如下

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|
|u2|local_variable_table_length|1|
|local_variable_info|local_variable_table|local_variable_table_length|

其中local_variable_info项目代表了一个栈帧与源码中的局部变量的关联，结构如下

|类型|名称|数量|
|------|------|------|
|u2|start_pc|1|
|u2|length|1|
|u2|name_index|1|
|u2|descriptor_index|1|
|u2|index|1|

index是这个局部变量在栈帧局部变量表中Slot的位置，当这个变量的数据类型是64位类型时(double、long)，它占用的Slot为index和index+1两个位置。

### 6.5 SourceFile属性

该属性用于记录生成这个Class文件的源码文件名称。该属性页数可选的，可以通过在javac中使用`-g:none`或者`-g:source`选项来忽略该属性信息。

在Java中，对于大多数类来说，类名与文件名一致，但是一些特殊情况例外，例如内部类。如果不生成这项属性，当抛出异常时，堆栈中将不会显示出错误代码所属的文件名，结构如下

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|
|u2|sourcefile_index|1|

是一个定长属性，sourcefile_index数据项指向常量池中CONSTANT_Utf8_info型常量的索引，常量值是源码文件的文件名。

### 6.6 ConstantValue属性

该属性的作用是通知虚拟机自动为静态变量赋值。只有被static关键字修饰的类变量才可以使用这项属性。

对于非static修饰的的变量(实例变量)的赋值是在实例构造器`<init>`中进行的；而对于类变量，则有两种方法可以选择：

+ 赋值在类构造器`<clinit>`方法中进行；
+ 使用ConstantValue属性来赋值。

目前Sun Javac编译器的选择是：**如果同时使用final和static来修饰一个变量(也就是常量，因为final)，并且这个变量的数据类型是基本类型或java.lang.String的话，就生成ConstantValue属性来初始化该变量，如果这个变量没有被final修饰，或者并非基本类型及字符串，则选择在`<clinit>`方法中进行初始化**。表结构如下

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|
|u2|contributevalue_index|1|

可以看出该属性是一个定长属性，它的attribute_length属性值必须为。该属性的数据项contributevalue_index代表了常量池中一个字面量常量的引用，根据常量字段类型的不同，字面量可以是CONSTANT_Long_info、CONSTANT_Float_info、CONSTANT_Double_info、CONSTANT_Integer_info、CONSTANT_String_info常量中的一种。

### 6.7 InnerClasses属性

该属性用于记录内部类与宿主类之间的关联。如果一个类中定义了内部类，那么编译器将会为它及它所包含的内部类生成InnerClasses属性(因为内部类在编译期间，会被独立出来，所以需要建立内部类与宿主类之间的关联)。结构如下

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|
|u2|number_of_classes|1|
|inner_classes_info|inner_classes|number_of_classes|

数据项number_of_classes代表需要记录多少个内部类信息，每一个内部类信息都由一个inner_classes_info表进行描述，number_of_classes有多少就有几个连续的inner_classes_info表，inner_classes_info表结构如下


|类型|名称|数量|
|------|------|------|
|u2|inner_classes_info_index|1|
|u2|outer_class_info_index|1|
|u2|inner_name_index|1|
|u2|inner_class_access_flags|1|

inner_classes_info_index和outer_class_info_index都是指向常量池中CONSTANT_Class_info型常量的索引，分别代表了内部类与宿主类的符号引用。

inner_name_index是指向常量池中CONSTANT_Utf8_info类型常量的索引，代表这个内部类的名称，如果是匿名内部类，则这项值为0.

inner_class_access_flags是内部类的访问标志，类似于类的access_flags，取值如下表所示

|标志名称|标志值|含义|
|------|------|------|
|ACC_PUBLIC|0x0001|内部类是否为public|
|ACC_PRIVATE|0x0002|内部类是否为private|
|ACC_PROTECTED|0x0004|内部类是否为protected|
|ACC_STATIC|0x0008|内部类是否为static|
|ACC_FINAL|0x0010|内部类是否为final|
|ACC_INTERFACE|0x0020|内部类是否为interface|
|ACC_ABSTRACT|0x0400|内部类是否为abstract|
|ACC_SYNTHETIC|0x1000|内部类是否并非由用户代码产生的|
|ACC_ANNOTATION|0x2000|内部类是否是一个注解|
|ACC_ENUM|0x4000|内部类是否是一个枚举|

### 6.8 Deprecated及Synthetic属性

这两个属性都属于标志类型的布尔属性，只存在有和没有的区别，没有属性值的概念。

Deprecated属性用于表示某个类、字段或方法，已经被程序作者定为不在推荐使用，它可以通过在代码中使用`@deprecated`注释进行设置。

`Synthetic`属性代表此字段或方法并不是由Java源码直接产生的，而是由编译器自行添加的，在JDK1.5之后，标识一个类、字段或方法是编译器自动产生的，也可以设置它们访问标志的ACC_SYNTHETIC标志位。

这两个字段的结构都如下表

|类型|名称|数量|
|------|------|------|
|u2|attribute_name_index|1|
|u4|attribute_length|1|

其中attribute_length数据项的值必须为0x00000000，因为没有任何属性值需要设置。

以上就是Class文件的结构。可以看出，**类型**就是一个连续地址空间，而**名称**则指明了该连续地址空间代表的是什么内容，**数量**则表示该名称的内部有多少个子项(即连续数几个)。