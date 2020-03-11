---
title: MSL 規格-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
ms.openlocfilehash: 8990d1373ea2121ce11337a43dbcdf3b9e1532bd
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418729"
---
# <a name="msl-specification"></a><span data-ttu-id="f2007-102">MSL 規格</span><span class="sxs-lookup"><span data-stu-id="f2007-102">MSL Specification</span></span>
<span data-ttu-id="f2007-103">對應規格語言（MSL）是以 XML 為基礎的語言，描述 Entity Framework 應用程式的概念模型和儲存模型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-103">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="f2007-104">在 Entity Framework 應用程式中，對應中繼資料會在組建時從 msl 檔案（以 MSL 撰寫）載入。</span><span class="sxs-lookup"><span data-stu-id="f2007-104">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="f2007-105">Entity Framework 在執行時間使用對應中繼資料，將針對概念模型的查詢轉譯為存放區特有的命令。</span><span class="sxs-lookup"><span data-stu-id="f2007-105">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="f2007-106">Entity Framework Designer （EF Designer）會在設計階段將對應資訊儲存在 .edmx 檔案中。</span><span class="sxs-lookup"><span data-stu-id="f2007-106">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="f2007-107">在組建期間，Entity Designer 會使用 .edmx 檔案中的資訊來建立在執行時間 Entity Framework 所需的 msl 檔案。</span><span class="sxs-lookup"><span data-stu-id="f2007-107">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="f2007-108">MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="f2007-108">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="f2007-109">如需概念模型命名空間名稱的詳細資訊，請參閱[CSDL 規格](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="f2007-109">For information about the conceptual model namespace name, see [CSDL Specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span> <span data-ttu-id="f2007-110">如需儲存體模型命名空間名稱的詳細資訊，請參閱[SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="f2007-110">For information about the storage model namespace name, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="f2007-111">MSL 的版本是以 XML 命名空間來區分。</span><span class="sxs-lookup"><span data-stu-id="f2007-111">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="f2007-112">MSL 版本</span><span class="sxs-lookup"><span data-stu-id="f2007-112">MSL Version</span></span> | <span data-ttu-id="f2007-113">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="f2007-113">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="f2007-114">MSL v1</span><span class="sxs-lookup"><span data-stu-id="f2007-114">MSL v1</span></span>      | <span data-ttu-id="f2007-115">urn：架構-microsoft-com： windows： storage： mapping： CS</span><span class="sxs-lookup"><span data-stu-id="f2007-115">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="f2007-116">MSL v2</span><span class="sxs-lookup"><span data-stu-id="f2007-116">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="f2007-117">MSL v3</span><span class="sxs-lookup"><span data-stu-id="f2007-117">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="f2007-118">Alias 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-118">Alias Element (MSL)</span></span>

<span data-ttu-id="f2007-119">對應規格語言（MSL）中的**Alias**元素是 mapping 元素的子系，用來定義概念模型和儲存模型命名空間的別名。</span><span class="sxs-lookup"><span data-stu-id="f2007-119">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="f2007-120">MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="f2007-120">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="f2007-121">如需概念模型命名空間名稱的詳細資訊，請參閱 Schema 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-121">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="f2007-122">如需儲存體模型命名空間名稱的詳細資訊，請參閱 Schema Element （SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-122">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="f2007-123">**Alias**元素不能有子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-123">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-124">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-124">Applicable Attributes</span></span>

<span data-ttu-id="f2007-125">下表描述可套用至**Alias**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-125">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="f2007-126">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-126">Attribute Name</span></span> | <span data-ttu-id="f2007-127">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-127">Is Required</span></span> | <span data-ttu-id="f2007-128">值</span><span class="sxs-lookup"><span data-stu-id="f2007-128">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="f2007-129">**索引鍵**</span><span class="sxs-lookup"><span data-stu-id="f2007-129">**Key**</span></span>        | <span data-ttu-id="f2007-130">是</span><span class="sxs-lookup"><span data-stu-id="f2007-130">Yes</span></span>         | <span data-ttu-id="f2007-131">[**值**] 屬性所指定之命名空間的別名。</span><span class="sxs-lookup"><span data-stu-id="f2007-131">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="f2007-132">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="f2007-132">**Value**</span></span>      | <span data-ttu-id="f2007-133">是</span><span class="sxs-lookup"><span data-stu-id="f2007-133">Yes</span></span>         | <span data-ttu-id="f2007-134">**Key**元素的值為別名的命名空間。</span><span class="sxs-lookup"><span data-stu-id="f2007-134">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="f2007-135">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-135">Example</span></span>

<span data-ttu-id="f2007-136">下列範例顯示的**alias**元素會定義概念模型中所定義之類型的別名 `c`。</span><span class="sxs-lookup"><span data-stu-id="f2007-136">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="associationend-element-msl"></a><span data-ttu-id="f2007-137">AssociationEnd 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-137">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="f2007-138">當概念模型中實體類型的修改函式對應至基礎資料庫中的預存程式時，會使用對應規格語言（MSL）中的**AssociationEnd**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-138">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="f2007-139">如果修改預存程式採用的參數值保留在 association 屬性中，則**AssociationEnd**專案會將屬性值對應至參數。</span><span class="sxs-lookup"><span data-stu-id="f2007-139">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="f2007-140">如需詳細資訊，請參閱下列範例。</span><span class="sxs-lookup"><span data-stu-id="f2007-140">For more information, see the example below.</span></span>

<span data-ttu-id="f2007-141">如需將實體類型的修改函式對應至預存程式的詳細資訊，請參閱 ModificationFunctionMapping 元素（MSL）和逐步解說：將實體對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-141">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="f2007-142">**AssociationEnd**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-142">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-143">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="f2007-143">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-144">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-144">Applicable Attributes</span></span>

<span data-ttu-id="f2007-145">下表描述適用于**AssociationEnd**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-145">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="f2007-146">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-146">Attribute Name</span></span>     | <span data-ttu-id="f2007-147">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-147">Is Required</span></span> | <span data-ttu-id="f2007-148">值</span><span class="sxs-lookup"><span data-stu-id="f2007-148">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-149">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="f2007-149">**AssociationSet**</span></span> | <span data-ttu-id="f2007-150">是</span><span class="sxs-lookup"><span data-stu-id="f2007-150">Yes</span></span>         | <span data-ttu-id="f2007-151">要對應的關聯名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-151">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="f2007-152">**From**</span><span class="sxs-lookup"><span data-stu-id="f2007-152">**From**</span></span>           | <span data-ttu-id="f2007-153">是</span><span class="sxs-lookup"><span data-stu-id="f2007-153">Yes</span></span>         | <span data-ttu-id="f2007-154">導覽屬性的**FromRole**屬性值，對應至要對應的關聯。</span><span class="sxs-lookup"><span data-stu-id="f2007-154">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="f2007-155">如需詳細資訊，請參閱 NavigationProperty 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-155">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="f2007-156">**若要**</span><span class="sxs-lookup"><span data-stu-id="f2007-156">**To**</span></span>             | <span data-ttu-id="f2007-157">是</span><span class="sxs-lookup"><span data-stu-id="f2007-157">Yes</span></span>         | <span data-ttu-id="f2007-158">導覽屬性的**ToRole**屬性值，對應至要對應的關聯。</span><span class="sxs-lookup"><span data-stu-id="f2007-158">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="f2007-159">如需詳細資訊，請參閱 NavigationProperty 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-159">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="f2007-160">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-160">Example</span></span>

<span data-ttu-id="f2007-161">請考慮下列概念模型實體類型：</span><span class="sxs-lookup"><span data-stu-id="f2007-161">Consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="Course">
   <Key>
     <PropertyRef Name="CourseID" />
   </Key>
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" MaxLength="100"
             FixedLength="false" Unicode="true" />
   <Property Type="Int32" Name="Credits" Nullable="false" />
   <NavigationProperty Name="Department"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Course" ToRole="Department" />
 </EntityType>
```

<span data-ttu-id="f2007-162">同時請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="f2007-162">Also consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[UpdateCourse]
                                @CourseID int,
                                @Title nvarchar(50),
                                @Credits int,
                                @DepartmentID int
                                AS
                                UPDATE Course SET Title=@Title,
                                                              Credits=@Credits,
                                                              DepartmentID=@DepartmentID
                                WHERE CourseID=@CourseID;
```

<span data-ttu-id="f2007-163">若要將 `Course` 實體的更新函式對應到此預存程式，您必須提供值給**DepartmentID**參數。</span><span class="sxs-lookup"><span data-stu-id="f2007-163">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="f2007-164">`DepartmentID` 的值不會對應至實體類型上的屬性；它會包含在獨立關聯中，該關聯的對應如下所示：</span><span class="sxs-lookup"><span data-stu-id="f2007-164">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
 </AssociationSetMapping>
