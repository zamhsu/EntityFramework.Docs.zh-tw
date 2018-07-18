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
# <a name="msl-specification"></a>MSL 規格
對應規格語言 (MSL) 是以 XML 為基礎的語言，描述概念模型和 Entity Framework 應用程式的儲存體模型之間的對應。

Entity Framework 應用程式中，對應中繼資料會在建置階段從.msl 檔 （以 MSL 撰寫） 載入。 Entity Framework 在執行階段會使用對應中繼資料，以針對概念模型存放區特有的命令以將查詢轉譯。

Entity Framework Designer （EF 設計工具） 會在設計階段儲存.edmx 檔案中的對應資訊。 在建置階段，Entity Designer 會使用資訊.edmx 檔案中建立的.msl 檔案所需的 Entity Framework 在執行階段

MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。 概念模型命名空間名稱的詳細資訊，請參閱[CSDL 規格](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。 儲存體模型命名空間名稱的詳細資訊，請參閱[SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)。

MSL 的版本是由 XML 命名空間來做區分。

| MSL 版本 | XML 命名空間                                        |
|:------------|:-----------------------------------------------------|
| MSL v1      | urn: schemas-microsoft-microsoft-com:windows:storage:mapping:CS |
| MSL v2      | http://schemas.microsoft.com/ado/2008/09/mapping/cs  |
| MSL v3      | http://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Alias 元素 (MSL)

**別名**對應規格語言 (MSL) 中的項目是用來定義概念模型和儲存體模型命名空間別名的對應項目子系。 MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。 如需概念模型命名空間名稱的資訊，請參閱結構描述項目 (CSDL)。 儲存體模型命名空間名稱的詳細資訊，請參閱結構描述項目 (SSDL)。

**別名**項目不能有子項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**別名**項目。

| 屬性名稱 | 必要 | 值                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **Key**        | [是]         | 所指定的命名空間的別名**值**屬性。 |
| **值**      | [是]         | 命名空間的值**金鑰**項目是一個別名。     |

### <a name="example"></a>範例

下列範例所示**別名**項目，定義別名， `c`，針對概念模型中定義的類型。

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

## <a name="associationend-element-msl"></a>AssociationEnd 項目 (MSL)

**AssociationEnd**概念模型中的實體類型的修改函式會對應至基礎資料庫中的預存程序時，會使用對應規格語言 (MSL) 中的項目。 如果預存程序會採用的參數，其值會保留在一個關聯的屬性，修改**AssociationEnd**元素會對應至參數的屬性值。 如需詳細資訊，請參閱下列範例。

如需有關如何將實體類型的修改函式對應至預存程序的詳細資訊，請參閱 ModificationFunctionMapping 項目 (MSL) 和逐步解說： 將實體對應至預存程序。

**AssociationEnd**元素可能具有下列子元素：

-   ScalarProperty

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至屬性**AssociationEnd**項目。

| 屬性名稱     | 必要 | 值                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet** | [是]         | 要對應的關聯名稱。                                                                                                                                 |
| **From**           | [是]         | 值**FromRole**對應至要對應的關聯的導覽屬性的屬性。 如需詳細資訊，請參閱 NavigationProperty 項目 (CSDL)。 |
| **若要**             | [是]         | 值**ToRole**對應至要對應的關聯的導覽屬性的屬性。 如需詳細資訊，請參閱 NavigationProperty 項目 (CSDL)。   |

### <a name="example"></a>範例

請考慮下列概念模型實體類型：

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

同時請考慮下列預存程序：

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

若要對應的更新函式`Course`此預存程序的實體，您必須提供值給**DepartmentID**參數。 `DepartmentID` 的值不會對應至實體類型上的屬性；它會包含在獨立關聯中，該關聯的對應如下所示：

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

下列程式碼示範**AssociationEnd**項目用來對應**DepartmentID**屬性**FK\_課程\_部門**要關聯**UpdateCourse**預存程序 (要更新函式**課程**實體類型對應):

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

## <a name="associationsetmapping-element-msl"></a>AssociationSetMapping 項目 (MSL)

**AssociationSetMapping**對應規格語言 (MSL) 中的項目會定義基礎資料庫中的概念模型和資料表資料行中的關聯之間的對應。

概念模型中的關聯指其屬性代表基礎資料庫中主要與外部索引鍵資料行的類型。 **AssociationSetMapping**項目會使用兩個的 EndProperty 項目來定義資料庫中的關聯類型屬性與資料行之間的對應。 您可以將條件放在這些對應，與條件的項目上。 ModificationFunctionMapping 項目資料庫中的預存程序，將對應的 insert、 update 和 delete 函式之關聯。 QueryView 項目中使用 Entity SQL 字串，以定義關聯和資料表資料行之間的唯讀對應。

> [!NOTE]
> 如果概念模型中的關聯定義參考條件約束，則關聯不需要是與對應**AssociationSetMapping**項目。 如果**AssociationSetMapping**項目已存在具有參考條件約束的關聯，在中所定義的對應**AssociationSetMapping**項目將會被忽略。 如需詳細資訊，請參閱 ReferentialConstraint 項目 (CSDL)。

**AssociationSetMapping**元素可能具有下列子元素

-   QueryView （零或一個）
-   EndProperty （零或兩個）
-   條件 （零或多個）
-   ModificationFunctionMapping （零或一個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**AssociationSetMapping**項目。

| 屬性名稱     | 必要 | 值                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **名稱**           | [是]         | 要對應的概念模型關聯集名稱。                      |
| **類型名稱**       | 否          | 要對應的概念模型關聯類型之命名空間限定名稱。 |
| **StoreEntitySet** | 否          | 要對應的資料表名稱。                                                 |

### <a name="example"></a>範例

下列範例所示**AssociationSetMapping**所在的項目**FK\_課程\_部門**概念模型中關聯集對應至**課程**資料庫資料表中的。 子系中所指定的關聯類型屬性與資料表資料行之間的對應**EndProperty**項目。

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

## <a name="complexproperty-element-msl"></a>ComplexProperty 項目 (MSL)

A **ComplexProperty**對應規格語言 (MSL) 中的項目會定義概念模型實體類型和資料表資料行上基礎資料庫中的複雜類型屬性之間的對應。 ScalarProperty 項目子系中指定的屬性資料行對應。

**ComplexType**屬性項目可以有下列子項目：

-   ScalarProperty （零或多個）
-   **ComplexProperty** （零或多個）
-   ComplextTypeMapping （零或多個）
-   條件 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至屬性**ComplexProperty**項目：

| 屬性名稱 | 必要 | 值                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **名稱**       | [是]         | 概念模型中要對應的實體類型之複雜屬性的名稱。 |
| **類型名稱**   | 否          | 概念模型屬性類型的命名空間限定名稱。                              |

### <a name="example"></a>範例

下列範例是以 School 模型為基礎。 下列複雜型別已加入至概念模型：

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

**LastName**並**FirstName**的屬性**人員**實體類型已取代為一個複雜屬性**名稱**:

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

下列 MSL 顯示**ComplexProperty**項目用來對應**名稱**至基礎資料庫中的資料行的屬性：

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

## <a name="complextypemapping-element-msl"></a>ComplexTypeMapping 項目 (MSL)

**ComplexTypeMapping**對應規格語言 (MSL) 中的項目子系的 ResultMapping 項目，並定義中的基礎概念模型中的函式匯入和預存程序之間的對應當下列條件成立時的資料庫：

-   函式匯入會傳回概念複雜類型。
-   預存程序所傳回之資料行名稱未與複雜類型上的屬性名稱完全相符。

根據預設，預存程序所傳回之資料行與複雜類型間的對應是以資料行和屬性名稱為基礎。 如果資料行名稱不完全符合屬性名稱，您必須使用**ComplexTypeMapping**來定義對應的項目。 如需預設對應的範例，請參閱 FunctionImportMapping 項目 (MSL)。

**ComplexTypeMapping**元素可能具有下列子元素：

-   ScalarProperty （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至屬性**ComplexTypeMapping**項目。

| 屬性名稱 | 必要 | 值                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **類型名稱**   | [是]         | 要對應的複雜類型的命名空間限定名稱。 |

### <a name="example"></a>範例

請考慮下列預存程序：

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

同時請考慮下列概念模型複雜類型：

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

若要建立函式匯入傳回前一個複雜型別的執行個體，資料行之間的對應傳回預存程序和實體類型必須定義於**ComplexTypeMapping**項目：

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

## <a name="condition-element-msl"></a>Condition 項目 (MSL)

**條件**對應規格語言 (MSL) 中的項目在概念模型和基礎資料庫之間的對應上放置條件。 XML 節點內定義的對應會將有效，如果要將所有條件，為在指定的子系**條件**符合項目。 否則，對應無效。 比方說，如果 MappingFragment 項目包含一或多個**條件**子項目內定義的對應**MappingFragment**才有效，如果要將所有節點的子系條件**條件**符合項目。

每個條件可以套用至**名稱**(由指定的概念模型實體屬性的名稱**名稱**屬性)，或有**ColumnName** （中的資料行的名稱所指定的資料庫**ColumnName**屬性)。 當**名稱**設定屬性時，針對實體的屬性值檢查條件。 當**ColumnName**設定屬性時，針對資料行值檢查條件。 只有其中一個**名稱**或是**ColumnName**屬性中指定**條件**項目。

