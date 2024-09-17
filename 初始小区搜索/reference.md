  

[CongZ's Blog](https://czgitaccount.github.io/)

择其善者而从之，其不善者而改之 !

- [  
    首页](https://czgitaccount.github.io/)
- [  
    标签](https://czgitaccount.github.io/tags/)
- [  
    分类](https://czgitaccount.github.io/categories/)
- [  
    归档](https://czgitaccount.github.io/archives/)
- [  
    关于](https://czgitaccount.github.io/about/)
-   
    搜索

# 5G-NR-SSB学习

 发表于 2020-03-08 |  分类于 [5G_Protocol](https://czgitaccount.github.io/categories/5G-Protocol/)

 字数统计: 3.1k |  阅读时长 ≈ 13

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/%E6%9D%83%E5%8A%9B%E7%9A%84%E6%B8%B8%E6%88%8F_%E7%90%BC%E6%81%A9%E9%9B%AA%E8%AF%BA.jpg)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/%E6%9D%83%E5%8A%9B%E7%9A%84%E6%B8%B8%E6%88%8F_%E7%90%BC%E6%81%A9%E9%9B%AA%E8%AF%BA.jpg)

《权力的游戏》 - 琼恩·雪诺

# 一、概述

SS/PBCU Block简称SSB，主要包含PSS、SSS以及PBCH。对于SSB的学习，主要采用如下步骤来学习，并加深理解的：

> 1. NR SSB 时域频域分析
> 2. PSS/SSS序列
> 3. PBCH payload 生成及处理
> 4. PBCH加扰/调制/RE映射
> 5. PBCH DMRS
> 6. UE SSB处理过程
> 7. 同步过程理解

# 二、学习进程

## [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-1-NR-SSB-%E6%97%B6%E5%9F%9F%E9%A2%91%E5%9F%9F%E5%88%86%E6%9E%90)2.1 NR SSB 时域频域分析

PSS和SSS主要用于UE获得时间同步和频率同步，获取小区ID；PBCH用于无线帧号同步以及SIB1的配置。与LTE的差异主要在于SSS序列都一样，不存在前后5 ms的差异且SSB的周期是可配置的，以及引入了beam index的概念。

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-1-1-SSB%E6%97%B6%E5%9F%9F%E5%88%86%E6%9E%90)2.1.1 SSB时域分析

在5G中，每个SSB对应一个beam， SSB的最短时间跨度为5 ms，也就是半帧。一个半帧中可能存在多个SSB，我们将一个半帧中存在的一个或多个SSB称为SS Burst Set，一个SS Burst Set中的SSB包含的信息相同。两个SS Burst Set出现的时间，也就是存在SSB的半帧出现的时间是可以配置的，成为SS Burst Set Periodicity，5 ms–160 ms范围。默认为20 ms。  
根据[TS 38.213]中的SSB的时间分布描述，总结成如下表所示。从表中可以看到，SSB符号的起始位置取决于Case类型以及所处频段，一个SSB在时域上占据4个符号。Num为SSB的总数。

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/1.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/1.png)

以Case A，小于等于3 GHz为例，其符号位置如下图所示，SSB总数为4，各个SSB代表了不同的波束方向。

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/2.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/2.png)

SSB的Case由Band决定，[TS 38.104]中规定

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/3.jpg)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/3.jpg)

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-1-2-SSB%E9%A2%91%E5%9F%9F%E5%88%86%E6%9E%90)2.1.2 SSB频域分析

由上一节可知SSB时域是由4个symbol组成，而频域是由20个RB，也即240个子载波构成，它包含着set 0 ,PSS,SSS,PBCH,DMRS,结构如下表所示

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/4.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/4.png)

综合以上，下图更直观的显示SSB时域频域分布，横轴为频域，纵轴为时域。

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/5.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/5.png)

