# StructedStepFileParser
## objects:
1. const StructedStepFile& m_File;// 输入
2. Body* m_pManifoldSolidBreps; // 输出
3. unordered_map<size_t, void*>  m_TranslatedItemInfo; //（数组类对象的索引，类对象的地址）

## member functions:

### Body* ParseAndCreateManifoldSolidBrep();
//对外接口

//Topology

	Lump* ParseManifoldSolidBrep(size_t p_Index);
	Shell* ParseShell(size_t p_Index);
	Shell* ParseClosedShell(size_t p_Index);
	Shell* ParseOpenShell(size_t p_Index);
	Face* ParseFace(size_t p_Index);
	Face* ParseAdvancedFace(size_t p_Index);
	Loop* ParseFaceBound(size_t p_Index);
	Loop* ParseFaceInnerBound(size_t p_Index);
	Loop* ParseFaceOuterBound(size_t p_Index);
	Loop* ParseLoop(size_t p_Index);
	Loop* ParseEdgeLoop(size_t p_Index);
	Coedge* ParseOrientedEdge(size_t p_Index);
	Edge* ParseEdge(size_t p_Index);
	Edge* ParseEdgeCurve(size_t p_Index);
	Vertex* ParseVertex(size_t p_Index);
	Vertex* ParseVertexPoint(size_t p_Index);

# 构造过程：

根据编号与类型逐步构造：

### Body* ParseAndCreateManifoldSolidBrep()

在StructedStepFile中存储了ManifoldSolidBrep的编号信息；调取manifoldSolidBrepIds的数组；
flag 标志位是false的话，就说明第一次构造，构造Lump指针类型首先指向SetOwner上一层拓扑结构，StructedStepFileParser这个类会初始化一个Body* m_pManifoldSolidBreps;的指针类型。
SolidBrep其他编号同样的也是会指向上一层Owner，上一个编号lump也会有一个next指针指向这个lump;

###  Lump* ParseManifoldSolidBrep(size_t p_Index)

流程：

1. 判断是否已经解析
2. 构造 new Lump
3. 获取属性字符串，即value
4. 分割字符串
5. 定义Shell*,pShell = ParseClosedShell(id - 1);
6. 指针指向owner&r_pLump->SetShell(pShell);


### Shell* ParseClosedShell(size_t p_Index)

1. 判断是否已经解析过
2. 根据 value 分割结果构建参数
3. 循环构建 face，类比构建lump

### Shell* ParseShell(size_t p_Index)

分类解析OPEN_SHELL、CLOSED_SHELL

### Shell* ParseOpenShell(size_t p_Index)

1. 获取属性字符串
2. 分割结果构建参数
3. 构建face对象

