## Brep结构

B-Rep 可以分为拓扑结构和几何结构两个部分。拓扑结构定义了实体的各个不同层次以及它们之间的关系，几何结构专用于描述顶点、曲线或曲面的空间位置或形状，以这样的方式准确地记录实体的形状信息。

STEP文件中Brep拓扑结构包括了：ManifoldSolidBrep (即body)、shell、face、loop、edge、Vertex;

几何结构主要有Surface(Face的几何信息)、Curve(Edge几何信息)、Point(Vertex几何信息)；

![[Pasted image 20230605163912.png]]

## STEP数据行

\#id = type(name,value);
value 包含了子拓扑、相应几何结构与其它信息；

## 拓扑结构与STEP文件表示

描述实体的各个不同层次以及它们之间的关系，在项目中主要用指针类型存储相关数据结构[[项目/STEP/step项目.canvas|step项目]]。

### 1. ManifoldSolidBrep

即body，子类：包含的ClosedShell；

//MANIFOLD_SOLID_BREP ( 'name', CLOSED_SHELL ) ;
#1024 = MANIFOLD_SOLID_BREP ("NONE", #1023);

### 2. Shell

主要包括OpenShell 和CloseShell两类
子类：包含的的拓扑面；

//StepClosedShell（String name, vector<StepFace*>）
#1023 = CLOSED_SHELL ("NONE", (#630, #678, #750, #798, #846, #894));

### 3. AdvancedFace

面是由面边界（faceBound）和几何曲面（surface）构成的拓扑实体，
子类：FaceBound、Surface、sameSense属性：用来判断几何曲面法线方向与Face方向是否一致；

//StepAdvancedFace(String name, vector<StepFaceBound*>, StepSurface*, bool sameSense)
#630 = ADVANCED_FACE ("NONE", (#624, ), #629, .T.);

### 4.FaceBound

包括FaceInnerBound（面内边界）与FaceOuterBound（面外边界）两类？
子类：loop、sameSense：用来判断是否与环的方向一致；

//FaceBound(Loop*, bool orientation)
#15=FACE_BOUND('',#26,.T.)
#58 = FACE_OUTER_BOUND ( 'NONE', #49, .T. )

### 5. Loop

Edge_loop 子类：OrientedEdge

//StepEdgeLoop(name,vector<StepOrientedEdge*>)
#106 = EDGE_LOOP ( 'NONE', ( #29, #20 ) ) ;

### 6.Edge

![[Pasted image 20230605175205.png|400]]

OrientedEdge有向边：
子类：曲线边、**标识符？**
#20 = ORIENTED_EDGE ( 'NONE', \*, \*, #92, .T. ) ;
//StepOrientedEdge(name,StepVertex*,StepVertex*,StepEdge*,bool)

EdgeCurve
子类：起点终点与曲线几何（Curve）、**标识符？**
#92 = EDGE_CURVE ( 'NONE', #119, #115, #21, .T. ) ;
StepEdgeCurve(name,StepVertex*,StepVertex*,StepCurve*, bool sameSense)

### 7. Vertex

VertexPoint 子类：笛卡尔点
#82 = VERTEX_POINT ( 'NONE', #78 ) -->（name，StepPoint*）

## 几何结构

### point

![[Pasted image 20230605190833.png]]

笛卡尔点CartesianPoint：
//StepCartesianPoint(name,vector<double> m_Coordinates)--->Point3d(real x,real y,real z)
#30 = CARTESIAN_POINT ( 'NONE',  ( 0.0, 10.0, 0.0 ) )