与LTE中PSS和SSS固定在带宽的中间73个子载波不同，NR中SSB可能的频域位置有很多。NR中有一系列全局同步信道号GSCN，每个GSCN都会对应一个确定的、绝对的频率位置，系统会把SSB放在这些GSCN上，对齐方式为SSB的10号RB的0号子载波与GSCN对齐，UE就会在这些GSCN上挨个盲检SSB。

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/GSCN.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/GSCN.png)

不同的operating band是分配给不同运营商的，所以UE是知道自己处在哪个operating band上的，比如n28分配给广电的，那么接入广电网络的UE在搜索SSB的时候，就盲检1901 – <<1>> – 2002这个范围内的GSCN(参考2.1.1表格)，并且只搜索15kHz子载波间隔、case A下的SSB。

确定整个CRB中SSB所处于的位置，需要通过KssbKssb和CORESET#0相应的offsetRB来确定。[TS 38.211]中KssbKssb来表示公共资源快NSSBCRBNCRBSSB子载波0相对SSB子载波0的偏移。KSSBKSSB的低4bit由MIB参数中的ssb-SubcarrerOffset给出。  
对于SSB类型B，即（μ= 3 or 4）来说,KSSBKSSB={0,…,11}, 4 bit足够。  
对于SSB类型A，即（μ= 0 or 1）来说, KSSBKSSB={0,…,23}, 4 bit不够，需要5 bit来表示，因此使用PBCH payload中aA¯¯¯¯+5aA¯+5来表示高比特位。下文会说明。  
最终SSB在整个CRB中的位置如下图所示：

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/6.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/6.png)

## [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-2-PSS-SSS%E5%BA%8F%E5%88%97)2.2 PSS/SSS序列

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-2-1-PCI)2.2.1 PCI

PCI(Physical Cell Identifier)，物理小区标识，NcellIDNIDcell定义如下：

$$  
N_{ID}^{cell} = 3N_{ID}^{(1)} + N_{ID}{(2)}$$
$$N_{ID}{cell}\in\lbrace 0,1,…,1007\rbrace\N_{ID}^{(1)}\in\lbrace 0,1,…,355\rbrace\N_{ID}^{(2)}\in \lbrace 0,1,2\rbrace  
$$

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-2-2-PSS)2.2.2 PSS

PSS(Primary Synchronization Signal)，主同步信号，序列定义如下：  

dpss(n)=1−2x(m)\m=(n+43N(2)ID)mod127\0≤n<127dpss(n)=1−2x(m)\m=(n+43NID(2))mod127\0≤n<127

x(m)x(m)由如下序列得出：  

x(i+7)=(x(i+4)+x(i))mod2 [x(6)x(5)x(4)x(3)x(2)x(1)x(0)]=[1110110]x(i+7)=(x(i+4)+x(i))mod2 [x(6)x(5)x(4)x(3)x(2)x(1)x(0)]=[1110110]

PSS部分映射在SSB中间12个RB上,占用144个子载波，PSS序列映射在56-182，前8个和后9个为保护间隔(guard band)。

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-2-3-SSS)2.2.3 SSS

SSS(Secondary Synchronization Signal)，辅同步信号，序列定义如下：  

dsss(n)=[1−2x0((n+m0)mod127)][1−2x1((n+m1)mod127)]\m0=15[N(1)ID112]+5N(2)ID\m1=N(1)IDmod112\0≤n<127dsss(n)=[1−2x0((n+m0)mod127)][1−2x1((n+m1)mod127)]\m0=15[NID(1)112]+5NID(2)\m1=NID(1)mod112\0≤n<127

  
x1(m)x1(m)和x2(m)x2(m)由如下序列得出：  

x0(i+7)=(x0(i+4)+x0(i))mod2 x1(i+7)=(x1(i+1)+x1(i))mod2 [x0(6)x0(5)x0(4)x0(3)x0(2)x0(1)x0(0)]=[0000001] [x1(6)x1(5)x1(4)x1(3)x1(2)x1(1)x1(0)]=[0000001]x0(i+7)=(x0(i+4)+x0(i))mod2 x1(i+7)=(x1(i+1)+x1(i))mod2 [x0(6)x0(5)x0(4)x0(3)x0(2)x0(1)x0(0)]=[0000001] [x1(6)x1(5)x1(4)x1(3)x1(2)x1(1)x1(0)]=[0000001]

  
SSS与PSS相同，映射在SSB中间12个RB上,占用144个子载波，PSS序列映射在56-182，前8个和后9个为保护间隔(guard band)。