```

<span data-ttu-id="f2007-165">下列程式碼顯示用來將**FK\_課程\_部門**關聯的**DepartmentID**屬性對應至**UpdateCourse**預存程式的**AssociationEnd**元素（**課程**實體類型的更新功能會對應到此專案）：</span><span class="sxs-lookup"><span data-stu-id="f2007-165">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdateCourse">
         <AssociationEnd AssociationSet="FK_Course_Department"
                         From="Course" To="Department">
           <ScalarProperty Name="DepartmentID"
                           ParameterName="DepartmentID"
                           Version="Current" />
         </AssociationEnd>
         <ScalarProperty Name="Credits" ParameterName="Credits"
                         Version="Current" />
         <ScalarProperty Name="Title" ParameterName="Title"
                         Version="Current" />
         <ScalarProperty Name="CourseID" ParameterName="CourseID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="f2007-166">AssociationSetMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-166">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-167">對應規格語言（MSL）中的**AssociationSetMapping**元素會定義概念模型中的關聯與基礎資料庫中資料表資料行之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-167">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="f2007-168">概念模型中的關聯指其屬性代表基礎資料庫中主要與外部索引鍵資料行的類型。</span><span class="sxs-lookup"><span data-stu-id="f2007-168">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="f2007-169">**AssociationSetMapping**元素會使用兩個 EndProperty 元素，來定義資料庫中關聯類型屬性和資料行之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-169">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="f2007-170">您可以使用 Condition 項目在這些對應上放置條件。</span><span class="sxs-lookup"><span data-stu-id="f2007-170">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="f2007-171">ModificationFunctionMapping 項目可以用來將關聯的插入、更新或刪除函式對應至資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="f2007-171">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="f2007-172">使用 QueryView 元素中的 Entity SQL 字串，定義關聯和資料表資料行之間的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-172">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-173">如果概念模型中的關聯定義了參考條件約束，則關聯不需要與**AssociationSetMapping**元素對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-173">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="f2007-174">如果具有參考條件約束之關聯的**AssociationSetMapping**元素存在，則會忽略**AssociationSetMapping**元素中定義的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-174">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="f2007-175">如需詳細資訊，請參閱 ReferentialConstraint 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-175">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="f2007-176">**AssociationSetMapping**元素可以有下列子項目</span><span class="sxs-lookup"><span data-stu-id="f2007-176">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="f2007-177">QueryView （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-177">QueryView (zero or one)</span></span>
-   <span data-ttu-id="f2007-178">EndProperty (零或兩個)</span><span class="sxs-lookup"><span data-stu-id="f2007-178">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="f2007-179">條件（零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-179">Condition (zero or more)</span></span>
-   <span data-ttu-id="f2007-180">ModificationFunctionMapping （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-180">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-181">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-181">Applicable Attributes</span></span>

<span data-ttu-id="f2007-182">下表描述可套用至**AssociationSetMapping**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-182">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="f2007-183">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-183">Attribute Name</span></span>     | <span data-ttu-id="f2007-184">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-184">Is Required</span></span> | <span data-ttu-id="f2007-185">值</span><span class="sxs-lookup"><span data-stu-id="f2007-185">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-186">**名稱**</span><span class="sxs-lookup"><span data-stu-id="f2007-186">**Name**</span></span>           | <span data-ttu-id="f2007-187">是</span><span class="sxs-lookup"><span data-stu-id="f2007-187">Yes</span></span>         | <span data-ttu-id="f2007-188">要對應的概念模型關聯集名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-188">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="f2007-189">**類型**</span><span class="sxs-lookup"><span data-stu-id="f2007-189">**TypeName**</span></span>       | <span data-ttu-id="f2007-190">否</span><span class="sxs-lookup"><span data-stu-id="f2007-190">No</span></span>          | <span data-ttu-id="f2007-191">要對應的概念模型關聯類型之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-191">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="f2007-192">**中**</span><span class="sxs-lookup"><span data-stu-id="f2007-192">**StoreEntitySet**</span></span> | <span data-ttu-id="f2007-193">否</span><span class="sxs-lookup"><span data-stu-id="f2007-193">No</span></span>          | <span data-ttu-id="f2007-194">要對應的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-194">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="f2007-195">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-195">Example</span></span>

<span data-ttu-id="f2007-196">下列範例顯示一個**AssociationSetMapping**專案，其中概念模型中的**FK\_課程\_部門**關聯集會對應到資料庫中的**課程**資料表。</span><span class="sxs-lookup"><span data-stu-id="f2007-196">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="f2007-197">關聯類型屬性和資料表資料行之間的對應是在子**EndProperty**元素中指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-197">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

## <a name="complexproperty-element-msl"></a><span data-ttu-id="f2007-198">ComplexProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-198">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="f2007-199">對應規格語言（MSL）中的**ComplexProperty**元素會定義概念模型實體類型上的複雜類型屬性與基礎資料庫中資料表資料行之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-199">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="f2007-200">屬性資料行對應會在 ScalarProperty 子項目中指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-200">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="f2007-201">**ComplexType**屬性專案可以具有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-201">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-202">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-202">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="f2007-203">**ComplexProperty** （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-203">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="f2007-204">ComplextTypeMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-204">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="f2007-205">條件（零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-205">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-206">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-206">Applicable Attributes</span></span>

<span data-ttu-id="f2007-207">下表描述適用于**ComplexProperty**元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="f2007-207">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="f2007-208">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-208">Attribute Name</span></span> | <span data-ttu-id="f2007-209">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-209">Is Required</span></span> | <span data-ttu-id="f2007-210">值</span><span class="sxs-lookup"><span data-stu-id="f2007-210">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-211">**名稱**</span><span class="sxs-lookup"><span data-stu-id="f2007-211">**Name**</span></span>       | <span data-ttu-id="f2007-212">是</span><span class="sxs-lookup"><span data-stu-id="f2007-212">Yes</span></span>         | <span data-ttu-id="f2007-213">概念模型中要對應的實體類型之複雜屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-213">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="f2007-214">**類型**</span><span class="sxs-lookup"><span data-stu-id="f2007-214">**TypeName**</span></span>   | <span data-ttu-id="f2007-215">否</span><span class="sxs-lookup"><span data-stu-id="f2007-215">No</span></span>          | <span data-ttu-id="f2007-216">概念模型屬性類型的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-216">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="f2007-217">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-217">Example</span></span>

<span data-ttu-id="f2007-218">下列範例是以 School 模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="f2007-218">The following example is based on the School model.</span></span> <span data-ttu-id="f2007-219">下列複雜型別已加入至概念模型：</span><span class="sxs-lookup"><span data-stu-id="f2007-219">The following complex type has been added to the conceptual model:</span></span>

``` xml
 <ComplexType Name="FullName">
   <Property Type="String" Name="LastName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
   <Property Type="String" Name="FirstName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
 </ComplexType>
```

<span data-ttu-id="f2007-220">**Person**實體類型的**LastName**和**FirstName**屬性已經由一個複雜屬性（ **Name**）取代：</span><span class="sxs-lookup"><span data-stu-id="f2007-220">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

``` xml
 <EntityType Name="Person">
   <Key>
     <PropertyRef Name="PersonID" />
   </Key>
   <Property Name="PersonID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="HireDate" Type="DateTime" />
   <Property Name="EnrollmentDate" Type="DateTime" />
   <Property Name="Name" Type="SchoolModel.FullName" Nullable="false" />
 </EntityType>