> [!NOTE]
> 當**條件**FunctionImportMapping 項目內，只能使用元素**名稱**屬性不適用。

**條件**項目可以是下列項目子系：

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

**條件**項目可以有任何子項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至屬性**條件**項目：

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ColumnName** | 否          | 其值用來評估條件之資料表資料行的名稱。                                                                                                                                                                                                                   |
| **IsNull**     | 否          | **真**或是**False**。 如果值為 **，則為 True**和資料行的值是**null**，或如果值為**False**資料行值不是**null**，條件為 true. 否則，條件不成立。 <br/> **IsNull**並**值**屬性不能用在相同的時間。 |
| **值**      | 否          | 要與資料行值比較的值。 如果值相同，則條件成立。 否則，條件不成立。 <br/> **IsNull**並**值**屬性不能用在相同的時間。                                                                       |
| **名稱**       | 否          | 其值用來評估條件之概念模型實體屬性的名稱。 <br/> 這個屬性不適用如果**條件**FunctionImportMapping 項目內使用項目。                                                                           |

### <a name="example"></a>範例

下列範例所示**條件**做為子系的項目**MappingFragment**項目。 當**HireDate**不是 null 和**EnrollmentDate**是 null，資料對應之間**SchoolModel.Instructor**型別和**PersonID**並**HireDate**的資料行**人員**資料表。 當**EnrollmentDate**不是 null 和**HireDate**是 null，資料對應之間**SchoolModel.Student**型別和**PersonID**並**註冊**的資料行**人員**資料表。

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