## [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-3-PBCH-payload-%E7%94%9F%E6%88%90%E5%8F%8A%E5%A4%84%E7%90%86)2.3 PBCH payload 生成及处理

PBCH payload 组成如下图所示：

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/7.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/7.png)

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-3-1-BCCH)2.3.1 BCCH

BCCH共24比特，其中B如果指示的是MIB信息，其中MIB信息只有23比特。看[TS 38.311 ]中的BCCH-BCH-Message结构，其中有1比特Choice指示，指示是mib还是messageClassExtension。  
MIB的内容如下图所示：

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/8.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/8.png)

**systemFrameNumer**- 无线帧号指示，这里只有6比特，还有4比特在PBCH payload中添加; **6 bit**  
**subCarrierSpacingCommon** - 指示SIB1的子载波间隔，FR1为15KHz或60KHz，FR2为30KHz或120KHz; **1 bit**  
**ssb-SubcarrerOffset **  
是距离SSB底部距离RB边界位置的偏移KSSBKSSB, **4 bit**  
对于FR2来说, KSSBKSSB={0,…,11}足够  
对于FR1来说，KSSBKSSB={0,…,23}不够，需要在PBCH payload中添加一位共同指示，即a(A+5)a(A+5)  
**dmrs-TypeA Position **- PUSCH/PDSCH DMRS位置参数 **1 bit**  
**pdcch-ConfigSIB1**- 共8比特，确定SIB1调度的时频位置 **8 bit**  
**cellBarred** - 小区是否被准许接入指示 **1 bit**  
**intraFreqReselection** - 是否允许同频重选指示 **1 bit**

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-3-2-PBCH-payload%E4%B8%AD%E5%85%B6%E4%BB%96%E5%86%85%E5%AE%B9)2.3.2 PBCH payload中其他内容

PBCH payload还需要加上SFN的低4比特，half-frame半帧信息（由于5ms内映射完所有SSB，需要1比特指示是在前5 ms还是后5 ms，nhfnhf=0时，表示前半帧；当nhfnhf=1，表示第二个半帧），以及3比特 SSB index(SSB index的高3比特)或者kssbkssb信息（取决于SSB总数，即 NR SSB时域频域分布中根据band指示确定的SSB总数Num，或者LmaxLmax），最终PBCH payload共**32比特**。

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-3-3-PBCH-payload-%E6%93%8D%E4%BD%9C)2.3.3 PBCH payload 操作

**step 1** - 确定载荷中内容（由前文已知）  
**step 2** - reorder  
根据比特信息重新进行排序，G(j)即为排序后的顺序。操作后 (**32 比特**) ，如下图所示：

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/9.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/9.png)

**step 3** - scrambling  
对于确定vv值的无线帧号对应的$2{nd}//3{rd}LSB，半帧信息以及SSBindex不进行加扰，加扰序列每80ms进行初始化，LSB，半帧信息以及SSBindex不进行加扰，加扰序列每80ms进行初始化，L_{max}$也即SSB总数。操作后(**32 比特**)，如下图所示：  
  
[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/10.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/10.png)

**step 4** - CRC 添加循环冗余码后输出数据长度为32+24 = **56 比特**  
**step 5** - Polar Coding 输出比特长度为N = 2929 = **512比特**  
**step 6** - Rate Matching 输出数据长度为E = **864 比特**（E = 864即文章NR SSB时域频域分布中的PBCH所占RE个数432乘以2）

## [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-4-PBCH%E5%8A%A0%E6%89%B0-%E8%B0%83%E5%88%B6-RE%E6%98%A0%E5%B0%84)2.4 PBCH加扰/调制/RE映射

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-4-1-%E5%8A%A0%E6%89%B0)2.4.1 加扰