```

<span data-ttu-id="f2007-221">下列 MSL 顯示用來將**Name**屬性對應至基礎資料庫中之資料行的**ComplexProperty**元素：</span><span class="sxs-lookup"><span data-stu-id="f2007-221">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
       <ComplexProperty Name="Name" TypeName="SchoolModel.FullName">
         <ScalarProperty Name="FirstName" ColumnName="FirstName" />
         <ScalarProperty Name="LastName" ColumnName="LastName" />  
       </ComplexProperty>
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="f2007-222">ComplexTypeMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-222">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-223">對應規格語言（MSL）中的**ComplexTypeMapping**專案是 ResultMapping 專案的子系，而且會定義概念模型中的函式匯入與基礎資料庫中的預存程式之間的對應（當下列條件成立時）：</span><span class="sxs-lookup"><span data-stu-id="f2007-223">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="f2007-224">函式匯入會傳回概念複雜類型。</span><span class="sxs-lookup"><span data-stu-id="f2007-224">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="f2007-225">預存程序所傳回之資料行名稱未與複雜類型上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="f2007-225">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="f2007-226">根據預設，預存程序所傳回之資料行與複雜類型間的對應是以資料行和屬性名稱為基礎。</span><span class="sxs-lookup"><span data-stu-id="f2007-226">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="f2007-227">如果資料行名稱與屬性名稱不完全相符，您就必須使用**ComplexTypeMapping**元素來定義對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-227">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="f2007-228">如需預設對應的範例，請參閱 FunctionImportMapping 元素（MSL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-228">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="f2007-229">**ComplexTypeMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-229">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-230">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-230">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-231">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-231">Applicable Attributes</span></span>

<span data-ttu-id="f2007-232">下表描述適用于**ComplexTypeMapping**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-232">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="f2007-233">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-233">Attribute Name</span></span> | <span data-ttu-id="f2007-234">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-234">Is Required</span></span> | <span data-ttu-id="f2007-235">值</span><span class="sxs-lookup"><span data-stu-id="f2007-235">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="f2007-236">**類型**</span><span class="sxs-lookup"><span data-stu-id="f2007-236">**TypeName**</span></span>   | <span data-ttu-id="f2007-237">是</span><span class="sxs-lookup"><span data-stu-id="f2007-237">Yes</span></span>         | <span data-ttu-id="f2007-238">要對應的複雜類型的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-238">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-239">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-239">Example</span></span>

<span data-ttu-id="f2007-240">請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="f2007-240">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="f2007-241">同時請考慮下列概念模型複雜類型：</span><span class="sxs-lookup"><span data-stu-id="f2007-241">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="f2007-242">若要建立會傳回前一個複雜類型之實例的函式匯入，必須在**ComplexTypeMapping**元素中定義預存程式所傳回之資料行與實體類型之間的對應：</span><span class="sxs-lookup"><span data-stu-id="f2007-242">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <ComplexTypeMapping TypeName="SchoolModel.GradeInfo">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </ComplexTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="condition-element-msl"></a><span data-ttu-id="f2007-243">Condition 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-243">Condition Element (MSL)</span></span>

<span data-ttu-id="f2007-244">對應規格語言（MSL）中的**Condition**元素會在概念模型與基礎資料庫之間的對應上放置條件。</span><span class="sxs-lookup"><span data-stu-id="f2007-244">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="f2007-245">如果符合子**條件**元素中所指定的所有條件，則在 XML 節點內定義的對應會是有效的。</span><span class="sxs-lookup"><span data-stu-id="f2007-245">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="f2007-246">否則，對應無效。</span><span class="sxs-lookup"><span data-stu-id="f2007-246">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="f2007-247">例如，如果 MappingFragment 元素包含一個或多個**Condition**子項目，則只有在符合子**條件**元素的所有條件時， **MappingFragment**節點內所定義的對應才會是有效的。</span><span class="sxs-lookup"><span data-stu-id="f2007-247">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="f2007-248">每個條件都可以套用至**名稱**（概念模型實體屬性的名稱，由**Name**屬性所指定），或**columnname** （資料庫中資料行的名稱，由**ColumnName**屬性所指定）。</span><span class="sxs-lookup"><span data-stu-id="f2007-248">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="f2007-249">設定**Name**屬性時，會根據實體屬性值來檢查條件。</span><span class="sxs-lookup"><span data-stu-id="f2007-249">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="f2007-250">設定**ColumnName**屬性時，會根據資料行值來檢查條件。</span><span class="sxs-lookup"><span data-stu-id="f2007-250">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="f2007-251">只有其中一個**Name**或**ColumnName**屬性可以在**Condition**元素中指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-251">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-252">在 FunctionImportMapping 元素內使用**Condition**元素時，只有**Name**屬性不適用。</span><span class="sxs-lookup"><span data-stu-id="f2007-252">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="f2007-253">**Condition**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="f2007-253">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="f2007-254">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="f2007-254">AssociationSetMapping</span></span>
-   <span data-ttu-id="f2007-255">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="f2007-255">ComplexProperty</span></span>
-   <span data-ttu-id="f2007-256">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="f2007-256">EntitySetMapping</span></span>
-   <span data-ttu-id="f2007-257">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="f2007-257">MappingFragment</span></span>
-   <span data-ttu-id="f2007-258">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="f2007-258">EntityTypeMapping</span></span>

<span data-ttu-id="f2007-259">**Condition**元素不可以有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-259">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-260">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-260">Applicable Attributes</span></span>

<span data-ttu-id="f2007-261">下表描述適用于**Condition**元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="f2007-261">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="f2007-262">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-262">Attribute Name</span></span> | <span data-ttu-id="f2007-263">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-263">Is Required</span></span> | <span data-ttu-id="f2007-264">值</span><span class="sxs-lookup"><span data-stu-id="f2007-264">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-265">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="f2007-265">**ColumnName**</span></span> | <span data-ttu-id="f2007-266">否</span><span class="sxs-lookup"><span data-stu-id="f2007-266">No</span></span>          | <span data-ttu-id="f2007-267">其值用來評估條件之資料表資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-267">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="f2007-268">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="f2007-268">**IsNull**</span></span>     | <span data-ttu-id="f2007-269">否</span><span class="sxs-lookup"><span data-stu-id="f2007-269">No</span></span>          | <span data-ttu-id="f2007-270">**True** 或 **False**。</span><span class="sxs-lookup"><span data-stu-id="f2007-270">**True** or **False**.</span></span> <span data-ttu-id="f2007-271">如果值為**True** ，且資料行值為**null**，或者如果值為**False**且資料行值不是**null**，則條件為 true。</span><span class="sxs-lookup"><span data-stu-id="f2007-271">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="f2007-272">否則，條件不成立。</span><span class="sxs-lookup"><span data-stu-id="f2007-272">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="f2007-273">不能同時使用**IsNull**和**Value**屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-273">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="f2007-274">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="f2007-274">**Value**</span></span>      | <span data-ttu-id="f2007-275">否</span><span class="sxs-lookup"><span data-stu-id="f2007-275">No</span></span>          | <span data-ttu-id="f2007-276">要與資料行值比較的值。</span><span class="sxs-lookup"><span data-stu-id="f2007-276">The value with which the column value is compared.</span></span> <span data-ttu-id="f2007-277">如果值相同，則條件成立。</span><span class="sxs-lookup"><span data-stu-id="f2007-277">If the values are the same, the condition is true.</span></span> <span data-ttu-id="f2007-278">否則，條件不成立。</span><span class="sxs-lookup"><span data-stu-id="f2007-278">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="f2007-279">不能同時使用**IsNull**和**Value**屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-279">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="f2007-280">**名稱**</span><span class="sxs-lookup"><span data-stu-id="f2007-280">**Name**</span></span>       | <span data-ttu-id="f2007-281">否</span><span class="sxs-lookup"><span data-stu-id="f2007-281">No</span></span>          | <span data-ttu-id="f2007-282">其值用來評估條件之概念模型實體屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-282">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="f2007-283">如果在 FunctionImportMapping 專案中使用**Condition**元素，則此屬性不適用。</span><span class="sxs-lookup"><span data-stu-id="f2007-283">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="f2007-284">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-284">Example</span></span>

<span data-ttu-id="f2007-285">下列範例會將**Condition**元素顯示為**MappingFragment**元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-285">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="f2007-286">當**雇用**項不是 Null 且**EnrollmentDate**是 null 時，資料會對應至**SchoolModel 的講師**類型，以及**Person**資料表的**PersonID**和**雇用**項資料行。</span><span class="sxs-lookup"><span data-stu-id="f2007-286">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="f2007-287">當**EnrollmentDate**不是 null，**而且雇傭**項是 null 時，資料會對應到**SchoolModel**的類型，以及**Person**資料表的**PersonID**和**註冊**資料行。</span><span class="sxs-lookup"><span data-stu-id="f2007-287">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="deletefunction-element-msl"></a><span data-ttu-id="f2007-288">DeleteFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-288">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="f2007-289">對應規格語言（MSL）中的**DeleteFunction**元素，會將概念模型中實體類型或關聯的刪除函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-289">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="f2007-290">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-290">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="f2007-291">如需詳細資訊，請參閱 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-291">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-292">如果您沒有將實體類型的所有三個插入、更新或刪除作業都對應到預存程式，則如果在執行時間執行並擲回 UpdateException，未對應的作業將會失敗。</span><span class="sxs-lookup"><span data-stu-id="f2007-292">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="f2007-293">套用至 EntityTypeMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="f2007-293">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="f2007-294">當套用至 EntityTypeMapping 元素時， **DeleteFunction**專案會將概念模型中實體類型的刪除函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-294">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="f2007-295">當**DeleteFunction**元素套用至**EntityTypeMapping**元素時，可以有下列子專案：</span><span class="sxs-lookup"><span data-stu-id="f2007-295">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="f2007-296">AssociationEnd （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-296">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="f2007-297">ComplexProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="f2007-297">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="f2007-298">ScarlarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-298">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="f2007-299">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-299">Applicable Attributes</span></span>

<span data-ttu-id="f2007-300">下表描述當屬性套用至**EntityTypeMapping**元素時，可以套用至**DeleteFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-300">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="f2007-301">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-301">Attribute Name</span></span>            | <span data-ttu-id="f2007-302">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-302">Is Required</span></span> | <span data-ttu-id="f2007-303">值</span><span class="sxs-lookup"><span data-stu-id="f2007-303">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-304">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="f2007-304">**FunctionName**</span></span>          | <span data-ttu-id="f2007-305">是</span><span class="sxs-lookup"><span data-stu-id="f2007-305">Yes</span></span>         | <span data-ttu-id="f2007-306">刪除函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-306">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="f2007-307">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="f2007-307">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="f2007-308">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="f2007-308">**RowsAffectedParameter**</span></span> | <span data-ttu-id="f2007-309">否</span><span class="sxs-lookup"><span data-stu-id="f2007-309">No</span></span>          | <span data-ttu-id="f2007-310">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-310">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="f2007-311">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-311">Example</span></span>

<span data-ttu-id="f2007-312">下列範例是以 School 模型為基礎，並顯示將**Person**實體類型的 delete 函數對應至**DeletePerson**預存程式的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-312">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="f2007-313">**DeletePerson**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-313">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="f2007-314">套用至 AssociationSetMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="f2007-314">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="f2007-315">當套用至 AssociationSetMapping 元素時， **DeleteFunction**專案會將概念模型中關聯的刪除函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-315">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="f2007-316">當**DeleteFunction**元素套用至**AssociationSetMapping**元素時，可以有下列子專案：</span><span class="sxs-lookup"><span data-stu-id="f2007-316">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="f2007-317">EndProperty</span><span class="sxs-lookup"><span data-stu-id="f2007-317">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="f2007-318">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-318">Applicable Attributes</span></span>

<span data-ttu-id="f2007-319">下表描述當屬性套用至**AssociationSetMapping**元素時，可以套用至**DeleteFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-319">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="f2007-320">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-320">Attribute Name</span></span>            | <span data-ttu-id="f2007-321">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-321">Is Required</span></span> | <span data-ttu-id="f2007-322">值</span><span class="sxs-lookup"><span data-stu-id="f2007-322">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-323">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="f2007-323">**FunctionName**</span></span>          | <span data-ttu-id="f2007-324">是</span><span class="sxs-lookup"><span data-stu-id="f2007-324">Yes</span></span>         | <span data-ttu-id="f2007-325">刪除函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-325">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="f2007-326">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="f2007-326">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="f2007-327">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="f2007-327">**RowsAffectedParameter**</span></span> | <span data-ttu-id="f2007-328">否</span><span class="sxs-lookup"><span data-stu-id="f2007-328">No</span></span>          | <span data-ttu-id="f2007-329">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-329">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="f2007-330">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-330">Example</span></span>

<span data-ttu-id="f2007-331">下列範例是以 School 模型為基礎，並顯示用來將**CourseInstructor**關聯的 delete 函數對應至**DeleteCourseInstructor**預存程式的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-331">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="f2007-332">**DeleteCourseInstructor**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-332">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="endproperty-element-msl"></a><span data-ttu-id="f2007-333">EndProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-333">EndProperty Element (MSL)</span></span>

<span data-ttu-id="f2007-334">對應規格語言（MSL）中的**EndProperty**專案會定義概念模型關聯的 end 或修改函式與基礎資料庫之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-334">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="f2007-335">屬性資料行對應會指定在 ScalarProperty 子項目中。</span><span class="sxs-lookup"><span data-stu-id="f2007-335">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="f2007-336">當**EndProperty**元素用來定義概念模型關聯結尾的對應時，它是 AssociationSetMapping 專案的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-336">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="f2007-337">當**EndProperty**專案用來定義概念模型關聯之修改函式的對應時，它是 InsertFunction 元素或 DeleteFunction 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-337">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="f2007-338">**EndProperty**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-338">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-339">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-339">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-340">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-340">Applicable Attributes</span></span>

<span data-ttu-id="f2007-341">下表描述適用于**EndProperty**元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="f2007-341">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="f2007-342">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-342">Attribute Name</span></span> | <span data-ttu-id="f2007-343">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-343">Is Required</span></span> | <span data-ttu-id="f2007-344">值</span><span class="sxs-lookup"><span data-stu-id="f2007-344">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="f2007-345">名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-345">Name</span></span>           | <span data-ttu-id="f2007-346">是</span><span class="sxs-lookup"><span data-stu-id="f2007-346">Yes</span></span>         | <span data-ttu-id="f2007-347">要對應的關聯端名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-347">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-348">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-348">Example</span></span>

<span data-ttu-id="f2007-349">下列範例會顯示**AssociationSetMapping**專案，在此元素中，概念模型中的**FK\_課程\_部門**關聯會對應至資料庫中的**課程**資料表。</span><span class="sxs-lookup"><span data-stu-id="f2007-349">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="f2007-350">關聯類型屬性和資料表資料行之間的對應是在子**EndProperty**元素中指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-350">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

### <a name="example"></a><span data-ttu-id="f2007-351">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-351">Example</span></span>

<span data-ttu-id="f2007-352">下列範例顯示將關聯（**CourseInstructor**）的插入和刪除函式對應至基礎資料庫中之預存程式的**EndProperty**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-352">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="f2007-353">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-353">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="f2007-354">EntityContainerMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-354">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-355">對應規格語言（MSL）中的**EntityContainerMapping**元素，會將概念模型中的實體容器對應至儲存體模型中的實體容器。</span><span class="sxs-lookup"><span data-stu-id="f2007-355">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="f2007-356">**EntityContainerMapping**元素是 Mapping 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-356">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="f2007-357">**EntityContainerMapping**元素可以有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="f2007-357">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="f2007-358">EntitySetMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="f2007-358">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="f2007-359">AssociationSetMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-359">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="f2007-360">FunctionImportMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-360">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-361">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-361">Applicable Attributes</span></span>

<span data-ttu-id="f2007-362">下表描述可套用至**EntityContainerMapping**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-362">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="f2007-363">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-363">Attribute Name</span></span>            | <span data-ttu-id="f2007-364">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-364">Is Required</span></span> | <span data-ttu-id="f2007-365">值</span><span class="sxs-lookup"><span data-stu-id="f2007-365">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-366">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="f2007-366">**StorageModelContainer**</span></span> | <span data-ttu-id="f2007-367">是</span><span class="sxs-lookup"><span data-stu-id="f2007-367">Yes</span></span>         | <span data-ttu-id="f2007-368">要對應至的儲存模型實體容器名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-368">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="f2007-369">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="f2007-369">**CdmEntityContainer**</span></span>    | <span data-ttu-id="f2007-370">是</span><span class="sxs-lookup"><span data-stu-id="f2007-370">Yes</span></span>         | <span data-ttu-id="f2007-371">要對應的概念模型實體容器名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-371">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="f2007-372">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="f2007-372">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="f2007-373">否</span><span class="sxs-lookup"><span data-stu-id="f2007-373">No</span></span>          | <span data-ttu-id="f2007-374">**True** 或 **False**。</span><span class="sxs-lookup"><span data-stu-id="f2007-374">**True** or **False**.</span></span> <span data-ttu-id="f2007-375">如果**為 False**，則不會產生任何更新視圖。</span><span class="sxs-lookup"><span data-stu-id="f2007-375">If **False**, no update views are generated.</span></span> <span data-ttu-id="f2007-376">當您擁有不正確唯讀對應時，此屬性應該設定為**False** ，因為資料可能無法成功地往返。</span><span class="sxs-lookup"><span data-stu-id="f2007-376">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="f2007-377">預設值為 **True**。</span><span class="sxs-lookup"><span data-stu-id="f2007-377">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-378">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-378">Example</span></span>

<span data-ttu-id="f2007-379">下列範例顯示將**SchoolModelEntities**容器（概念模型實體容器）對應至**SchoolModelStoreContainer**容器（儲存體模型實體容器）的**EntityContainerMapping**元素：</span><span class="sxs-lookup"><span data-stu-id="f2007-379">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolModelEntities">
   <EntitySetMapping Name="Courses">
     <EntityTypeMapping TypeName="c.Course">
       <MappingFragment StoreEntitySet="Course">
         <ScalarProperty Name="CourseID" ColumnName="CourseID" />
         <ScalarProperty Name="Title" ColumnName="Title" />
         <ScalarProperty Name="Credits" ColumnName="Credits" />
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments">
     <EntityTypeMapping TypeName="c.Department">
       <MappingFragment StoreEntitySet="Department">
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         <ScalarProperty Name="Name" ColumnName="Name" />
         <ScalarProperty Name="Budget" ColumnName="Budget" />
         <ScalarProperty Name="StartDate" ColumnName="StartDate" />
         <ScalarProperty Name="Administrator" ColumnName="Administrator" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
 </EntityContainerMapping>
```

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="f2007-380">EntitySetMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-380">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-381">對應規格語言（MSL）中的**EntitySetMapping**專案會將概念模型實體集內的所有類型對應到儲存模型中的實體集。</span><span class="sxs-lookup"><span data-stu-id="f2007-381">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="f2007-382">概念模型中的實體集就是相同型別 (及衍生型別) 之實體的執行個體邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="f2007-382">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="f2007-383">儲存模型中的實體集代表基礎資料庫中的資料表或檢視。</span><span class="sxs-lookup"><span data-stu-id="f2007-383">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="f2007-384">概念模型實體集是由**EntitySetMapping**元素的**Name**屬性值所指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-384">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="f2007-385">對應的 to 資料表或 view 是由每個子 MappingFragment 元素中的**中**屬性或**EntitySetMapping**專案本身所指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-385">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="f2007-386">**EntitySetMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-386">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-387">EntityTypeMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="f2007-387">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="f2007-388">QueryView （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-388">QueryView (zero or one)</span></span>
-   <span data-ttu-id="f2007-389">MappingFragment （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-389">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-390">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-390">Applicable Attributes</span></span>

