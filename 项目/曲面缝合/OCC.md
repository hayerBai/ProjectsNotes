# 基础类

## handle

是一种智能指针，用于管理指向OCC中各种对象的指针，如几何形状、拓扑结构等。使用`Handle`类可以有效地管理对象的生命周期，防止内存泄漏，并且简化对象之间的共享和传递。

1. **自动内存管理**：通过引用计数机制自动管理对象的生命周期。当某个对象不再被任何`Handle`引用时，该对象会被自动销毁，从而避免内存泄漏。
2. **类型安全**：`Handle`类是模板化的，可以为不同的OCC对象类型提供专用的`Handle`，从而在编译时期提供类型检查，增加代码的安全性。
3. **易用性**：相比直接使用裸指针，`Handle`提供了更加方便的接口来访问和操作对象，使得开发者可以更加专注于业务逻辑的实现。
## TopExp_Explorer

用于遍历拓扑结构中的各种元素。
```
TopoDS_Shape shape; // 假设这是一个已经定义并初始化了的形状
TopExp_Explorer explorer;
for (explorer.Init(shape, TopAbs_FACE); explorer.More(); explorer.Next()) {
    TopoDS_Face face = TopoDS::Face(explorer.Current());
    // 在这里处理面（face）
}
```
## STEPControl_Reader

读取外部存储的 STEP 文件。处理文件中几何与拓扑结构，转换为 OCC 内部数据结构。

```
#include <STEPControl_Reader.hxx>
#include <TopoDS_Shape.hxx>
#include <BRepTools.hxx>
#include <iostream>
int main() {
    // 创建 STEPControl_Reader 对象
    STEPControl_Reader reader;

    // 读取 STEP 文件
    IFSelect_ReturnStatus status = reader.ReadFile("/path/to/your/stepfile.stp");

    // 检查文件是否成功读取
    if (status != IFSelect_ReturnStatus::IFSelect_RetDone) {
        std::cerr << "Error reading STEP file." << std::endl;
        return 1; // 读取文件失败
    }

    // 将读取的数据转换为 OCC 的数据结构
    Standard_Integer num = reader.TransferRoots();

    // 检查转换的形状数量
    std::cout << "Number of shapes read: " << num << std::endl;

    // 获取转换后的形状
    TopoDS_Shape shape = reader.OneShape();

    // 可以进一步处理 shape，例如检查其属性或进行几何操作

    // 将导入的形状保存到一个新的 BREP 文件中（可选）
    BRepTools::Write(shape, "/path/to/your/outputfile.brep");

    return 0; // 程序成功结束
}
```
# 缝合类
## **BRepBuilderAPI_Sewing**

用于在Open CASCADE Technology (OCC) 中进行曲面缝合操作的类。这个类提供了一系列成员变量和成员函数，用于控制缝合操作的细节，并处理缝合过程中产生的数据。

### 成员变量
```
protected:
  Standard_Real myTolerance;
  Standard_Boolean mySewing;
  Standard_Boolean myAnalysis;
  Standard_Boolean myCutting;
  Standard_Boolean myNonmanifold;
  TopTools_IndexedDataMapOfShapeShape myOldShapes;
  TopoDS_Shape mySewedShape;
  TopTools_IndexedMapOfShape myDegenerated;
  TopTools_IndexedMapOfShape myFreeEdges;
  TopTools_IndexedMapOfShape myMultipleEdges;
  TopTools_IndexedDataMapOfShapeListOfShape myContigousEdges;
  TopTools_DataMapOfShapeShape myContigSecBound;
  Standard_Integer myNbShapes;
  Standard_Integer myNbVertices;
  Standard_Integer myNbEdges;
  TopTools_IndexedDataMapOfShapeListOfShape myBoundFaces;
  TopTools_DataMapOfShapeListOfShape myBoundSections;
  TopTools_DataMapOfShapeShape mySectionBound;
  TopTools_IndexedDataMapOfShapeShape myVertexNode;
  TopTools_IndexedDataMapOfShapeShape myVertexNodeFree;
  TopTools_DataMapOfShapeListOfShape myNodeSections;
  TopTools_DataMapOfShapeListOfShape myCuttingNode;
  TopTools_IndexedMapOfShape myLittleFace;
  TopoDS_Shape myShape;
  Handle(BRepTools_ReShape) myReShape;

private:
  Standard_Boolean myFaceMode;
  Standard_Boolean myFloatingEdgesMode;
  Standard_Boolean mySameParameterMode;
  Standard_Boolean myLocalToleranceMode;
  Standard_Real myMinTolerance;
  Standard_Real myMaxTolerance;
  TopTools_MapOfShape myMergedEdges;
  ```
