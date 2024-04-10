### 一、 功能总体描述

- **IGES文件读取**：能够打开和读取IGES格式的文件，该文件可能包含复杂的三维几何数据和其他CAD信息。
- **数据解析**：将IGES文件中的数据解析为本项目几何结构与拓扑结构，以便后续程序能够使用这些数据进行进一步的处理。
- **实体识别**：能够识别IGES文件中的实体类型，如点、直线、曲线、曲面、体素等。
- **属性提取**：从IGES文件中提取实体的属性信息，如颜色、层次、线型等。
- **坐标转换**：将IGES文件中的数据从其原始坐标系转换到目标坐标系，以适应应用程序的需求。
- **拓扑修复**：修复face拓扑层以下的拓扑关系，使面得以正确离散与显示。
- **性能优化**：优化解析算法和数据结构，以确保高效的文件解析和数据存储。

### 二、整体设计思路与处理流程

![[IGES_Reader流程图.png]]
### 二、IGES读取与解析类与功能解析

### 1.  IGES_Reader实现类

在本模块，为完成IGES读取与解析的总体功能，设计如下几个类：

1. 文件读取缓冲区类：FileLineBuffer
2. 文件读取与解析类：IgsReader
3. 起始段数据结构类：IgsStartSection
4. 全局段数据结构类：IgsGlobalSection
5. 数据索引与参数数据段数据结构类：IgsDirAndParaSection
6. Nurbs曲线处理解析类：Igs126Type
7. Nurbs曲面处理解析类：Igs128Type
8. 旋转曲面处理解析类：Igs120Type
9. 功能函数类：IgsFunction

### 2. 文件读取缓冲区类：FileLineBuffer

主要功能：读取文件，设置一个自定义（默认128行）的行读取缓冲区，能够调取后几行信息，可以增加并行处理功能。

#### 3. 文件读取与解析类：IgsReader

##### （1）主要功能

文件读取与解析主线类，主要功能如下：
- 读取文件、创建IgsStartSection、IgsGlobalSection、IgsDirAndParaSection类来暂存读取内容；
- 获取解析不同几何图元的数据结构；
- 构建正确的几何与拓扑结构；
- 矩阵变换与拓扑修复。

##### (2) 对象

- **IgsStartSection** igsStart: 存储起始段数据；
- **IgsGlobalSection** igsGlobal:  存储全局段数据；
- **unorder_map<int, IgsDirAndParaSection>** IgsDirAndParaMap: 存储数据索引与参数数据段与数据ID哈希表；
- **vector<\int>**  type144IdArray: 记录裁剪曲线ID；
- 参数分节符与记录分节符；
- 文件各数据段行数；
- **unorder_map<int, share_ptr<\Igs128Type>>**  记录完成解析的B样条曲面与ID哈希表；
- **unorder_map<int, share_ptr<\Igs120Type>>**  记录解析过的旋转曲面与ID哈希表；

##### (2) 方法

#### 4.  Nurbs曲面处理解析类：Igs128Type

##### （1）主要功能

- 判断是否为平面，解析为平面、B样条曲面、Nurbs曲面；解析获取该平面(有界曲面）Loop。

##### (2) 对象

##### (2) 方法

#### 5.  Nurbs曲线处理解析类：Igs126Type

##### （1）主要功能

- 判断是否为直线，解析为直线、B样条曲线、Nurbs曲线；*（若有需要待补充：解析为圆弧、椭圆、双曲线、抛物线）*
##### (2) 对象

##### (2) 方法

#### 5.  旋转曲面处理解析类：Igs120Type

##### （1）主要功能

- 构建旋转面，获取旋转面surface、获取旋转面Loop;
##### (2) 对象

##### (2) 方法