<center>
## IGS 文件数据结构
</center>

### 1. 文件数据结构

IGES文件是基于 ASCII 文本的格式，每行正好有 80 个字符长。分为五个部分，由第 73 列中的特定大写字母表示。分别是：

（1）标志（FLAG)段；  
（2）开始（START）段；  
（3）全局（GLOBAL）段；  
（4）元素索引（DIRECTORY ENTRY）段；  
（5）参数数据（PARAMTER DATA）段；  
（6）结束（TERMINATE）段。

### 2. 开始段（Start Section）

文件开始段可供人阅读的有关该文件的一些前言性质的说明，如下所示。在第1~72列上可以写入任何的内容的ASCII码字符。“开始”部分必须至少有 1 行，可以为空。

	SolidWorks IGES file using analytic representation for surfaces         S      1

### 3. 全局段（Global Section）

文件的全局段包含由前置处理器写入、后置处理器处理该文件所需的信息。它描述了IGES文件在使用的参数分隔符、记录分隔符、文件名、IGES版本、直线颜色、单位、建立该文件的时间、作者等信息。详细说明见下表：

| 索引 | 类型 | 描述 |
|:----------------:|:----------------:|:----------------:|
|1 | 字符串 | 参数分隔符（缺省为逗号）| 
|2 |字符串 |记录分隔符（缺省为分号）|
|3 | 字符串 |发送系统产品ID |
|4 |字符串 |文件名 |
|5 |字符串 |系统ID |
|6 |字符串 |前置处理器版本 |
|7 |整数 |整数的二进制表示位数 |
|8 |整数 |发送系统单精度浮点数十进制最大幂次|
|9    |整数  |发送系统单精度浮点数有效位数  |
|10 | 整数  |发送系统双精度浮点数十进制最大幂次  |
|11 | 整数  |发送系统双精度浮点数有效位数  |
|12 | 字符串  |接收系统产品ID |
|13 | 实数  |模型空间比例  |
|14 | 整数 | 单位标志 |
|15 | 字符串  |单位  |
|16 | 整数  |直线线宽的最大等级  |
|17 | 实数  |最大直线线宽  |
|18 | 字符串  |交换文件生成的日期和时间| 
|19 | 实数 | 用户设定的模型等级的最小值  |
|20  |实数  |模型的近似最大坐标值  |
|21  |字符串  |作者名  |
|22  |字符串  |作者单位  |
|23  |整数  |对应于创建本文件的IGES标准版本号的整数  |
|24  |整数  |绘图标准  |
|25  |字符串  |创建或最近修改模型的日期和时间 |

文件生成的日期和时间格式为13HYYMMDD.HHNNSS，其中13表示字符串长度，“H”表示字符串，YY年数的末两位 HH 小时（00－23）MM 月（01－12）NN 分钟（00－59）DD 日（01－31）SS 秒（00－59） 。

例： 

	1H,,1H;,17Htest1_cube.SLDPRT,38HE:\PROJECT\simple_model\test1_cube.igs, G      1
	15HSolidWorks 2021,15HSolidWorks 2021,32,308,15,308,15,17Htest1_cube.SLDG      2
	PRT,1.,2,2HMM,50,0.125,13H230731.100507,1E-08,499990.,6Hbayiem,,11,0,   G      3
	13H230731.100507;                                                       G      4

### 3. 元素索引（DIRECTORY ENTRY）

“数据索引”和“数据参数”部分包含有关 IGES 文件格式的基本数据的信息：它是实体。IGES 中大约有 150 种不同的已定义图元（包括某些图元类型的不同“形式”）。实体在“数据索引”部分中描述，如下所示：

	    1        2       3       4       5       6       7       8       9      10
	
	   116       1       0       1       0       0       0       0       1D      1
	   116       1       5       1       0                               0D      2