#### 控制和配置变量

- **<u>myTolerance</u>**：这个变量定义了缝合操作中可以接受的最大容差。在确定两个形状是否足够接近以便进行缝合时，将使用这个容差值。
- **mySewing**：指示是否执行缝合操作。
- **myAnalysis**：指示是否对形状进行分析，以发现可能的缝合边界。
- **myCutting**：控制是否应剪切自由边，这是处理形状边界时的一个操作。
- **myNonmanifold**：标志是否允许非流形（non-manifold）拓扑存在于缝合结果中。
#### 数据结构

- <u>**myOldShapes** 和 **mySewedShape**</u>：分别存储缝合前的原始形状和缝合后的结果形状。
- **myDegenerated**, **myFreeEdges**, **myMultipleEdges**, **myContigousEdges**：这些索引映射和列表分别用于追踪缝合过程中识别的退化形状、自由边、多重边和共享边。
- **myBoundFaces**, **myBoundSections**, **mySectionBound**, **myVertexNode**, **myVertexNodeFree**, **myNodeSections**, **myCuttingNode**：这些数据结构用于管理缝合过程中的各种拓扑关系和节点信息。
	1. **myBoundFaces**：这个数据结构存储与缝合边界相关的面信息。在缝合过程中，面与面之间的边界需要被识别和处理，`myBoundFaces` 通过索引数据映射的形式，将边界边与这些边界边所属的面关联起来，以便在缝合过程中可以准确地处理这些边界。
	2. **myBoundSections**：用于管理缝合边界上的各个段（sections）与它们所对应的边界边。在缝合过程中，形状的边界会被细分为多个段，`myBoundSections` 提供了一种方式来追踪这些细分段与原始边界边之间的关系。
	3. **mySectionBound**：这个数据结构记录了缝合中产生的每个段（section）与它们原始的边界边之间的映射。这是为了在缝合过程结束后，能够将缝合生成的新边界边映射回它们对应的原始边界边。
	4. **myVertexNode** 和 **myVertexNodeFree**：这两个索引数据映射分别管理缝合过程中的节点信息。`myVertexNode` 存储了与缝合操作中的顶点相关的节点，而 `myVertexNodeFree` 则专门用于管理那些“自由”的节点，即那些不直接参与缝合操作但与缝合过程中产生或修改的顶点相关的节点。
	5. **myNodeSections**：这个数据映射管理节点与缝合段之间的关系。在缝合多个面时，边界会被分割成多个段，而这些段又与特定的节点相关联。`myNodeSections` 提供了一种方式来追踪这些关系，以确保缝合操作能够准确地处理各个拓扑元素。
	6. **myCuttingNode**：用于管理在缝合过程中可能进行的“剪切”操作相关的节点信息。剪切操作通常涉及到调整或重建形状的边界，`myCuttingNode` 存储了这些操作中涉及的节点，以便可以准确地执行这些调整。
#### 私有变量

