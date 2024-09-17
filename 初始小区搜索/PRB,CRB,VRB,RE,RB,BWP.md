**定义**
1. **RE（Resource Element）**：资源元素是5G NR中最小的资源单位，代表频域上的一个子载波和一个时域上的一个OFDM符号的交点。

2. **RB（Resource Block）**：资源块是频域上12个连续子载波的集合，是调度资源的基本单位。在时域上，一个RB通常持续一个时隙的长度。

3. **PRB（Physical Resource Block）**：物理资源块是实际用于承载用户数据和控制信息的资源块，是在BWP（Bandwidth Part）内定义的。

4. **CRB（Common Resource Block）**：公共资源块是在整个系统带宽内对PRB进行统一索引的参考点。CRB0的子载波0与“参考点A”对齐，用于定位资源的位置。

5. **VRB（Virtual Resource Block）**：虚拟资源块是MAC层在分配资源时使用的逻辑资源块，VRB映射到PRB上，可以是集中式或分布式映射。

6. **BWP（Bandwidth Part）**：部分带宽是5G NR中新引入的概念，它是CRB的一个子集，代表UE实际工作带宽。BWP允许UE使用小于信道带宽的频谱资源进行传输，支持带宽自适应。

**相互间的关系**

1. MAC层首先根据VRB进行资源分配，然后VRB映射到PRB上。
2. PRB定义在BWP内，而BWP是CRB的一个子集。CRB提供了一个在整个带宽内对PRB进行统一索引的参考框架。BWP则允许网络根据UE的需求和能力动态地分配带宽资源。


PointA、公共资源块、物理资源块的关系：
$$n_{CRB} = n_{PRB}+N_{BWP,i}^{start}$$


![[Pasted image 20240912074158.png]]





![[Pasted image 20240912064412.png]]