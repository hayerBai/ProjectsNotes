## 问题1 

标头一大串在解析过程中存储为啥，还是删了不要了，具体代码在哪里体现和处理？

## 问题2

	wstring lineInMultiLine = result[j];
	//判断最后一个字符是否为逗号,如果不是逗号并且不是分号，则加上逗号
	if (isInsertComma(line)) {
		line += _T(",") + lineInMultiLine;
	}
	else {
		line += lineInMultiLine;
	}

	if (true == IsMultiLineDataItemEnd(lineInMultiLine))
	{
		break;
	}

这种特例，为什么，是数据少一个符号吗


	/如果 读取的 id 不等于 预期 id 则需要补全

文件缺失，少编号？

## 问题3

DataItem中  曲线的一种特殊情况曲面的一种特殊情况   处理，为什么需要特殊处理

## 问题4

StructedStepFile::FillNonExistDataItem

## 问题5

	uint32_t Lump::SetOwner(Body* body)
	{
	    if (body == nullptr) {
	        return ErrorCode::RET_NULL_PTR;
	    }
	    ownerPtr_ = body;
	    return ErrorCode::RET_OK;
	}

干啥用的？ uint32_t ？？ErrorCode::RET_NULL_PTR;？？

## 问题6

	Body* body = new Body();
	body->SetContainLump(model);
	model->SetOwner(body);

为什么还要再构造一个body,调用对外接口不就行了吗

## 问题7

	SplitBy(value, _T(","));

分割成什么样子？ \_T是啥意思；

pShell = ParseClosedShell(id - 1);   id-1???

pShell->SetNext(pShell);？？？

## 问题8

问什么要构建SubShell；

## 问题9

step存储结构：

//MANIFOLD_SOLID_BREP ( 'name', CLOSED_SHELL ) ;
#1024 = MANIFOLD_SOLID_BREP ("NONE", #1023);

//StepClosedShell（String name, vector<StepFace*>）
#1023 = CLOSED_SHELL ("NONE", (#630, #678, #750, #798, #846, #894));

//StepAdvancedFace(String name, vector<StepFaceBound*>, StepSurface*, bool sameSense)
#630 = ADVANCED_FACE ("NONE", (#624, ), #629, .T.);

#15=FACE_BOUND('',#26,.T.) 

#58 = FACE_OUTER_BOUND ( 'NONE', #49, .T. )
//StepFaceOuterBound(String name, StepLoop*, bool orientation)

#106 = EDGE_LOOP ( 'NONE', ( #29, #20 ) ) ;
//StepEdgeLoop(name,vector<StepOrientedEdge*>)

#20 = ORIENTED_EDGE ( 'NONE', \*, \*, #92, .T. ) 
//StepOrientedEdge(name,StepVertex*,StepVertex*,StepEdge*,bool)

#92 = EDGE_CURVE ( 'NONE', #119, #115, #21, .T. ) ;
//StepEdgeCurve(name,StepVertex*,StepVertex*,StepCurve*, bool sameSense)

#82 = VERTEX_POINT ( 'NONE', #78 ) -->（name，StepPoint*）

#30 = CARTESIAN_POINT ( 'NONE',  ( 0.0, 10.0, 0.0 ) )
//StepCartesianPoint(name,vector<double> m_Coordinates)--->Point3d(real x,real y,real z)


几何信息&拓扑信息？

拓扑结构与几何结构有什么关系

Brep