首先，该文件列出了它在“数据索引”部分中包含的所有实体。此部分由第 73 列中的 D 指定，并列出有关它所描述的实体的属性。索引段中的每一行被拆分为 10 个 8 个字符的字段，每个实体都有该部分的两行。这表示每个实体在“数据索引”部分中都有 20 个字段，这些字段通常是右对齐的。这些字段映射到以下属性：

|    **1**    |             **2**             |            **3**            |          **4**          |               **5**              |
|:-----------:|:-----------------------------:|:---------------------------:|:-----------------------:|:--------------------------------:|
|    列 1-8   |            列 9-16            |           列 17-24          |         列 25-32        |             列 33-40             |
| Entity Type |           PD pointer          |          Structure          |    Line Font Pattern    |               Level              |
| Entity Type |       Line Weight Number      |         Color Number        |   Parameter Line Count  |            Form Number           |
|    **6**    |             **7**             |            **8**            |          **9**          |              **10**              |
|   列 41-48  |            列 49-56           |           列 57-64          |         列 65-72        |             列 73-80             |
|     View    | Transformation matrix pointer | Label Display Associativity |      Status Number      | Section Code and Sequence Number |
|   Reserved  |            Reserved           |         Entity Label        | Entity Subscript Number | Section Code and Sequence Number |

数据解析如下：

![[微信图片_20230807101229.jpg]]
![[微信图片_20230807101405.jpg]]
### 4.参数数据段（Parameter Data）

该段记录了每个元素的几何数据，其格式是不固定的。根据每个元素参数数据的多少，决定它在参数数据段中有几行。格式如下图所示，其中DE为该元素在元素索引段中的元素索引的开始行号。