<span data-ttu-id="f2007-391">下表描述可套用至**EntitySetMapping**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-391">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="f2007-392">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-392">Attribute Name</span></span>           | <span data-ttu-id="f2007-393">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-393">Is Required</span></span> | <span data-ttu-id="f2007-394">值</span><span class="sxs-lookup"><span data-stu-id="f2007-394">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-395">**名稱**</span><span class="sxs-lookup"><span data-stu-id="f2007-395">**Name**</span></span>                 | <span data-ttu-id="f2007-396">是</span><span class="sxs-lookup"><span data-stu-id="f2007-396">Yes</span></span>         | <span data-ttu-id="f2007-397">要對應的概念模型實體集名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-397">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="f2007-398">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="f2007-398">**TypeName** **1**</span></span>       | <span data-ttu-id="f2007-399">否</span><span class="sxs-lookup"><span data-stu-id="f2007-399">No</span></span>          | <span data-ttu-id="f2007-400">要對應的概念模型實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-400">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="f2007-401">**中** **1**</span><span class="sxs-lookup"><span data-stu-id="f2007-401">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="f2007-402">否</span><span class="sxs-lookup"><span data-stu-id="f2007-402">No</span></span>          | <span data-ttu-id="f2007-403">要對應至的儲存模型實體集名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-403">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="f2007-404">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="f2007-404">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="f2007-405">否</span><span class="sxs-lookup"><span data-stu-id="f2007-405">No</span></span>          | <span data-ttu-id="f2007-406">**True**或**False** ，視是否只傳回相異資料列而定。</span><span class="sxs-lookup"><span data-stu-id="f2007-406">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="f2007-407">如果此屬性設定為**True**，則 EntityContainerMapping 元素的**GenerateUpdateViews**屬性必須設定為**False**。</span><span class="sxs-lookup"><span data-stu-id="f2007-407">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="f2007-408">**1** **TypeName**和**中**屬性可以用來取代 EntityTypeMapping 和 MappingFragment 子項目，以將單一實體類型對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="f2007-408">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="f2007-409">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-409">Example</span></span>

<span data-ttu-id="f2007-410">下列範例顯示的**EntitySetMapping**專案，會將概念模型的**課程**實體集內的三個類型（基底類型和兩個衍生類型）對應至基礎資料庫中的三個不同資料表。</span><span class="sxs-lookup"><span data-stu-id="f2007-410">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="f2007-411">資料表是由每個**MappingFragment**元素中的**中**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-411">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.Course)">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnlineCourse)">
     <MappingFragment StoreEntitySet="OnlineCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="URL" ColumnName="URL" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnsiteCourse)">
     <MappingFragment StoreEntitySet="OnsiteCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Time" ColumnName="Time" />
       <ScalarProperty Name="Days" ColumnName="Days" />
       <ScalarProperty Name="Location" ColumnName="Location" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="f2007-412">EntityTypeMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-412">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-413">對應規格語言（MSL）中的**EntityTypeMapping**專案會定義概念模型中的實體類型和基礎資料庫中的資料表或 views 之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-413">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="f2007-414">如需概念模型實體類型和基礎資料庫資料表或檢視的詳細資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="f2007-414">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="f2007-415">要對應的概念模型實體類型是由**EntityTypeMapping**元素的**TypeName**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-415">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="f2007-416">要對應的資料表或視圖是由子 MappingFragment 元素的**中**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="f2007-416">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="f2007-417">ModificationFunctionMapping 子項目可以用來將實體類型的插入、更新或刪除函式對應至資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="f2007-417">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="f2007-418">**EntityTypeMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-418">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-419">MappingFragment （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-419">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="f2007-420">ModificationFunctionMapping （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-420">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="f2007-421">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="f2007-421">ScalarProperty</span></span>