加扰部分相关参数取决于小区ID与SSB index。[TS 38.211 7.3.3.1] , 加扰由以下公式得出：  

b(i)˜=(b(i)+c(i+vMbit))mod2b(i)~=(b(i)+c(i+vMbit))mod2

  
加扰序列 c(i)c(i) 在[TS 38.211 5.2.1]给出，由GoldGold 序列生成，此时初始化为 cinit=NcellIDcinit=NIDcell。  
-对于 Lmax=4Lmax=4，vv is the 2 LSB of SSB index ;  
-对于 Lmax=8or64Lmax=8or64，vv is the 3 LSB of SSB index ;

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-4-2-%E8%B0%83%E5%88%B6)2.4.2 调制

PBCH使用QPSK调制方式。

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-4-3-%E6%98%A0%E5%B0%84)2.4.3 映射

参照2.1节 NR SSB时域频域分布中的PBCH数据部分的RE映射，先时域后频域。

## [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-5-PBCH-DMRS)2.5 PBCH DMRS

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-5-1-%E5%BA%8F%E5%88%97%E7%94%9F%E6%88%90)2.5.1 序列生成

UE应该采用参考信号序列r(m)r(m)对PBCH DMRS定义，相关参数取决于小区ID，SSB index还有半帧信息来觉得。由[TS 38.211 7.4.1.4.1]给出：  
$$  
r(m) = \frac{1}{ \sqrt{2} }(1-2_c(2m)) + j\frac{1}{ \sqrt{2} }(1-2_c(2m+1))  
$$

加扰序列 c(i)c(i) 在[TS 38.211 5.2.1]给出，由GoldGold 序列生成，此时初始化为 cinitcinit由以下得出：

$$  
c_{init} = 2^{11}( \overline{i}_{SSB}+1)([N_{ID}^{cell}/4]+1) + 2^6( \overline{i}_{SSB}+1)) + (N_{ID}^{cell}mod4)  
$$

-对于 Lmax=4Lmax=4，$\overline{i}_{SSB}是SSBindex的2比特最低有效位和1比特半帧信息是SSBindex的2比特最低有效位和1比特半帧信息n_{hf}(构成3比特，高位是半帧信息，也即(构成3比特，高位是半帧信息，也即\overline{i}_{SSB} = {i}_{SSB} + 4n_{hf});−对于);−对于L_{max} = 8\quad or\quad 64 ，，\overline{i}_{SSB}是SSBindex的3比特最低有效位(也即是SSBindex的3比特最低有效位(也即\overline{i}_{SSB} = {i}_{SSB})−以上)−以上{i}_{SSB}也即分别对应2.4.1中的也即分别对应2.4.1中的v$。

### [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-5-2-%E6%98%A0%E5%B0%84)2.5.2 映射

参照2.1节 NR SSB时域频域分布中的PBCH DMRS数据部分的RE映射，v=NcellIDmod4v=NIDcellmod4(此时的v不同于上文)，先时域后频域。**同频邻区设置不同的偏移有利于降低导频干扰**。

## [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-6-UE-SSB%E5%A4%84%E7%90%86%E8%BF%87%E7%A8%8B)2.6 UE SSB处理过程

UE的处理过程即为上述过程的一个逆过程。

1. 通过PSS/SSS可以获得SSB的符号起始位置，小区ID；
    
2. 通过小区ID，可以获得PBCH DMRS的频域位置，PBCH DMRS携带了3比特信息，具体代表的信息取决于LmaxLmax。PSS/SSS频点信息即确定了其所属Band，也就能够确定LmaxLmax的值，那么  
    1）Lmax=4Lmax=4，确定SSB index以及半帧指示；  
    2）Lmax=8or64Lmax=8or64，确定SSB index低3比特；
    
3. PBCH RE解映射，解调制，2.4.1节处加扰对应的第一次解扰，此处解扰通过小区ID与由步骤2中确定SSB index的比特信息可以确定；
    