类型解析参考：[IGES文件解析1——几何图元_iges文件格式_3333yyt的博客-CSDN博客](https://blog.csdn.net/younggung/article/details/128990985)

#### 圆弧（100型）

	100,20000.,0.,0.,20000.,0.,-20000.,0.;                                11P     94
	
	Z：XT，YT 平面上的 z 位移
	X：中心坐标x
	Y：中心坐标y
	X1：起始点x坐标
	Y1：起始点y坐标
	X2：终止点x坐标
	Y2：终止点y坐标

#### 复合曲线（102型）

	102,2,5,9;                                                            13P     95	
	
	N：构成此图元的曲线数
	DE(1)：指向第一条曲线的索引
	...
	DE(N)：指向最后一条曲线的索引

#### 线（110型）

	110,-20000.,0.,20000.,-20000.,0.,0.;                                  41P    197
	
	X1	起点x 坐标
	Y1	起点的 y 坐标
	Z1	起点的Z坐标
	X2	终点 x 坐标
	Y2	终点的 y 坐标
	Z2	终点的Z坐标

#### 旋转表面（120型）

	120,39,41,0.,6.28318530717959;                                        43P    198
	
	Axis：描述旋转轴的指向线的指针
	Surface：指向通用实体的指针
	SA：起始角度（弧度）
	EA：终止角度（弧度）

#### 变换矩阵（124型）

通过[矩阵乘法](https://so.csdn.net/so/search?q=%E7%9F%A9%E9%98%B5%E4%B9%98%E6%B3%95&spm=1001.2101.3001.7020)和向量加法转换实体以给出变换，如下所示：

	124,-1.60812264967664E-16,-1.,0.,10000.,6.12323399573677E-17,          5P      3
	-9.84691127781427E-33,-1.,10000.,1.,-1.60812264967664E-16,             5P      4
	6.12323399573677E-17,1.60812264967664E-12;                             5P      5
	
	         | R11  R12  R13 |          | T1 |              
		R =  | R21  R22  R23 |     T =  | T2 |       
	         | R31  R32  R33 |          | T3 |           
       ET = RE + T, where E is the entity coordinate 

#### 有理B样条曲线（126型）

	126,1,1,1,0,1,0,0.,0.,1.,1.,1.,1.,3.141592644,3.141592654,0.,         23P     21
	3.141592644,0.,0.,0.,1.,0.,0.,1.;                                     23P     22
	
	K：控制点个数-1
	M：基函数阶数	
	Flag1：0=非平面，1=平面
	Flag2：0=开曲线，1=闭曲线
	Flag3：0=有理数，1=多项式
	Flag4：0=非周期性，1=周期性
	T1：节点序列的第一个值
	...
	T(1 + K + M)]：结序列的最后一个值
	W0：第一个控制点的权重
	...
	WK：最后一个控制点的 z
	X0：第一个控制点的 x
	ZK：最后一个控制点的 z
	V0：起始参数值
	V1：结束参数值
	XN：单位法向 x（如果是平面）
	YN：单位法向 y（如果是平面）
	ZN：单位法向 z（如果是平面）

#### 有理B样条曲面（128型）

	128,1,1,1,1,0,0,1,0,0,0.,0.,1.,1.,0.,0.,1.,1.,1.,1.,1.,1.,             3P      2
	-20240.,-20240.,20000.,20240.,-20240.,20000.,-20240.,20240.,           3P      3
	20000.,20240.,20240.,20000.,0.,1.,0.,1.;                               3P      4
	
	K1：第一方向上的控制点个数；
	K2：第二方向上的控制点个数；
	M1：第一方向基函数的阶数；
	M2：第二方向基函数的阶数；
	Flag1：0 = 第一方向关闭，1 = 未关闭
	Flag2：0 = 第二方向闭合，1 = 未闭合
	Flag3：0 = 有理数，1 = 多项式
	Flag4：0 = 第一方向非周期，1 = 周期性
	Flag5：0 = 第二方向上的非周期性，1 = 周期性
	T1(0)：第一方向上结序列的第一个值
	...
	T1(1 + K1 + M1)：第一方向上结序列的最后一个值
	...
	T2(0)：第二方向上结序列的第一个值
	...
	T2(1 + K2 + M2)：第二方向上结序列的最后一个值
	W(0, 0)：第一个控制点权重
	...
	W(K1, K2)：最后一个控制点权重
	X(0, 0)：第一个控制点
	K(K1)(K2)：最后一个控制点
	U0：开始第一个参数值
	U1：结束第一个参数值
	V0：开始第二个参数值
	V1：结束第二个参数值

#### 参数曲面上的曲线（142型 ）

	142,1,3,13,15,1;                                                      17P     97
	
	Flag1：指示曲线的创建方式：0 = 未指定，1 = 投影，2 = 曲面相交，3 = 等参数曲线；
	Surface：曲线所在曲面的元素索引位置；
	Curve：参数域上边界曲线的元素索引位置；
	Mapping：空间域上边界曲线的元素索引位置；
	Representation：曲线的首选表示形式：0 = 未指定，1 = S(B(t))，2 = C(t)，3 = 两者相等

#### 裁剪曲面（144型）

	144,3,1,0,17;                                                         19P     98
	
	Surface:要修剪的曲面图元索引；
	Flag：0=边界是曲面的边界，1=边界不是曲面边界；
	N：构成内边界的闭合曲线数；
	OuterBound：指向外边界的参数化曲面（类型 142）图元上的曲线索引；
	Inner1：指向第一个内边界曲线索引；
	...
	InnerN：指向最后一个内边界曲线索引；

#### 颜色定义（314型）

用于在RGB颜色空间中为其他实体提供自定义颜色。

	314,79.2156862745098,81.9607843137255,93.3333333333333,;               1P      1
	
	RED：红，0-100
	GREEN：绿，0-100
	BLUE：蓝，0-100
	Name：可选的颜色名

### 5. 结束段

结束段只有一行，在前32个字符里，分别用8个字符记录了开始段、全局段、元素索引段和参数数据段的段码和每段的总行数。第33~72个字符没有用到。最后8个字符为结束段的段码和行数。