-   <span data-ttu-id="f2007-422">條件</span><span class="sxs-lookup"><span data-stu-id="f2007-422">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-423">**MappingFragment**和**ModificationFunctionMapping**元素不能同時是**EntityTypeMapping**專案的子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-423">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="f2007-424">**ScalarProperty**和**Condition**元素在 FunctionImportMapping 專案中使用時，只能是**EntityTypeMapping**元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-424">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-425">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-425">Applicable Attributes</span></span>

<span data-ttu-id="f2007-426">下表描述可套用至**EntityTypeMapping**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-426">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="f2007-427">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-427">Attribute Name</span></span> | <span data-ttu-id="f2007-428">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-428">Is Required</span></span> | <span data-ttu-id="f2007-429">值</span><span class="sxs-lookup"><span data-stu-id="f2007-429">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-430">**類型**</span><span class="sxs-lookup"><span data-stu-id="f2007-430">**TypeName**</span></span>   | <span data-ttu-id="f2007-431">是</span><span class="sxs-lookup"><span data-stu-id="f2007-431">Yes</span></span>         | <span data-ttu-id="f2007-432">要對應的概念模型實體類型之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-432">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="f2007-433">如果型別是抽象型別或衍生型別，值必須是 `IsOfType(Namespace-qualified_type_name)`。</span><span class="sxs-lookup"><span data-stu-id="f2007-433">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-434">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-434">Example</span></span>

<span data-ttu-id="f2007-435">下列範例顯示具有兩個子**EntityTypeMapping**元素的 EntitySetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-435">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="f2007-436">在第一個**EntityTypeMapping**元素中， **SchoolModel person**實體類型會對應到**person**資料表。</span><span class="sxs-lookup"><span data-stu-id="f2007-436">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="f2007-437">在第二個**EntityTypeMapping**專案中， **SchoolModel**類型的更新功能會對應至資料庫中的預存程式**UpdatePerson**。</span><span class="sxs-lookup"><span data-stu-id="f2007-437">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate" ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="f2007-438">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-438">Example</span></span>

<span data-ttu-id="f2007-439">下一個範例會顯示型別階層的對應，在該階層內根型別是抽象型別。</span><span class="sxs-lookup"><span data-stu-id="f2007-439">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="f2007-440">請注意**TypeName**屬性的 `IsOfType` 語法用法。</span><span class="sxs-lookup"><span data-stu-id="f2007-440">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="f2007-441">FunctionImportMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-441">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-442">對應規格語言（MSL）中的**FunctionImportMapping**元素會定義概念模型中的函式匯入與基礎資料庫中的預存程式或函數之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-442">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="f2007-443">函式匯入必須在概念模型中宣告，而預存程序則必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-443">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="f2007-444">如需詳細資訊，請參閱 FunctionImport 元素（CSDL）和 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-444">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-445">根據預設，如果函式匯入傳回概念模型實體類型或複雜型別，則基礎預存程序所傳回的資料行名稱必須與概念模型類型上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="f2007-445">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="f2007-446">如果資料行名稱與屬性名稱不完全相符，則必須在 ResultMapping 元素中定義對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-446">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="f2007-447">**FunctionImportMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-447">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-448">ResultMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-448">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-449">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-449">Applicable Attributes</span></span>

<span data-ttu-id="f2007-450">下表描述適用于**FunctionImportMapping**元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="f2007-450">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="f2007-451">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-451">Attribute Name</span></span>         | <span data-ttu-id="f2007-452">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-452">Is Required</span></span> | <span data-ttu-id="f2007-453">值</span><span class="sxs-lookup"><span data-stu-id="f2007-453">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-454">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="f2007-454">**FunctionImportName**</span></span> | <span data-ttu-id="f2007-455">是</span><span class="sxs-lookup"><span data-stu-id="f2007-455">Yes</span></span>         | <span data-ttu-id="f2007-456">概念模型中要對應的函式匯入名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-456">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="f2007-457">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="f2007-457">**FunctionName**</span></span>       | <span data-ttu-id="f2007-458">是</span><span class="sxs-lookup"><span data-stu-id="f2007-458">Yes</span></span>         | <span data-ttu-id="f2007-459">儲存體模型中要對應的函式之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-459">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-460">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-460">Example</span></span>

<span data-ttu-id="f2007-461">下列範例是以 School 模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="f2007-461">The following example is based on the School model.</span></span> <span data-ttu-id="f2007-462">請考量儲存體模型中的下列函式：</span><span class="sxs-lookup"><span data-stu-id="f2007-462">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="f2007-463">同時考量概念模型中的這個函式匯入：</span><span class="sxs-lookup"><span data-stu-id="f2007-463">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="f2007-464">下列範例顯示用來將函式和函式匯入對應到彼此的**FunctionImportMapping**元素：</span><span class="sxs-lookup"><span data-stu-id="f2007-464">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="f2007-465">InsertFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-465">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="f2007-466">對應規格語言（MSL）中的**InsertFunction**元素，會將概念模型中實體類型或關聯的插入函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-466">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="f2007-467">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-467">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="f2007-468">如需詳細資訊，請參閱 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-468">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-469">如果您沒有將實體類型的所有三個插入、更新或刪除作業都對應到預存程式，則如果在執行時間執行並擲回 UpdateException，未對應的作業將會失敗。</span><span class="sxs-lookup"><span data-stu-id="f2007-469">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="f2007-470">**InsertFunction**元素可以是 ModificationFunctionMapping 專案的子系，並套用至 EntityTypeMapping 元素或 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-470">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="f2007-471">套用至 EntityTypeMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="f2007-471">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="f2007-472">當套用至 EntityTypeMapping 元素時， **InsertFunction**專案會將概念模型中實體類型的插入函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-472">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="f2007-473">當**InsertFunction**元素套用至**EntityTypeMapping**元素時，可以有下列子專案：</span><span class="sxs-lookup"><span data-stu-id="f2007-473">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="f2007-474">AssociationEnd （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-474">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="f2007-475">ComplexProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="f2007-475">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="f2007-476">ResultBinding （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-476">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="f2007-477">ScarlarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-477">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="f2007-478">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-478">Applicable Attributes</span></span>

<span data-ttu-id="f2007-479">下表描述套用至**EntityTypeMapping**元素時，可以套用至**InsertFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-479">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="f2007-480">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-480">Attribute Name</span></span>            | <span data-ttu-id="f2007-481">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-481">Is Required</span></span> | <span data-ttu-id="f2007-482">值</span><span class="sxs-lookup"><span data-stu-id="f2007-482">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-483">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="f2007-483">**FunctionName**</span></span>          | <span data-ttu-id="f2007-484">是</span><span class="sxs-lookup"><span data-stu-id="f2007-484">Yes</span></span>         | <span data-ttu-id="f2007-485">插入函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-485">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="f2007-486">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="f2007-486">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="f2007-487">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="f2007-487">**RowsAffectedParameter**</span></span> | <span data-ttu-id="f2007-488">否</span><span class="sxs-lookup"><span data-stu-id="f2007-488">No</span></span>          | <span data-ttu-id="f2007-489">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-489">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="f2007-490">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-490">Example</span></span>

<span data-ttu-id="f2007-491">下列範例是以 School 模型為基礎，並顯示用來將 Person 實體類型的插入函式對應至**InsertPerson**預存程式的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-491">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="f2007-492">**InsertPerson**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-492">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="f2007-493">套用至 AssociationSetMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="f2007-493">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="f2007-494">當套用至 AssociationSetMapping 專案時， **InsertFunction**專案會將概念模型中關聯的插入函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-494">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="f2007-495">當**InsertFunction**元素套用至**AssociationSetMapping**元素時，可以有下列子專案：</span><span class="sxs-lookup"><span data-stu-id="f2007-495">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="f2007-496">EndProperty</span><span class="sxs-lookup"><span data-stu-id="f2007-496">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="f2007-497">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-497">Applicable Attributes</span></span>

<span data-ttu-id="f2007-498">下表描述當屬性套用至**AssociationSetMapping**元素時，可以套用至**InsertFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-498">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="f2007-499">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-499">Attribute Name</span></span>            | <span data-ttu-id="f2007-500">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-500">Is Required</span></span> | <span data-ttu-id="f2007-501">值</span><span class="sxs-lookup"><span data-stu-id="f2007-501">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-502">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="f2007-502">**FunctionName**</span></span>          | <span data-ttu-id="f2007-503">是</span><span class="sxs-lookup"><span data-stu-id="f2007-503">Yes</span></span>         | <span data-ttu-id="f2007-504">插入函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-504">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="f2007-505">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="f2007-505">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="f2007-506">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="f2007-506">**RowsAffectedParameter**</span></span> | <span data-ttu-id="f2007-507">否</span><span class="sxs-lookup"><span data-stu-id="f2007-507">No</span></span>          | <span data-ttu-id="f2007-508">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-508">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="f2007-509">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-509">Example</span></span>

<span data-ttu-id="f2007-510">下列範例是以 School 模型為基礎，並顯示用來將**CourseInstructor**關聯的插入函式對應至**InsertCourseInstructor**預存程式的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-510">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="f2007-511">**InsertCourseInstructor**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-511">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="mapping-element-msl"></a><span data-ttu-id="f2007-512">Mapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-512">Mapping Element (MSL)</span></span>