## <a name="deletefunction-element-msl"></a>DeleteFunction 項目 (MSL)

**DeleteFunction**對應規格語言 (MSL) 中的項目對應至基礎資料庫中的預存程序的概念模型中的關聯的實體類型的刪除函式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱函式項目 (SSDL)。

> [!NOTE]
> 如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。

### <a name="deletefunction-applied-to-entitytypemapping"></a>套用至 EntityTypeMapping 的 DeleteFunction

當套用至 EntityTypeMapping 項目**DeleteFunction**項目對應至預存程序的概念模型中的實體類型的刪除函式。

**DeleteFunction**項目可以擁有下列子項目時套用至**EntityTypeMapping**項目：

-   AssociationEnd （零或多個）
-   ComplexProperty （零或多個）
-   ScarlarProperty （零或多個）

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**DeleteFunction**項目時套用至**EntityTypeMapping**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [是]         | 刪除函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例以 School 模型為基礎，並顯示**DeleteFunction**對應的刪除函式的項目**人員**實體類型**DeletePerson**預存程序。 **DeletePerson**宣告於儲存體模型中預存程序。

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

### <a name="deletefunction-applied-to-associationsetmapping"></a>套用至 AssociationSetMapping 的 DeleteFunction

當套用至 AssociationSetMapping 項目**DeleteFunction**項目對應至預存程序的概念模型中關聯的刪除函式。

**DeleteFunction**項目可以擁有下列子項目時套用至**AssociationSetMapping**項目：

-   EndProperty

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**DeleteFunction**項目時套用至**AssociationSetMapping**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [是]         | 刪除函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例以 School 模型為基礎，並顯示**DeleteFunction**用來刪除函式的對應項目**CourseInstructor**關聯**DeleteCourseInstructor**預存程序。 **DeleteCourseInstructor**宣告於儲存體模型中預存程序。

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

## <a name="endproperty-element-msl"></a>EndProperty 項目 (MSL)

**EndProperty**對應規格語言 (MSL) 中的項目定義結尾或修改函式的概念模型關聯和基礎資料庫間的對應。 子 ScalarProperty 項目中指定的屬性資料行對應。

當**EndProperty**項目用來定義概念模型關聯一端的對應，它是 AssociationSetMapping 項目子系。 當**EndProperty**項目用來定義概念模型關聯的修改函式的對應，它是 InsertFunction 項目或 DeleteFunction 項目子系。

**EndProperty**元素可能具有下列子元素：

-   ScalarProperty （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至屬性**EndProperty**項目：

| 屬性名稱 | 必要 | 值                                                 |
|:---------------|:------------|:------------------------------------------------------|
| 名稱           | [是]         | 要對應的關聯端名稱。 |

### <a name="example"></a>範例

下列範例所示**AssociationSetMapping**所在的項目**FK\_課程\_部門**概念模型中的關聯會對應至**課程**資料庫資料表中的。 子系中所指定的關聯類型屬性與資料表資料行之間的對應**EndProperty**項目。

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

### <a name="example"></a>範例

下列範例所示**EndProperty**項目對應關聯的插入和刪除函式 (**CourseInstructor**) 與基礎資料庫中的預存程序。 對應至的函式會在儲存體模型中宣告。

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

