# Buck 降压电路的原理

## 前言

本文为DC-DC开关电源Buck电路原理的学习文档，包括电路拓扑图的分析，数学公式讲解以及实际电路设计时的要点。希望读者在学习中能有所收获

**（PS：由于有些内容与Boost电路学习文档重合，在此不再花笔墨过多描述，建议读者先看《Boost升压电路的原理》一文后再学习本文）**

作者比较🥦，文档内容涵盖或许不够全面，如有专业性错误还请读者见谅并指出，谢谢🙏

免责声明：内容来源于本人学习总结和各种学习资源，如有侵权可及时联系作者

| version | author |  date  | statement |
| :-----: | :----: | :----: | :-------: |
|  V 1.0  | Q.Z.Q  | 2023.6 |     /     |

+++

+++

## 电路拓扑

电路拓扑如下

![](https://pic.imgdb.cn/item/649847aa1ddac507ccb044c7.jpg)

上图是个异步 Buck，同步 Buck 就是将里面的二极管换成 MOS管下文都是用异步 Buck 来分析的（异步相比于同步要复杂一点，多了一个二极管导通压降），如果异步的明白那么同步的自然也明白了。
并且，根据这个拓扑推导的公式也是适用同步 Buck 的，只需要让公式里面的二极管压降为0即可

下图为同步Buck电路拓扑

![](https://pic.imgdb.cn/item/649848ba1ddac507ccb22f2e.jpg)

### **基本原理**

与Boost电路拓扑一样，上图中MOS管就是一个开关，只要开关速度够快（即开关频率够高），控制好导通与关断时间（即充放电时间），配合输出滤波电容，就可以得到基本稳定的 V~o~（即输出电压）了。

#### 过程分析

1. MOS管导通时

电路状态如下

![](https://pic.imgdb.cn/item/64985a001ddac507ccce7448.jpg)

开关导通时，二极管不导通，电感两端电压是 $V_i-V_o$ ，为恒定值。如果把电感电流向右定义为正，因为 $U_L=L{d_i\over d_t}=V_i-V_o$ ，有${d_i\over d_t}={V_i-V_o\over L}=常数$  

**所以电感电流是线性增大的**

2. MOS管截止时

电路状态如下

![](https://pic.imgdb.cn/item/64995db31ddac507cc20d29f.jpg)

在开关变为不导通的时候，由于电感两端电流不能突变，方向继续从左向右（如上图所示）所以会感应出电压，让二极管导通。

假设二极管导通电压是 V~d~，因为二极管阳极接地，所以阴极电压是-V~d~ ，因此电感两端电压是$-V_d-V_o$ 。同理，$U_L=L{d_i\over d_t}=-V_d-V_o$ ，有${d_i\over d_t}={-V_d-V_o\over L}=常数$ 此时电感电流是线性减小的

## 公式推导与理解

### **Buck伏秒平衡法则**

PS：具体推导请参考《Boost 升压电路的原理》，本文直接给出结论；有兴趣的读者可以自行推导
$$
\begin{cases}
	导通时：L{d_i\over d_t}=|V_i-V_o|=V_i-V_o
	\\
	截止时：L{d_i\over d_t}=|-V_d-V_o|=V_d+V_o
\end{cases}
$$


我们记

- 导通时间为	T~on~
- 截止时间为	T~off~
- 周期				T = T~on~ + T~off~ = 1/f

有
$$
(V_i -V_o)*T_{on}=(V_d+V_o)*T_{off}
$$
这就是Buck电路中的伏秒平衡法则

如果是同步Buck，只需将 $V_d=0$ 带入

有
$$
(V_i -V_o)*T_{on}=V_o*T_{off}
$$

### **元器件参数选型公式**

同样，推到前给出已知量和要求未知量，如下图

![](https://pic.imgdb.cn/item/649506c81ddac507cc0a9495.jpg)

#### 导通/截止时间与占空比

由Buck伏秒平衡法则，我们可以推出
$$
\begin{cases}
	导通时间：T_{on}=({V_{o}+V_d\over V_i+V_d})*{1\over f}
	\\
	截止时间：T_{off}={V_{i}-V_o\over V_i+V_d}*{1\over f}
	\\
	占空比：D={T_{on}\over T}={V_{o}+V_d\over V_i+V_d}
\end{cases}
$$
对于同步Buck电路，我们有
$$
\begin{cases}
	导通时间：T_{on}={V_{o}\over V_i}*{1\over f}
	\\
	截止时间：T_{off}={V_{i}-V_o\over V_i}*{1\over f}
	\\
	占空比：D={T_{on}\over T}={V_{o}\over V_i}
\end{cases}
$$
我们可以看出：**占空比只与输出输出电压有关，和其他量均无关**

#### 电感

##### 电感平均电流

我们知道，输出电压 V~o~ （理想情况下是基本不变的），即输出电容两端也如此，则电容的平均电流为0；由基尔霍夫电流定律，我们可知**电感的平均电流即平均输出电流 I~o~**

![](https://pic.imgdb.cn/item/649ce5151ddac507ccaf2409.jpg)

即电感平均电流 $I_L=I_o$

##### 电感纹波电流

纹波电流的大小就等于在开关导通时电感电流增大的值，也等于关断时电感电流减小的值。我们就计算一个周期导通时间电感电流的增加量即可知道电感纹波电流的大小
$$
根据~U=L{di\over dt}
\\
有~V_i\int _0 ^{T_{on}}1dt=L\int _0 ^{T_{on}}{di\over dt}dt
\\
即~L*\triangle I_L=V_i *T_{on}~有~\triangle I_L={V_i*T_{on}\over L}
\\
代入~T_{on}=({V_{o}+V_d\over V_i+V_d})*{1\over f}
\\
\triangle I_L={V_o+V_d\over f*L}*{{V_i-V_o\over V_i+V_o}}
$$
同步Buck的电流纹波为
$$
\triangle I_L={V_o\over f*L}*({1-{V_o\over V_i}})
$$
从中可以看出**电感电流的纹波和输出电流（负载电流）大小无关**

##### **电感的峰值电流**

电感的峰值电流计算公式有：
$$
I_{LP}=I_o+{\triangle I_L\over2}
$$
**电感选型时，电感的饱和电流必须大于这个 I~Lp~ ，并且要留一定的裕量**

##### **电感感值**

一般来说，让电感的纹波电流 $\triangle I_L$ 等于电感平均电流的$ 			20\%\sim40\% $之间即 $\triangle I_L=(0.2\sim0.4)*I_L$

结合上面的推导我们有
$$
\begin{cases}
	电感平均电流:I_L=I_o
	\\
	电感纹波电流:\triangle I_L={V_o+V_d\over f*L}*{{V_i-V_o\over V_i+V_o}}
	\\
	电流关系:\triangle I_L=(0.2\sim0.4)*I_L
\end{cases}
$$
所以我们可以得出**电感感值范围**
$$
L={V_o+V_d\over(0.2\sim0.4)*f*I_o}*{{V_i-V_o\over V_i+V_o}}
$$
同步Buck的感值选择
$$
L={V_o\over(0.2\sim0.4)*f*I_o}*{{V_i-V_o\over V_i+V_o}}
$$

#### 输入电容

**输入电容的作用主要是滤波和提供瞬时电流**

类比电感，应该可以理解**输入电容是控制输入电压纹波大小**的主要因素（不理解没关系，记住就好了），而**输入电压纹波主要分为两部分：一部分是电容充放电时，电容储存的电荷量的变化导致了电容上电压的变化（高中物理学过的电容定义式 $Q=C*U_C$ ）；另一部分是电容等效串联电阻ESR，电容充放电过程中有电流流过，则这部分也会产生一部分压降，我们记为 U~ESR~** 

所以输入电压纹波有
$$
\triangle V_i=U_C+U_{ESR}
$$

##### 电源平均输入电流

在往下学习前，我们先推导一下电源的平均输入电流，我们后面计算会用到

根据能量守恒定律，在不考虑MOS管损耗前提下，一个充放电周期里，电路上损耗器件只有续流二极管和负载；而二极管只在开关截止下导通有损耗，且流经二极管的电流等于电感上的电流等于输出电流 I~o~ 所以我们有
$$
\begin{cases}
	二极管功率:P_d=V_d*I_o*{T_{off}\over T}
	\\
	负载(输出)功率:P_o=V_o*I_o
	\\
	电源输入功率:P_i=Vi*I_i
\end{cases}
$$
由能量守恒定律有 $P_i=P_d+P_o$

我们有
$$
I_i={I_o\over V_i}*(V_o+V_d*{V_i-V_o\over V_i+V_d})
$$
同步Buck则有 $I_i={V_o\over V_i}*I_o$

（如果考虑效率则有 $I_i={V_o\over V_i*\eta}*I_o$）

##### 因电荷产生的电压纹波

如下图，由基尔霍夫电流定律知道，节点电流和为 0，并且（理想情况下）电源输入的电流恒定，那么当电感电流的变化量必然等于电容电流的变化量，因为最终三者的和为 0

![](https://pic.imgdb.cn/item/649cee041ddac507ccbf8f12.jpg)

三者电流波形如下

![](https://pic.imgdb.cn/item/649cee541ddac507ccc08df9.jpg)

我们知道，**一个周期内，输入电容充放电时变化的电荷量一定是一样的**，所以我们计算其中一个即可；显然输入电容在充电的时候更好计算，此时流经输入电容的电流就是恒定的输入电流 I~i~

由 $Q=I*T$ 

有
$$
U_C={Q\over C_i}={I_i*T_{off}\over C_i}
\\={I_o\over C_i*V_i*f}*(V_o+V_d*{V_i-V_o\over V_i+V_d})*{V_i-V_o\over V_i+V_d}
$$
同步Buck有
$$
U_C={I_o\over C_i*V_i*f}*V_o*{V_i-V_o\over V_i}
$$

##### 因ESR产生的电压纹波

我们只要知道输入电容上流过的电流，由 $U_{ESR}=I_C*ESR$ 即可得出ESR产生的电压纹波

下面我们分析一下流经输入电容的电流

- 开关截止

电源输入电流 I~i~ 全部进入输入滤波电容，因为 I~i~ 恒定，因此输入滤波电容的电流就是恒为 I~i~ ，此时电容充电，如果我们把充电电流方向定义为正，那么电流就是 I~i~ 

- 开关导通

电感原本从二极管续流，变成了从MOS 管续流，因为之前电感一直在放电，所以切换时电感电流最小，等于 $I_L-{\triangle I_L\over2}$ ，在整个 T~on~ 时间段内，电感是被充电的，所以电感电流一直在增直到达到峰值电流 $I_L+{\triangle I_L\over2}$ 

由基尔霍夫电流定律我们知道，输出电容的放电电流为$-(I_L+{\triangle I_L\over2}-I_i)$ (负号是因为方向与充电电流方向相反)

我们用充电时输入电容因ESR产生的电压纹波减去放电时输入电容因ESR产生的电压纹波即可得出一个周期输入电容因ESR产生的电压纹波大小
$$
U_{ESR}=(I_o+{V_o+V_d\over2*f*L}*{{V_i-V_o\over V_i+V_o}})*ESR
$$
同步Buck有
$$
U_{ESR}=\{I_o+{V_o\over2*f*L}*{(1-{V_o\over V_i}})\}*ESR
$$

##### **输入电容容值**

综上我们知道
$$
\triangle V_i=U_C+U_{ESR}
\\={I_o\over C_i*V_i*f}*(V_o+V_d*{V_i-V_o\over V_i+V_d})*{V_i-V_o\over V_i+V_d}+
\\(I_o+{V_o+V_d\over2*f*L}*{{V_i-V_o\over V_i+V_o}})*ESR
$$
同步Buck有
$$
\triangle V_i=U_C+U_{ESR}
\\={I_o\over C_i*V_i*f}*V_o*{V_i-V_o\over V_i}+\\\{I_o+{V_o\over2*f*L}*{(1-{V_o\over V_i}})\}*ESR
$$
考虑到我们的电容实际使用情况

- 陶瓷电容 ESR 小，容量小，U~C~ 对纹波起决定作用，所以可以近似 $\triangle V_i=U_C$

$$
陶瓷电容:
C_i≥{I_o\over \triangle V_i*V_i*f}*(V_o+V_d*{V_i-V_o\over V_i+V_d})*{V_i-V_o\over V_i+V_d}
\\
同步Buck:
C_i≥{I_o\over \triangle V_i *V_i*f}*V_o*{V_i-V_o\over V_i}
$$

- 铝电解电容ESR大，容量大，U~ESR~ 对纹波起决定作用，所以可以近似 $\triangle V_i=U_{ESR}$

$$
铝电解电容:
ESR\le {\triangle V_i\over I_o+{V_o+V_d\over2*f*L}*{V_i-V_o\over V_i+V_d}}
\\
同步Buck:
ESR\le {\triangle V_i\over I_o+{V_o\over2*f*L}*(1-{V_o\over V_i})}
$$

#### 输出电容

**输出电容的作用与输入电容类似，主要也是滤波和提供瞬时电流**

与输入电容类似，**输出电容控制着输出电压纹波大小，同样输出电压纹波也主要由电容电荷引起的纹波、ESR引起的电压纹波两部分构成**

相比输入纹波，我们更关心输出纹波的大小

##### 因电荷产生的电压纹波

我们研究输出节点（如下图），假设负载电流恒定，由基尔霍夫电流定律，我们可知电感电流的变化量一定等于电容电流的变化量

![](https://pic.imgdb.cn/item/649d91331ddac507ccd4c0cb.jpg)

画出电流波形，有

![](https://pic.imgdb.cn/item/649d91541ddac507ccd50527.jpg)

显然，电容电流大于0时，电容在充电，电容电流小于0时，电容在放电。并且图中也可以看到，**容充电和放电时间长度是一样的，都是周期的一半**

我们知道，电流是单位时间流过的电荷量，即 $Q=I*T$​ 输入电容上电荷总的变化量就是上图带颜色区域的面积，且已知三角形高是 $\triangle I_L\over 2$ 底是 $T\over2$ 
$$
U_C={Q\over C_o}={{1\over2}*{\triangle I_L\over 2}*{T\over2}\over C_o}
\\
={V_o+V_d\over8*f^2*C_o*L}*{V_i-V_o\over V_i+V_d}
$$
同步Buck有
$$
U_C={V_o\over8*f^2*C_o*L}*{V_i-V_o\over V_i}
$$

##### 因ESR产生的电压纹波

由前面的电流波形图我们知道，输出电容充电电流（方向规定为正方向）最大为 $\triangle I_L \over2$ 放电电流最大则为 $-\triangle I_L \over2$ 

所以ESR产生的电压纹波为
$$
U_{ESR}={\triangle I_L \over2}*ESR-(-{\triangle I_L \over2})*ESR
\\
=\triangle I_L *ESR
\\
={V_o+V_d\over f*L}*{V_i-V_o\over V_i+V_d}*ESR
$$
同步Buck有：
$$
U_{ESR}={V_o\over f*L}*{V_i-V_o\over V_i}*ESR
$$

##### **输出电容容值**

综上，我们有
$$
\triangle V_o=U_C+U_{ESR}
\\
={V_o+V_d\over f*L}*{V_i-V_o\over V_i+V_d}*(ESR+{1\over8*f*C_o})
$$
同步Buck有
$$
\triangle V_o=U_C+U_{ESR}
\\
={V_o\over f*L}*{V_i-V_o\over V_i}*(ESR+{1\over8*f*C_o})
$$
考虑到我们的电容实际使用情况
$$
陶瓷电容:
C_i≥{{V_o+V_d\over8*f^2*\triangle V_o*L}*{V_i-V_o\over V_i+V_d}}
\\
同步Buck:
C_i≥{{V_o\over8*f^2*\triangle V_o*L}*{V_i-V_o\over V_i}}
$$

$$
铝电解电容:
ESR\le {\triangle V_o*f*L\over V_o+V_d}*{V_i+V_d\over V_i-V_o}
\\
同步Buck:
ESR\le {\triangle V_o*f*L\over V_o}*{V_i\over V_i-V_o}
$$

## **PCB布局布线细节**

### 基本原则：

- **优先参照datasheet中推荐布局与layout要求**
- **尽量在一层走完线，保留一块完整的地平面**

### 芯片引脚设计：

- **输入电容 C~i~ 小容量靠近芯片VI和GND引脚，大容量远离芯片**
- **输出电容 C~o~ 大容量靠近芯片，小容量远离芯片**
- FB（反馈）距离SW远点，**输出信号经过输出电容后再经过反馈回路，反馈节点回流芯片的信号线要短且细**
- 小信号地连一起 (FB分压电阻，COMP，SS) 然后再与PGND单点相连，或者通过过孔连到背面
- **电感尽量采用一体成型屏蔽电感**
- 电感两个引脚间距离尽可能大一些，非一体成型电感最好把电感地下的铜挖空，还要让敏感电路和回路远离电感。特别是VO到FB的走线，特别要避开电感和二极管，**不能和电感平行**

### 降低EMI

- 优先选用晶圆倒装工艺的芯片（覆晶式（FCOL，Flip-Chip-On-Lead）封装）
- 传导EMI：添加 $\pi$LC 滤波电路；输入使用电感输出使用磁珠滤除高频干扰
- 辐射EMI：优化布局，减少 $di\over dt$ 回路，使干扰信号被地平面吸收

有兴趣的可以参考[视频](https://www.bilibili.com/video/BV1ef4y1n7x1/?spm_id_from=333.788&vd_source=f10f0062b3069d075458559da23d053d)

## 公式汇总

![](https://pic.imgdb.cn/item/649d9b3f1ddac507cce558ff.jpg)

![](https://pic.imgdb.cn/item/649d9b621ddac507cce59000.jpg)

## 结束语

关于Buck电路的理论学习到这里就暂时结束了，虽然大部分都是理想情况下元器件的电路分析，实际运用上会有大量寄生参数等不良影响导致结果与理论分析相差很远；

虽然上述推导的公式不能直接套用，但是经过理论的学习我们能大致明白电路在工作时的各元器件的工作状态与工作原理，对我们在后续电路测试与维护中有不可忽视的作用。但重要的还是多实践多积累，少点眼高手低纸上谈兵！

Buck电路模块相关设计可参考《如何设计芯片的外围电路》《基于远翔FP6151XR-G1的BUCK转压电路》