<span data-ttu-id="f2007-513">對應規格語言（MSL）中的**mapping**元素包含將概念模型中定義之物件對應至資料庫的資訊（如儲存模型中所述）。</span><span class="sxs-lookup"><span data-stu-id="f2007-513">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="f2007-514">如需詳細資訊，請參閱 CSDL 規格和 SSDL 規格。</span><span class="sxs-lookup"><span data-stu-id="f2007-514">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="f2007-515">**Mapping**元素是對應規格的根項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-515">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="f2007-516">對應規格的 XML 命名空間是 https://schemas.microsoft.com/ado/2009/11/mapping/cs。</span><span class="sxs-lookup"><span data-stu-id="f2007-516">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="f2007-517">對應項目可以擁有下列子項目 (依列出的順序)：</span><span class="sxs-lookup"><span data-stu-id="f2007-517">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="f2007-518">Alias （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-518">Alias (zero or more)</span></span>
-   <span data-ttu-id="f2007-519">EntityContainerMapping （只有一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-519">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="f2007-520">MSL 中所參考之概念及儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="f2007-520">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="f2007-521">如需概念模型命名空間名稱的詳細資訊，請參閱 Schema 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-521">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="f2007-522">如需儲存體模型命名空間名稱的詳細資訊，請參閱 Schema Element （SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-522">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="f2007-523">MSL 中所使用之命名空間的別名可以使用 Alias 項目加以定義。</span><span class="sxs-lookup"><span data-stu-id="f2007-523">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-524">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-524">Applicable Attributes</span></span>

<span data-ttu-id="f2007-525">下表描述可套用至**Mapping**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-525">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="f2007-526">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-526">Attribute Name</span></span> | <span data-ttu-id="f2007-527">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-527">Is Required</span></span> | <span data-ttu-id="f2007-528">值</span><span class="sxs-lookup"><span data-stu-id="f2007-528">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="f2007-529">**空格鍵**</span><span class="sxs-lookup"><span data-stu-id="f2007-529">**Space**</span></span>      | <span data-ttu-id="f2007-530">是</span><span class="sxs-lookup"><span data-stu-id="f2007-530">Yes</span></span>         | <span data-ttu-id="f2007-531">**C-S**。</span><span class="sxs-lookup"><span data-stu-id="f2007-531">**C-S**.</span></span> <span data-ttu-id="f2007-532">這是固定值，無法變更。</span><span class="sxs-lookup"><span data-stu-id="f2007-532">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-533">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-533">Example</span></span>

<span data-ttu-id="f2007-534">下列範例顯示以 School 模型的一部分為基礎的**對應**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-534">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="f2007-535">如需 School 模型的詳細資訊，請參閱快速入門（Entity Framework）：</span><span class="sxs-lookup"><span data-stu-id="f2007-535">For more information about the School model, see Quickstart (Entity Framework):</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="f2007-536">MappingFragment 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-536">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="f2007-537">對應規格語言（MSL）中的**MappingFragment**專案會定義概念模型實體類型的屬性與資料庫中的資料表或視圖之間的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-537">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="f2007-538">如需概念模型實體類型和基礎資料庫資料表或檢視的詳細資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="f2007-538">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="f2007-539">**MappingFragment**可以是 EntityTypeMapping 元素或 EntitySetMapping 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-539">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="f2007-540">**MappingFragment**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-540">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-541">ComplexType （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-541">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="f2007-542">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-542">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="f2007-543">條件（零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-543">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-544">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-544">Applicable Attributes</span></span>

<span data-ttu-id="f2007-545">下表描述可套用至**MappingFragment**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-545">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="f2007-546">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-546">Attribute Name</span></span>          | <span data-ttu-id="f2007-547">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-547">Is Required</span></span> | <span data-ttu-id="f2007-548">值</span><span class="sxs-lookup"><span data-stu-id="f2007-548">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-549">**中**</span><span class="sxs-lookup"><span data-stu-id="f2007-549">**StoreEntitySet**</span></span>      | <span data-ttu-id="f2007-550">是</span><span class="sxs-lookup"><span data-stu-id="f2007-550">Yes</span></span>         | <span data-ttu-id="f2007-551">要對應的資料表或檢視之名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-551">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="f2007-552">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="f2007-552">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="f2007-553">否</span><span class="sxs-lookup"><span data-stu-id="f2007-553">No</span></span>          | <span data-ttu-id="f2007-554">**True**或**False** ，視是否只傳回相異資料列而定。</span><span class="sxs-lookup"><span data-stu-id="f2007-554">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="f2007-555">如果此屬性設定為**True**，則 EntityContainerMapping 元素的**GenerateUpdateViews**屬性必須設定為**False**。</span><span class="sxs-lookup"><span data-stu-id="f2007-555">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-556">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-556">Example</span></span>

<span data-ttu-id="f2007-557">下列範例顯示**MappingFragment**元素做為**EntityTypeMapping**元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-557">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="f2007-558">在此範例中，概念模型中**課程**類型的屬性會對應至資料庫中**課程**資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="f2007-558">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="f2007-559">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-559">Example</span></span>

<span data-ttu-id="f2007-560">下列範例顯示**MappingFragment**元素做為**EntitySetMapping**元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-560">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="f2007-561">如上述範例所示，概念模型中的**課程**類型屬性會對應至資料庫中**課程**資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="f2007-561">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses" TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
 </EntitySetMapping>
```

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="f2007-562">ModificationFunctionMapping 屬性 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-562">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-563">對應規格語言（MSL）中的**ModificationFunctionMapping**元素，會將概念模型實體類型的插入、更新和刪除函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-563">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="f2007-564">**ModificationFunctionMapping**元素也可以將概念模型中多對多關聯的插入和刪除函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-564">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="f2007-565">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-565">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="f2007-566">如需詳細資訊，請參閱 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-566">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-567">如果您沒有將實體類型的所有三個插入、更新或刪除作業都對應到預存程式，則如果在執行時間執行並擲回 UpdateException，未對應的作業將會失敗。</span><span class="sxs-lookup"><span data-stu-id="f2007-567">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="f2007-568">如果繼承階層架構中某個實體的修改函式已對應至預存程序，則階層架構中所有類型的修改函式都必須對應至預存程序。</span><span class="sxs-lookup"><span data-stu-id="f2007-568">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="f2007-569">**ModificationFunctionMapping**元素可以是 EntityTypeMapping 元素或 AssociationSetMapping 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-569">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="f2007-570">**ModificationFunctionMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-570">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-571">DeleteFunction （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-571">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="f2007-572">InsertFunction （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-572">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="f2007-573">UpdateFunction （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-573">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="f2007-574">**ModificationFunctionMapping**元素沒有適用的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-574">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="f2007-575">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-575">Example</span></span>

<span data-ttu-id="f2007-576">下列範例顯示 School 模型中**人員**實體集的實體集對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-576">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="f2007-577">除了**person**實體類型的資料行對應之外，還會顯示**person**類型的插入、更新和刪除功能的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-577">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="f2007-578">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-578">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="f2007-579">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-579">Example</span></span>

<span data-ttu-id="f2007-580">下列範例顯示 School 模型中**CourseInstructor**關聯集的關聯集對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-580">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="f2007-581">除了**CourseInstructor**關聯的資料行對應之外，也會顯示**CourseInstructor**關聯的插入和刪除函數對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-581">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="f2007-582">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-582">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="f2007-583">QueryView 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-583">QueryView Element (MSL)</span></span>

<span data-ttu-id="f2007-584">對應規格語言（MSL）中的**QueryView**元素會定義概念模型中的實體類型或關聯與基礎資料庫中的資料表之間的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-584">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="f2007-585">對應是使用針對儲存體模型評估的 Entity SQL 查詢來定義，而且您會根據概念模型中的實體或關聯來表示結果集。</span><span class="sxs-lookup"><span data-stu-id="f2007-585">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="f2007-586">因為查詢檢視是唯讀的，無法使用標準更新命令來更新查詢檢視所定義的類型。</span><span class="sxs-lookup"><span data-stu-id="f2007-586">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="f2007-587">您可以使用修改函式來更新這些類型。</span><span class="sxs-lookup"><span data-stu-id="f2007-587">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="f2007-588">如需詳細資訊，請參閱如何：將修改函數對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-588">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-589">在**QueryView**元素中，不支援包含**GroupBy**、群組匯總或導覽屬性的 Entity SQL 運算式。</span><span class="sxs-lookup"><span data-stu-id="f2007-589">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="f2007-590">**QueryView**元素可以是 EntitySetMapping 元素或 AssociationSetMapping 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-590">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="f2007-591">若是前者，查詢檢視會定義概念模型中之實體的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-591">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="f2007-592">若是後者，查詢檢視會定義概念模型中之關聯的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-592">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-593">如果**AssociationSetMapping**元素適用于與參考條件約束的關聯，則會忽略**AssociationSetMapping**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-593">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="f2007-594">如需詳細資訊，請參閱 ReferentialConstraint 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-594">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="f2007-595">**QueryView**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-595">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-596">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-596">Applicable Attributes</span></span>

<span data-ttu-id="f2007-597">下表描述可套用至**QueryView**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-597">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="f2007-598">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-598">Attribute Name</span></span> | <span data-ttu-id="f2007-599">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-599">Is Required</span></span> | <span data-ttu-id="f2007-600">值</span><span class="sxs-lookup"><span data-stu-id="f2007-600">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-601">**類型**</span><span class="sxs-lookup"><span data-stu-id="f2007-601">**TypeName**</span></span>   | <span data-ttu-id="f2007-602">否</span><span class="sxs-lookup"><span data-stu-id="f2007-602">No</span></span>          | <span data-ttu-id="f2007-603">要由查詢檢視對應的概念模型類型名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-603">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-604">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-604">Example</span></span>

<span data-ttu-id="f2007-605">下列範例顯示**QueryView**元素做為**EntitySetMapping**專案的子系，並定義 School 模型中**部門**實體類型的查詢檢視對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-605">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

``` xml
 <EntitySetMapping Name="Departments" >
   <QueryView>
     SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                         d.Name,
                                         d.Budget,
                                         d.StartDate)
     FROM SchoolModelStoreContainer.Department AS d
     WHERE d.Budget > 150000
   </QueryView>
 </EntitySetMapping>