## <a name="entitycontainermapping-element-msl"></a>EntityContainerMapping 項目 (MSL)

**EntityContainerMapping**對應規格語言 (MSL) 中的項目對應至儲存體模型中的實體容器的概念模型中的實體容器。 **EntityContainerMapping**項目是對應項目的子系。

**EntityContainerMapping**元素可能具有下列子項目 （列出的順序）：

-   EntitySetMapping （零或多個）
-   AssociationSetMapping （零或多個）
-   FunctionImportMapping （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntityContainerMapping**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | [是]         | 要對應至的儲存模型實體容器名稱。                                                                                                                                                                                     |
| **CdmEntityContainer**    | [是]         | 要對應的概念模型實體容器名稱。                                                                                                                                                                                  |
| **GenerateUpdateViews**   | 否          | **真**或是**False**。 如果**False**，會產生任何更新檢視。 此屬性應該設為**False**當您有會變為無效，因為資料可能無法成功來回的唯讀對應。 <br/> 預設值是 **True**。 |

### <a name="example"></a>範例

下列範例所示**EntityContainerMapping**對應的項目**SchoolModelEntities**容器 （概念模型實體容器） **SchoolModelStoreContainer**容器 （儲存體模型實體容器）：

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

## <a name="entitysetmapping-element-msl"></a>EntitySetMapping 項目 (MSL)

**EntitySetMapping**設定儲存體模型中的對應規格語言 (MSL) 對應概念模型實體中的所有型別設定為實體中的項目。 概念模型中的實體集是邏輯容器相同的型別 （和衍生型別） 的實體的執行個體。 儲存體模型中的實體集代表資料表或檢視基礎資料庫中。 概念模型實體集指定的值所**名稱**屬性**EntitySetMapping**項目。 對應到資料表或檢視由**StoreEntitySet**屬性中每個子 MappingFragment 項目，或在**EntitySetMapping**項目本身。

**EntitySetMapping**元素可能具有下列子元素：

-   EntityTypeMapping （零或多個）
-   QueryView （零或一個）
-   MappingFragment （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntitySetMapping**項目。

| 屬性名稱           | 必要 | 值                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                 | [是]         | 要對應的概念模型實體集名稱。                                                                                                                                                             |
| **TypeName** **1**       | 否          | 要對應的概念模型實體類型名稱。                                                                                                                                                            |
| **StoreEntitySet** **1** | 否          | 要對應至的儲存模型實體集名稱。                                                                                                                                                             |
| **MakeColumnsDistinct**  | 否          | **True**或是**False**取決於是否會傳回只相異的資料列。 <br/> 如果此屬性設為 **，則為 True**，則**GenerateUpdateViews** EntityContainerMapping 項目屬性必須設為**False**。 |

 

**1** **TypeName**並**StoreEntitySet**屬性可用來取代 EntityTypeMapping 和 MappingFragment 子項目中的單一實體型別對應至單一資料表。

### <a name="example"></a>範例

下列範例所示**EntitySetMapping**中的地圖 （基底型別和兩個衍生的類型） 的三種類型的項目**課程**概念模型中的三個不同資料表的實體集基礎資料庫。 所指定的資料表**StoreEntitySet**中每個屬性**MappingFragment**項目。

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

## <a name="entitytypemapping-element-msl"></a>EntityTypeMapping 項目 (MSL)

**EntityTypeMapping**對應規格語言 (MSL) 中的項目會定義概念模型和資料表中的實體類型或檢視表之間的對應基礎資料庫中。 如需概念模型實體類型和基礎資料庫資料表或檢視的資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。 要對應概念模型實體類型由**TypeName**屬性**EntityTypeMapping**項目。 所指定的資料表或檢視要對應**StoreEntitySet**子 MappingFragment 項目的屬性。

ModificationFunctionMapping 子項目可以用來對應插入、 更新或刪除資料庫中的預存程序的實體類型的函式。

**EntityTypeMapping**元素可能具有下列子元素：

-   MappingFragment （零或多個）
-   ModificationFunctionMapping （零或一個）
-   ScalarProperty
-   條件

> [!NOTE]
> **MappingFragment**並**ModificationFunctionMapping**項目不能子項目的**EntityTypeMapping**在同一時間的項目。


> [!NOTE]
> **ScalarProperty**並**條件**項目只能是子項目的**EntityTypeMapping** FunctionImportMapping 項目內使用時的項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntityTypeMapping**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **類型名稱**   | [是]         | 要對應的概念模型實體類型之命名空間限定名稱。 <br/> 如果型別是抽象型別或衍生型別，值必須是 `IsOfType(Namespace-qualified_type_name)`。 |

