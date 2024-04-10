# class :: StructedStepFile
继承 TextFile
class StructedStepFileDataItem;
## class StructedStepFileDataItem
	const size_t m_StepId;//编号
	const String m_StepType;//类型
	const String m_Value;//值

数据protected：

	vector<StructedStepFileDataItem*> m_Datas;
	vector<StructedStepFileDataItem*> m_OtherEntities;  // 存放除拓扑、几何之外的数据 

//暂时性的
	protected: 
	
		vector<size_t>  m_ManifoldSolidBrepIndexs; 
		//得到ManifoldSolidBrep的索引值数组
		vector<size_t> m_ShellBasedSurfaceModelIndexs; //ShellBasedSurfaceModel 的索引值	

# functions
## Load

## LoadParse

## isInsertComma

判断每行最后是否需要插入逗号：

	char = line.length()-1;
	char = space : 前有"(", return F; 无，return T;
	char = , / ; / ( , return F;
	others : return T.

## \[\] 重载

	return \*(m_Datas.at(p_Index));

## IsDataSectionBegin

判断是否为 *数据头* ：

	//共 2 个条件来确定是 数据头， 等更多例子调试
	//1. 非空
	//2. 起始字符串为 _T("DATA;") //这里暂时不要求绝对等于， 主要防止有空格等不标准表示
	return (false == p_Line.empty()) && (0 == p_Line.compare(_T("DATA;")));

## IsSingleLineDataItem

判断是否为 *单行* 数据项：
	//共 3 个条件来确定是 单行 数据项， 等更多例子调试
	//1. 非空 //结合后 2 个条件， 用 2 <= p_Line.size() 替代非空
	//2. 起始字符为 #
	//3. 中止符为 ;

## IsSectionEnd

判断是否为 *数据尾* ：
	//共 2 个条件来确定是 SectionEnd， 等更多例子调试
	//1. 非空
	//2. 起始字符串为 _T("ENDSEC;") //这里暂时不要求绝对等于， 主要防止有空格等不标准表示

## IsMultiLineDataItemBegin

判断多行数据项开头

## IsMultiLineDataItemEnd

判断多行数据项结尾

## NormalizeDataItem()

对数据项的识别与预处理；
StructedStepFile::DataItemInfo StructedStepFile::NormalizeDataItem(String& p_Line)
返回类型：DataItemInfo

## ParseStructedDataItem()

解析 DataItem ；
StructedStepFileDataItem* StructedStepFile::ParseStructedDataItem(const DataItemInfo& p_Info, const String& p_DataItem)