```

<span data-ttu-id="f2007-606">因為查詢只會傳回儲存體模型中**部門**類型的成員子集，所以 School 模型中的**部門**類型已根據此對應進行修改，如下所示：</span><span class="sxs-lookup"><span data-stu-id="f2007-606">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

``` xml
 <EntityType Name="Department">
   <Key>
     <PropertyRef Name="DepartmentID" />
   </Key>
   <Property Type="Int32" Name="DepartmentID" Nullable="false" />
   <Property Type="String" Name="Name" Nullable="false"
             MaxLength="50" FixedLength="false" Unicode="true" />
   <Property Type="Decimal" Name="Budget" Nullable="false"
             Precision="19" Scale="4" />
   <Property Type="DateTime" Name="StartDate" Nullable="false" />
   <NavigationProperty Name="Courses"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Department" ToRole="Course" />
 </EntityType>
```

### <a name="example"></a><span data-ttu-id="f2007-607">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-607">Example</span></span>

<span data-ttu-id="f2007-608">下一個範例顯示**QueryView**元素做為**AssociationSetMapping**專案的子系，並定義 School 模型中 `FK_Course_Department` 關聯的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-608">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolEntities">
   <EntitySetMapping Name="Courses" >
     <QueryView>
       SELECT VALUE SchoolModel.Course(c.CourseID,
                                       c.Title,
                                       c.Credits)
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments" >
     <QueryView>
       SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                           d.Name,
                                           d.Budget,
                                           d.StartDate)
       FROM SchoolModelStoreContainer.Department AS d
       WHERE d.Budget > 150000
     </QueryView>
   </EntitySetMapping>
   <AssociationSetMapping Name="FK_Course_Department" >
     <QueryView>
       SELECT VALUE SchoolModel.FK_Course_Department(
         CREATEREF(SchoolEntities.Departments, row(c.DepartmentID), SchoolModel.Department),
         CREATEREF(SchoolEntities.Courses, row(c.CourseID)) )
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </AssociationSetMapping>
 </EntityContainerMapping>
```
 
### <a name="comments"></a><span data-ttu-id="f2007-609">註解</span><span class="sxs-lookup"><span data-stu-id="f2007-609">Comments</span></span>

<span data-ttu-id="f2007-610">您可以定義查詢檢視來啟用下列案例：</span><span class="sxs-lookup"><span data-stu-id="f2007-610">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="f2007-611">定義概念模型中的實體，其中未包含儲存體模型中實體的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-611">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="f2007-612">這包括沒有預設值且不支援**null**值的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-612">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="f2007-613">將儲存體模型中的計算資料行對應至概念模型中實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-613">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="f2007-614">定義分割概念模型實體所用之條件不是以實體為依據的對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-614">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="f2007-615">當您使用**Condition**元素指定條件式對應時，提供的條件必須等於指定的值。</span><span class="sxs-lookup"><span data-stu-id="f2007-615">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="f2007-616">如需詳細資訊，請參閱 Condition 元素（MSL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-616">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="f2007-617">將儲存體模型中的相同資料行對應至概念模型中的多種類型。</span><span class="sxs-lookup"><span data-stu-id="f2007-617">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="f2007-618">將多種類型對應至相同資料表。</span><span class="sxs-lookup"><span data-stu-id="f2007-618">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="f2007-619">定義概念模型中不是以關聯式結構描述之外部索引鍵為依據的關聯。</span><span class="sxs-lookup"><span data-stu-id="f2007-619">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="f2007-620">使用自訂商務邏輯來設定概念模型中的屬性值。</span><span class="sxs-lookup"><span data-stu-id="f2007-620">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="f2007-621">例如，您可以在概念模型中，將資料來源中的字串值 "T" 對應至布林值**true**。</span><span class="sxs-lookup"><span data-stu-id="f2007-621">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="f2007-622">為查詢結果定義條件篩選器。</span><span class="sxs-lookup"><span data-stu-id="f2007-622">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="f2007-623">對概念模型資料強加的限制比儲存體模型的少。</span><span class="sxs-lookup"><span data-stu-id="f2007-623">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="f2007-624">例如，您可以將概念模型中的屬性設為 null，即使它所對應的資料行不支援**null**值也一樣。</span><span class="sxs-lookup"><span data-stu-id="f2007-624">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="f2007-625">下列考量適用於為實體定義查詢檢視的情況：</span><span class="sxs-lookup"><span data-stu-id="f2007-625">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="f2007-626">查詢檢視是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="f2007-626">Query views are read-only.</span></span> <span data-ttu-id="f2007-627">您只能使用修改函式來更新這些實體。</span><span class="sxs-lookup"><span data-stu-id="f2007-627">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="f2007-628">當您依據查詢檢視定義實體類型時，也必須依據查詢檢視定義所有的相關實體。</span><span class="sxs-lookup"><span data-stu-id="f2007-628">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="f2007-629">當您將多對多關聯對應到儲存模型中的實體（代表關聯式結構描述中的連結資料表）時，您必須在這個連結資料表的**AssociationSetMapping**專案中定義**QueryView**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-629">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="f2007-630">必須為類型階層架構中的所有類型定義查詢檢視。</span><span class="sxs-lookup"><span data-stu-id="f2007-630">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="f2007-631">您可以透過下列方法完成這項作業：</span><span class="sxs-lookup"><span data-stu-id="f2007-631">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="f2007-632">使用單一**QueryView**元素，指定單一 Entity SQL 查詢，以傳回階層中所有實體類型的聯集。</span><span class="sxs-lookup"><span data-stu-id="f2007-632">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="f2007-633">使用單一**QueryView**專案，指定單一 Entity SQL 查詢，使用 CASE 運算子根據特定條件傳回階層中的特定實體類型。</span><span class="sxs-lookup"><span data-stu-id="f2007-633">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="f2007-634">具有階層中特定類型的其他**QueryView**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-634">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="f2007-635">在此情況下，請使用**QueryView**元素的**TypeName**屬性來指定每個視圖的實體類型。</span><span class="sxs-lookup"><span data-stu-id="f2007-635">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="f2007-636">定義查詢檢視時，您無法在**EntitySetMapping**元素上指定**StorageSetName**屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-636">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="f2007-637">當定義了查詢檢視時， **EntitySetMapping**元素也不能包含**屬性**對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-637">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="f2007-638">ResultBinding 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-638">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="f2007-639">當實體類型修改函式對應至基礎資料庫中的預存程式時，對應規格語言（MSL）中的**ResultBinding**專案會將預存程式所傳回的資料行值對應到概念模型中的實體屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-639">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="f2007-640">例如，當 insert 預存程式傳回識別欄位的值時， **ResultBinding**元素會將傳回的值對應至概念模型中的實體類型屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-640">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="f2007-641">**ResultBinding**元素可以是 InsertFunction 元素或 UpdateFunction 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="f2007-641">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="f2007-642">**ResultBinding**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-642">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-643">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-643">Applicable Attributes</span></span>

<span data-ttu-id="f2007-644">下表描述適用于**ResultBinding**元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="f2007-644">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="f2007-645">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-645">Attribute Name</span></span> | <span data-ttu-id="f2007-646">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-646">Is Required</span></span> | <span data-ttu-id="f2007-647">值</span><span class="sxs-lookup"><span data-stu-id="f2007-647">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-648">**名稱**</span><span class="sxs-lookup"><span data-stu-id="f2007-648">**Name**</span></span>       | <span data-ttu-id="f2007-649">是</span><span class="sxs-lookup"><span data-stu-id="f2007-649">Yes</span></span>         | <span data-ttu-id="f2007-650">概念模型中要對應之實體屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-650">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="f2007-651">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="f2007-651">**ColumnName**</span></span> | <span data-ttu-id="f2007-652">是</span><span class="sxs-lookup"><span data-stu-id="f2007-652">Yes</span></span>         | <span data-ttu-id="f2007-653">要對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-653">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="f2007-654">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-654">Example</span></span>

<span data-ttu-id="f2007-655">下列範例是以 School 模型為基礎，並顯示用來將**Person**實體類型的插入功能對應至**InsertPerson**預存程式的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-655">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="f2007-656">（ **InsertPerson**預存程式如下所示，並在儲存體模型中宣告）。**ResultBinding**元素是用來將預存程式（**NewPersonID**）所傳回的資料行值對應至實體類型屬性（**PersonID**）。</span><span class="sxs-lookup"><span data-stu-id="f2007-656">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```

<span data-ttu-id="f2007-657">下列 Transact-sql 描述**InsertPerson**預存程式：</span><span class="sxs-lookup"><span data-stu-id="f2007-657">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[InsertPerson]
                                @LastName nvarchar(50),
                                @FirstName nvarchar(50),
                                @HireDate datetime,
                                @EnrollmentDate datetime
                                AS
                                INSERT INTO dbo.Person (LastName,
                                                                             FirstName,
                                                                             HireDate,
                                                                             EnrollmentDate)
                                VALUES (@LastName,
                                               @FirstName,
                                               @HireDate,
                                               @EnrollmentDate);
                                SELECT SCOPE_IDENTITY() as NewPersonID;