### <a name="example"></a>範例

下列範例示範具有兩個子 EntitySetMapping 項目**EntityTypeMapping**項目。 在第一個**EntityTypeMapping**項目**SchoolModel.Person**實體類型對應至**人員**資料表。 在第二個**EntityTypeMapping**元素中，更新功能**SchoolModel.Person**類型會對應至預存程序中， **UpdatePerson**，在資料庫中.

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

### <a name="example"></a>範例

下一個範例會顯示型別階層的對應，在該階層內根型別是抽象型別。 請注意，使用`IsOfType`語法**TypeName**屬性。

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

## <a name="functionimportmapping-element-msl"></a>FunctionImportMapping 項目 (MSL)

**FunctionImportMapping**對應規格語言 (MSL) 中的項目會定義概念模型中預存程序的函式匯入或函式之間的對應基礎資料庫中。 函式匯入必須在概念模型中宣告，而預存程序則必須在儲存體模型中宣告。 如需詳細資訊，請參閱 FunctionImport 項目 (CSDL) 和函式項目 (SSDL)。

> [!NOTE]
> 根據預設，如果函式匯入傳回概念模型實體類型或複雜型別，則基礎預存程序所傳回的資料行名稱必須與概念模型類型上的屬性名稱完全相符。 如果資料行名稱不完全相符的屬性名稱，必須能 ResultMapping 項目中定義的對應。

**FunctionImportMapping**元素可能具有下列子元素：

-   ResultMapping （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至屬性**FunctionImportMapping**項目：

| 屬性名稱         | 必要 | 值                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | [是]         | 概念模型中要對應的函式匯入名稱。           |
| **FunctionName**       | [是]         | 儲存體模型中要對應的函式之命名空間限定名稱。 |

### <a name="example"></a>範例

下列範例是以 School 模型為基礎。 請考量儲存體模型中的下列函式：

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

同時考量概念模型中的這個函式匯入：

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

下列範例所示**FunctionImportMapping**用來對應函式和函式彼此的匯入上方的項目：

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>InsertFunction 項目 (MSL)

**InsertFunction**對應規格語言 (MSL) 中的項目對應至基礎資料庫中的預存程序的概念模型中的關聯的實體類型的插入函式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱函式項目 (SSDL)。

> [!NOTE]
> 如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。

**InsertFunction**項目可以是子系 ModificationFunctionMapping 項目，而且套用至 EntityTypeMapping 項目或 AssociationSetMapping 項目。

### <a name="insertfunction-applied-to-entitytypemapping"></a>套用至 EntityTypeMapping 的 InsertFunction

當套用至 EntityTypeMapping 項目**InsertFunction**項目對應至預存程序的概念模型中的實體類型的插入函式。

**InsertFunction**項目可以擁有下列子項目時套用至**EntityTypeMapping**項目：

-   AssociationEnd （零或多個）
-   ComplexProperty （零或多個）
-   ResultBinding （零或一個）
-   ScarlarProperty （零或多個）

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**InsertFunction**項目時套用至**EntityTypeMapping**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [是]         | 插入函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例以 School 模型為基礎，並顯示**InsertFunction**項目用來將 Person 實體類型的插入函式的對應**InsertPerson**預存程序。 **InsertPerson**宣告於儲存體模型中預存程序。

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
### <a name="insertfunction-applied-to-associationsetmapping"></a>套用至 AssociationSetMapping 的 InsertFunction

當套用至 AssociationSetMapping 項目**InsertFunction**項目對應至預存程序的概念模型中關聯的插入函式。

**InsertFunction**項目可以擁有下列子項目時套用至**AssociationSetMapping**項目：

-   EndProperty

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**InsertFunction**項目時套用至**AssociationSetMapping**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [是]         | 插入函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例以 School 模型為基礎，並顯示**InsertFunction**用來插入函式的對應項目**CourseInstructor**關聯**InsertCourseInstructor**預存程序。 **InsertCourseInstructor**宣告於儲存體模型中預存程序。

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

## <a name="mapping-element-msl"></a>Mapping 項目 (MSL)

**對應**對應規格語言 (MSL) 中的項目包含對應至資料庫的概念模型中定義 （如儲存體模型中所述） 的物件資訊。 如需詳細資訊，請參閱 CSDL 規格和 SSDL 規格。

**對應**項目是對應規格的根項目。 對應規格的 XML 命名空間是http://schemas.microsoft.com/ado/2009/11/mapping/cs。

對應項目可以擁有下列子項目 (依列出的順序)：

-   別名 （零或多個）
-   EntityContainerMapping （只有一個）