- **myFaceMode**, **myFloatingEdgesMode**, **mySameParameterMode**, **myLocalToleranceMode**：这些标志用于控制缝合操作的特定行为，如是否在处理面时采用特定模式、是否处理浮动边、是否应用相同参数模式以及是否考虑局部容差。
	1. **myFaceMode**：
	    - 此标志控制着是否启用面模式。当启用时，缝合操作将专注于处理形状中的面，尤其是那些相邻且几乎共边的面。这种模式下，算法会尝试将这些面合并成更大的单一面，从而简化模型的拓扑结构。面模式对于提高模型的整体几何连续性非常有用，尤其是在需要生成干净、高质量表面网格的场合。
	2. **myFloatingEdgesMode**：
	    - 此标志决定是否处理浮动边。浮动边是指那些在形状的拓扑结构中不属于任何面的边界的边。在某些几何处理或模型准备中，识别并适当处理这些浮动边是很重要的，因为它们可能会影响模型的准确性或用于后续的分析处理。
	3. **mySameParameterMode**
	    - 启用相同参数模式时，缝合操作会尽可能保持形状的参数化特性不变。这意味着在缝合后的模型中，边界上的点会与原始模型中相对应的点保持相同的参数值。这对于保持模型几何的精确度非常重要，尤其是在精细的几何建模或需要高精度数值分析的应用中。
	4. **myLocalToleranceMode**：
	    - 此标志控制着是否考虑局部容差。通常，缝合操作使用全局容差来决定哪些边界可以被合并。启用局部容差模式后，缝合过程也会考虑形状局部区域内的特定容差值，这使得缝合操作可以更加细致地根据模型的局部特性进行调整。这在处理具有不同几何精度要求的复杂模型时特别有用。
- **myMinTolerance** 和 **myMaxTolerance**：分别指定了缝合操作中使用的最小和最大容差值，提供了额外的控制层面。
- **myMergedEdges**：追踪在缝合过程中合并的边。