4. PBCH 解速率匹配，Polar译码，CRC校验，2.3.3节处加扰对应的第二次解扰，注意到无线帧号对应的2nd/3rd LSB未进行加扰，因此可以确定加扰序列，Cinit 80ms作为一次初始化，如下图所示：
    

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/11.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/11.png)

5. 解交织，确定PBCH payload；

## [](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#2-7-%E5%90%8C%E6%AD%A5%E8%BF%87%E7%A8%8B%E7%90%86%E8%A7%A3)2.7 同步过程理解

同步过程是移动终端获得无线网络的时间和频率的过程，是终端接入网络的前提。终端要知道网络在哪个时间，哪个频率上发送什么消息，才能正确接收网络下发的信息，而同步过程就是为了使终端知道这个时间和频率信息而进行的一系列操作。这些操作与2.6节**UE SSB处理过程**相互重叠。更形象的流程如下：

[![](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/%E6%A6%82%E5%BF%B5.png)](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/%E6%A6%82%E5%BF%B5.png)

第一步的时间与频率同步所指的是一般意义上，我们在通信系统课程中会学到的，接收端与发射端在时间域和频率域上的同步。它并不在5G协议的规定范围之内，一般都是由芯片厂家自己实现的。这一步常用的方法有互相关检测和自相关检测等，也就是通过将接收信号与已知信号(PSS)做互相关来检测已知信号的位置，或者通过对接收信号自身做自相关来检测循环前缀（CP）的位置。  
这一步是为了获得OFDM的符号同步（时间同步）和检测同步信号所在频率（频率同步）。

一个可能的过程：  
1、终端将射频接收机调谐到指定频点;  
2、在时域对PSS做**互相关检测**以取得时域同步，同时获得N(2)IDNID(2); (在SSB的第一个symbol时间内，SSB频域范围内只有PSS信号，因此可以对它做相关检测; 相反，因为SSS所在的第三个symbol时间内还有PBCH，所以无法对它做时域相关检测）  
3、根据PSS的位置可以获得SSS的位置，在频域对SSS做互相关检测，可以获得频域同步并同时获得N(1)IDNID(1);  
4、由N(1)IDNID(1); 和N(2)IDNID(2); 可以获得PCI，由PCI又可以进一步解码PBCH DMRS, 从而获得SSB index和 half frame number nhfnhf;  
5、最后利用对DMRS的信道估计，终端解码PBCH并获得系统消息MIB。

这里是一个matlab对上述过程的模拟程序：

> [https://www.mathworks.com/help/5g/examples/NR-Synchronization-Procedures.html](https://www.mathworks.com/help/5g/examples/NR-Synchronization-Procedures.html)

由上一步可知，UE SSB处理过程与同步过程如出一辙，就是为了使终端知道这个时间和频率信息而进行的一系列操作。

> 主要参考：  
> 微信公众号：5G菜鸟成长日记，  
> 博客：[https://www.cnblogs.com/beilou310/p/11162798.html](https://www.cnblogs.com/beilou310/p/11162798.html)  
> 博客：[https://blog.csdn.net/qq_44113393/article/details/89844595](https://blog.csdn.net/qq_44113393/article/details/89844595)  
> 博客：[https://blog.csdn.net/m0_45416816/article/details/96605980](https://blog.csdn.net/m0_45416816/article/details/96605980)

  
  

----------- 本文结束 -----------

  
  
  
  

[_SSB_](https://czgitaccount.github.io/tags/SSB/)

[5G-NR-PDCCH学习](https://czgitaccount.github.io/5G_Protocol/5G-NR-PDCCH%E5%AD%A6%E4%B9%A0/ "5G-NR-PDCCH学习")

- 文章目录
- 站点概览

1. [一、概述](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#)
2. [二、学习进程](https://czgitaccount.github.io/5G_Protocol/5G-NR-SSB%E5%AD%A6%E4%B9%A0/#)

© 2021 从之丶

 1%