MSL 中所參考之概念及儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。 如需概念模型命名空間名稱的資訊，請參閱結構描述項目 (CSDL)。 儲存體模型命名空間名稱的詳細資訊，請參閱結構描述項目 (SSDL)。 MSL 中所使用的命名空間的別名可以使用別名項目加以定義。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**對應**項目。

| 屬性名稱 | 必要 | 值                                                 |
|:---------------|:------------|:------------------------------------------------------|
| **空格鍵**      | [是]         | **C-S**。 這是固定值，無法變更。 |

### <a name="example"></a>範例

下列範例所示**對應**項目，根據 School 模型的一部分。 如需 School 模型的詳細資訊，請參閱快速入門 (Entity Framework):

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

## <a name="mappingfragment-element-msl"></a>MappingFragment 項目 (MSL)

**MappingFragment**對應規格語言 (MSL) 中的項目會定義概念模型實體類型和資料表或檢視資料庫中的屬性之間的對應。 如需概念模型實體類型和基礎資料庫資料表或檢視的資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。 **MappingFragment**可以 EntityTypeMapping 項目或 EntitySetMapping 項目子系項目。

**MappingFragment**元素可能具有下列子元素：

-   ComplexType （零或多個）
-   ScalarProperty （零或多個）
-   條件 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**MappingFragment**項目。

| 屬性名稱          | 必要 | 值                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StoreEntitySet**      | [是]         | 要對應的資料表或檢視之名稱。                                                                                                                                                                           |
| **MakeColumnsDistinct** | 否          | **True**或是**False**取決於是否會傳回只相異的資料列。 <br/> 如果此屬性設為 **，則為 True**，則**GenerateUpdateViews** EntityContainerMapping 項目屬性必須設為**False**。 |

### <a name="example"></a>範例

下列範例所示**MappingFragment**項目做為子系**EntityTypeMapping**項目。 在此範例中的屬性**課程**概念模型中的型別會對應到的資料行**課程**資料庫資料表中的。

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

### <a name="example"></a>範例

下列範例所示**MappingFragment**項目做為子系**EntitySetMapping**項目。 上述的屬性，例如**課程**概念模型中的型別會對應到的資料行**課程**資料庫資料表中的。

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

## <a name="modificationfunctionmapping-element-msl"></a>ModificationFunctionMapping 屬性 (MSL)

**ModificationFunctionMapping**對應規格語言 (MSL) 中的項目會對應插入、 更新和刪除基礎資料庫中的預存程序的概念模型實體類型的函式。 **ModificationFunctionMapping**項目也可以將對應的插入及刪除基礎資料庫中的預存程序的概念模型中的多對多關聯性的函式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱函式項目 (SSDL)。

> [!NOTE]
> 如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。


> [!NOTE]
> 如果繼承階層架構中某個實體的修改函式已對應至預存程序，則階層架構中所有類型的修改函式都必須對應至預存程序。

**ModificationFunctionMapping**項目可以是子系 EntityTypeMapping 項目或 AssociationSetMapping 項目。

**ModificationFunctionMapping**元素可能具有下列子元素：

-   DeleteFunction （零或一個）
-   InsertFunction （零或一個）
-   UpdateFunction （零或一個）

任何屬性都適用於**ModificationFunctionMapping**項目。

### <a name="example"></a>範例

下列範例顯示的實體集對應**人**School 模型中的實體集。 除了針對的資料行對應**Person**實體類型，對應的插入、 更新和刪除函式**人員**類型會顯示。 對應至的函式會在儲存體模型中宣告。

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

### <a name="example"></a>範例

下列範例示範集對應的關聯**CourseInstructor** School 模型中關聯集。 除了針對的資料行對應**CourseInstructor**關聯的插入和刪除函式的對應**CourseInstructor**關聯會顯示。 對應至的函式會在儲存體模型中宣告。

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
 

 

## <a name="queryview-element-msl"></a>QueryView 項目 (MSL)

**QueryView**對應規格語言 (MSL) 中的項目在概念模型和基礎資料庫中的資料表中定義的實體類型或關聯之間的唯讀對應。 對應會定義使用 Entity SQL 查詢，就會對儲存體模型中，評估和 express 的實體或概念模型中的關聯來表示結果集。 因為查詢檢視是唯讀的，無法使用標準更新命令來更新查詢檢視所定義的類型。 您可以使用修改函式來更新這些類型。 如需詳細資訊，請參閱 < 如何： 對應至預存程序的修改函式。

> [!NOTE]
> 在  **QueryView**項目，包含的 Entity SQL 運算式**GroupBy**，不支援群組彙總或導覽屬性。

 

**QueryView**項目可以是子系 EntitySetMapping 項目或 AssociationSetMapping 項目。 若是前者，查詢檢視會定義概念模型中之實體的唯讀對應。 若是後者，查詢檢視會定義概念模型中之關聯的唯讀對應。