### 成员函数_public

	public:
	  Standard_EXPORT BRepBuilderAPI_Sewing(const Standard_Real tolerance = 1.0e-06, const Standard_Boolean option1 = Standard_True, const Standard_Boolean option2 = Standard_True, const Standard_Boolean option3 = Standard_True, const Standard_Boolean option4 = Standard_False);
	  
	  //! initialize the parameters if necessary
	  Standard_EXPORT void Init (const Standard_Real tolerance = 1.0e-06, const Standard_Boolean option1 = Standard_True, const Standard_Boolean option2 = Standard_True, const Standard_Boolean option3 = Standard_True, const Standard_Boolean option4 = Standard_False);
	  
	  //! Loades the context shape.
	  Standard_EXPORT void Load (const TopoDS_Shape& shape);
	  
	  //! Defines the shapes to be sewed or controlled
	  Standard_EXPORT void Add (const TopoDS_Shape& shape);
	  
	  //! Computing
	  //! theProgress - progress indicator of algorithm
	  Standard_EXPORT void Perform (const Message_ProgressRange& theProgress = Message_ProgressRange());
	  
	  //! Gives the sewed shape
	  //! a null shape if nothing constructed
	  //! may be a face, a shell, a solid or a compound
	  Standard_EXPORT const TopoDS_Shape& SewedShape() const;
	  
	  //! set context
	  Standard_EXPORT void SetContext (const Handle(BRepTools_ReShape)& theContext);
	  
	  //! return context
	  Standard_EXPORT const Handle(BRepTools_ReShape)& GetContext() const;
	  
	  //! Gives the number of free edges (edge shared by one face)
	  Standard_EXPORT Standard_Integer NbFreeEdges() const;
	  
	  //! Gives each free edge
	  Standard_EXPORT const TopoDS_Edge& FreeEdge (const Standard_Integer index) const;
	  
	  //! Gives the number of multiple edges
	  //! (edge shared by more than two faces)
	  Standard_EXPORT Standard_Integer NbMultipleEdges() const;
	  
	  //! Gives each multiple edge
	  Standard_EXPORT const TopoDS_Edge& MultipleEdge (const Standard_Integer index) const;
	  
	  //! Gives the number of contiguous edges (edge shared by two faces)
	  Standard_EXPORT Standard_Integer NbContigousEdges() const;
	  
	  //! Gives each contiguous edge
	  Standard_EXPORT const TopoDS_Edge& ContigousEdge (const Standard_Integer index) const;
	  
	  //! Gives the sections (edge) belonging to a contiguous edge
	  Standard_EXPORT const TopTools_ListOfShape& ContigousEdgeCouple (const Standard_Integer index) const;
	  
	  //! Indicates if a section is bound (before use SectionToBoundary)
	  Standard_EXPORT Standard_Boolean IsSectionBound (const TopoDS_Edge& section) const;
	  
	  //! Gives the original edge (free boundary) which becomes the
	  //! the section. Remember that sections constitute  common edges.
	  //! This imformation is important for control because with
	  //! original edge we can find the surface to which the section
	  //! is attached.
	  Standard_EXPORT const TopoDS_Edge& SectionToBoundary (const TopoDS_Edge& section) const;
	  
	  //! Gives the number of degenerated shapes
	  Standard_EXPORT Standard_Integer NbDegeneratedShapes() const;
	  
	  //! Gives each degenerated shape
	  Standard_EXPORT const TopoDS_Shape& DegeneratedShape (const Standard_Integer index) const;
	  
	  //! Indicates if a input shape is degenerated
	  Standard_EXPORT Standard_Boolean IsDegenerated (const TopoDS_Shape& shape) const;
	  
	  //! Indicates if a input shape has been modified
	  Standard_EXPORT Standard_Boolean IsModified (const TopoDS_Shape& shape) const;
	  
	  //! Gives a modifieded shape
	  Standard_EXPORT const TopoDS_Shape& Modified (const TopoDS_Shape& shape) const;
	  
	  //! Indicates if a input subshape has been modified
	  Standard_EXPORT Standard_Boolean IsModifiedSubShape (const TopoDS_Shape& shape) const;
	  
	  //! Gives a modifieded subshape
	  Standard_EXPORT TopoDS_Shape ModifiedSubShape (const TopoDS_Shape& shape) const;
	  
	  //! print the information
	  Standard_EXPORT void Dump() const;
	  
	  //! Gives the number of deleted faces (faces smallest than tolerance)
	  Standard_EXPORT Standard_Integer NbDeletedFaces() const;
	  
	  //! Gives each deleted face
	  Standard_EXPORT const TopoDS_Face& DeletedFace (const Standard_Integer index) const;
	  
	  //! Gives a modified shape
	  Standard_EXPORT TopoDS_Face WhichFace (const TopoDS_Edge& theEdg, const Standard_Integer index = 1) const;
	  
	  //! Gets same parameter mode.
	    Standard_Boolean SameParameterMode() const;
	  
	  //! Sets same parameter mode.
	    void SetSameParameterMode (const Standard_Boolean SameParameterMode);
	  
	  //! Gives set tolerance.
	    Standard_Real Tolerance() const;
	  
	  //! Sets tolerance
	    void SetTolerance (const Standard_Real theToler);
	  
	  //! Gives set min tolerance.
	    Standard_Real MinTolerance() const;
	  
	  //! Sets min tolerance
	    void SetMinTolerance (const Standard_Real theMinToler);
	  
	  //! Gives set max tolerance
	    Standard_Real MaxTolerance() const;
	  
	  //! Sets max tolerance.
	    void SetMaxTolerance (const Standard_Real theMaxToler);
	  
	  //! Returns mode for sewing faces By default - true.
	    Standard_Boolean FaceMode() const;
	  
	  //! Sets mode for sewing faces By default - true.
	    void SetFaceMode (const Standard_Boolean theFaceMode);
	  
	  //! Returns mode for sewing floating edges By default - false.
	    Standard_Boolean FloatingEdgesMode() const;
	  
	  //! Sets mode for sewing floating edges By default - false.
	  //! Returns mode for cutting floating edges By default - false.
	  //! Sets mode for cutting floating edges By default - false.
	    void SetFloatingEdgesMode (const Standard_Boolean theFloatingEdgesMode);
	  
	  //! Returns mode for accounting of local tolerances
	  //! of edges and vertices during of merging.
	    Standard_Boolean LocalTolerancesMode() const;
	  
	  //! Sets mode for accounting of local tolerances
	  //! of edges and vertices during of merging
	  //! in this case WorkTolerance = myTolerance + tolEdge1+ tolEdg2;
	    void SetLocalTolerancesMode (const Standard_Boolean theLocalTolerancesMode);
	  
	  //! Sets mode for non-manifold sewing.
	    void SetNonManifoldMode (const Standard_Boolean theNonManifoldMode);
	  
	  //! Gets mode for non-manifold sewing.
	  //!
	  //! INTERNAL FUNCTIONS ---
	    Standard_Boolean NonManifoldMode() const;
	  DEFINE_STANDARD_RTTIEXT(BRepBuilderAPI_Sewing,Standard_Transient)

