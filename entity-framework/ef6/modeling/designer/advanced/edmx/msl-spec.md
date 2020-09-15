---
title: MSL 規格-EF6
description: Entity Framework 6 中的 MSL 規格
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 36527b9827a7576f73c9ef476462d9392b601984
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070108"
---
# <a name="msl-specification"></a><span data-ttu-id="4d6b3-103">MSL 規格</span><span class="sxs-lookup"><span data-stu-id="4d6b3-103">MSL Specification</span></span>
<span data-ttu-id="4d6b3-104">對應規格語言 (MSL) 是以 XML 為基礎的語言，描述 Entity Framework 應用程式的概念模型和儲存模型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-104">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="4d6b3-105">在 Entity Framework 應用程式中，對應中繼資料會從 msl 檔案載入， (在組建階段以 MSL) 寫入。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-105">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="4d6b3-106">Entity Framework 在執行時間使用對應中繼資料，將針對概念模型的查詢轉譯為存放區特有的命令。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-106">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="4d6b3-107">Entity Framework Designer (EF 設計工具) 在設計階段將對應資訊儲存在 .edmx 檔中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-107">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="4d6b3-108">在組建階段，Entity Designer 會使用 .edmx 檔案中的資訊來建立在執行時間 Entity Framework 所需的 msl 檔案。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-108">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="4d6b3-109">MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-109">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="4d6b3-110">如需概念模型命名空間名稱的詳細資訊，請參閱 [CSDL 規格](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-110">For information about the conceptual model namespace name, see [CSDL Specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span> <span data-ttu-id="4d6b3-111">如需儲存模型命名空間名稱的詳細資訊，請參閱 [SSDL 規格](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-111">For information about the storage model namespace name, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="4d6b3-112">MSL 的版本是以 XML 命名空間來區分。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-112">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="4d6b3-113">MSL 版本</span><span class="sxs-lookup"><span data-stu-id="4d6b3-113">MSL Version</span></span> | <span data-ttu-id="4d6b3-114">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="4d6b3-114">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="4d6b3-115">MSL v1</span><span class="sxs-lookup"><span data-stu-id="4d6b3-115">MSL v1</span></span>      | <span data-ttu-id="4d6b3-116">urn：架構-microsoft-com： windows： storage：對應： CS</span><span class="sxs-lookup"><span data-stu-id="4d6b3-116">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="4d6b3-117">MSL v2</span><span class="sxs-lookup"><span data-stu-id="4d6b3-117">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="4d6b3-118">MSL v3</span><span class="sxs-lookup"><span data-stu-id="4d6b3-118">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="4d6b3-119">Alias 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-119">Alias Element (MSL)</span></span>

<span data-ttu-id="4d6b3-120">對應規格語言 (MSL) 中的 **Alias** 專案是對應元素的子系，可用來定義概念模型和儲存模型命名空間的別名。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-120">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="4d6b3-121">MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-121">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="4d6b3-122">如需概念模型命名空間名稱的詳細資訊，請參閱 (CSDL) 的架構元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-122">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="4d6b3-123">如需儲存模型命名空間名稱的詳細資訊，請參閱 (SSDL) 的架構元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-123">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="4d6b3-124">**Alias**元素不能有子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-124">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-125">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-125">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-126">下表說明可套用至 **Alias** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-126">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="4d6b3-127">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-127">Attribute Name</span></span> | <span data-ttu-id="4d6b3-128">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-128">Is Required</span></span> | <span data-ttu-id="4d6b3-129">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-129">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-130">**金鑰**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-130">**Key**</span></span>        | <span data-ttu-id="4d6b3-131">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-131">Yes</span></span>         | <span data-ttu-id="4d6b3-132">**Value**屬性所指定之命名空間的別名。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-132">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="4d6b3-133">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-133">**Value**</span></span>      | <span data-ttu-id="4d6b3-134">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-134">Yes</span></span>         | <span data-ttu-id="4d6b3-135">**Key**元素值為別名的命名空間。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-135">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="4d6b3-136">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-136">Example</span></span>

<span data-ttu-id="4d6b3-137">下列範例**Alias** `c` 會針對概念模型中定義的類型，顯示定義別名的 alias 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-137">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

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

## <a name="associationend-element-msl"></a><span data-ttu-id="4d6b3-138">AssociationEnd 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-138">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="4d6b3-139">當概念模型中實體類型的修改函式對應至基礎資料庫中的預存程式時，會使用對應規格語言 (MSL) 中的 **AssociationEnd** 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-139">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="4d6b3-140">如果修改預存程式採用的參數值保留在 association 屬性中， **AssociationEnd** 專案就會將屬性值對應至參數。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-140">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="4d6b3-141">如需詳細資訊，請參閱下列範例。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-141">For more information, see the example below.</span></span>

<span data-ttu-id="4d6b3-142">如需將實體類型的修改函式對應至預存程式的詳細資訊，請參閱 ModificationFunctionMapping 元素 (MSL) 和逐步解說：將實體對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-142">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="4d6b3-143">**AssociationEnd**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-143">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-144">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="4d6b3-144">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-145">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-145">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-146">下表描述適用于 **AssociationEnd** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-146">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="4d6b3-147">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-147">Attribute Name</span></span>     | <span data-ttu-id="4d6b3-148">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-148">Is Required</span></span> | <span data-ttu-id="4d6b3-149">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-149">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-150">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-150">**AssociationSet**</span></span> | <span data-ttu-id="4d6b3-151">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-151">Yes</span></span>         | <span data-ttu-id="4d6b3-152">要對應的關聯名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-152">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="4d6b3-153">**From**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-153">**From**</span></span>           | <span data-ttu-id="4d6b3-154">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-154">Yes</span></span>         | <span data-ttu-id="4d6b3-155">導覽屬性的 **FromRole** 屬性值，對應至所對應的關聯。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-155">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="4d6b3-156">如需詳細資訊，請參閱 (CSDL) 的 NavigationProperty 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-156">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="4d6b3-157">**若要**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-157">**To**</span></span>             | <span data-ttu-id="4d6b3-158">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-158">Yes</span></span>         | <span data-ttu-id="4d6b3-159">導覽屬性的 **ToRole** 屬性值，對應至所對應的關聯。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-159">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="4d6b3-160">如需詳細資訊，請參閱 (CSDL) 的 NavigationProperty 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-160">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="4d6b3-161">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-161">Example</span></span>

<span data-ttu-id="4d6b3-162">請考慮下列概念模型實體類型：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-162">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="4d6b3-163">同時請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-163">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="4d6b3-164">為了將實體的更新函式對應 `Course` 到這個預存程式，您必須提供值給 **DepartmentID** 參數。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-164">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="4d6b3-165">`DepartmentID` 的值不會對應至實體類型上的屬性；它會包含在獨立關聯中，該關聯的對應如下所示：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-165">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="4d6b3-166">下列程式碼顯示用來將**FK \_ 課程 \_ 部門**關聯的**DepartmentID**屬性對應到**UpdateCourse**預存程式的**AssociationEnd**元素， (**課程**實體類型的更新函式對應) ：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-166">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="4d6b3-167">AssociationSetMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-167">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-168">對應規格語言 (MSL 中的 **AssociationSetMapping** 專案) 會定義概念模型中的關聯和基礎資料庫中的資料表資料行之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-168">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="4d6b3-169">概念模型中的關聯指其屬性代表基礎資料庫中主要與外部索引鍵資料行的類型。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-169">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="4d6b3-170">**AssociationSetMapping**元素會使用兩個 EndProperty 元素來定義資料庫中關聯類型屬性和資料行之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-170">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="4d6b3-171">您可以使用 Condition 項目在這些對應上放置條件。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-171">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="4d6b3-172">ModificationFunctionMapping 項目可以用來將關聯的插入、更新或刪除函式對應至資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-172">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="4d6b3-173">在 QueryView 元素中使用 Entity SQL 字串，以定義關聯和資料表資料行之間的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-173">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-174">如果為概念模型中的關聯定義了參考條件約束，則關聯不需要與 **AssociationSetMapping** 元素對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-174">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="4d6b3-175">如果具有參考條件約束的關聯有 **AssociationSetMapping** 專案，則會忽略 **AssociationSetMapping** 元素中定義的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-175">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="4d6b3-176">如需詳細資訊，請參閱 (CSDL) 的 ReferentialConstraint 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-176">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="4d6b3-177">**AssociationSetMapping**元素可以有下列子項目</span><span class="sxs-lookup"><span data-stu-id="4d6b3-177">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="4d6b3-178">QueryView (零或一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-178">QueryView (zero or one)</span></span>
-   <span data-ttu-id="4d6b3-179">EndProperty (零或兩個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-179">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="4d6b3-180">Condition (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-180">Condition (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-181">ModificationFunctionMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-181">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-182">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-182">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-183">下表說明可套用至 **AssociationSetMapping** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-183">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="4d6b3-184">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-184">Attribute Name</span></span>     | <span data-ttu-id="4d6b3-185">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-185">Is Required</span></span> | <span data-ttu-id="4d6b3-186">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-186">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-187">**名稱**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-187">**Name**</span></span>           | <span data-ttu-id="4d6b3-188">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-188">Yes</span></span>         | <span data-ttu-id="4d6b3-189">要對應的概念模型關聯集名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-189">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="4d6b3-190">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-190">**TypeName**</span></span>       | <span data-ttu-id="4d6b3-191">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-191">No</span></span>          | <span data-ttu-id="4d6b3-192">要對應的概念模型關聯類型之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-192">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="4d6b3-193">**中**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-193">**StoreEntitySet**</span></span> | <span data-ttu-id="4d6b3-194">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-194">No</span></span>          | <span data-ttu-id="4d6b3-195">要對應的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-195">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="4d6b3-196">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-196">Example</span></span>

<span data-ttu-id="4d6b3-197">下列範例顯示 **AssociationSetMapping** 元素，其中概念模型中的 **FK \_ 課程 \_ 部門** 關聯會對應至資料庫中的 **課程** 資料表。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-197">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="4d6b3-198">關聯類型屬性和資料表資料行之間的對應是在子 **EndProperty** 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-198">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="4d6b3-199">ComplexProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-199">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="4d6b3-200">對應規格語言 (MSL 中的 **ComplexProperty** 專案) 會定義概念模型實體類型上的複雜類型屬性和基礎資料庫中之資料表資料行之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-200">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="4d6b3-201">屬性資料行對應會在 ScalarProperty 子項目中指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-201">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="4d6b3-202">**ComplexType**屬性元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-202">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-203">ScalarProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-203">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-204">**ComplexProperty** (零或多個) </span><span class="sxs-lookup"><span data-stu-id="4d6b3-204">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-205">ComplextTypeMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-205">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-206">Condition (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-206">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-207">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-207">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-208">下表描述適用于 **ComplexProperty** 元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-208">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="4d6b3-209">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-209">Attribute Name</span></span> | <span data-ttu-id="4d6b3-210">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-210">Is Required</span></span> | <span data-ttu-id="4d6b3-211">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-211">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-212">**名稱**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-212">**Name**</span></span>       | <span data-ttu-id="4d6b3-213">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-213">Yes</span></span>         | <span data-ttu-id="4d6b3-214">概念模型中要對應的實體類型之複雜屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-214">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="4d6b3-215">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-215">**TypeName**</span></span>   | <span data-ttu-id="4d6b3-216">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-216">No</span></span>          | <span data-ttu-id="4d6b3-217">概念模型屬性類型的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-217">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="4d6b3-218">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-218">Example</span></span>

<span data-ttu-id="4d6b3-219">下列範例是以 School 模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-219">The following example is based on the School model.</span></span> <span data-ttu-id="4d6b3-220">下列複雜型別已加入至概念模型：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-220">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="4d6b3-221">**Person**實體類型的**LastName**和**FirstName**屬性已取代為一個複雜的屬性，**名稱**：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-221">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="4d6b3-222">下列 MSL 顯示用來將**Name**屬性對應到基礎資料庫中之資料行的**ComplexProperty**元素：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-222">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="4d6b3-223">ComplexTypeMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-223">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-224">對應規格語言 (MSL) 中的 **ComplexTypeMapping** 專案是 ResultMapping 專案的子系，並在下列情況成立時，定義概念模型中的函式匯入與基礎資料庫中的預存程式之間的對應：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-224">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="4d6b3-225">函式匯入會傳回概念複雜類型。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-225">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="4d6b3-226">預存程序所傳回之資料行名稱未與複雜類型上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-226">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="4d6b3-227">根據預設，預存程序所傳回之資料行與複雜類型間的對應是以資料行和屬性名稱為基礎。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-227">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="4d6b3-228">如果資料行名稱與屬性名稱不完全相符，您必須使用 **ComplexTypeMapping** 元素來定義對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-228">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="4d6b3-229">如需預設對應的範例，請參閱 FunctionImportMapping 元素 (MSL) 。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-229">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="4d6b3-230">**ComplexTypeMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-230">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-231">ScalarProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-231">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-232">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-232">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-233">下表描述適用于 **ComplexTypeMapping** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-233">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="4d6b3-234">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-234">Attribute Name</span></span> | <span data-ttu-id="4d6b3-235">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-235">Is Required</span></span> | <span data-ttu-id="4d6b3-236">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-236">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-237">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-237">**TypeName**</span></span>   | <span data-ttu-id="4d6b3-238">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-238">Yes</span></span>         | <span data-ttu-id="4d6b3-239">要對應的複雜類型的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-239">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-240">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-240">Example</span></span>

<span data-ttu-id="4d6b3-241">請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-241">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="4d6b3-242">同時請考慮下列概念模型複雜類型：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-242">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="4d6b3-243">為了建立會傳回前一個複雜類型之實例的函式匯入，必須在 **ComplexTypeMapping** 專案中定義預存程式所傳回之資料行與實體類型之間的對應：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-243">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="4d6b3-244">Condition 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-244">Condition Element (MSL)</span></span>

<span data-ttu-id="4d6b3-245">對應規格語言 (MSL 中的 **Condition** 元素) 在概念模型與基礎資料庫之間的對應上放置條件。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-245">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="4d6b3-246">如果符合子 **條件** 元素中所指定的所有條件，則在 XML 節點內定義的對應就會是有效的。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-246">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="4d6b3-247">否則，對應無效。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-247">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="4d6b3-248">例如，如果 MappingFragment 專案包含一或多個**條件**子項目，則只有在符合子**條件**元素的所有條件時， **MappingFragment**節點內所定義的對應才會是有效的。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-248">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="4d6b3-249">每個條件可以套用至概念模型實體屬性名稱 (名稱屬性所指定**的名稱，**) **或 (資料庫**中的資料行名稱（由**columnname** \*\*屬性) \*\*指定）。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-249">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="4d6b3-250">當設定 **Name** 屬性時，會針對實體屬性值檢查條件。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-250">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="4d6b3-251">設定 **ColumnName** 屬性時，會根據資料行值來檢查條件。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-251">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="4d6b3-252">只有一個 **Name** 或 **ColumnName** 屬性可以在 **Condition** 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-252">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-253">在 FunctionImportMapping 元素中使用 **Condition** 元素時，只有 **Name** 屬性不適用。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-253">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="4d6b3-254">**Condition**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-254">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="4d6b3-255">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="4d6b3-255">AssociationSetMapping</span></span>
-   <span data-ttu-id="4d6b3-256">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="4d6b3-256">ComplexProperty</span></span>
-   <span data-ttu-id="4d6b3-257">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="4d6b3-257">EntitySetMapping</span></span>
-   <span data-ttu-id="4d6b3-258">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="4d6b3-258">MappingFragment</span></span>
-   <span data-ttu-id="4d6b3-259">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="4d6b3-259">EntityTypeMapping</span></span>

<span data-ttu-id="4d6b3-260">**Condition**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-260">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-261">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-261">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-262">下表描述適用于 **Condition** 元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-262">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="4d6b3-263">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-263">Attribute Name</span></span> | <span data-ttu-id="4d6b3-264">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-264">Is Required</span></span> | <span data-ttu-id="4d6b3-265">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-265">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-266">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-266">**ColumnName**</span></span> | <span data-ttu-id="4d6b3-267">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-267">No</span></span>          | <span data-ttu-id="4d6b3-268">其值用來評估條件之資料表資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-268">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="4d6b3-269">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-269">**IsNull**</span></span>     | <span data-ttu-id="4d6b3-270">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-270">No</span></span>          | <span data-ttu-id="4d6b3-271">**True** 或 **False**。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-271">**True** or **False**.</span></span> <span data-ttu-id="4d6b3-272">如果值為 **True** 且資料行值為 **null**，或者如果值為 **False** 且資料行值不是 **null**，則條件為 true。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-272">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="4d6b3-273">否則，條件不成立。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-273">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="4d6b3-274">不能同時使用 **IsNull** 和 **Value** 屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-274">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="4d6b3-275">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-275">**Value**</span></span>      | <span data-ttu-id="4d6b3-276">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-276">No</span></span>          | <span data-ttu-id="4d6b3-277">要與資料行值比較的值。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-277">The value with which the column value is compared.</span></span> <span data-ttu-id="4d6b3-278">如果值相同，則條件成立。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-278">If the values are the same, the condition is true.</span></span> <span data-ttu-id="4d6b3-279">否則，條件不成立。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-279">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="4d6b3-280">不能同時使用 **IsNull** 和 **Value** 屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-280">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="4d6b3-281">**Name**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-281">**Name**</span></span>       | <span data-ttu-id="4d6b3-282">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-282">No</span></span>          | <span data-ttu-id="4d6b3-283">其值用來評估條件之概念模型實體屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-283">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="4d6b3-284">如果在 FunctionImportMapping 元素內使用 **Condition** 元素，則不適用這個屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-284">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="4d6b3-285">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-285">Example</span></span>

<span data-ttu-id="4d6b3-286">下列範例會將 **條件** 元素顯示為 **MappingFragment** 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-286">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="4d6b3-287">當**雇用**值不是 Null 且**EnrollmentDate**為 null 時，資料會在**SchoolModel**與**Person**資料表的**PersonID**和**雇用**日期資料行之間對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-287">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="4d6b3-288">當**EnrollmentDate**不是 null，且**雇用**值為 null 時，會在**SchoolModel**與**Person**資料表的**PersonID**和**註冊**資料行之間對應資料。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-288">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="4d6b3-289">DeleteFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-289">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="4d6b3-290">對應規格語言 (MSL 中的 **DeleteFunction** 專案) 將概念模型中之實體類型或關聯的刪除函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-290">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="4d6b3-291">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-291">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="4d6b3-292">如需詳細資訊，請參閱 (SSDL) 的 Function 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-292">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-293">如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-293">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="4d6b3-294">套用至 EntityTypeMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="4d6b3-294">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="4d6b3-295">當套用至 EntityTypeMapping 元素時， **DeleteFunction** 專案會將概念模型中之實體類型的刪除函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-295">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="4d6b3-296">**DeleteFunction**元素套用至**EntityTypeMapping**專案時，可能會有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-296">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="4d6b3-297">AssociationEnd (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-297">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-298">ComplexProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-298">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-299">ScarlarProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-299">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-300">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-300">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-301">下表描述可套用至 **DeleteFunction** 專案的屬性（當它套用至 **EntityTypeMapping** 元素時）。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-301">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="4d6b3-302">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-302">Attribute Name</span></span>            | <span data-ttu-id="4d6b3-303">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-303">Is Required</span></span> | <span data-ttu-id="4d6b3-304">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-304">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-305">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-305">**FunctionName**</span></span>          | <span data-ttu-id="4d6b3-306">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-306">Yes</span></span>         | <span data-ttu-id="4d6b3-307">刪除函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-307">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="4d6b3-308">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-308">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="4d6b3-309">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-309">**RowsAffectedParameter**</span></span> | <span data-ttu-id="4d6b3-310">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-310">No</span></span>          | <span data-ttu-id="4d6b3-311">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-311">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="4d6b3-312">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-312">Example</span></span>

<span data-ttu-id="4d6b3-313">下列範例是以 School 模型為基礎，並顯示將**Person**實體類型的刪除函式對應至**DeletePerson**預存程式的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-313">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="4d6b3-314">**DeletePerson**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-314">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="4d6b3-315">套用至 AssociationSetMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="4d6b3-315">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="4d6b3-316">當套用至 AssociationSetMapping 元素時， **DeleteFunction** 專案會將概念模型中關聯的刪除函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-316">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="4d6b3-317">**DeleteFunction**元素在套用至**AssociationSetMapping**專案時，可能會有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-317">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="4d6b3-318">EndProperty</span><span class="sxs-lookup"><span data-stu-id="4d6b3-318">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-319">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-319">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-320">下表描述套用至**AssociationSetMapping**專案時，可套用至**DeleteFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-320">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="4d6b3-321">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-321">Attribute Name</span></span>            | <span data-ttu-id="4d6b3-322">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-322">Is Required</span></span> | <span data-ttu-id="4d6b3-323">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-323">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-324">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-324">**FunctionName**</span></span>          | <span data-ttu-id="4d6b3-325">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-325">Yes</span></span>         | <span data-ttu-id="4d6b3-326">刪除函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-326">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="4d6b3-327">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-327">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="4d6b3-328">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-328">**RowsAffectedParameter**</span></span> | <span data-ttu-id="4d6b3-329">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-329">No</span></span>          | <span data-ttu-id="4d6b3-330">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-330">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="4d6b3-331">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-331">Example</span></span>

<span data-ttu-id="4d6b3-332">下列範例是以 School 模型為基礎，並顯示用來將**CourseInstructor**關聯的刪除函式對應至**DeleteCourseInstructor**預存程式的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-332">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="4d6b3-333">**DeleteCourseInstructor**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-333">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="4d6b3-334">EndProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-334">EndProperty Element (MSL)</span></span>

<span data-ttu-id="4d6b3-335">對應規格語言 (MSL 中的 **EndProperty** 專案) 會定義概念模型關聯和基礎資料庫的 end 或修改函數之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-335">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="4d6b3-336">屬性資料行對應會指定在 ScalarProperty 子項目中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-336">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="4d6b3-337">當 **EndProperty** 元素用來定義概念模型關聯 end 的對應時，它是 AssociationSetMapping 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-337">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="4d6b3-338">當 **EndProperty** 元素用來定義概念模型關聯之修改函式的對應時，它是 InsertFunction 元素或 DeleteFunction 專案的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-338">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="4d6b3-339">**EndProperty**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-339">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-340">ScalarProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-340">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-341">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-341">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-342">下表描述適用于 **EndProperty** 元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-342">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="4d6b3-343">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-343">Attribute Name</span></span> | <span data-ttu-id="4d6b3-344">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-344">Is Required</span></span> | <span data-ttu-id="4d6b3-345">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-345">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="4d6b3-346">名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-346">Name</span></span>           | <span data-ttu-id="4d6b3-347">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-347">Yes</span></span>         | <span data-ttu-id="4d6b3-348">要對應的關聯端名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-348">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-349">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-349">Example</span></span>

<span data-ttu-id="4d6b3-350">下列範例顯示 **AssociationSetMapping** 元素，其中概念模型中的 **FK \_ 課程 \_ 部門** 關聯會對應至資料庫中的 **課程** 資料表。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-350">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="4d6b3-351">關聯類型屬性和資料表資料行之間的對應是在子 **EndProperty** 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-351">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="4d6b3-352">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-352">Example</span></span>

<span data-ttu-id="4d6b3-353">下列範例顯示 **EndProperty** 專案，將關聯的插入和刪除函數 (**CourseInstructor**) 對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-353">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="4d6b3-354">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-354">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="4d6b3-355">EntityContainerMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-355">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-356">對應規格語言 (MSL 中的 **EntityContainerMapping** 專案) 將概念模型中的實體容器對應至儲存體模型中的實體容器。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-356">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="4d6b3-357">**EntityContainerMapping**元素是對應元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-357">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="4d6b3-358">**EntityContainerMapping**元素可以依照列出的順序 (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-358">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="4d6b3-359">EntitySetMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-359">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-360">AssociationSetMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-360">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-361">FunctionImportMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-361">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-362">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-362">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-363">下表說明可套用至 **EntityContainerMapping** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-363">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="4d6b3-364">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-364">Attribute Name</span></span>            | <span data-ttu-id="4d6b3-365">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-365">Is Required</span></span> | <span data-ttu-id="4d6b3-366">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-366">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-367">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-367">**StorageModelContainer**</span></span> | <span data-ttu-id="4d6b3-368">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-368">Yes</span></span>         | <span data-ttu-id="4d6b3-369">要對應至的儲存模型實體容器名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-369">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="4d6b3-370">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-370">**CdmEntityContainer**</span></span>    | <span data-ttu-id="4d6b3-371">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-371">Yes</span></span>         | <span data-ttu-id="4d6b3-372">要對應的概念模型實體容器名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-372">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="4d6b3-373">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-373">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="4d6b3-374">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-374">No</span></span>          | <span data-ttu-id="4d6b3-375">**True** 或 **False**。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-375">**True** or **False**.</span></span> <span data-ttu-id="4d6b3-376">如果 **為 False**，則不會產生任何更新視圖。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-376">If **False**, no update views are generated.</span></span> <span data-ttu-id="4d6b3-377">當您有唯讀對應，且資料可能無法成功地往返時，這個屬性應該設定為 **False** 。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-377">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="4d6b3-378">預設值是 **True**。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-378">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-379">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-379">Example</span></span>

<span data-ttu-id="4d6b3-380">下列範例顯示 **EntityContainerMapping** 元素，此專案 **會將概念** 模型實體容器)  (概念模型實體容器，以 (儲存體模型實體容器) 的 **SchoolModelStoreContainer** 容器：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-380">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="4d6b3-381">EntitySetMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-381">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-382">對應規格語言 (MSL 中的 **EntitySetMapping** 專案) 將概念模型實體集中的所有型別對應到儲存體模型中的實體集。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-382">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="4d6b3-383">概念模型中的實體集就是相同型別 (及衍生型別) 之實體的執行個體邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-383">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="4d6b3-384">儲存模型中的實體集代表基礎資料庫中的資料表或檢視。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-384">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="4d6b3-385">概念模型實體集是由**EntitySetMapping**元素的**Name**屬性值所指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-385">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="4d6b3-386">對應至資料表或視圖是由每個子 MappingFragment 專案或**EntitySetMapping**元素本身中的**中**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-386">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="4d6b3-387">**EntitySetMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-387">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-388">EntityTypeMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-388">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-389">QueryView (零或一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-389">QueryView (zero or one)</span></span>
-   <span data-ttu-id="4d6b3-390">MappingFragment (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-390">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-391">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-391">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-392">下表說明可套用至 **EntitySetMapping** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-392">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="4d6b3-393">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-393">Attribute Name</span></span>           | <span data-ttu-id="4d6b3-394">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-394">Is Required</span></span> | <span data-ttu-id="4d6b3-395">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-395">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-396">**名稱**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-396">**Name**</span></span>                 | <span data-ttu-id="4d6b3-397">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-397">Yes</span></span>         | <span data-ttu-id="4d6b3-398">要對應的概念模型實體集名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-398">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="4d6b3-399">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-399">**TypeName** **1**</span></span>       | <span data-ttu-id="4d6b3-400">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-400">No</span></span>          | <span data-ttu-id="4d6b3-401">要對應的概念模型實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-401">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="4d6b3-402">**中** **1**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-402">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="4d6b3-403">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-403">No</span></span>          | <span data-ttu-id="4d6b3-404">要對應至的儲存模型實體集名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-404">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="4d6b3-405">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-405">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="4d6b3-406">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-406">No</span></span>          | <span data-ttu-id="4d6b3-407">**True** 或 **False** ，取決於是否只傳回相異資料列。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-407">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="4d6b3-408">如果這個屬性設定為 **True**，則 EntityContainerMapping 專案的 **GenerateUpdateViews** 屬性必須設定為 **False**。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-408">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="4d6b3-409">**1** ： **TypeName** 和 **中** 屬性可用來取代 EntityTypeMapping 和 MappingFragment 子項目，以將單一實體類型對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-409">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="4d6b3-410">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-410">Example</span></span>

<span data-ttu-id="4d6b3-411">下列範例顯示 **EntitySetMapping** 元素，此專案會將三種類型 (基底類型，以及在概念模型的 **課程** 實體集中) 的兩個衍生型別，以及基礎資料庫中的三個不同資料表。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-411">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="4d6b3-412">資料表是由每個**MappingFragment**元素中的**中**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-412">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="4d6b3-413">EntityTypeMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-413">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-414">對應規格語言 (MSL 中的 **EntityTypeMapping** 專案) 會定義概念模型中的實體類型和基礎資料庫中的資料表或視圖之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-414">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="4d6b3-415">如需概念模型實體類型和基礎資料庫資料表或檢視的詳細資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-415">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="4d6b3-416">正在對應的概念模型實體類型是由**EntityTypeMapping**專案的**TypeName**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-416">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="4d6b3-417">正在對應的資料表或視圖是由子 MappingFragment 專案的 **中** 屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-417">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="4d6b3-418">ModificationFunctionMapping 子項目可以用來將實體類型的插入、更新或刪除函式對應至資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-418">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="4d6b3-419">**EntityTypeMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-419">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-420">MappingFragment (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-420">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-421">ModificationFunctionMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-421">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="4d6b3-422">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="4d6b3-422">ScalarProperty</span></span>
-   <span data-ttu-id="4d6b3-423">條件</span><span class="sxs-lookup"><span data-stu-id="4d6b3-423">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-424">**MappingFragment** 和 **ModificationFunctionMapping** 元素不能同時為 **EntityTypeMapping** 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-424">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="4d6b3-425">**ScalarProperty**和**Condition**元素在 FunctionImportMapping 元素中使用時，只能是**EntityTypeMapping**元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-425">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-426">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-426">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-427">下表說明可套用至 **EntityTypeMapping** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-427">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="4d6b3-428">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-428">Attribute Name</span></span> | <span data-ttu-id="4d6b3-429">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-429">Is Required</span></span> | <span data-ttu-id="4d6b3-430">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-430">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-431">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-431">**TypeName**</span></span>   | <span data-ttu-id="4d6b3-432">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-432">Yes</span></span>         | <span data-ttu-id="4d6b3-433">要對應的概念模型實體類型之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-433">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="4d6b3-434">如果型別是抽象型別或衍生型別，值必須是 `IsOfType(Namespace-qualified_type_name)`。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-434">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-435">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-435">Example</span></span>

<span data-ttu-id="4d6b3-436">下列範例顯示具有兩個子 **EntityTypeMapping** 專案的 EntitySetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-436">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="4d6b3-437">在第一個 **EntityTypeMapping** 元素中， **SchoolModel person** 實體類型會對應到 **person** 資料表。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-437">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="4d6b3-438">在第二個 **EntityTypeMapping** 元素中， **SchoolModel** 的更新功能會對應到資料庫中的預存程式 **UpdatePerson**。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-438">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="4d6b3-439">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-439">Example</span></span>

<span data-ttu-id="4d6b3-440">下一個範例會顯示型別階層的對應，在該階層內根型別是抽象型別。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-440">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="4d6b3-441">請注意 `IsOfType` **TypeName** 屬性的語法用法。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-441">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="4d6b3-442">FunctionImportMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-442">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-443">對應規格語言 (MSL 中的 **FunctionImportMapping** 元素) 會定義概念模型中的函式匯入與基礎資料庫中的預存程式或函數之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-443">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="4d6b3-444">函式匯入必須在概念模型中宣告，而預存程序則必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-444">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="4d6b3-445">如需詳細資訊，請參閱 (SSDL) 的 FunctionImport 元素 (CSDL) 和 Function 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-445">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-446">根據預設，如果函式匯入傳回概念模型實體類型或複雜型別，則基礎預存程序所傳回的資料行名稱必須與概念模型類型上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-446">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="4d6b3-447">如果資料行名稱與屬性名稱不完全相符，則必須在 ResultMapping 元素中定義對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-447">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="4d6b3-448">**FunctionImportMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-448">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-449">ResultMapping (零或多個) </span><span class="sxs-lookup"><span data-stu-id="4d6b3-449">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-450">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-450">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-451">下表描述適用于 **FunctionImportMapping** 元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-451">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="4d6b3-452">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-452">Attribute Name</span></span>         | <span data-ttu-id="4d6b3-453">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-453">Is Required</span></span> | <span data-ttu-id="4d6b3-454">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-454">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-455">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-455">**FunctionImportName**</span></span> | <span data-ttu-id="4d6b3-456">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-456">Yes</span></span>         | <span data-ttu-id="4d6b3-457">概念模型中要對應的函式匯入名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-457">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="4d6b3-458">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-458">**FunctionName**</span></span>       | <span data-ttu-id="4d6b3-459">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-459">Yes</span></span>         | <span data-ttu-id="4d6b3-460">儲存體模型中要對應的函式之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-460">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-461">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-461">Example</span></span>

<span data-ttu-id="4d6b3-462">下列範例是以 School 模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-462">The following example is based on the School model.</span></span> <span data-ttu-id="4d6b3-463">請考量儲存體模型中的下列函式：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-463">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="4d6b3-464">同時考量概念模型中的這個函式匯入：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-464">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="4d6b3-465">下列範例顯示用來將函式和函式匯入對應至彼此的 **FunctionImportMapping** 專案：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-465">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="4d6b3-466">InsertFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-466">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="4d6b3-467">對應規格語言 (MSL 中的 **InsertFunction** 專案) 將概念模型中之實體類型或關聯的插入函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-467">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="4d6b3-468">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-468">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="4d6b3-469">如需詳細資訊，請參閱 (SSDL) 的 Function 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-469">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-470">如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-470">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="4d6b3-471">**InsertFunction**元素可以是 ModificationFunctionMapping 專案的子系，並套用至 EntityTypeMapping 元素或 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-471">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="4d6b3-472">套用至 EntityTypeMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="4d6b3-472">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="4d6b3-473">當套用至 EntityTypeMapping 元素時， **InsertFunction** 專案會將概念模型中實體類型的插入函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-473">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="4d6b3-474">**InsertFunction**元素套用至**EntityTypeMapping**專案時，可能會有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-474">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="4d6b3-475">AssociationEnd (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-475">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-476">ComplexProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-476">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-477">ResultBinding (零或一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-477">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="4d6b3-478">ScarlarProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-478">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-479">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-479">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-480">下表描述套用至**EntityTypeMapping**專案時，可套用至**InsertFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-480">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="4d6b3-481">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-481">Attribute Name</span></span>            | <span data-ttu-id="4d6b3-482">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-482">Is Required</span></span> | <span data-ttu-id="4d6b3-483">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-483">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-484">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-484">**FunctionName**</span></span>          | <span data-ttu-id="4d6b3-485">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-485">Yes</span></span>         | <span data-ttu-id="4d6b3-486">插入函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-486">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="4d6b3-487">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-487">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="4d6b3-488">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-488">**RowsAffectedParameter**</span></span> | <span data-ttu-id="4d6b3-489">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-489">No</span></span>          | <span data-ttu-id="4d6b3-490">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-490">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="4d6b3-491">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-491">Example</span></span>

<span data-ttu-id="4d6b3-492">下列範例是以 School 模型為基礎，並顯示用來將 Person 實體類型的插入函式對應至**InsertPerson**預存程式的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-492">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="4d6b3-493">**InsertPerson**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-493">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="4d6b3-494">套用至 AssociationSetMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="4d6b3-494">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="4d6b3-495">當套用至 AssociationSetMapping 元素時， **InsertFunction** 專案會將概念模型中關聯的插入函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-495">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="4d6b3-496">**InsertFunction**元素在套用至**AssociationSetMapping**專案時，可能會有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-496">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="4d6b3-497">EndProperty</span><span class="sxs-lookup"><span data-stu-id="4d6b3-497">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-498">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-498">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-499">下表描述套用至**AssociationSetMapping**專案時，可套用至**InsertFunction**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-499">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="4d6b3-500">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-500">Attribute Name</span></span>            | <span data-ttu-id="4d6b3-501">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-501">Is Required</span></span> | <span data-ttu-id="4d6b3-502">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-502">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-503">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-503">**FunctionName**</span></span>          | <span data-ttu-id="4d6b3-504">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-504">Yes</span></span>         | <span data-ttu-id="4d6b3-505">插入函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-505">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="4d6b3-506">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-506">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="4d6b3-507">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-507">**RowsAffectedParameter**</span></span> | <span data-ttu-id="4d6b3-508">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-508">No</span></span>          | <span data-ttu-id="4d6b3-509">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-509">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="4d6b3-510">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-510">Example</span></span>

<span data-ttu-id="4d6b3-511">下列範例是以 School 模型為基礎，並顯示用來將**CourseInstructor**關聯的插入函式對應至**InsertCourseInstructor**預存程式的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-511">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="4d6b3-512">**InsertCourseInstructor**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-512">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="4d6b3-513">Mapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-513">Mapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-514">對應規格語言 (MSL 中的 **對應** 元素) 包含將概念模型中定義的物件對應至資料庫 (的資訊，如儲存體模型) 中所述。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-514">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="4d6b3-515">如需詳細資訊，請參閱 CSDL 規格和 SSDL 規格。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-515">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="4d6b3-516">**Mapping**元素是對應規格的根項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-516">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="4d6b3-517">對應規格的 XML 命名空間為 https://schemas.microsoft.com/ado/2009/11/mapping/cs 。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-517">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="4d6b3-518">對應項目可以擁有下列子項目 (依列出的順序)：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-518">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="4d6b3-519">Alias (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-519">Alias (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-520">EntityContainerMapping (只有一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-520">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="4d6b3-521">MSL 中所參考之概念及儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-521">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="4d6b3-522">如需概念模型命名空間名稱的詳細資訊，請參閱 (CSDL) 的架構元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-522">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="4d6b3-523">如需儲存模型命名空間名稱的詳細資訊，請參閱 (SSDL) 的架構元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-523">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="4d6b3-524">MSL 中所使用之命名空間的別名可以使用 Alias 項目加以定義。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-524">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-525">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-525">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-526">下表說明可套用至 **對應** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-526">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="4d6b3-527">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-527">Attribute Name</span></span> | <span data-ttu-id="4d6b3-528">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-528">Is Required</span></span> | <span data-ttu-id="4d6b3-529">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-529">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="4d6b3-530">**空間**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-530">**Space**</span></span>      | <span data-ttu-id="4d6b3-531">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-531">Yes</span></span>         | <span data-ttu-id="4d6b3-532">**C-S**。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-532">**C-S**.</span></span> <span data-ttu-id="4d6b3-533">這是固定值，無法變更。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-533">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-534">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-534">Example</span></span>

<span data-ttu-id="4d6b3-535">下列範例顯示以 School 模型的一部分為基礎的 **對應** 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-535">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="4d6b3-536">如需 School 模型的詳細資訊，請參閱快速入門 (Entity Framework) ：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-536">For more information about the School model, see Quickstart (Entity Framework):</span></span>

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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="4d6b3-537">MappingFragment 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-537">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="4d6b3-538">對應規格語言 (MSL 中的 **MappingFragment** 專案) 會定義概念模型實體類型的屬性和資料庫中的資料表或視圖之間的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-538">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="4d6b3-539">如需概念模型實體類型和基礎資料庫資料表或檢視的詳細資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-539">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="4d6b3-540">**MappingFragment**可以是 EntityTypeMapping 元素或 EntitySetMapping 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-540">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="4d6b3-541">**MappingFragment**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-541">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-542">ComplexType (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-542">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-543">ScalarProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-543">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-544">Condition (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-544">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-545">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-545">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-546">下表說明可套用至 **MappingFragment** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-546">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="4d6b3-547">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-547">Attribute Name</span></span>          | <span data-ttu-id="4d6b3-548">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-548">Is Required</span></span> | <span data-ttu-id="4d6b3-549">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-549">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-550">**中**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-550">**StoreEntitySet**</span></span>      | <span data-ttu-id="4d6b3-551">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-551">Yes</span></span>         | <span data-ttu-id="4d6b3-552">要對應的資料表或檢視之名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-552">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="4d6b3-553">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-553">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="4d6b3-554">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-554">No</span></span>          | <span data-ttu-id="4d6b3-555">**True** 或 **False** ，取決於是否只傳回相異資料列。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-555">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="4d6b3-556">如果這個屬性設定為 **True**，則 EntityContainerMapping 專案的 **GenerateUpdateViews** 屬性必須設定為 **False**。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-556">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-557">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-557">Example</span></span>

<span data-ttu-id="4d6b3-558">下列範例顯示 **MappingFragment** 專案做為 **EntityTypeMapping** 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-558">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="4d6b3-559">在此範例中，概念模型中 **課程** 類型的屬性會對應至資料庫中 **課程** 資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-559">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="4d6b3-560">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-560">Example</span></span>

<span data-ttu-id="4d6b3-561">下列範例顯示 **MappingFragment** 專案做為 **EntitySetMapping** 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-561">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="4d6b3-562">如同上述範例，概念模型中 **課程** 類型的屬性會對應至資料庫中 **課程** 資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-562">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="4d6b3-563">ModificationFunctionMapping 屬性 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-563">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-564">對應規格語言 (MSL 中的 **ModificationFunctionMapping** 元素) 將概念模型實體類型的插入、更新和刪除函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-564">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="4d6b3-565">**ModificationFunctionMapping**元素也可以將概念模型中多對多關聯的插入和刪除函數對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-565">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="4d6b3-566">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-566">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="4d6b3-567">如需詳細資訊，請參閱 (SSDL) 的 Function 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-567">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-568">如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-568">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="4d6b3-569">如果繼承階層架構中某個實體的修改函式已對應至預存程序，則階層架構中所有類型的修改函式都必須對應至預存程序。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-569">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="4d6b3-570">**ModificationFunctionMapping**元素可以是 EntityTypeMapping 元素或 AssociationSetMapping 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-570">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="4d6b3-571">**ModificationFunctionMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-571">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-572">DeleteFunction (零或一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-572">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="4d6b3-573">InsertFunction (零或一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-573">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="4d6b3-574">UpdateFunction (零或一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-574">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="4d6b3-575">**ModificationFunctionMapping**元素沒有任何可用的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-575">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="4d6b3-576">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-576">Example</span></span>

<span data-ttu-id="4d6b3-577">下列範例顯示 School 模型中的 **人員** 實體集的實體集對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-577">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="4d6b3-578">除了 **person** 實體類型的資料行對應之外，還會顯示 **person** 型別的插入、更新和刪除函式的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-578">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="4d6b3-579">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-579">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="4d6b3-580">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-580">Example</span></span>

<span data-ttu-id="4d6b3-581">下列範例顯示 School 模型中 **CourseInstructor** 關聯集的關聯集對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-581">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="4d6b3-582">除了 **CourseInstructor** 關聯的資料行對應之外，還會顯示 **CourseInstructor** 關聯的插入和刪除函數對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-582">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="4d6b3-583">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-583">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="4d6b3-584">QueryView 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-584">QueryView Element (MSL)</span></span>

<span data-ttu-id="4d6b3-585">對應規格語言 (MSL 中的 **QueryView** 專案) 會定義概念模型中的實體類型或關聯和基礎資料庫中的資料表之間的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-585">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="4d6b3-586">對應會使用針對儲存體模型評估的 Entity SQL 查詢來定義，而您會根據概念模型中的實體或關聯來表示結果集。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-586">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="4d6b3-587">因為查詢檢視是唯讀的，無法使用標準更新命令來更新查詢檢視所定義的類型。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-587">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="4d6b3-588">您可以使用修改函式來更新這些類型。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-588">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="4d6b3-589">如需詳細資訊，請參閱如何：將修改函式對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-589">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-590">在 **QueryView** 元素中，不支援包含 **GroupBy**、群組匯總或導覽屬性 Entity SQL 運算式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-590">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="4d6b3-591">**QueryView**元素可以是 EntitySetMapping 元素或 AssociationSetMapping 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-591">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="4d6b3-592">若是前者，查詢檢視會定義概念模型中之實體的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-592">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="4d6b3-593">若是後者，查詢檢視會定義概念模型中之關聯的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-593">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-594">如果 **AssociationSetMapping** 元素適用于與參考條件約束的關聯，則會忽略 **AssociationSetMapping** 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-594">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="4d6b3-595">如需詳細資訊，請參閱 (CSDL) 的 ReferentialConstraint 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-595">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="4d6b3-596">**QueryView**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-596">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-597">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-597">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-598">下表說明可套用至 **QueryView** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-598">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="4d6b3-599">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-599">Attribute Name</span></span> | <span data-ttu-id="4d6b3-600">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-600">Is Required</span></span> | <span data-ttu-id="4d6b3-601">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-601">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-602">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-602">**TypeName**</span></span>   | <span data-ttu-id="4d6b3-603">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-603">No</span></span>          | <span data-ttu-id="4d6b3-604">要由查詢檢視對應的概念模型類型名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-604">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-605">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-605">Example</span></span>

<span data-ttu-id="4d6b3-606">下列範例會將 **QueryView** 元素顯示為 **EntitySetMapping** 專案的子系，並在 School 模型中定義 **部門** 實體類型的查詢檢視對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-606">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="4d6b3-607">因為查詢只會傳回儲存體模型中 **部門** 類型的成員子集，所以學校模型中的 **部門** 類型已根據此對應進行修改，如下所示：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-607">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="4d6b3-608">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-608">Example</span></span>

<span data-ttu-id="4d6b3-609">下一個範例顯示 **QueryView** 專案做為 **AssociationSetMapping** 專案的子系，並定義 `FK_Course_Department` School 模型中關聯的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-609">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="4d6b3-610">註解</span><span class="sxs-lookup"><span data-stu-id="4d6b3-610">Comments</span></span>

<span data-ttu-id="4d6b3-611">您可以定義查詢檢視來啟用下列案例：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-611">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="4d6b3-612">定義概念模型中的實體，其中未包含儲存體模型中實體的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-612">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="4d6b3-613">這包括沒有預設值且不支援 **null** 值的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-613">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="4d6b3-614">將儲存體模型中的計算資料行對應至概念模型中實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-614">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="4d6b3-615">定義分割概念模型實體所用之條件不是以實體為依據的對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-615">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="4d6b3-616">當您使用 **Condition** 元素指定條件式對應時，提供的條件必須等於指定的值。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-616">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="4d6b3-617">如需詳細資訊，請參閱 Condition 元素 (MSL) 。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-617">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="4d6b3-618">將儲存體模型中的相同資料行對應至概念模型中的多種類型。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-618">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="4d6b3-619">將多種類型對應至相同資料表。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-619">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="4d6b3-620">定義概念模型中不是以關聯式結構描述之外部索引鍵為依據的關聯。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-620">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="4d6b3-621">使用自訂商務邏輯來設定概念模型中的屬性值。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-621">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="4d6b3-622">例如，您可以將資料來源中的字串值 "T" 對應到概念模型中的 **true**值（布林值）。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-622">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="4d6b3-623">為查詢結果定義條件篩選器。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-623">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="4d6b3-624">對概念模型資料強加的限制比儲存體模型的少。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-624">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="4d6b3-625">例如，您可以將概念模型中的屬性設為 null，即使它所對應的資料行不支援 **null**值也是如此。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-625">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="4d6b3-626">下列考量適用於為實體定義查詢檢視的情況：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-626">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="4d6b3-627">查詢檢視是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-627">Query views are read-only.</span></span> <span data-ttu-id="4d6b3-628">您只能使用修改函式來更新這些實體。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-628">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="4d6b3-629">當您依據查詢檢視定義實體類型時，也必須依據查詢檢視定義所有的相關實體。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-629">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="4d6b3-630">當您將多對多關聯對應到儲存模型中的實體（代表關聯式結構描述中的連結資料表）時，您必須在此連結資料表的**AssociationSetMapping**元素中定義**QueryView**專案。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-630">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="4d6b3-631">必須為類型階層架構中的所有類型定義查詢檢視。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-631">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="4d6b3-632">您可以透過下列方法完成這項作業：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-632">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="4d6b3-633">具有單一 **QueryView** 元素，其指定會傳回階層中所有實體類型聯集的單一 Entity SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-633">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="4d6b3-634">具有單一 **QueryView** 元素，此專案會指定使用 CASE 運算子的單一 Entity SQL 查詢，以根據特定條件傳回階層中的特定實體類型。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-634">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="4d6b3-635">具有階層中特定類型的其他 **QueryView** 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-635">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="4d6b3-636">在此情況下，請使用**QueryView**元素的**TypeName**屬性來指定每個視圖的實體類型。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-636">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="4d6b3-637">當查詢檢視已定義時，您不能在**EntitySetMapping**元素上指定**StorageSetName**屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-637">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="4d6b3-638">當定義查詢檢視時， **EntitySetMapping**元素也不能包含 **屬性** 對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-638">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="4d6b3-639">ResultBinding 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-639">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="4d6b3-640">當實體類型修改函式對應至基礎資料庫中的預存程式時，對應規格語言 (MSL 中的 **ResultBinding** 專案) 會將預存程式所傳回的資料行值對應至概念模型中的實體屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-640">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="4d6b3-641">例如，當 insert 預存程式傳回 identity 資料行的值時， **ResultBinding** 專案會將傳回的值對應至概念模型中的實體類型屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-641">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="4d6b3-642">**ResultBinding**元素可以是 InsertFunction 元素或 UpdateFunction 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-642">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="4d6b3-643">**ResultBinding**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-643">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-644">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-644">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-645">下表描述適用于 **ResultBinding** 元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-645">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="4d6b3-646">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-646">Attribute Name</span></span> | <span data-ttu-id="4d6b3-647">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-647">Is Required</span></span> | <span data-ttu-id="4d6b3-648">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-648">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-649">**名稱**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-649">**Name**</span></span>       | <span data-ttu-id="4d6b3-650">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-650">Yes</span></span>         | <span data-ttu-id="4d6b3-651">概念模型中要對應之實體屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-651">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="4d6b3-652">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-652">**ColumnName**</span></span> | <span data-ttu-id="4d6b3-653">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-653">Yes</span></span>         | <span data-ttu-id="4d6b3-654">要對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-654">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="4d6b3-655">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-655">Example</span></span>

<span data-ttu-id="4d6b3-656">下列範例是以 School 模型為基礎，並顯示用來將**Person**實體類型的插入函式對應至**InsertPerson**預存程式的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-656">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="4d6b3-657"> (**InsertPerson** 預存程式如下所示，並在儲存體模型中宣告。 ) **ResultBinding** 元素可用來將預存程式所傳回的資料行值， (**NewPersonID**) 對應至實體類型屬性 (**PersonID**) 。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-657">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="4d6b3-658">下列 Transact-sql 描述 **InsertPerson** 預存程式：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-658">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="4d6b3-659">ResultMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-659">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="4d6b3-660">對應規格語言 (MSL 中的 **ResultMapping** 專案) 會在下列情況成立時，定義概念模型中的函式匯入與基礎資料庫中的預存程式之間的對應：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-660">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="4d6b3-661">函式匯入會傳回概念模型實體類型或複雜型別。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-661">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="4d6b3-662">預存程序所傳回之資料行名稱未與實體類型或複雜型別上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-662">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="4d6b3-663">根據預設，預存程序所傳回之資料行與實體類型或複雜型別間的對應是以資料行和屬性名稱為基礎。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-663">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="4d6b3-664">如果資料行名稱與屬性名稱不完全相符，您必須使用 **ResultMapping** 元素來定義對應。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-664">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="4d6b3-665">如需預設對應的範例，請參閱 FunctionImportMapping 元素 (MSL) 。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-665">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="4d6b3-666">**ResultMapping**元素是 FunctionImportMapping 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-666">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="4d6b3-667">**ResultMapping**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-667">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-668">EntityTypeMapping (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-668">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-669">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="4d6b3-669">ComplexTypeMapping</span></span>

<span data-ttu-id="4d6b3-670">**ResultMapping**元素沒有任何可用的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-670">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="4d6b3-671">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-671">Example</span></span>

<span data-ttu-id="4d6b3-672">請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-672">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="4d6b3-673">同時請考慮下列概念模型實體類型：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-673">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="4d6b3-674">為了建立會傳回前一個實體類型之實例的函式匯入，必須在 **ResultMapping** 專案中定義預存程式所傳回之資料行與實體類型之間的對應：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-674">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="4d6b3-675">ScalarProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-675">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="4d6b3-676">對應規格語言 (MSL 中的 **ScalarProperty** 專案) 將概念模型實體類型、複雜類型或關聯的屬性對應至基礎資料庫中的資料表資料行或預存程式參數。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-676">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="4d6b3-677">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-677">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="4d6b3-678">如需詳細資訊，請參閱 (SSDL) 的 Function 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-678">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="4d6b3-679">**ScalarProperty**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-679">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="4d6b3-680">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="4d6b3-680">MappingFragment</span></span>
-   <span data-ttu-id="4d6b3-681">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="4d6b3-681">InsertFunction</span></span>
-   <span data-ttu-id="4d6b3-682">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="4d6b3-682">UpdateFunction</span></span>
-   <span data-ttu-id="4d6b3-683">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="4d6b3-683">DeleteFunction</span></span>
-   <span data-ttu-id="4d6b3-684">EndProperty</span><span class="sxs-lookup"><span data-stu-id="4d6b3-684">EndProperty</span></span>
-   <span data-ttu-id="4d6b3-685">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="4d6b3-685">ComplexProperty</span></span>
-   <span data-ttu-id="4d6b3-686">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="4d6b3-686">ResultMapping</span></span>

<span data-ttu-id="4d6b3-687">**ScalarProperty**專案是**MappingFragment**、 **ComplexProperty**或**EndProperty**元素的子系，會將概念模型中的屬性對應至資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-687">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="4d6b3-688">**ScalarProperty**專案是**InsertFunction**、 **UpdateFunction**或**DeleteFunction**元素的子系，會將概念模型中的屬性對應至預存程式參數。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-688">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="4d6b3-689">**ScalarProperty**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-689">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-690">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-690">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-691">適用于 **ScalarProperty** 元素的屬性會因專案的角色而有所不同。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-691">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="4d6b3-692">下表描述當使用 **ScalarProperty** 元素將概念模型屬性對應至資料庫中的資料行時，適用的屬性：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-692">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="4d6b3-693">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-693">Attribute Name</span></span> | <span data-ttu-id="4d6b3-694">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-694">Is Required</span></span> | <span data-ttu-id="4d6b3-695">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-695">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="4d6b3-696">**名稱**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-696">**Name**</span></span>       | <span data-ttu-id="4d6b3-697">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-697">Yes</span></span>         | <span data-ttu-id="4d6b3-698">要對應的概念模型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-698">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="4d6b3-699">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-699">**ColumnName**</span></span> | <span data-ttu-id="4d6b3-700">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-700">Yes</span></span>         | <span data-ttu-id="4d6b3-701">要對應的資料表資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-701">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="4d6b3-702">下表描述當用來將概念模型屬性對應至預存程式參數時，適用于 **ScalarProperty** 元素的屬性：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-702">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="4d6b3-703">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-703">Attribute Name</span></span>    | <span data-ttu-id="4d6b3-704">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-704">Is Required</span></span> | <span data-ttu-id="4d6b3-705">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-705">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-706">**名稱**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-706">**Name**</span></span>          | <span data-ttu-id="4d6b3-707">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-707">Yes</span></span>         | <span data-ttu-id="4d6b3-708">要對應的概念模型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-708">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="4d6b3-709">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-709">**ParameterName**</span></span> | <span data-ttu-id="4d6b3-710">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-710">Yes</span></span>         | <span data-ttu-id="4d6b3-711">要對應的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-711">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="4d6b3-712">**版本**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-712">**Version**</span></span>       | <span data-ttu-id="4d6b3-713">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-713">No</span></span>          | <span data-ttu-id="4d6b3-714">[**目前**] 或 [**原始**]，取決於目前的值或屬性的原始值是否應該用於平行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-714">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="4d6b3-715">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-715">Example</span></span>

<span data-ttu-id="4d6b3-716">下列範例顯示以兩種方式使用的 **ScalarProperty** 元素：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-716">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="4d6b3-717">將 **person** 實體類型的屬性對應到 **person**資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-717">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="4d6b3-718">將 **Person** 實體類型的屬性對應至 **UpdatePerson** 預存程式的參數。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-718">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="4d6b3-719">預存程序已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-719">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="4d6b3-720">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-720">Example</span></span>

<span data-ttu-id="4d6b3-721">下一個範例顯示用來將概念模型關聯的插入和刪除函數對應至資料庫中預存程式的 **ScalarProperty** 專案。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-721">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="4d6b3-722">預存程序已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-722">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="4d6b3-723">UpdateFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-723">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="4d6b3-724">對應規格語言 (MSL 中的 **UpdateFunction** 元素) 將概念模型中實體類型的更新函式對應至基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-724">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="4d6b3-725">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-725">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="4d6b3-726">如需詳細資訊，請參閱 (SSDL) 的 Function 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-726">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="4d6b3-727">如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-727">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="4d6b3-728">**UpdateFunction**元素可以是 ModificationFunctionMapping 元素的子系，並套用至 EntityTypeMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-728">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="4d6b3-729">**UpdateFunction**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="4d6b3-729">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="4d6b3-730">AssociationEnd (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-730">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-731">ComplexProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-731">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="4d6b3-732">ResultBinding (零或一個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-732">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="4d6b3-733">ScarlarProperty (零或多個)</span><span class="sxs-lookup"><span data-stu-id="4d6b3-733">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="4d6b3-734">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="4d6b3-734">Applicable Attributes</span></span>

<span data-ttu-id="4d6b3-735">下表說明可套用至 **UpdateFunction** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-735">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="4d6b3-736">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="4d6b3-736">Attribute Name</span></span>            | <span data-ttu-id="4d6b3-737">必要</span><span class="sxs-lookup"><span data-stu-id="4d6b3-737">Is Required</span></span> | <span data-ttu-id="4d6b3-738">值</span><span class="sxs-lookup"><span data-stu-id="4d6b3-738">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4d6b3-739">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-739">**FunctionName**</span></span>          | <span data-ttu-id="4d6b3-740">是</span><span class="sxs-lookup"><span data-stu-id="4d6b3-740">Yes</span></span>         | <span data-ttu-id="4d6b3-741">更新函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-741">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="4d6b3-742">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-742">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="4d6b3-743">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="4d6b3-743">**RowsAffectedParameter**</span></span> | <span data-ttu-id="4d6b3-744">否</span><span class="sxs-lookup"><span data-stu-id="4d6b3-744">No</span></span>          | <span data-ttu-id="4d6b3-745">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-745">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="4d6b3-746">範例</span><span class="sxs-lookup"><span data-stu-id="4d6b3-746">Example</span></span>

<span data-ttu-id="4d6b3-747">下列範例是以 School 模型為基礎，並顯示用來將**Person**實體類型的更新函式對應至**UpdatePerson**預存程式的**UpdateFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-747">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="4d6b3-748">**UpdatePerson**預存程式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="4d6b3-748">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