> [!NOTE]
> 如果**AssociationSetMapping**項目是參考條件約束，與關聯**AssociationSetMapping**項目會被忽略。 如需詳細資訊，請參閱 ReferentialConstraint 項目 (CSDL)。

**QueryView**項目不能有任何子項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**QueryView**項目。

| 屬性名稱 | 必要 | 值                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **類型名稱**   | 否          | 要由查詢檢視對應的概念模型類型名稱。 |

### <a name="example"></a>範例

下列範例所示**QueryView**的子系的項目**EntitySetMapping**項目，並定義查詢檢視對應**部門**中的實體類型School 模型。

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

因為查詢只會傳回的成員子集**部門**儲存體模型中的型別**部門**School 模型中的型別已經修改，依據這個對應，如下所示：

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

### <a name="example"></a>範例

下一個範例所示**QueryView**為的子系的項目**AssociationSetMapping**項目，並定義的唯讀對應`FK_Course_Department`School 模型中的關聯。

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
 
### <a name="comments"></a>註解

您可以定義查詢檢視來啟用下列案例：

-   定義概念模型中的實體，其中未包含儲存體模型中實體的所有屬性。 這包括沒有預設值，而且不支援的屬性**null**值。
-   將儲存體模型中的計算資料行對應至概念模型中實體類型的屬性。
-   定義分割概念模型實體所用之條件不是以實體為依據的對應。 當您指定條件式對應，使用**條件**項目，提供的條件必須等於指定的值。 如需詳細資訊，請參閱條件項目 (MSL)。
-   將儲存體模型中的相同資料行對應至概念模型中的多種類型。
-   將多種類型對應至相同資料表。
-   定義概念模型中不是以關聯式結構描述之外部索引鍵為依據的關聯。
-   使用自訂商務邏輯來設定概念模型中的屬性值。 例如，您可以將對應的字串值的值的資料來源中的"T" **，則為 true**，布林值，將概念模型中的。
-   為查詢結果定義條件篩選器。
-   對概念模型資料強加的限制比儲存體模型的少。 比方說，您可以讓屬性在概念模型中可為 null 即使它對應的資料行不支援**null**值。

下列考量適用於為實體定義查詢檢視的情況：

-   查詢檢視是唯讀的。 您只能使用修改函式來更新這些實體。
-   當您依據查詢檢視定義實體類型時，也必須依據查詢檢視定義所有的相關實體。
-   當您將多對多關聯對應至代表關聯式結構描述中的連結資料表的儲存體模型中的實體時，您必須定義**QueryView**中的項目**AssociationSetMapping**這個連結資料表的項目。
-   必須為類型階層架構中的所有類型定義查詢檢視。 您可以透過下列方法完成這項作業：
-   -   使用單一**QueryView**項目，指定單一的 Entity SQL 查詢會傳回階層中的所有實體類型的聯集。
    -   使用單一**QueryView**根據特定條件的項目，指定單一的 Entity SQL 查詢來傳回特定實體類型階層架構中使用 CASE 運算子。
    -   加上一個**QueryView**特定型別階層架構中的項目。 在此案例中，使用**TypeName**屬性**QueryView**項目來指定每個檢視的實體類型。
-   定義查詢檢視時，您無法指定**StorageSetName**屬性上**EntitySetMapping**項目。
-   定義查詢檢視時， **EntitySetMapping**項目不能同時包含**屬性**對應。

## <a name="resultbinding-element-msl"></a>ResultBinding 項目 (MSL)

**ResultBinding**對應規格語言 (MSL) 中的項目對應時，所傳回預存程序至概念模型中的實體屬性的實體類型修改函式會對應至預存的資料行值基礎資料庫中的程序。 例如，當身分識別資料行的值由 insert 預存程序， **ResultBinding**項目對應至概念模型中的實體類型屬性的傳回的值。

**ResultBinding**項目可以是子系 InsertFunction 項目或 UpdateFunction 項目。

**ResultBinding**項目不能有任何子項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至屬性**ResultBinding**項目：

| 屬性名稱 | 必要 | 值                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **名稱**       | [是]         | 概念模型中要對應之實體屬性的名稱。 |
| **ColumnName** | [是]         | 要對應的資料行名稱。                                          |

### <a name="example"></a>範例

下列範例以 School 模型為基礎，並顯示**InsertFunction**項目用來將對應的插入函式**人員**實體類型**InsertPerson**預存程序。 ( **InsertPerson**預存程序如下所示，並宣告儲存模型中。)A **ResultBinding**項目用來對應預存程序所傳回的資料行值 (**NewPersonID**) 的實體型別屬性 (**PersonID**)。

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

