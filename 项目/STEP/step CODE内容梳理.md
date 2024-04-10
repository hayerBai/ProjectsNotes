# Function
## main
1. Body* CreateCirCularColumn()
	//创造圆柱体Body，
	Point3D* 三维坐标点；
	ConsPoint* (Point3D*)
	ModelPoint* (ConsPoint*)
	Vertex* (ModelPoint*)
	Vector3D* (1, 0, 0)
	EntityUcs*(Vector3D* , double);
	ConsCircle*(EntityUcs*,1)
	ModelCircle*(ConsCircle*)
	Edge*(Vertex*, Vertex*, ModelCircle*)
	//圆柱边界
	ConsLine*(Point3D*, Vector3D*)
	构造函数ConsLine*->SetInterval(0, 1);
	Vertex*->SetEdge(Edge*);
	……
2. Body* CreateRectangle()
	//创造长方体Body，

## StepExample
// Input
1. void StepFileLoad(const String& p_Path);
	//判断路径是否为空，建立StructedStepFile，打印stepFile.GetDataCount();
	StepFile 结构体StructedStepFile？
	stepFile.GetDataCount()
	
2. Body* StepFileLoadAndParse(const String& p_Path);
	//判断路径是否为空，建立StructedStepFile，加载路径文件，
	//利用文件建立StructedStepFileParser，parser
	//利用函数ParseAndCreateManifoldSolidBrep()将结构体parser转为body结构体
	StructedStepFile
	StructedStepFileParser
	ParseAndCreateManifoldSolidBrep()
	
// <font color="yellow">Output 输出 study ofstream</font>
void WriteBegin(ofstream& ofs);
void WriteBound(ofstream& ofs);
void WriteEnd(ofstream& ofs);
void printStepFile(string savepath, Body* body);

## steptool
	//对一个宽字符串进行分割
	std::vector<String> splitWs(const std::wstring& s, Char delim);
	void splitw(const String& s, Char delim, std::vector<String>& elems);

	//将一个string 转换成wstring
	String string2wstring(const string& s);

## StringUtility
	bool IsZero(real p_Value, real p_Tolerance = 1e-6);		//在容差范围内，检测 p_Value 是否为 0.0
	bool IsNotZero(real p_Value, real p_Tolerance = 1e-6);	//在容差范围内，检测 p_Value 是否不为 0.0
	bool IsLess(real p_Value, real p_Target, real p_Tolerance = 1e-6);
	string IntToString(int int_num);
	string DoubleToString(double double_num);
	StringArray SplitBy(const String& p_String, Char p_Split);
	StringArray SplitBy(const String& p_String, const vector<size_t>& p_SplitPosition);
	//	StringArray SplitBy(const String& p_String, const vector<Char>& p_Splits);
	
	StringArray SplitBy(const String& p_String, const String& p_Split);
	StringArray SplitBy(const String& p_String, const vector<String>& p_Splits);

	String RemoveSuccessive(const String& p_String, Char p_Char);

	bool IsBeginWith(const String& p_String, const String& p_Begin);

	vector<size_t> FindNotInTagRange(const String& p_String, const array2<Char>& p_Tag, Char p_Char);

## StructedStepFile
	bool IsDataSectionBegin(const String& p_Line);
	bool IsSingleLineDataItem(const String& p_Line);
	bool IsSectionEnd(const String& p_Line);

	bool IsMultiLineDataItemBegin(const String& p_Line);
	bool IsMultiLineDataItemEnd(const String& p_Line);

	DataItemInfo NormalizeDataItem(String& p_Line);
	StructedStepFileDataItem* ParseStructedDataItem(const DataItemInfo& p_Info, const String& p_DataItem);

	void FillNonExistDataItem(size_t p_FirstDataItemId);

	bool isInsertComma(String& lineInMultiLine);

	String GetB_SplineCurveValue(String& stepValueStr);
	String GetB_SplineSurfaceValue(String& stepValueStr);

## StructedStepFileDataItem
	//构造函数
	StructedStepFileDataItem(const size_t p_StepId, const String& p_StepType, const String& p_Value);
	bool IsNull() const { return m_Value.empty(); }

## StructedStepFileParser



# Class

# 流程
1. main：输入filepath ->StepFileLoadAndParse