- **构造函数 (`BRepBuilderAPI_Sewing`)**: 允许用户在创建`BRepBuilderAPI_Sewing`对象时指定缝合操作的初始参数。
- **`Init`**: 初始化缝合操作的参数。
- **`Load`**: 加载用于缝合的上下文形状。
- **`Add`**: 添加需要缝合或控制的形状。
- **`Perform`**: 执行缝合操作，是整个缝合流程中最关键的步骤。
- **`SewedShape`**: 返回缝合后的形状，这是执行缝合操作后获取结果的主要方式。
- **`SetContext`, `GetContext`**: 设置和获取缝合操作的上下文。
- **`NbFreeEdges`, `FreeEdge`, `NbMultipleEdges`, `MultipleEdge`, etc.**: 这些函数提供了对缝合过程中生成的自由边、多重边和连续边等的访问。
- **`IsSectionBound`, `SectionToBoundary`**: 用于查询和获取与特定缝合段（section）相关的边界信息。
- **`Dump`**: 打印缝合操作的信息，对于调试和分析非常有用。
- **`NbDeletedFaces`, `DeletedFace`, `WhichFace`**: 提供对于在缝合过程中删除的面的访问。
- - **`Dump`**: 打印缝合操作的信息，对于调试和分析非常有用。
#### SectionToBoundary

	Standard_EXPORT const TopoDS_Edge& SectionToBoundary (const TopoDS_Edge& section) const;

扮演着在缝合过程中管理和识别边界信息的关键角色。函数的主要目的是将缝合过程中产生的“节”（即在两个或多个面之间共享的边）映射回它们各自的原始边界边。在这里，“原始边界边”指的是在缝合之前，单独一个面的边界边。

- **输入参数**：`const TopoDS_Edge& section`。这是一个代表缝合中产生的节的边（`TopoDS_Edge`），即两个面共享的边界。这个边在缝合过程中被识别为连接这些面的一个共同边。
    
- **返回值**：`const TopoDS_Edge&`。函数返回一个常量引用，指向与输入的`section`边对应的原始边界边。这个原始边界边是在缝合操作之前存在的边，它属于某个特定的面。
##### 用途和重要性

- **控制和验证**：通过将缝合中的节映射回它的原始边界边，开发者可以更容易地控制和验证缝合操作的结果。例如，可以检查缝合后的边是否保留了原始几何特征和拓扑位置。
    
- **连接信息**：这个函数提供了一种方法，用于识别缝合操作影响的面之间的连接关系。知道哪些原始边界边构成了缝合中的节，可以帮助理解不同面如何通过缝合操作联系在一起。
    
- **进一步操作**：了解节与原始边界边之间的关系，对于进行进一步的几何操作和分析至关重要，比如需要基于原始面进行几何修改或属性赋值时。
#### NbDegeneratedShapes




这个类的设计允许细致地控制缝合操作，包括设置容差、选择是否处理特定类型的边和面、以及处理非流形情况。通过调用这些成员函数，用户可以根据具体需求定制化缝合流程，实现从简单的边缘缝合到复杂的形状重构的各种操作。