下列 TRANSACT-SQL 描述**InsertPerson**預存程序：

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

## <a name="resultmapping-element-msl"></a>ResultMapping 項目 (MSL)

**ResultMapping**對應規格語言 (MSL) 中的項目會定義基礎資料庫中的概念模型中的函式匯入和預存程序之間的對應當下列條件成立：

-   函式匯入會傳回概念模型實體類型或複雜型別。
-   預存程序所傳回之資料行名稱未與實體類型或複雜型別上的屬性名稱完全相符。

根據預設，預存程序所傳回之資料行與實體類型或複雜型別間的對應是以資料行和屬性名稱為基礎。 如果資料行名稱不完全符合屬性名稱，您必須使用**ResultMapping**來定義對應的項目。 如需預設對應的範例，請參閱 FunctionImportMapping 項目 (MSL)。

**ResultMapping**元素是 FunctionImportMapping 項目子系元素。

**ResultMapping**元素可能具有下列子元素：

-   EntityTypeMapping （零或多個）
-   ComplexTypeMapping

任何屬性都適用於**ResultMapping**項目。

### <a name="example"></a>範例

請考慮下列預存程序：

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

同時請考慮下列概念模型實體類型：

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

若要建立函式匯入傳回前一個的實體類型之執行個體，資料行之間的對應傳回預存程序和實體類型必須定義於**ResultMapping**項目：

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

## <a name="scalarproperty-element-msl"></a>ScalarProperty 項目 (MSL)

**ScalarProperty**對應規格語言 (MSL) 中的項目將在概念模型實體類型、 複雜型別或關聯的屬性對應至基礎資料庫中的預存程序參數或資料表資料行。

> [!NOTE]
> 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱函式項目 (SSDL)。

**ScalarProperty**項目可以是下列項目子系：

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

為子系**MappingFragment**， **ComplexProperty**，或**EndProperty**項目**ScalarProperty**的屬性對應在資料庫中的資料行的概念模型。 為子系**InsertFunction**， **UpdateFunction**，或**DeleteFunction**項目**ScalarProperty**的屬性對應在 預存程序參數的概念模型。

**ScalarProperty**項目不能有任何子項目。

### <a name="applicable-attributes"></a>適用屬性

若要套用的屬性**ScalarProperty**項目而有所不同的項目角色。

下表描述的屬性時可適用**ScalarProperty**元素用來將概念模型屬性對應至資料庫中的資料行：

| 屬性名稱 | 必要 | 值                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **名稱**       | [是]         | 要對應的概念模型屬性名稱。 |
| **ColumnName** | [是]         | 要對應的資料表資料行名稱。              |

下表描述可套用至屬性**ScalarProperty**時它用來將概念模型屬性對應至預存程序參數的項目：

| 屬性名稱    | 必要 | 值                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**          | [是]         | 要對應的概念模型屬性名稱。                                                                                 |
| **參數名稱** | [是]         | 要對應的參數名稱。                                                                                                 |
| **版本**       | 否          | **目前**或是**原始**取決於是否目前的值或屬性的原始值應該用於並行存取檢查。 |

### <a name="example"></a>範例

下列範例所示**ScalarProperty**兩種方式使用的項目：

-   若要將屬性對應**Person**實體類型的資料行**人員**資料表。
-   若要將屬性對應**Person**實體類型的參數**UpdatePerson**預存程序。 預存程序已宣告於儲存模型中。

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

### <a name="example"></a>範例

下一個範例所示**ScalarProperty**用來對應插入和刪除函式在資料庫中的預存程序的概念模型關聯的項目。 預存程序已宣告於儲存模型中。

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

## <a name="updatefunction-element-msl"></a>UpdateFunction 項目 (MSL)

**UpdateFunction**對應規格語言 (MSL) 中的項目對應至基礎資料庫中的預存程序的概念模型中的實體類型的更新函式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱函式項目 (SSDL)。

> [!NOTE]
>  如果您不會將這三個插入、 更新或刪除作業的預存程序的實體類型、 對應的作業將會失敗，如果在執行階段執行和 UpdateException 就會擲回。

**UpdateFunction**項目可以是子系 ModificationFunctionMapping 項目，而且套用至 EntityTypeMapping 項目。

**UpdateFunction**元素可能具有下列子元素：

-   AssociationEnd （零或多個）
-   ComplexProperty （零或多個）
-   ResultBinding （零或一個）
-   ScarlarProperty （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**UpdateFunction**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [是]         | 更新函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

### <a name="example"></a>範例

下列範例以 School 模型為基礎，並顯示**UpdateFunction**用來更新函式的對應項目**人員**實體類型**UpdatePerson**預存程序。 **UpdatePerson**宣告於儲存體模型中預存程序。

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
