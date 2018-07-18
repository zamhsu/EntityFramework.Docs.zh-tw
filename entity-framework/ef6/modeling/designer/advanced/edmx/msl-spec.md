---
title: MSL 規格-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
caps.latest.revision: 4
ms.openlocfilehash: 7448efc99f9fd9c6cdf930256a26347376fb354c
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120347"
---
# <a name="msl-specification"></a><span data-ttu-id="3bf09-102">MSL 規格</span><span class="sxs-lookup"><span data-stu-id="3bf09-102">MSL Specification</span></span>
<span data-ttu-id="3bf09-103">對應規格語言 (MSL) 是以 XML 為基礎的語言，描述概念模型和 Entity Framework 應用程式的儲存體模型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-103">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="3bf09-104">Entity Framework 應用程式中，對應中繼資料會在建置階段從.msl 檔 （以 MSL 撰寫） 載入。</span><span class="sxs-lookup"><span data-stu-id="3bf09-104">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="3bf09-105">Entity Framework 在執行階段會使用對應中繼資料，以針對概念模型存放區特有的命令以將查詢轉譯。</span><span class="sxs-lookup"><span data-stu-id="3bf09-105">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="3bf09-106">Entity Framework Designer （EF 設計工具） 會在設計階段儲存.edmx 檔案中的對應資訊。</span><span class="sxs-lookup"><span data-stu-id="3bf09-106">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="3bf09-107">在建置階段，Entity Designer 會使用資訊.edmx 檔案中建立的.msl 檔案所需的 Entity Framework 在執行階段</span><span class="sxs-lookup"><span data-stu-id="3bf09-107">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="3bf09-108">MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="3bf09-108">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="3bf09-109">概念模型命名空間名稱的詳細資訊，請參閱[CSDL 規格](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-109">For information about the conceptual model namespace name, see [CSDL Specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span> <span data-ttu-id="3bf09-110">儲存體模型命名空間名稱的詳細資訊，請參閱[SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-110">For information about the storage model namespace name, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="3bf09-111">MSL 的版本是由 XML 命名空間來做區分。</span><span class="sxs-lookup"><span data-stu-id="3bf09-111">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="3bf09-112">MSL 版本</span><span class="sxs-lookup"><span data-stu-id="3bf09-112">MSL Version</span></span> | <span data-ttu-id="3bf09-113">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="3bf09-113">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="3bf09-114">MSL v1</span><span class="sxs-lookup"><span data-stu-id="3bf09-114">MSL v1</span></span>      | <span data-ttu-id="3bf09-115">urn: schemas-microsoft-microsoft-com:windows:storage:mapping:CS</span><span class="sxs-lookup"><span data-stu-id="3bf09-115">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="3bf09-116">MSL v2</span><span class="sxs-lookup"><span data-stu-id="3bf09-116">MSL v2</span></span>      | http://schemas.microsoft.com/ado/2008/09/mapping/cs  |
| <span data-ttu-id="3bf09-117">MSL v3</span><span class="sxs-lookup"><span data-stu-id="3bf09-117">MSL v3</span></span>      | http://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="3bf09-118">Alias 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-118">Alias Element (MSL)</span></span>

<span data-ttu-id="3bf09-119">**別名**對應規格語言 (MSL) 中的項目是用來定義概念模型和儲存體模型命名空間別名的對應項目子系。</span><span class="sxs-lookup"><span data-stu-id="3bf09-119">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="3bf09-120">MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="3bf09-120">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="3bf09-121">如需概念模型命名空間名稱的資訊，請參閱結構描述項目 (CSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-121">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="3bf09-122">儲存體模型命名空間名稱的詳細資訊，請參閱結構描述項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-122">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="3bf09-123">**別名**項目不能有子項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-123">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-124">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-124">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-125">下表描述可套用至的屬性**別名**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-125">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="3bf09-126">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-126">Attribute Name</span></span> | <span data-ttu-id="3bf09-127">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-127">Is Required</span></span> | <span data-ttu-id="3bf09-128">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-128">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-129">**Key**</span><span class="sxs-lookup"><span data-stu-id="3bf09-129">**Key**</span></span>        | <span data-ttu-id="3bf09-130">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-130">Yes</span></span>         | <span data-ttu-id="3bf09-131">所指定的命名空間的別名**值**屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-131">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="3bf09-132">**值**</span><span class="sxs-lookup"><span data-stu-id="3bf09-132">**Value**</span></span>      | <span data-ttu-id="3bf09-133">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-133">Yes</span></span>         | <span data-ttu-id="3bf09-134">命名空間的值**金鑰**項目是一個別名。</span><span class="sxs-lookup"><span data-stu-id="3bf09-134">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="3bf09-135">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-135">Example</span></span>

<span data-ttu-id="3bf09-136">下列範例所示**別名**項目，定義別名， `c`，針對概念模型中定義的類型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-136">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="http://schemas.microsoft.com/ado/2009/11/mapping/cs">
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

## <a name="associationend-element-msl"></a><span data-ttu-id="3bf09-137">AssociationEnd 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-137">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="3bf09-138">**AssociationEnd**概念模型中的實體類型的修改函式會對應至基礎資料庫中的預存程序時，會使用對應規格語言 (MSL) 中的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-138">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="3bf09-139">如果預存程序會採用的參數，其值會保留在一個關聯的屬性，修改**AssociationEnd**元素會對應至參數的屬性值。</span><span class="sxs-lookup"><span data-stu-id="3bf09-139">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="3bf09-140">如需詳細資訊，請參閱下列範例。</span><span class="sxs-lookup"><span data-stu-id="3bf09-140">For more information, see the example below.</span></span>

<span data-ttu-id="3bf09-141">如需有關如何將實體類型的修改函式對應至預存程序的詳細資訊，請參閱 ModificationFunctionMapping 項目 (MSL) 和逐步解說： 將實體對應至預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-141">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="3bf09-142">**AssociationEnd**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-142">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-143">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="3bf09-143">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-144">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-144">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-145">下表描述可套用至屬性**AssociationEnd**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-145">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="3bf09-146">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-146">Attribute Name</span></span>     | <span data-ttu-id="3bf09-147">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-147">Is Required</span></span> | <span data-ttu-id="3bf09-148">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-148">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-149">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="3bf09-149">**AssociationSet**</span></span> | <span data-ttu-id="3bf09-150">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-150">Yes</span></span>         | <span data-ttu-id="3bf09-151">要對應的關聯名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-151">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="3bf09-152">**From**</span><span class="sxs-lookup"><span data-stu-id="3bf09-152">**From**</span></span>           | <span data-ttu-id="3bf09-153">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-153">Yes</span></span>         | <span data-ttu-id="3bf09-154">值**FromRole**對應至要對應的關聯的導覽屬性的屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-154">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="3bf09-155">如需詳細資訊，請參閱 NavigationProperty 項目 (CSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-155">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="3bf09-156">**若要**</span><span class="sxs-lookup"><span data-stu-id="3bf09-156">**To**</span></span>             | <span data-ttu-id="3bf09-157">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-157">Yes</span></span>         | <span data-ttu-id="3bf09-158">值**ToRole**對應至要對應的關聯的導覽屬性的屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-158">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="3bf09-159">如需詳細資訊，請參閱 NavigationProperty 項目 (CSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-159">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="3bf09-160">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-160">Example</span></span>

<span data-ttu-id="3bf09-161">請考慮下列概念模型實體類型：</span><span class="sxs-lookup"><span data-stu-id="3bf09-161">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="3bf09-162">同時請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="3bf09-162">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="3bf09-163">若要對應的更新函式`Course`此預存程序的實體，您必須提供值給**DepartmentID**參數。</span><span class="sxs-lookup"><span data-stu-id="3bf09-163">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="3bf09-164">`DepartmentID` 的值不會對應至實體類型上的屬性；它會包含在獨立關聯中，該關聯的對應如下所示：</span><span class="sxs-lookup"><span data-stu-id="3bf09-164">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="3bf09-165">下列程式碼示範**AssociationEnd**項目用來對應**DepartmentID**屬性**FK\_課程\_部門**要關聯**UpdateCourse**預存程序 (要更新函式**課程**實體類型對應):</span><span class="sxs-lookup"><span data-stu-id="3bf09-165">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="3bf09-166">AssociationSetMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-166">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-167">**AssociationSetMapping**對應規格語言 (MSL) 中的項目會定義基礎資料庫中的概念模型和資料表資料行中的關聯之間的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-167">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="3bf09-168">概念模型中的關聯指其屬性代表基礎資料庫中主要與外部索引鍵資料行的類型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-168">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="3bf09-169">**AssociationSetMapping**項目會使用兩個的 EndProperty 項目來定義資料庫中的關聯類型屬性與資料行之間的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-169">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="3bf09-170">您可以將條件放在這些對應，與條件的項目上。</span><span class="sxs-lookup"><span data-stu-id="3bf09-170">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="3bf09-171">ModificationFunctionMapping 項目資料庫中的預存程序，將對應的 insert、 update 和 delete 函式之關聯。</span><span class="sxs-lookup"><span data-stu-id="3bf09-171">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="3bf09-172">QueryView 項目中使用 Entity SQL 字串，以定義關聯和資料表資料行之間的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-172">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-173">如果概念模型中的關聯定義參考條件約束，則關聯不需要是與對應**AssociationSetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-173">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="3bf09-174">如果**AssociationSetMapping**項目已存在具有參考條件約束的關聯，在中所定義的對應**AssociationSetMapping**項目將會被忽略。</span><span class="sxs-lookup"><span data-stu-id="3bf09-174">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="3bf09-175">如需詳細資訊，請參閱 ReferentialConstraint 項目 (CSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-175">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="3bf09-176">**AssociationSetMapping**元素可能具有下列子元素</span><span class="sxs-lookup"><span data-stu-id="3bf09-176">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="3bf09-177">QueryView （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-177">QueryView (zero or one)</span></span>
-   <span data-ttu-id="3bf09-178">EndProperty （零或兩個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-178">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="3bf09-179">條件 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-179">Condition (zero or more)</span></span>
-   <span data-ttu-id="3bf09-180">ModificationFunctionMapping （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-180">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-181">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-181">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-182">下表描述可套用至的屬性**AssociationSetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-182">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="3bf09-183">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-183">Attribute Name</span></span>     | <span data-ttu-id="3bf09-184">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-184">Is Required</span></span> | <span data-ttu-id="3bf09-185">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-185">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-186">**名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-186">**Name**</span></span>           | <span data-ttu-id="3bf09-187">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-187">Yes</span></span>         | <span data-ttu-id="3bf09-188">要對應的概念模型關聯集名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-188">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="3bf09-189">**類型名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-189">**TypeName**</span></span>       | <span data-ttu-id="3bf09-190">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-190">No</span></span>          | <span data-ttu-id="3bf09-191">要對應的概念模型關聯類型之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-191">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="3bf09-192">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="3bf09-192">**StoreEntitySet**</span></span> | <span data-ttu-id="3bf09-193">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-193">No</span></span>          | <span data-ttu-id="3bf09-194">要對應的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-194">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="3bf09-195">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-195">Example</span></span>

<span data-ttu-id="3bf09-196">下列範例所示**AssociationSetMapping**所在的項目**FK\_課程\_部門**概念模型中關聯集對應至**課程**資料庫資料表中的。</span><span class="sxs-lookup"><span data-stu-id="3bf09-196">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="3bf09-197">子系中所指定的關聯類型屬性與資料表資料行之間的對應**EndProperty**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-197">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="3bf09-198">ComplexProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-198">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="3bf09-199">A **ComplexProperty**對應規格語言 (MSL) 中的項目會定義概念模型實體類型和資料表資料行上基礎資料庫中的複雜類型屬性之間的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-199">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="3bf09-200">ScalarProperty 項目子系中指定的屬性資料行對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-200">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="3bf09-201">**ComplexType**屬性項目可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-201">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-202">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-202">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="3bf09-203">**ComplexProperty** （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-203">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="3bf09-204">ComplextTypeMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-204">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="3bf09-205">條件 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-205">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-206">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-206">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-207">下表描述可套用至屬性**ComplexProperty**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-207">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="3bf09-208">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-208">Attribute Name</span></span> | <span data-ttu-id="3bf09-209">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-209">Is Required</span></span> | <span data-ttu-id="3bf09-210">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-210">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-211">**名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-211">**Name**</span></span>       | <span data-ttu-id="3bf09-212">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-212">Yes</span></span>         | <span data-ttu-id="3bf09-213">概念模型中要對應的實體類型之複雜屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-213">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="3bf09-214">**類型名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-214">**TypeName**</span></span>   | <span data-ttu-id="3bf09-215">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-215">No</span></span>          | <span data-ttu-id="3bf09-216">概念模型屬性類型的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-216">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="3bf09-217">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-217">Example</span></span>

<span data-ttu-id="3bf09-218">下列範例是以 School 模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="3bf09-218">The following example is based on the School model.</span></span> <span data-ttu-id="3bf09-219">下列複雜型別已加入至概念模型：</span><span class="sxs-lookup"><span data-stu-id="3bf09-219">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="3bf09-220">**LastName**並**FirstName**的屬性**人員**實體類型已取代為一個複雜屬性**名稱**:</span><span class="sxs-lookup"><span data-stu-id="3bf09-220">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="3bf09-221">下列 MSL 顯示**ComplexProperty**項目用來對應**名稱**至基礎資料庫中的資料行的屬性：</span><span class="sxs-lookup"><span data-stu-id="3bf09-221">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="3bf09-222">ComplexTypeMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-222">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-223">**ComplexTypeMapping**對應規格語言 (MSL) 中的項目子系的 ResultMapping 項目，並定義中的基礎概念模型中的函式匯入和預存程序之間的對應當下列條件成立時的資料庫：</span><span class="sxs-lookup"><span data-stu-id="3bf09-223">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="3bf09-224">函式匯入會傳回概念複雜類型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-224">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="3bf09-225">預存程序所傳回之資料行名稱未與複雜類型上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="3bf09-225">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="3bf09-226">根據預設，預存程序所傳回之資料行與複雜類型間的對應是以資料行和屬性名稱為基礎。</span><span class="sxs-lookup"><span data-stu-id="3bf09-226">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="3bf09-227">如果資料行名稱不完全符合屬性名稱，您必須使用**ComplexTypeMapping**來定義對應的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-227">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="3bf09-228">如需預設對應的範例，請參閱 FunctionImportMapping 項目 (MSL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-228">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="3bf09-229">**ComplexTypeMapping**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-229">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-230">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-230">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-231">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-231">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-232">下表描述可套用至屬性**ComplexTypeMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-232">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="3bf09-233">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-233">Attribute Name</span></span> | <span data-ttu-id="3bf09-234">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-234">Is Required</span></span> | <span data-ttu-id="3bf09-235">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-235">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="3bf09-236">**類型名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-236">**TypeName**</span></span>   | <span data-ttu-id="3bf09-237">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-237">Yes</span></span>         | <span data-ttu-id="3bf09-238">要對應的複雜類型的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-238">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-239">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-239">Example</span></span>

<span data-ttu-id="3bf09-240">請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="3bf09-240">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="3bf09-241">同時請考慮下列概念模型複雜類型：</span><span class="sxs-lookup"><span data-stu-id="3bf09-241">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="3bf09-242">若要建立函式匯入傳回前一個複雜型別的執行個體，資料行之間的對應傳回預存程序和實體類型必須定義於**ComplexTypeMapping**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-242">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="3bf09-243">Condition 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-243">Condition Element (MSL)</span></span>

<span data-ttu-id="3bf09-244">**條件**對應規格語言 (MSL) 中的項目在概念模型和基礎資料庫之間的對應上放置條件。</span><span class="sxs-lookup"><span data-stu-id="3bf09-244">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="3bf09-245">XML 節點內定義的對應會將有效，如果要將所有條件，為在指定的子系**條件**符合項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-245">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="3bf09-246">否則，對應無效。</span><span class="sxs-lookup"><span data-stu-id="3bf09-246">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="3bf09-247">比方說，如果 MappingFragment 項目包含一或多個**條件**子項目內定義的對應**MappingFragment**才有效，如果要將所有節點的子系條件**條件**符合項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-247">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="3bf09-248">每個條件可以套用至**名稱**(由指定的概念模型實體屬性的名稱**名稱**屬性)，或有**ColumnName** （中的資料行的名稱所指定的資料庫**ColumnName**屬性)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-248">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="3bf09-249">當**名稱**設定屬性時，針對實體的屬性值檢查條件。</span><span class="sxs-lookup"><span data-stu-id="3bf09-249">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="3bf09-250">當**ColumnName**設定屬性時，針對資料行值檢查條件。</span><span class="sxs-lookup"><span data-stu-id="3bf09-250">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="3bf09-251">只有其中一個**名稱**或是**ColumnName**屬性中指定**條件**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-251">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-252">當**條件**FunctionImportMapping 項目內，只能使用元素**名稱**屬性不適用。</span><span class="sxs-lookup"><span data-stu-id="3bf09-252">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="3bf09-253">**條件**項目可以是下列項目子系：</span><span class="sxs-lookup"><span data-stu-id="3bf09-253">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="3bf09-254">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="3bf09-254">AssociationSetMapping</span></span>
-   <span data-ttu-id="3bf09-255">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="3bf09-255">ComplexProperty</span></span>
-   <span data-ttu-id="3bf09-256">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="3bf09-256">EntitySetMapping</span></span>
-   <span data-ttu-id="3bf09-257">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="3bf09-257">MappingFragment</span></span>
-   <span data-ttu-id="3bf09-258">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="3bf09-258">EntityTypeMapping</span></span>

<span data-ttu-id="3bf09-259">**條件**項目可以有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-259">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-260">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-260">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-261">下表描述可套用至屬性**條件**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-261">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="3bf09-262">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-262">Attribute Name</span></span> | <span data-ttu-id="3bf09-263">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-263">Is Required</span></span> | <span data-ttu-id="3bf09-264">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-264">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-265">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-265">**ColumnName**</span></span> | <span data-ttu-id="3bf09-266">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-266">No</span></span>          | <span data-ttu-id="3bf09-267">其值用來評估條件之資料表資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-267">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="3bf09-268">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="3bf09-268">**IsNull**</span></span>     | <span data-ttu-id="3bf09-269">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-269">No</span></span>          | <span data-ttu-id="3bf09-270">**真**或是**False**。</span><span class="sxs-lookup"><span data-stu-id="3bf09-270">**True** or **False**.</span></span> <span data-ttu-id="3bf09-271">如果值為 **，則為 True**和資料行的值是**null**，或如果值為**False**資料行值不是**null**，條件為 true.</span><span class="sxs-lookup"><span data-stu-id="3bf09-271">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="3bf09-272">否則，條件不成立。</span><span class="sxs-lookup"><span data-stu-id="3bf09-272">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="3bf09-273">**IsNull**並**值**屬性不能用在相同的時間。</span><span class="sxs-lookup"><span data-stu-id="3bf09-273">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="3bf09-274">**值**</span><span class="sxs-lookup"><span data-stu-id="3bf09-274">**Value**</span></span>      | <span data-ttu-id="3bf09-275">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-275">No</span></span>          | <span data-ttu-id="3bf09-276">要與資料行值比較的值。</span><span class="sxs-lookup"><span data-stu-id="3bf09-276">The value with which the column value is compared.</span></span> <span data-ttu-id="3bf09-277">如果值相同，則條件成立。</span><span class="sxs-lookup"><span data-stu-id="3bf09-277">If the values are the same, the condition is true.</span></span> <span data-ttu-id="3bf09-278">否則，條件不成立。</span><span class="sxs-lookup"><span data-stu-id="3bf09-278">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="3bf09-279">**IsNull**並**值**屬性不能用在相同的時間。</span><span class="sxs-lookup"><span data-stu-id="3bf09-279">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="3bf09-280">**名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-280">**Name**</span></span>       | <span data-ttu-id="3bf09-281">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-281">No</span></span>          | <span data-ttu-id="3bf09-282">其值用來評估條件之概念模型實體屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-282">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="3bf09-283">這個屬性不適用如果**條件**FunctionImportMapping 項目內使用項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-283">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="3bf09-284">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-284">Example</span></span>

<span data-ttu-id="3bf09-285">下列範例所示**條件**做為子系的項目**MappingFragment**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-285">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="3bf09-286">當**HireDate**不是 null 和**EnrollmentDate**是 null，資料對應之間**SchoolModel.Instructor**型別和**PersonID**並**HireDate**的資料行**人員**資料表。</span><span class="sxs-lookup"><span data-stu-id="3bf09-286">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="3bf09-287">當**EnrollmentDate**不是 null 和**HireDate**是 null，資料對應之間**SchoolModel.Student**型別和**PersonID**並**註冊**的資料行**人員**資料表。</span><span class="sxs-lookup"><span data-stu-id="3bf09-287">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="3bf09-288">DeleteFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-288">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="3bf09-289">**DeleteFunction**對應規格語言 (MSL) 中的項目對應至基礎資料庫中的預存程序的概念模型中的關聯的實體類型的刪除函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-289">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="3bf09-290">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-290">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3bf09-291">如需詳細資訊，請參閱函式項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-291">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-292">如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。</span><span class="sxs-lookup"><span data-stu-id="3bf09-292">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="3bf09-293">套用至 EntityTypeMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="3bf09-293">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="3bf09-294">當套用至 EntityTypeMapping 項目**DeleteFunction**項目對應至預存程序的概念模型中的實體類型的刪除函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-294">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3bf09-295">**DeleteFunction**項目可以擁有下列子項目時套用至**EntityTypeMapping**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-295">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="3bf09-296">AssociationEnd （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-296">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="3bf09-297">ComplexProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-297">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="3bf09-298">ScarlarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-298">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-299">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-299">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-300">下表描述可套用至的屬性**DeleteFunction**項目時套用至**EntityTypeMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-300">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="3bf09-301">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-301">Attribute Name</span></span>            | <span data-ttu-id="3bf09-302">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-302">Is Required</span></span> | <span data-ttu-id="3bf09-303">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-303">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-304">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-304">**FunctionName**</span></span>          | <span data-ttu-id="3bf09-305">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-305">Yes</span></span>         | <span data-ttu-id="3bf09-306">刪除函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-306">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="3bf09-307">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-307">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3bf09-308">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3bf09-308">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3bf09-309">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-309">No</span></span>          | <span data-ttu-id="3bf09-310">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-310">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3bf09-311">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-311">Example</span></span>

<span data-ttu-id="3bf09-312">下列範例以 School 模型為基礎，並顯示**DeleteFunction**對應的刪除函式的項目**人員**實體類型**DeletePerson**預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-312">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="3bf09-313">**DeletePerson**宣告於儲存體模型中預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-313">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="3bf09-314">套用至 AssociationSetMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="3bf09-314">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="3bf09-315">當套用至 AssociationSetMapping 項目**DeleteFunction**項目對應至預存程序的概念模型中關聯的刪除函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-315">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3bf09-316">**DeleteFunction**項目可以擁有下列子項目時套用至**AssociationSetMapping**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-316">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="3bf09-317">EndProperty</span><span class="sxs-lookup"><span data-stu-id="3bf09-317">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-318">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-318">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-319">下表描述可套用至的屬性**DeleteFunction**項目時套用至**AssociationSetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-319">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="3bf09-320">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-320">Attribute Name</span></span>            | <span data-ttu-id="3bf09-321">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-321">Is Required</span></span> | <span data-ttu-id="3bf09-322">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-322">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-323">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-323">**FunctionName**</span></span>          | <span data-ttu-id="3bf09-324">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-324">Yes</span></span>         | <span data-ttu-id="3bf09-325">刪除函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-325">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="3bf09-326">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-326">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3bf09-327">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3bf09-327">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3bf09-328">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-328">No</span></span>          | <span data-ttu-id="3bf09-329">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-329">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3bf09-330">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-330">Example</span></span>

<span data-ttu-id="3bf09-331">下列範例以 School 模型為基礎，並顯示**DeleteFunction**用來刪除函式的對應項目**CourseInstructor**關聯**DeleteCourseInstructor**預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-331">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="3bf09-332">**DeleteCourseInstructor**宣告於儲存體模型中預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-332">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="3bf09-333">EndProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-333">EndProperty Element (MSL)</span></span>

<span data-ttu-id="3bf09-334">**EndProperty**對應規格語言 (MSL) 中的項目定義結尾或修改函式的概念模型關聯和基礎資料庫間的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-334">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="3bf09-335">子 ScalarProperty 項目中指定的屬性資料行對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-335">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="3bf09-336">當**EndProperty**項目用來定義概念模型關聯一端的對應，它是 AssociationSetMapping 項目子系。</span><span class="sxs-lookup"><span data-stu-id="3bf09-336">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="3bf09-337">當**EndProperty**項目用來定義概念模型關聯的修改函式的對應，它是 InsertFunction 項目或 DeleteFunction 項目子系。</span><span class="sxs-lookup"><span data-stu-id="3bf09-337">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="3bf09-338">**EndProperty**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-338">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-339">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-339">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-340">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-340">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-341">下表描述可套用至屬性**EndProperty**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-341">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="3bf09-342">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-342">Attribute Name</span></span> | <span data-ttu-id="3bf09-343">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-343">Is Required</span></span> | <span data-ttu-id="3bf09-344">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-344">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="3bf09-345">名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-345">Name</span></span>           | <span data-ttu-id="3bf09-346">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-346">Yes</span></span>         | <span data-ttu-id="3bf09-347">要對應的關聯端名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-347">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-348">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-348">Example</span></span>

<span data-ttu-id="3bf09-349">下列範例所示**AssociationSetMapping**所在的項目**FK\_課程\_部門**概念模型中的關聯會對應至**課程**資料庫資料表中的。</span><span class="sxs-lookup"><span data-stu-id="3bf09-349">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="3bf09-350">子系中所指定的關聯類型屬性與資料表資料行之間的對應**EndProperty**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-350">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="3bf09-351">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-351">Example</span></span>

<span data-ttu-id="3bf09-352">下列範例所示**EndProperty**項目對應關聯的插入和刪除函式 (**CourseInstructor**) 與基礎資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-352">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="3bf09-353">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-353">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="3bf09-354">EntityContainerMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-354">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-355">**EntityContainerMapping**對應規格語言 (MSL) 中的項目對應至儲存體模型中的實體容器的概念模型中的實體容器。</span><span class="sxs-lookup"><span data-stu-id="3bf09-355">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="3bf09-356">**EntityContainerMapping**項目是對應項目的子系。</span><span class="sxs-lookup"><span data-stu-id="3bf09-356">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="3bf09-357">**EntityContainerMapping**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="3bf09-357">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3bf09-358">EntitySetMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-358">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="3bf09-359">AssociationSetMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-359">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="3bf09-360">FunctionImportMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-360">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-361">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-361">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-362">下表描述可套用至的屬性**EntityContainerMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-362">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="3bf09-363">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-363">Attribute Name</span></span>            | <span data-ttu-id="3bf09-364">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-364">Is Required</span></span> | <span data-ttu-id="3bf09-365">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-365">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-366">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="3bf09-366">**StorageModelContainer**</span></span> | <span data-ttu-id="3bf09-367">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-367">Yes</span></span>         | <span data-ttu-id="3bf09-368">要對應至的儲存模型實體容器名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-368">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="3bf09-369">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="3bf09-369">**CdmEntityContainer**</span></span>    | <span data-ttu-id="3bf09-370">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-370">Yes</span></span>         | <span data-ttu-id="3bf09-371">要對應的概念模型實體容器名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-371">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="3bf09-372">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="3bf09-372">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="3bf09-373">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-373">No</span></span>          | <span data-ttu-id="3bf09-374">**真**或是**False**。</span><span class="sxs-lookup"><span data-stu-id="3bf09-374">**True** or **False**.</span></span> <span data-ttu-id="3bf09-375">如果**False**，會產生任何更新檢視。</span><span class="sxs-lookup"><span data-stu-id="3bf09-375">If **False**, no update views are generated.</span></span> <span data-ttu-id="3bf09-376">此屬性應該設為**False**當您有會變為無效，因為資料可能無法成功來回的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-376">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="3bf09-377">預設值是 **True**。</span><span class="sxs-lookup"><span data-stu-id="3bf09-377">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-378">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-378">Example</span></span>

<span data-ttu-id="3bf09-379">下列範例所示**EntityContainerMapping**對應的項目**SchoolModelEntities**容器 （概念模型實體容器） **SchoolModelStoreContainer**容器 （儲存體模型實體容器）：</span><span class="sxs-lookup"><span data-stu-id="3bf09-379">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="3bf09-380">EntitySetMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-380">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-381">**EntitySetMapping**設定儲存體模型中的對應規格語言 (MSL) 對應概念模型實體中的所有型別設定為實體中的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-381">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="3bf09-382">概念模型中的實體集是邏輯容器相同的型別 （和衍生型別） 的實體的執行個體。</span><span class="sxs-lookup"><span data-stu-id="3bf09-382">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="3bf09-383">儲存體模型中的實體集代表資料表或檢視基礎資料庫中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-383">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="3bf09-384">概念模型實體集指定的值所**名稱**屬性**EntitySetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-384">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="3bf09-385">對應到資料表或檢視由**StoreEntitySet**屬性中每個子 MappingFragment 項目，或在**EntitySetMapping**項目本身。</span><span class="sxs-lookup"><span data-stu-id="3bf09-385">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="3bf09-386">**EntitySetMapping**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-386">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-387">EntityTypeMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-387">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="3bf09-388">QueryView （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-388">QueryView (zero or one)</span></span>
-   <span data-ttu-id="3bf09-389">MappingFragment （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-389">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-390">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-390">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-391">下表描述可套用至的屬性**EntitySetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-391">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="3bf09-392">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-392">Attribute Name</span></span>           | <span data-ttu-id="3bf09-393">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-393">Is Required</span></span> | <span data-ttu-id="3bf09-394">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-394">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-395">**名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-395">**Name**</span></span>                 | <span data-ttu-id="3bf09-396">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-396">Yes</span></span>         | <span data-ttu-id="3bf09-397">要對應的概念模型實體集名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-397">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="3bf09-398">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="3bf09-398">**TypeName** **1**</span></span>       | <span data-ttu-id="3bf09-399">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-399">No</span></span>          | <span data-ttu-id="3bf09-400">要對應的概念模型實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-400">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="3bf09-401">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="3bf09-401">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="3bf09-402">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-402">No</span></span>          | <span data-ttu-id="3bf09-403">要對應至的儲存模型實體集名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-403">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="3bf09-404">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="3bf09-404">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="3bf09-405">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-405">No</span></span>          | <span data-ttu-id="3bf09-406">**True**或是**False**取決於是否會傳回只相異的資料列。</span><span class="sxs-lookup"><span data-stu-id="3bf09-406">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="3bf09-407">如果此屬性設為 **，則為 True**，則**GenerateUpdateViews** EntityContainerMapping 項目屬性必須設為**False**。</span><span class="sxs-lookup"><span data-stu-id="3bf09-407">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="3bf09-408">**1** **TypeName**並**StoreEntitySet**屬性可用來取代 EntityTypeMapping 和 MappingFragment 子項目中的單一實體型別對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="3bf09-408">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="3bf09-409">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-409">Example</span></span>

<span data-ttu-id="3bf09-410">下列範例所示**EntitySetMapping**中的地圖 （基底型別和兩個衍生的類型） 的三種類型的項目**課程**概念模型中的三個不同資料表的實體集基礎資料庫。</span><span class="sxs-lookup"><span data-stu-id="3bf09-410">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="3bf09-411">所指定的資料表**StoreEntitySet**中每個屬性**MappingFragment**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-411">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="3bf09-412">EntityTypeMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-412">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-413">**EntityTypeMapping**對應規格語言 (MSL) 中的項目會定義概念模型和資料表中的實體類型或檢視表之間的對應基礎資料庫中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-413">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="3bf09-414">如需概念模型實體類型和基礎資料庫資料表或檢視的資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-414">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="3bf09-415">要對應概念模型實體類型由**TypeName**屬性**EntityTypeMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-415">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="3bf09-416">所指定的資料表或檢視要對應**StoreEntitySet**子 MappingFragment 項目的屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-416">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="3bf09-417">ModificationFunctionMapping 子項目可以用來對應插入、 更新或刪除資料庫中的預存程序的實體類型的函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-417">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="3bf09-418">**EntityTypeMapping**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-418">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-419">MappingFragment （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-419">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="3bf09-420">ModificationFunctionMapping （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-420">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="3bf09-421">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="3bf09-421">ScalarProperty</span></span>
-   <span data-ttu-id="3bf09-422">條件</span><span class="sxs-lookup"><span data-stu-id="3bf09-422">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-423">**MappingFragment**並**ModificationFunctionMapping**項目不能子項目的**EntityTypeMapping**在同一時間的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-423">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="3bf09-424">**ScalarProperty**並**條件**項目只能是子項目的**EntityTypeMapping** FunctionImportMapping 項目內使用時的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-424">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-425">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-425">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-426">下表描述可套用至的屬性**EntityTypeMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-426">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="3bf09-427">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-427">Attribute Name</span></span> | <span data-ttu-id="3bf09-428">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-428">Is Required</span></span> | <span data-ttu-id="3bf09-429">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-429">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-430">**類型名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-430">**TypeName**</span></span>   | <span data-ttu-id="3bf09-431">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-431">Yes</span></span>         | <span data-ttu-id="3bf09-432">要對應的概念模型實體類型之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-432">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="3bf09-433">如果型別是抽象型別或衍生型別，值必須是 `IsOfType(Namespace-qualified_type_name)`。</span><span class="sxs-lookup"><span data-stu-id="3bf09-433">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-434">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-434">Example</span></span>

<span data-ttu-id="3bf09-435">下列範例示範具有兩個子 EntitySetMapping 項目**EntityTypeMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-435">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="3bf09-436">在第一個**EntityTypeMapping**項目**SchoolModel.Person**實體類型對應至**人員**資料表。</span><span class="sxs-lookup"><span data-stu-id="3bf09-436">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="3bf09-437">在第二個**EntityTypeMapping**元素中，更新功能**SchoolModel.Person**類型會對應至預存程序中， **UpdatePerson**，在資料庫中.</span><span class="sxs-lookup"><span data-stu-id="3bf09-437">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="3bf09-438">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-438">Example</span></span>

<span data-ttu-id="3bf09-439">下一個範例會顯示型別階層的對應，在該階層內根型別是抽象型別。</span><span class="sxs-lookup"><span data-stu-id="3bf09-439">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="3bf09-440">請注意，使用`IsOfType`語法**TypeName**屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-440">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="3bf09-441">FunctionImportMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-441">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-442">**FunctionImportMapping**對應規格語言 (MSL) 中的項目會定義概念模型中預存程序的函式匯入或函式之間的對應基礎資料庫中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-442">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="3bf09-443">函式匯入必須在概念模型中宣告，而預存程序則必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-443">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="3bf09-444">如需詳細資訊，請參閱 FunctionImport 項目 (CSDL) 和函式項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-444">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-445">根據預設，如果函式匯入傳回概念模型實體類型或複雜型別，則基礎預存程序所傳回的資料行名稱必須與概念模型類型上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="3bf09-445">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="3bf09-446">如果資料行名稱不完全相符的屬性名稱，必須能 ResultMapping 項目中定義的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-446">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="3bf09-447">**FunctionImportMapping**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-447">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-448">ResultMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-448">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-449">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-449">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-450">下表描述可套用至屬性**FunctionImportMapping**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-450">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="3bf09-451">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-451">Attribute Name</span></span>         | <span data-ttu-id="3bf09-452">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-452">Is Required</span></span> | <span data-ttu-id="3bf09-453">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-453">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-454">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-454">**FunctionImportName**</span></span> | <span data-ttu-id="3bf09-455">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-455">Yes</span></span>         | <span data-ttu-id="3bf09-456">概念模型中要對應的函式匯入名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-456">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="3bf09-457">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-457">**FunctionName**</span></span>       | <span data-ttu-id="3bf09-458">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-458">Yes</span></span>         | <span data-ttu-id="3bf09-459">儲存體模型中要對應的函式之命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-459">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-460">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-460">Example</span></span>

<span data-ttu-id="3bf09-461">下列範例是以 School 模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="3bf09-461">The following example is based on the School model.</span></span> <span data-ttu-id="3bf09-462">請考量儲存體模型中的下列函式：</span><span class="sxs-lookup"><span data-stu-id="3bf09-462">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="3bf09-463">同時考量概念模型中的這個函式匯入：</span><span class="sxs-lookup"><span data-stu-id="3bf09-463">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="3bf09-464">下列範例所示**FunctionImportMapping**用來對應函式和函式彼此的匯入上方的項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-464">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="3bf09-465">InsertFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-465">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="3bf09-466">**InsertFunction**對應規格語言 (MSL) 中的項目對應至基礎資料庫中的預存程序的概念模型中的關聯的實體類型的插入函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-466">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="3bf09-467">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-467">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3bf09-468">如需詳細資訊，請參閱函式項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-468">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-469">如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。</span><span class="sxs-lookup"><span data-stu-id="3bf09-469">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="3bf09-470">**InsertFunction**項目可以是子系 ModificationFunctionMapping 項目，而且套用至 EntityTypeMapping 項目或 AssociationSetMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-470">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="3bf09-471">套用至 EntityTypeMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="3bf09-471">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="3bf09-472">當套用至 EntityTypeMapping 項目**InsertFunction**項目對應至預存程序的概念模型中的實體類型的插入函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-472">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3bf09-473">**InsertFunction**項目可以擁有下列子項目時套用至**EntityTypeMapping**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-473">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="3bf09-474">AssociationEnd （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-474">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="3bf09-475">ComplexProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-475">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="3bf09-476">ResultBinding （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-476">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="3bf09-477">ScarlarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-477">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-478">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-478">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-479">下表描述可套用至的屬性**InsertFunction**項目時套用至**EntityTypeMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-479">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="3bf09-480">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-480">Attribute Name</span></span>            | <span data-ttu-id="3bf09-481">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-481">Is Required</span></span> | <span data-ttu-id="3bf09-482">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-482">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-483">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-483">**FunctionName**</span></span>          | <span data-ttu-id="3bf09-484">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-484">Yes</span></span>         | <span data-ttu-id="3bf09-485">插入函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-485">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="3bf09-486">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-486">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3bf09-487">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3bf09-487">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3bf09-488">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-488">No</span></span>          | <span data-ttu-id="3bf09-489">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-489">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3bf09-490">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-490">Example</span></span>

<span data-ttu-id="3bf09-491">下列範例以 School 模型為基礎，並顯示**InsertFunction**項目用來將 Person 實體類型的插入函式的對應**InsertPerson**預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-491">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="3bf09-492">**InsertPerson**宣告於儲存體模型中預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-492">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="3bf09-493">套用至 AssociationSetMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="3bf09-493">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="3bf09-494">當套用至 AssociationSetMapping 項目**InsertFunction**項目對應至預存程序的概念模型中關聯的插入函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-494">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3bf09-495">**InsertFunction**項目可以擁有下列子項目時套用至**AssociationSetMapping**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-495">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="3bf09-496">EndProperty</span><span class="sxs-lookup"><span data-stu-id="3bf09-496">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-497">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-497">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-498">下表描述可套用至的屬性**InsertFunction**項目時套用至**AssociationSetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-498">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="3bf09-499">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-499">Attribute Name</span></span>            | <span data-ttu-id="3bf09-500">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-500">Is Required</span></span> | <span data-ttu-id="3bf09-501">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-501">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-502">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-502">**FunctionName**</span></span>          | <span data-ttu-id="3bf09-503">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-503">Yes</span></span>         | <span data-ttu-id="3bf09-504">插入函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-504">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="3bf09-505">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-505">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3bf09-506">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3bf09-506">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3bf09-507">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-507">No</span></span>          | <span data-ttu-id="3bf09-508">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-508">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3bf09-509">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-509">Example</span></span>

<span data-ttu-id="3bf09-510">下列範例以 School 模型為基礎，並顯示**InsertFunction**用來插入函式的對應項目**CourseInstructor**關聯**InsertCourseInstructor**預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-510">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="3bf09-511">**InsertCourseInstructor**宣告於儲存體模型中預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-511">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="3bf09-512">Mapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-512">Mapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-513">**對應**對應規格語言 (MSL) 中的項目包含對應至資料庫的概念模型中定義 （如儲存體模型中所述） 的物件資訊。</span><span class="sxs-lookup"><span data-stu-id="3bf09-513">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="3bf09-514">如需詳細資訊，請參閱 CSDL 規格和 SSDL 規格。</span><span class="sxs-lookup"><span data-stu-id="3bf09-514">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="3bf09-515">**對應**項目是對應規格的根項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-515">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="3bf09-516">對應規格的 XML 命名空間是http://schemas.microsoft.com/ado/2009/11/mapping/cs。</span><span class="sxs-lookup"><span data-stu-id="3bf09-516">The XML namespace for mapping specifications is http://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="3bf09-517">對應項目可以擁有下列子項目 (依列出的順序)：</span><span class="sxs-lookup"><span data-stu-id="3bf09-517">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3bf09-518">別名 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-518">Alias (zero or more)</span></span>
-   <span data-ttu-id="3bf09-519">EntityContainerMapping （只有一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-519">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="3bf09-520">MSL 中所參考之概念及儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。</span><span class="sxs-lookup"><span data-stu-id="3bf09-520">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="3bf09-521">如需概念模型命名空間名稱的資訊，請參閱結構描述項目 (CSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-521">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="3bf09-522">儲存體模型命名空間名稱的詳細資訊，請參閱結構描述項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-522">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="3bf09-523">MSL 中所使用的命名空間的別名可以使用別名項目加以定義。</span><span class="sxs-lookup"><span data-stu-id="3bf09-523">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-524">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-524">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-525">下表描述可套用至的屬性**對應**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-525">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="3bf09-526">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-526">Attribute Name</span></span> | <span data-ttu-id="3bf09-527">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-527">Is Required</span></span> | <span data-ttu-id="3bf09-528">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-528">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="3bf09-529">**空格鍵**</span><span class="sxs-lookup"><span data-stu-id="3bf09-529">**Space**</span></span>      | <span data-ttu-id="3bf09-530">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-530">Yes</span></span>         | <span data-ttu-id="3bf09-531">**C-S**。</span><span class="sxs-lookup"><span data-stu-id="3bf09-531">**C-S**.</span></span> <span data-ttu-id="3bf09-532">這是固定值，無法變更。</span><span class="sxs-lookup"><span data-stu-id="3bf09-532">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-533">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-533">Example</span></span>

<span data-ttu-id="3bf09-534">下列範例所示**對應**項目，根據 School 模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="3bf09-534">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="3bf09-535">如需 School 模型的詳細資訊，請參閱快速入門 (Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="3bf09-535">For more information about the School model, see Quickstart (Entity Framework):</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="http://schemas.microsoft.com/ado/2009/11/mapping/cs">
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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="3bf09-536">MappingFragment 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-536">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="3bf09-537">**MappingFragment**對應規格語言 (MSL) 中的項目會定義概念模型實體類型和資料表或檢視資料庫中的屬性之間的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-537">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="3bf09-538">如需概念模型實體類型和基礎資料庫資料表或檢視的資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-538">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="3bf09-539">**MappingFragment**可以 EntityTypeMapping 項目或 EntitySetMapping 項目子系項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-539">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="3bf09-540">**MappingFragment**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-540">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-541">ComplexType （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-541">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="3bf09-542">ScalarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-542">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="3bf09-543">條件 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-543">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-544">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-544">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-545">下表描述可套用至的屬性**MappingFragment**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-545">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="3bf09-546">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-546">Attribute Name</span></span>          | <span data-ttu-id="3bf09-547">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-547">Is Required</span></span> | <span data-ttu-id="3bf09-548">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-548">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-549">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="3bf09-549">**StoreEntitySet**</span></span>      | <span data-ttu-id="3bf09-550">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-550">Yes</span></span>         | <span data-ttu-id="3bf09-551">要對應的資料表或檢視之名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-551">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="3bf09-552">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="3bf09-552">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="3bf09-553">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-553">No</span></span>          | <span data-ttu-id="3bf09-554">**True**或是**False**取決於是否會傳回只相異的資料列。</span><span class="sxs-lookup"><span data-stu-id="3bf09-554">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="3bf09-555">如果此屬性設為 **，則為 True**，則**GenerateUpdateViews** EntityContainerMapping 項目屬性必須設為**False**。</span><span class="sxs-lookup"><span data-stu-id="3bf09-555">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-556">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-556">Example</span></span>

<span data-ttu-id="3bf09-557">下列範例所示**MappingFragment**項目做為子系**EntityTypeMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-557">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="3bf09-558">在此範例中的屬性**課程**概念模型中的型別會對應到的資料行**課程**資料庫資料表中的。</span><span class="sxs-lookup"><span data-stu-id="3bf09-558">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="3bf09-559">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-559">Example</span></span>

<span data-ttu-id="3bf09-560">下列範例所示**MappingFragment**項目做為子系**EntitySetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-560">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="3bf09-561">上述的屬性，例如**課程**概念模型中的型別會對應到的資料行**課程**資料庫資料表中的。</span><span class="sxs-lookup"><span data-stu-id="3bf09-561">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="3bf09-562">ModificationFunctionMapping 屬性 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-562">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-563">**ModificationFunctionMapping**對應規格語言 (MSL) 中的項目會對應插入、 更新和刪除基礎資料庫中的預存程序的概念模型實體類型的函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-563">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="3bf09-564">**ModificationFunctionMapping**項目也可以將對應的插入及刪除基礎資料庫中的預存程序的概念模型中的多對多關聯性的函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-564">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="3bf09-565">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-565">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3bf09-566">如需詳細資訊，請參閱函式項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-566">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-567">如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。</span><span class="sxs-lookup"><span data-stu-id="3bf09-567">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="3bf09-568">如果繼承階層架構中某個實體的修改函式已對應至預存程序，則階層架構中所有類型的修改函式都必須對應至預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-568">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="3bf09-569">**ModificationFunctionMapping**項目可以是子系 EntityTypeMapping 項目或 AssociationSetMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-569">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="3bf09-570">**ModificationFunctionMapping**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-570">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-571">DeleteFunction （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-571">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="3bf09-572">InsertFunction （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-572">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="3bf09-573">UpdateFunction （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-573">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="3bf09-574">任何屬性都適用於**ModificationFunctionMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-574">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="3bf09-575">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-575">Example</span></span>

<span data-ttu-id="3bf09-576">下列範例顯示的實體集對應**人**School 模型中的實體集。</span><span class="sxs-lookup"><span data-stu-id="3bf09-576">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="3bf09-577">除了針對的資料行對應**Person**實體類型，對應的插入、 更新和刪除函式**人員**類型會顯示。</span><span class="sxs-lookup"><span data-stu-id="3bf09-577">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="3bf09-578">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-578">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="3bf09-579">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-579">Example</span></span>

<span data-ttu-id="3bf09-580">下列範例示範集對應的關聯**CourseInstructor** School 模型中關聯集。</span><span class="sxs-lookup"><span data-stu-id="3bf09-580">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="3bf09-581">除了針對的資料行對應**CourseInstructor**關聯的插入和刪除函式的對應**CourseInstructor**關聯會顯示。</span><span class="sxs-lookup"><span data-stu-id="3bf09-581">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="3bf09-582">對應至的函式會在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-582">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="3bf09-583">QueryView 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-583">QueryView Element (MSL)</span></span>

<span data-ttu-id="3bf09-584">**QueryView**對應規格語言 (MSL) 中的項目在概念模型和基礎資料庫中的資料表中定義的實體類型或關聯之間的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-584">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="3bf09-585">對應會定義使用 Entity SQL 查詢，就會對儲存體模型中，評估和 express 的實體或概念模型中的關聯來表示結果集。</span><span class="sxs-lookup"><span data-stu-id="3bf09-585">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="3bf09-586">因為查詢檢視是唯讀的，無法使用標準更新命令來更新查詢檢視所定義的類型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-586">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="3bf09-587">您可以使用修改函式來更新這些類型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-587">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="3bf09-588">如需詳細資訊，請參閱 < 如何： 對應至預存程序的修改函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-588">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-589">在  **QueryView**項目，包含的 Entity SQL 運算式**GroupBy**，不支援群組彙總或導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-589">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="3bf09-590">**QueryView**項目可以是子系 EntitySetMapping 項目或 AssociationSetMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-590">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="3bf09-591">若是前者，查詢檢視會定義概念模型中之實體的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-591">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="3bf09-592">若是後者，查詢檢視會定義概念模型中之關聯的唯讀對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-592">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-593">如果**AssociationSetMapping**項目是參考條件約束，與關聯**AssociationSetMapping**項目會被忽略。</span><span class="sxs-lookup"><span data-stu-id="3bf09-593">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="3bf09-594">如需詳細資訊，請參閱 ReferentialConstraint 項目 (CSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-594">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="3bf09-595">**QueryView**項目不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-595">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-596">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-596">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-597">下表描述可套用至的屬性**QueryView**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-597">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="3bf09-598">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-598">Attribute Name</span></span> | <span data-ttu-id="3bf09-599">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-599">Is Required</span></span> | <span data-ttu-id="3bf09-600">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-600">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-601">**類型名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-601">**TypeName**</span></span>   | <span data-ttu-id="3bf09-602">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-602">No</span></span>          | <span data-ttu-id="3bf09-603">要由查詢檢視對應的概念模型類型名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-603">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-604">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-604">Example</span></span>

<span data-ttu-id="3bf09-605">下列範例所示**QueryView**的子系的項目**EntitySetMapping**項目，並定義查詢檢視對應**部門**中的實體類型School 模型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-605">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="3bf09-606">因為查詢只會傳回的成員子集**部門**儲存體模型中的型別**部門**School 模型中的型別已經修改，依據這個對應，如下所示：</span><span class="sxs-lookup"><span data-stu-id="3bf09-606">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="3bf09-607">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-607">Example</span></span>

<span data-ttu-id="3bf09-608">下一個範例所示**QueryView**為的子系的項目**AssociationSetMapping**項目，並定義的唯讀對應`FK_Course_Department`School 模型中的關聯。</span><span class="sxs-lookup"><span data-stu-id="3bf09-608">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="3bf09-609">註解</span><span class="sxs-lookup"><span data-stu-id="3bf09-609">Comments</span></span>

<span data-ttu-id="3bf09-610">您可以定義查詢檢視來啟用下列案例：</span><span class="sxs-lookup"><span data-stu-id="3bf09-610">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="3bf09-611">定義概念模型中的實體，其中未包含儲存體模型中實體的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-611">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="3bf09-612">這包括沒有預設值，而且不支援的屬性**null**值。</span><span class="sxs-lookup"><span data-stu-id="3bf09-612">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="3bf09-613">將儲存體模型中的計算資料行對應至概念模型中實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="3bf09-613">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="3bf09-614">定義分割概念模型實體所用之條件不是以實體為依據的對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-614">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="3bf09-615">當您指定條件式對應，使用**條件**項目，提供的條件必須等於指定的值。</span><span class="sxs-lookup"><span data-stu-id="3bf09-615">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="3bf09-616">如需詳細資訊，請參閱條件項目 (MSL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-616">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="3bf09-617">將儲存體模型中的相同資料行對應至概念模型中的多種類型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-617">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="3bf09-618">將多種類型對應至相同資料表。</span><span class="sxs-lookup"><span data-stu-id="3bf09-618">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="3bf09-619">定義概念模型中不是以關聯式結構描述之外部索引鍵為依據的關聯。</span><span class="sxs-lookup"><span data-stu-id="3bf09-619">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="3bf09-620">使用自訂商務邏輯來設定概念模型中的屬性值。</span><span class="sxs-lookup"><span data-stu-id="3bf09-620">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="3bf09-621">例如，您可以將對應的字串值的值的資料來源中的"T" **，則為 true**，布林值，將概念模型中的。</span><span class="sxs-lookup"><span data-stu-id="3bf09-621">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="3bf09-622">為查詢結果定義條件篩選器。</span><span class="sxs-lookup"><span data-stu-id="3bf09-622">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="3bf09-623">對概念模型資料強加的限制比儲存體模型的少。</span><span class="sxs-lookup"><span data-stu-id="3bf09-623">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="3bf09-624">比方說，您可以讓屬性在概念模型中可為 null 即使它對應的資料行不支援**null**值。</span><span class="sxs-lookup"><span data-stu-id="3bf09-624">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="3bf09-625">下列考量適用於為實體定義查詢檢視的情況：</span><span class="sxs-lookup"><span data-stu-id="3bf09-625">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="3bf09-626">查詢檢視是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="3bf09-626">Query views are read-only.</span></span> <span data-ttu-id="3bf09-627">您只能使用修改函式來更新這些實體。</span><span class="sxs-lookup"><span data-stu-id="3bf09-627">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="3bf09-628">當您依據查詢檢視定義實體類型時，也必須依據查詢檢視定義所有的相關實體。</span><span class="sxs-lookup"><span data-stu-id="3bf09-628">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="3bf09-629">當您將多對多關聯對應至代表關聯式結構描述中的連結資料表的儲存體模型中的實體時，您必須定義**QueryView**中的項目**AssociationSetMapping**這個連結資料表的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-629">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="3bf09-630">必須為類型階層架構中的所有類型定義查詢檢視。</span><span class="sxs-lookup"><span data-stu-id="3bf09-630">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="3bf09-631">您可以透過下列方法完成這項作業：</span><span class="sxs-lookup"><span data-stu-id="3bf09-631">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="3bf09-632">使用單一**QueryView**項目，指定單一的 Entity SQL 查詢會傳回階層中的所有實體類型的聯集。</span><span class="sxs-lookup"><span data-stu-id="3bf09-632">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="3bf09-633">使用單一**QueryView**根據特定條件的項目，指定單一的 Entity SQL 查詢來傳回特定實體類型階層架構中使用 CASE 運算子。</span><span class="sxs-lookup"><span data-stu-id="3bf09-633">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="3bf09-634">加上一個**QueryView**特定型別階層架構中的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-634">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="3bf09-635">在此案例中，使用**TypeName**屬性**QueryView**項目來指定每個檢視的實體類型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-635">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="3bf09-636">定義查詢檢視時，您無法指定**StorageSetName**屬性上**EntitySetMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-636">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="3bf09-637">定義查詢檢視時， **EntitySetMapping**項目不能同時包含**屬性**對應。</span><span class="sxs-lookup"><span data-stu-id="3bf09-637">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="3bf09-638">ResultBinding 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-638">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="3bf09-639">**ResultBinding**對應規格語言 (MSL) 中的項目對應時，所傳回預存程序至概念模型中的實體屬性的實體類型修改函式會對應至預存的資料行值基礎資料庫中的程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-639">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="3bf09-640">例如，當身分識別資料行的值由 insert 預存程序， **ResultBinding**項目對應至概念模型中的實體類型屬性的傳回的值。</span><span class="sxs-lookup"><span data-stu-id="3bf09-640">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="3bf09-641">**ResultBinding**項目可以是子系 InsertFunction 項目或 UpdateFunction 項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-641">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="3bf09-642">**ResultBinding**項目不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-642">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-643">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-643">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-644">下表描述可套用至屬性**ResultBinding**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-644">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="3bf09-645">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-645">Attribute Name</span></span> | <span data-ttu-id="3bf09-646">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-646">Is Required</span></span> | <span data-ttu-id="3bf09-647">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-647">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-648">**名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-648">**Name**</span></span>       | <span data-ttu-id="3bf09-649">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-649">Yes</span></span>         | <span data-ttu-id="3bf09-650">概念模型中要對應之實體屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-650">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="3bf09-651">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-651">**ColumnName**</span></span> | <span data-ttu-id="3bf09-652">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-652">Yes</span></span>         | <span data-ttu-id="3bf09-653">要對應的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-653">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="3bf09-654">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-654">Example</span></span>

<span data-ttu-id="3bf09-655">下列範例以 School 模型為基礎，並顯示**InsertFunction**項目用來將對應的插入函式**人員**實體類型**InsertPerson**預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-655">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="3bf09-656">( **InsertPerson**預存程序如下所示，並宣告儲存模型中。)A **ResultBinding**項目用來對應預存程序所傳回的資料行值 (**NewPersonID**) 的實體型別屬性 (**PersonID**)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-656">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="3bf09-657">下列 TRANSACT-SQL 描述**InsertPerson**預存程序：</span><span class="sxs-lookup"><span data-stu-id="3bf09-657">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="3bf09-658">ResultMapping 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-658">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="3bf09-659">**ResultMapping**對應規格語言 (MSL) 中的項目會定義基礎資料庫中的概念模型中的函式匯入和預存程序之間的對應當下列條件成立：</span><span class="sxs-lookup"><span data-stu-id="3bf09-659">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="3bf09-660">函式匯入會傳回概念模型實體類型或複雜型別。</span><span class="sxs-lookup"><span data-stu-id="3bf09-660">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="3bf09-661">預存程序所傳回之資料行名稱未與實體類型或複雜型別上的屬性名稱完全相符。</span><span class="sxs-lookup"><span data-stu-id="3bf09-661">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="3bf09-662">根據預設，預存程序所傳回之資料行與實體類型或複雜型別間的對應是以資料行和屬性名稱為基礎。</span><span class="sxs-lookup"><span data-stu-id="3bf09-662">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="3bf09-663">如果資料行名稱不完全符合屬性名稱，您必須使用**ResultMapping**來定義對應的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-663">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="3bf09-664">如需預設對應的範例，請參閱 FunctionImportMapping 項目 (MSL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-664">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="3bf09-665">**ResultMapping**元素是 FunctionImportMapping 項目子系元素。</span><span class="sxs-lookup"><span data-stu-id="3bf09-665">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="3bf09-666">**ResultMapping**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-666">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-667">EntityTypeMapping （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-667">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="3bf09-668">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="3bf09-668">ComplexTypeMapping</span></span>

<span data-ttu-id="3bf09-669">任何屬性都適用於**ResultMapping**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-669">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="3bf09-670">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-670">Example</span></span>

<span data-ttu-id="3bf09-671">請考慮下列預存程序：</span><span class="sxs-lookup"><span data-stu-id="3bf09-671">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="3bf09-672">同時請考慮下列概念模型實體類型：</span><span class="sxs-lookup"><span data-stu-id="3bf09-672">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="3bf09-673">若要建立函式匯入傳回前一個的實體類型之執行個體，資料行之間的對應傳回預存程序和實體類型必須定義於**ResultMapping**項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-673">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="3bf09-674">ScalarProperty 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-674">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="3bf09-675">**ScalarProperty**對應規格語言 (MSL) 中的項目將在概念模型實體類型、 複雜型別或關聯的屬性對應至基礎資料庫中的預存程序參數或資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="3bf09-675">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="3bf09-676">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-676">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3bf09-677">如需詳細資訊，請參閱函式項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-677">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="3bf09-678">**ScalarProperty**項目可以是下列項目子系：</span><span class="sxs-lookup"><span data-stu-id="3bf09-678">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="3bf09-679">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="3bf09-679">MappingFragment</span></span>
-   <span data-ttu-id="3bf09-680">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="3bf09-680">InsertFunction</span></span>
-   <span data-ttu-id="3bf09-681">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="3bf09-681">UpdateFunction</span></span>
-   <span data-ttu-id="3bf09-682">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="3bf09-682">DeleteFunction</span></span>
-   <span data-ttu-id="3bf09-683">EndProperty</span><span class="sxs-lookup"><span data-stu-id="3bf09-683">EndProperty</span></span>
-   <span data-ttu-id="3bf09-684">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="3bf09-684">ComplexProperty</span></span>
-   <span data-ttu-id="3bf09-685">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="3bf09-685">ResultMapping</span></span>

<span data-ttu-id="3bf09-686">為子系**MappingFragment**， **ComplexProperty**，或**EndProperty**項目**ScalarProperty**的屬性對應在資料庫中的資料行的概念模型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-686">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="3bf09-687">為子系**InsertFunction**， **UpdateFunction**，或**DeleteFunction**項目**ScalarProperty**的屬性對應在 預存程序參數的概念模型。</span><span class="sxs-lookup"><span data-stu-id="3bf09-687">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="3bf09-688">**ScalarProperty**項目不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-688">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-689">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-689">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-690">若要套用的屬性**ScalarProperty**項目而有所不同的項目角色。</span><span class="sxs-lookup"><span data-stu-id="3bf09-690">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="3bf09-691">下表描述的屬性時可適用**ScalarProperty**元素用來將概念模型屬性對應至資料庫中的資料行：</span><span class="sxs-lookup"><span data-stu-id="3bf09-691">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="3bf09-692">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-692">Attribute Name</span></span> | <span data-ttu-id="3bf09-693">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-693">Is Required</span></span> | <span data-ttu-id="3bf09-694">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-694">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="3bf09-695">**名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-695">**Name**</span></span>       | <span data-ttu-id="3bf09-696">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-696">Yes</span></span>         | <span data-ttu-id="3bf09-697">要對應的概念模型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-697">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="3bf09-698">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-698">**ColumnName**</span></span> | <span data-ttu-id="3bf09-699">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-699">Yes</span></span>         | <span data-ttu-id="3bf09-700">要對應的資料表資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-700">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="3bf09-701">下表描述可套用至屬性**ScalarProperty**時它用來將概念模型屬性對應至預存程序參數的項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-701">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="3bf09-702">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-702">Attribute Name</span></span>    | <span data-ttu-id="3bf09-703">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-703">Is Required</span></span> | <span data-ttu-id="3bf09-704">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-704">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-705">**名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-705">**Name**</span></span>          | <span data-ttu-id="3bf09-706">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-706">Yes</span></span>         | <span data-ttu-id="3bf09-707">要對應的概念模型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-707">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="3bf09-708">**參數名稱**</span><span class="sxs-lookup"><span data-stu-id="3bf09-708">**ParameterName**</span></span> | <span data-ttu-id="3bf09-709">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-709">Yes</span></span>         | <span data-ttu-id="3bf09-710">要對應的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-710">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="3bf09-711">**版本**</span><span class="sxs-lookup"><span data-stu-id="3bf09-711">**Version**</span></span>       | <span data-ttu-id="3bf09-712">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-712">No</span></span>          | <span data-ttu-id="3bf09-713">**目前**或是**原始**取決於是否目前的值或屬性的原始值應該用於並行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="3bf09-713">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="3bf09-714">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-714">Example</span></span>

<span data-ttu-id="3bf09-715">下列範例所示**ScalarProperty**兩種方式使用的項目：</span><span class="sxs-lookup"><span data-stu-id="3bf09-715">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="3bf09-716">若要將屬性對應**Person**實體類型的資料行**人員**資料表。</span><span class="sxs-lookup"><span data-stu-id="3bf09-716">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="3bf09-717">若要將屬性對應**Person**實體類型的參數**UpdatePerson**預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-717">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="3bf09-718">預存程序已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-718">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="3bf09-719">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-719">Example</span></span>

<span data-ttu-id="3bf09-720">下一個範例所示**ScalarProperty**用來對應插入和刪除函式在資料庫中的預存程序的概念模型關聯的項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-720">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="3bf09-721">預存程序已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-721">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="3bf09-722">UpdateFunction 項目 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3bf09-722">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="3bf09-723">**UpdateFunction**對應規格語言 (MSL) 中的項目對應至基礎資料庫中的預存程序的概念模型中的實體類型的更新函式。</span><span class="sxs-lookup"><span data-stu-id="3bf09-723">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="3bf09-724">修改函式所對應的預存程序必須在儲存體模型中宣告。</span><span class="sxs-lookup"><span data-stu-id="3bf09-724">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3bf09-725">如需詳細資訊，請參閱函式項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="3bf09-725">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="3bf09-726">如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。</span><span class="sxs-lookup"><span data-stu-id="3bf09-726">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="3bf09-727">**UpdateFunction**項目可以是子系 ModificationFunctionMapping 項目，而且套用至 EntityTypeMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-727">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="3bf09-728">**UpdateFunction**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="3bf09-728">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="3bf09-729">AssociationEnd （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-729">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="3bf09-730">ComplexProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-730">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="3bf09-731">ResultBinding （零或一個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-731">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="3bf09-732">ScarlarProperty （零或多個）</span><span class="sxs-lookup"><span data-stu-id="3bf09-732">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3bf09-733">適用屬性</span><span class="sxs-lookup"><span data-stu-id="3bf09-733">Applicable Attributes</span></span>

<span data-ttu-id="3bf09-734">下表描述可套用至的屬性**UpdateFunction**項目。</span><span class="sxs-lookup"><span data-stu-id="3bf09-734">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="3bf09-735">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="3bf09-735">Attribute Name</span></span>            | <span data-ttu-id="3bf09-736">必要</span><span class="sxs-lookup"><span data-stu-id="3bf09-736">Is Required</span></span> | <span data-ttu-id="3bf09-737">值</span><span class="sxs-lookup"><span data-stu-id="3bf09-737">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3bf09-738">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3bf09-738">**FunctionName**</span></span>          | <span data-ttu-id="3bf09-739">[是]</span><span class="sxs-lookup"><span data-stu-id="3bf09-739">Yes</span></span>         | <span data-ttu-id="3bf09-740">更新函式所對應至之預存程序的命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-740">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="3bf09-741">預存程序必須已宣告於儲存模型中。</span><span class="sxs-lookup"><span data-stu-id="3bf09-741">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3bf09-742">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3bf09-742">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3bf09-743">否</span><span class="sxs-lookup"><span data-stu-id="3bf09-743">No</span></span>          | <span data-ttu-id="3bf09-744">會傳回受影響之資料列數的輸出參數名稱。</span><span class="sxs-lookup"><span data-stu-id="3bf09-744">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="3bf09-745">範例</span><span class="sxs-lookup"><span data-stu-id="3bf09-745">Example</span></span>

<span data-ttu-id="3bf09-746">下列範例以 School 模型為基礎，並顯示**UpdateFunction**用來更新函式的對應項目**人員**實體類型**UpdatePerson**預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-746">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="3bf09-747">**UpdatePerson**宣告於儲存體模型中預存程序。</span><span class="sxs-lookup"><span data-stu-id="3bf09-747">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