```

## <a name="resultmapping-element-msl"></a><span data-ttu-id="f2007-658">ResultMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-658">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="f2007-659">對應規格語言（MSL）中的**ResultMapping**元素會定義概念模型中的函式匯入與基礎資料庫中的預存程式之間的對應（當下列條件成立時）：</span><span class="sxs-lookup"><span data-stu-id="f2007-659">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="f2007-660">函式匯入會傳回概念模型實體類型或複雜型別。</span><span class="sxs-lookup"><span data-stu-id="f2007-660">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="f2007-661">預存程序所傳回之資料行名稱未與實體類型或複雜型別上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="f2007-661">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="f2007-662">根據預設，預存程序所傳回之資料行與實體類型或複雜型別間的對應是以資料行和屬性名稱為基礎。</span><span class="sxs-lookup"><span data-stu-id="f2007-662">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="f2007-663">如果資料行名稱與屬性名稱不完全相符，您就必須使用**ResultMapping**元素來定義對應。</span><span class="sxs-lookup"><span data-stu-id="f2007-663">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="f2007-664">如需預設對應的範例，請參閱 FunctionImportMapping 元素（MSL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-664">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="f2007-665">**ResultMapping**元素是 FunctionImportMapping 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-665">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="f2007-666">**ResultMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-666">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-667">EntityTypeMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="f2007-667">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="f2007-668">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="f2007-668">ComplexTypeMapping</span></span>

<span data-ttu-id="f2007-669">**ResultMapping**元素沒有適用的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-669">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="f2007-670">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-670">Example</span></span>

<span data-ttu-id="f2007-671">請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="f2007-671">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="f2007-672">同時請考慮下列概念模型實體類型：</span><span class="sxs-lookup"><span data-stu-id="f2007-672">Also consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="StudentGrade">
   <Key>
     <PropertyRef Name="EnrollmentID" />
   </Key>
   <Property Name="EnrollmentID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="CourseID" Type="Int32" Nullable="false" />
   <Property Name="StudentID" Type="Int32" Nullable="false" />
   <Property Name="Grade" Type="Decimal" Precision="3" Scale="2" />
 </EntityType>
```

<span data-ttu-id="f2007-673">為了建立會傳回前一個實體類型之實例的函式匯入，必須在**ResultMapping**元素中定義預存程式所傳回之資料行與實體類型之間的對應：</span><span class="sxs-lookup"><span data-stu-id="f2007-673">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <EntityTypeMapping TypeName="SchoolModel.StudentGrade">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </EntityTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="f2007-674">ScalarProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-674">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="f2007-675">對應規格語言（MSL）中的**ScalarProperty**元素，會將概念模型實體類型、複雜類型或關聯上的屬性對應至基礎資料庫中的資料表資料行或預存程式參數。</span><span class="sxs-lookup"><span data-stu-id="f2007-675">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="f2007-676">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-676">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="f2007-677">如需詳細資訊，請參閱 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-677">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="f2007-678">**ScalarProperty**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="f2007-678">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="f2007-679">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="f2007-679">MappingFragment</span></span>
-   <span data-ttu-id="f2007-680">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="f2007-680">InsertFunction</span></span>
-   <span data-ttu-id="f2007-681">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="f2007-681">UpdateFunction</span></span>
-   <span data-ttu-id="f2007-682">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="f2007-682">DeleteFunction</span></span>
-   <span data-ttu-id="f2007-683">EndProperty</span><span class="sxs-lookup"><span data-stu-id="f2007-683">EndProperty</span></span>
-   <span data-ttu-id="f2007-684">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="f2007-684">ComplexProperty</span></span>
-   <span data-ttu-id="f2007-685">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="f2007-685">ResultMapping</span></span>

<span data-ttu-id="f2007-686">**ScalarProperty**元素做為**MappingFragment**、 **ComplexProperty**或**EndProperty**元素的子系，會將概念模型中的屬性對應至資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="f2007-686">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="f2007-687">**ScalarProperty**元素做為**InsertFunction**、 **UpdateFunction**或**DeleteFunction**元素的子系，會將概念模型中的屬性對應至預存程式參數。</span><span class="sxs-lookup"><span data-stu-id="f2007-687">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="f2007-688">**ScalarProperty**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="f2007-688">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-689">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-689">Applicable Attributes</span></span>

<span data-ttu-id="f2007-690">套用至**ScalarProperty**元素的屬性會根據專案的角色而有所不同。</span><span class="sxs-lookup"><span data-stu-id="f2007-690">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="f2007-691">下表描述當**ScalarProperty**元素用來將概念模型屬性對應至資料庫中的資料行時，適用的屬性：</span><span class="sxs-lookup"><span data-stu-id="f2007-691">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="f2007-692">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-692">Attribute Name</span></span> | <span data-ttu-id="f2007-693">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-693">Is Required</span></span> | <span data-ttu-id="f2007-694">值</span><span class="sxs-lookup"><span data-stu-id="f2007-694">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="f2007-695">**名稱**</span><span class="sxs-lookup"><span data-stu-id="f2007-695">**Name**</span></span>       | <span data-ttu-id="f2007-696">是</span><span class="sxs-lookup"><span data-stu-id="f2007-696">Yes</span></span>         | <span data-ttu-id="f2007-697">要對應的概念模型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-697">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="f2007-698">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="f2007-698">**ColumnName**</span></span> | <span data-ttu-id="f2007-699">是</span><span class="sxs-lookup"><span data-stu-id="f2007-699">Yes</span></span>         | <span data-ttu-id="f2007-700">要對應的資料表資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-700">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="f2007-701">下表描述當用來將概念模型屬性對應至預存程式參數時，適用于**ScalarProperty**元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="f2007-701">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="f2007-702">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-702">Attribute Name</span></span>    | <span data-ttu-id="f2007-703">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-703">Is Required</span></span> | <span data-ttu-id="f2007-704">值</span><span class="sxs-lookup"><span data-stu-id="f2007-704">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-705">**名稱**</span><span class="sxs-lookup"><span data-stu-id="f2007-705">**Name**</span></span>          | <span data-ttu-id="f2007-706">是</span><span class="sxs-lookup"><span data-stu-id="f2007-706">Yes</span></span>         | <span data-ttu-id="f2007-707">要對應的概念模型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-707">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="f2007-708">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="f2007-708">**ParameterName**</span></span> | <span data-ttu-id="f2007-709">是</span><span class="sxs-lookup"><span data-stu-id="f2007-709">Yes</span></span>         | <span data-ttu-id="f2007-710">要對應的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-710">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="f2007-711">**版本**</span><span class="sxs-lookup"><span data-stu-id="f2007-711">**Version**</span></span>       | <span data-ttu-id="f2007-712">否</span><span class="sxs-lookup"><span data-stu-id="f2007-712">No</span></span>          | <span data-ttu-id="f2007-713">[**目前**] 或 [**原始**]，取決於目前的值或屬性的原始值是否應用於平行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="f2007-713">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="f2007-714">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-714">Example</span></span>

<span data-ttu-id="f2007-715">下列範例顯示以兩種方式使用的**ScalarProperty**元素：</span><span class="sxs-lookup"><span data-stu-id="f2007-715">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="f2007-716">將**person**實體類型的屬性對應至**person**資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="f2007-716">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="f2007-717">將**Person**實體類型的屬性對應至**UpdatePerson**預存程式的參數。</span><span class="sxs-lookup"><span data-stu-id="f2007-717">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="f2007-718">預存程序已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="f2007-718">The stored procedures are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="f2007-719">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-719">Example</span></span>

<span data-ttu-id="f2007-720">下一個範例顯示用來將概念模型關聯的插入和刪除函式對應至資料庫中之預存程式的**ScalarProperty**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-720">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="f2007-721">預存程序已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="f2007-721">The stored procedures are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="updatefunction-element-msl"></a><span data-ttu-id="f2007-722">UpdateFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="f2007-722">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="f2007-723">對應規格語言（MSL）中的**UpdateFunction**元素，會將概念模型中實體類型的更新函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2007-723">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="f2007-724">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-724">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="f2007-725">如需詳細資訊，請參閱 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="f2007-725">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="f2007-726">如果您沒有將實體類型的所有三個插入、更新或刪除作業都對應到預存程式，則如果在執行時間執行並擲回 UpdateException，未對應的作業將會失敗。</span><span class="sxs-lookup"><span data-stu-id="f2007-726">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="f2007-727">**UpdateFunction**元素可以是 ModificationFunctionMapping 元素的子系，並套用至 EntityTypeMapping 專案。</span><span class="sxs-lookup"><span data-stu-id="f2007-727">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="f2007-728">**UpdateFunction**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="f2007-728">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="f2007-729">AssociationEnd （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-729">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="f2007-730">ComplexProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="f2007-730">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="f2007-731">ResultBinding （零或一個）</span><span class="sxs-lookup"><span data-stu-id="f2007-731">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="f2007-732">ScarlarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="f2007-732">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="f2007-733">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="f2007-733">Applicable Attributes</span></span>

<span data-ttu-id="f2007-734">下表描述可套用至**UpdateFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2007-734">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="f2007-735">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="f2007-735">Attribute Name</span></span>            | <span data-ttu-id="f2007-736">必要</span><span class="sxs-lookup"><span data-stu-id="f2007-736">Is Required</span></span> | <span data-ttu-id="f2007-737">值</span><span class="sxs-lookup"><span data-stu-id="f2007-737">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f2007-738">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="f2007-738">**FunctionName**</span></span>          | <span data-ttu-id="f2007-739">是</span><span class="sxs-lookup"><span data-stu-id="f2007-739">Yes</span></span>         | <span data-ttu-id="f2007-740">更新函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-740">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="f2007-741">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="f2007-741">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="f2007-742">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="f2007-742">**RowsAffectedParameter**</span></span> | <span data-ttu-id="f2007-743">否</span><span class="sxs-lookup"><span data-stu-id="f2007-743">No</span></span>          | <span data-ttu-id="f2007-744">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="f2007-744">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="f2007-745">範例</span><span class="sxs-lookup"><span data-stu-id="f2007-745">Example</span></span>

<span data-ttu-id="f2007-746">下列範例是以 School 模型為基礎，並顯示用來將**Person**實體類型的更新函式對應至**UpdatePerson**預存程式的**UpdateFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="f2007-746">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="f2007-747">**UpdatePerson**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="f2007-747">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
