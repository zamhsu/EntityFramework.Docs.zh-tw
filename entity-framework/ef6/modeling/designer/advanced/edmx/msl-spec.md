---
title: MSL 規格-EF6
description: Entity Framework 6 中的 MSL 規格
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 0780a549d4cbb60bf84bbed927d52588e99148e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066339"
---
# <a name="msl-specification"></a>MSL 規格
對應規格語言 (MSL) 是以 XML 為基礎的語言，描述 Entity Framework 應用程式的概念模型和儲存模型之間的對應。

在 Entity Framework 應用程式中，對應中繼資料會從 msl 檔案載入， (在組建階段以 MSL) 寫入。 Entity Framework 在執行時間使用對應中繼資料，將針對概念模型的查詢轉譯為存放區特有的命令。

Entity Framework Designer (EF 設計工具) 在設計階段將對應資訊儲存在 .edmx 檔中。 在組建階段，Entity Designer 會使用 .edmx 檔案中的資訊來建立在執行時間 Entity Framework 所需的 msl 檔案。

MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。 如需概念模型命名空間名稱的詳細資訊，請參閱 [CSDL 規格](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)。 如需儲存模型命名空間名稱的詳細資訊，請參閱 [SSDL 規格](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)。

MSL 的版本是以 XML 命名空間來區分。

| MSL 版本 | XML 命名空間                                        |
|:------------|:-----------------------------------------------------|
| MSL v1      | urn：架構-microsoft-com： windows： storage：對應： CS |
| MSL v2      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| MSL v3      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Alias 元素 (MSL)

對應規格語言 (MSL) 中的 **Alias** 專案是對應元素的子系，可用來定義概念模型和儲存模型命名空間的別名。 MSL 中所參考之所有概念或儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。 如需概念模型命名空間名稱的詳細資訊，請參閱 (CSDL) 的架構元素。 如需儲存模型命名空間名稱的詳細資訊，請參閱 (SSDL) 的架構元素。

**Alias**元素不能有子項目。

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **Alias** 元素的屬性。

| 屬性名稱 | 必要 | 值                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **金鑰**        | 是         | **Value**屬性所指定之命名空間的別名。 |
| **ReplTest1**      | 是         | **Key**元素值為別名的命名空間。     |

### <a name="example"></a>範例

下列範例**Alias** `c` 會針對概念模型中定義的類型，顯示定義別名的 alias 元素。

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

## <a name="associationend-element-msl"></a>AssociationEnd 項目 (MSL)

當概念模型中實體類型的修改函式對應至基礎資料庫中的預存程式時，會使用對應規格語言 (MSL) 中的 **AssociationEnd** 元素。 如果修改預存程式採用的參數值保留在 association 屬性中， **AssociationEnd** 專案就會將屬性值對應至參數。 如需詳細資訊，請參閱下列範例。

如需將實體類型的修改函式對應至預存程式的詳細資訊，請參閱 ModificationFunctionMapping 元素 (MSL) 和逐步解說：將實體對應至預存程式。

**AssociationEnd**元素可以有下列子項目：

-   ScalarProperty

### <a name="applicable-attributes"></a>適用的屬性

下表描述適用于 **AssociationEnd** 元素的屬性。

| 屬性名稱     | 必要 | 值                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet** | 是         | 要對應的關聯名稱。                                                                                                                                 |
| **From**           | 是         | 導覽屬性的 **FromRole** 屬性值，對應至所對應的關聯。 如需詳細資訊，請參閱 (CSDL) 的 NavigationProperty 元素。 |
| **若要**             | 是         | 導覽屬性的 **ToRole** 屬性值，對應至所對應的關聯。 如需詳細資訊，請參閱 (CSDL) 的 NavigationProperty 元素。   |

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

為了將實體的更新函式對應 `Course` 到這個預存程式，您必須提供值給 **DepartmentID** 參數。 `DepartmentID` 的值不會對應至實體類型上的屬性；它會包含在獨立關聯中，該關聯的對應如下所示：

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

下列程式碼顯示用來將**FK \_ 課程 \_ 部門**關聯的**DepartmentID**屬性對應到**UpdateCourse**預存程式的**AssociationEnd**元素， (**課程**實體類型的更新函式對應) ：

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

對應規格語言 (MSL 中的 **AssociationSetMapping** 專案) 會定義概念模型中的關聯和基礎資料庫中的資料表資料行之間的對應。

概念模型中的關聯指其屬性代表基礎資料庫中主要與外部索引鍵資料行的類型。 **AssociationSetMapping**元素會使用兩個 EndProperty 元素來定義資料庫中關聯類型屬性和資料行之間的對應。 您可以使用 Condition 項目在這些對應上放置條件。 ModificationFunctionMapping 項目可以用來將關聯的插入、更新或刪除函式對應至資料庫中的預存程序。 在 QueryView 元素中使用 Entity SQL 字串，以定義關聯和資料表資料行之間的唯讀對應。

> [!NOTE]
> 如果為概念模型中的關聯定義了參考條件約束，則關聯不需要與 **AssociationSetMapping** 元素對應。 如果具有參考條件約束的關聯有 **AssociationSetMapping** 專案，則會忽略 **AssociationSetMapping** 元素中定義的對應。 如需詳細資訊，請參閱 (CSDL) 的 ReferentialConstraint 元素。

**AssociationSetMapping**元素可以有下列子項目

-   QueryView (零或一個)
-   EndProperty (零或兩個)
-   Condition (零或多個)
-   ModificationFunctionMapping (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **AssociationSetMapping** 元素的屬性。

| 屬性名稱     | 必要 | 值                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **名稱**           | 是         | 要對應的概念模型關聯集名稱。                      |
| **TypeName**       | 否          | 要對應的概念模型關聯類型之命名空間限定名稱。 |
| **中** | 否          | 要對應的資料表名稱。                                                 |

### <a name="example"></a>範例

下列範例顯示 **AssociationSetMapping** 元素，其中概念模型中的 **FK \_ 課程 \_ 部門** 關聯會對應至資料庫中的 **課程** 資料表。 關聯類型屬性和資料表資料行之間的對應是在子 **EndProperty** 元素中指定。

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

對應規格語言 (MSL 中的 **ComplexProperty** 專案) 會定義概念模型實體類型上的複雜類型屬性和基礎資料庫中之資料表資料行之間的對應。 屬性資料行對應會在 ScalarProperty 子項目中指定。

**ComplexType**屬性元素可以有下列子項目：

-   ScalarProperty (零或多個)
-   **ComplexProperty** (零或多個) 
-   ComplextTypeMapping (零或多個)
-   Condition (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表描述適用于 **ComplexProperty** 元素的屬性：

| 屬性名稱 | 必要 | 值                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **名稱**       | 是         | 概念模型中要對應的實體類型之複雜屬性的名稱。 |
| **TypeName**   | 否          | 概念模型屬性類型的命名空間限定名稱。                              |

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

**Person**實體類型的**LastName**和**FirstName**屬性已取代為一個複雜的屬性，**名稱**：

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

下列 MSL 顯示用來將**Name**屬性對應到基礎資料庫中之資料行的**ComplexProperty**元素：

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

對應規格語言 (MSL) 中的 **ComplexTypeMapping** 專案是 ResultMapping 專案的子系，並在下列情況成立時，定義概念模型中的函式匯入與基礎資料庫中的預存程式之間的對應：

-   函式匯入會傳回概念複雜類型。
-   預存程序所傳回之資料行名稱未與複雜類型上的屬性名稱完全相符。

根據預設，預存程序所傳回之資料行與複雜類型間的對應是以資料行和屬性名稱為基礎。 如果資料行名稱與屬性名稱不完全相符，您必須使用 **ComplexTypeMapping** 元素來定義對應。 如需預設對應的範例，請參閱 FunctionImportMapping 元素 (MSL) 。

**ComplexTypeMapping**元素可以有下列子項目：

-   ScalarProperty (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表描述適用于 **ComplexTypeMapping** 元素的屬性。

| 屬性名稱 | 必要 | 值                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **TypeName**   | 是         | 要對應的複雜類型的命名空間限定名稱。 |

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

為了建立會傳回前一個複雜類型之實例的函式匯入，必須在 **ComplexTypeMapping** 專案中定義預存程式所傳回之資料行與實體類型之間的對應：

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

對應規格語言 (MSL 中的 **Condition** 元素) 在概念模型與基礎資料庫之間的對應上放置條件。 如果符合子 **條件** 元素中所指定的所有條件，則在 XML 節點內定義的對應就會是有效的。 否則，對應無效。 例如，如果 MappingFragment 專案包含一或多個**條件**子項目，則只有在符合子**條件**元素的所有條件時， **MappingFragment**節點內所定義的對應才會是有效的。

每個條件可以套用至概念模型實體屬性名稱 (名稱屬性所指定**的名稱，**) **或 (資料庫**中的資料行名稱（由**columnname** **屬性) **指定）。 當設定 **Name** 屬性時，會針對實體屬性值檢查條件。 設定 **ColumnName** 屬性時，會根據資料行值來檢查條件。 只有一個 **Name** 或 **ColumnName** 屬性可以在 **Condition** 元素中指定。

> [!NOTE]
> 在 FunctionImportMapping 元素中使用 **Condition** 元素時，只有 **Name** 屬性不適用。

**Condition**元素可以是下列元素的子系：

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

**Condition**元素不能有任何子項目。

### <a name="applicable-attributes"></a>適用的屬性

下表描述適用于 **Condition** 元素的屬性：

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ColumnName** | 否          | 其值用來評估條件之資料表資料行的名稱。                                                                                                                                                                                                                   |
| **IsNull**     | 否          | **True** 或 **False**。 如果值為 **True** 且資料行值為 **null**，或者如果值為 **False** 且資料行值不是 **null**，則條件為 true。 否則，條件不成立。 <br/> 不能同時使用 **IsNull** 和 **Value** 屬性。 |
| **ReplTest1**      | 否          | 要與資料行值比較的值。 如果值相同，則條件成立。 否則，條件不成立。 <br/> 不能同時使用 **IsNull** 和 **Value** 屬性。                                                                       |
| **名稱**       | 否          | 其值用來評估條件之概念模型實體屬性的名稱。 <br/> 如果在 FunctionImportMapping 元素內使用 **Condition** 元素，則不適用這個屬性。                                                                           |

### <a name="example"></a>範例

下列範例會將 **條件** 元素顯示為 **MappingFragment** 元素的子系。 當**雇用**值不是 Null 且**EnrollmentDate**為 null 時，資料會在**SchoolModel**與**Person**資料表的**PersonID**和**雇用**日期資料行之間對應。 當**EnrollmentDate**不是 null，且**雇用**值為 null 時，會在**SchoolModel**與**Person**資料表的**PersonID**和**註冊**資料行之間對應資料。

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

對應規格語言 (MSL 中的 **DeleteFunction** 專案) 將概念模型中之實體類型或關聯的刪除函式對應至基礎資料庫中的預存程式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱 (SSDL) 的 Function 元素。

> [!NOTE]
> 如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。

### <a name="deletefunction-applied-to-entitytypemapping"></a>套用至 EntityTypeMapping 的 DeleteFunction

當套用至 EntityTypeMapping 元素時， **DeleteFunction** 專案會將概念模型中之實體類型的刪除函式對應至預存程式。

**DeleteFunction**元素套用至**EntityTypeMapping**專案時，可能會有下列子項目：

-   AssociationEnd (零或多個)
-   ComplexProperty (零或多個)
-   ScarlarProperty (零或多個)

#### <a name="applicable-attributes"></a>適用的屬性

下表描述可套用至 **DeleteFunction** 專案的屬性（當它套用至 **EntityTypeMapping** 元素時）。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 刪除函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例是以 School 模型為基礎，並顯示將**Person**實體類型的刪除函式對應至**DeletePerson**預存程式的**DeleteFunction**元素。 **DeletePerson**預存程式會在儲存體模型中宣告。

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

當套用至 AssociationSetMapping 元素時， **DeleteFunction** 專案會將概念模型中關聯的刪除函式對應至預存程式。

**DeleteFunction**元素在套用至**AssociationSetMapping**專案時，可能會有下列子項目：

-   EndProperty

#### <a name="applicable-attributes"></a>適用的屬性

下表描述套用至**AssociationSetMapping**專案時，可套用至**DeleteFunction**元素的屬性。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 刪除函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例是以 School 模型為基礎，並顯示用來將**CourseInstructor**關聯的刪除函式對應至**DeleteCourseInstructor**預存程式的**DeleteFunction**元素。 **DeleteCourseInstructor**預存程式會在儲存體模型中宣告。

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

對應規格語言 (MSL 中的 **EndProperty** 專案) 會定義概念模型關聯和基礎資料庫的 end 或修改函數之間的對應。 屬性資料行對應會指定在 ScalarProperty 子項目中。

當 **EndProperty** 元素用來定義概念模型關聯 end 的對應時，它是 AssociationSetMapping 元素的子系。 當 **EndProperty** 元素用來定義概念模型關聯之修改函式的對應時，它是 InsertFunction 元素或 DeleteFunction 專案的子系。

**EndProperty**元素可以有下列子項目：

-   ScalarProperty (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表描述適用于 **EndProperty** 元素的屬性：

| 屬性名稱 | 必要 | 值                                                 |
|:---------------|:------------|:------------------------------------------------------|
| 名稱           | 是         | 要對應的關聯端名稱。 |

### <a name="example"></a>範例

下列範例顯示 **AssociationSetMapping** 元素，其中概念模型中的 **FK \_ 課程 \_ 部門** 關聯會對應至資料庫中的 **課程** 資料表。 關聯類型屬性和資料表資料行之間的對應是在子 **EndProperty** 元素中指定。

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

下列範例顯示 **EndProperty** 專案，將關聯的插入和刪除函數 (**CourseInstructor**) 對應至基礎資料庫中的預存程式。 對應至的函式會在儲存體模型中宣告。

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

對應規格語言 (MSL 中的 **EntityContainerMapping** 專案) 將概念模型中的實體容器對應至儲存體模型中的實體容器。 **EntityContainerMapping**元素是對應元素的子系。

**EntityContainerMapping**元素可以依照列出的順序 (下列子項目) ：

-   EntitySetMapping (零或多個)
-   AssociationSetMapping (零或多個)
-   FunctionImportMapping (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **EntityContainerMapping** 元素的屬性。

| 屬性名稱            | 必要 | 值                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | 是         | 要對應至的儲存模型實體容器名稱。                                                                                                                                                                                     |
| **CdmEntityContainer**    | 是         | 要對應的概念模型實體容器名稱。                                                                                                                                                                                  |
| **GenerateUpdateViews**   | 否          | **True** 或 **False**。 如果 **為 False**，則不會產生任何更新視圖。 當您有唯讀對應，且資料可能無法成功地往返時，這個屬性應該設定為 **False** 。 <br/> 預設值是 **True**。 |

### <a name="example"></a>範例

下列範例顯示 **EntityContainerMapping** 元素，此專案 **會將概念** 模型實體容器)  (概念模型實體容器，以 (儲存體模型實體容器) 的 **SchoolModelStoreContainer** 容器：

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

對應規格語言 (MSL 中的 **EntitySetMapping** 專案) 將概念模型實體集中的所有型別對應到儲存體模型中的實體集。 概念模型中的實體集就是相同型別 (及衍生型別) 之實體的執行個體邏輯容器。 儲存模型中的實體集代表基礎資料庫中的資料表或檢視。 概念模型實體集是由**EntitySetMapping**元素的**Name**屬性值所指定。 對應至資料表或視圖是由每個子 MappingFragment 專案或**EntitySetMapping**元素本身中的**中**屬性所指定。

**EntitySetMapping**元素可以有下列子項目：

-   EntityTypeMapping (零或多個)
-   QueryView (零或一個)
-   MappingFragment (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **EntitySetMapping** 元素的屬性。

| 屬性名稱           | 必要 | 值                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                 | 是         | 要對應的概念模型實體集名稱。                                                                                                                                                             |
| **TypeName** **1**       | 否          | 要對應的概念模型實體類型名稱。                                                                                                                                                            |
| **中** **1** | 否          | 要對應至的儲存模型實體集名稱。                                                                                                                                                             |
| **MakeColumnsDistinct**  | 否          | **True** 或 **False** ，取決於是否只傳回相異資料列。 <br/> 如果這個屬性設定為 **True**，則 EntityContainerMapping 專案的 **GenerateUpdateViews** 屬性必須設定為 **False**。 |

 

**1** ： **TypeName** 和 **中** 屬性可用來取代 EntityTypeMapping 和 MappingFragment 子項目，以將單一實體類型對應至單一資料表。

### <a name="example"></a>範例

下列範例顯示 **EntitySetMapping** 元素，此專案會將三種類型 (基底類型，以及在概念模型的 **課程** 實體集中) 的兩個衍生型別，以及基礎資料庫中的三個不同資料表。 資料表是由每個**MappingFragment**元素中的**中**屬性所指定。

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

對應規格語言 (MSL 中的 **EntityTypeMapping** 專案) 會定義概念模型中的實體類型和基礎資料庫中的資料表或視圖之間的對應。 如需概念模型實體類型和基礎資料庫資料表或檢視的詳細資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。 正在對應的概念模型實體類型是由**EntityTypeMapping**專案的**TypeName**屬性所指定。 正在對應的資料表或視圖是由子 MappingFragment 專案的 **中** 屬性所指定。

ModificationFunctionMapping 子項目可以用來將實體類型的插入、更新或刪除函式對應至資料庫中的預存程序。

**EntityTypeMapping**元素可以有下列子項目：

-   MappingFragment (零或多個)
-   ModificationFunctionMapping (零或多個)
-   ScalarProperty
-   條件

> [!NOTE]
> **MappingFragment** 和 **ModificationFunctionMapping** 元素不能同時為 **EntityTypeMapping** 元素的子項目。


> [!NOTE]
> **ScalarProperty**和**Condition**元素在 FunctionImportMapping 元素中使用時，只能是**EntityTypeMapping**元素的子項目。

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **EntityTypeMapping** 元素的屬性。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **TypeName**   | 是         | 要對應的概念模型實體類型之命名空間限定名稱。 <br/> 如果型別是抽象型別或衍生型別，值必須是 `IsOfType(Namespace-qualified_type_name)`。 |

### <a name="example"></a>範例

下列範例顯示具有兩個子 **EntityTypeMapping** 專案的 EntitySetMapping 元素。 在第一個 **EntityTypeMapping** 元素中， **SchoolModel person** 實體類型會對應到 **person** 資料表。 在第二個 **EntityTypeMapping** 元素中， **SchoolModel** 的更新功能會對應到資料庫中的預存程式 **UpdatePerson**。

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

下一個範例會顯示型別階層的對應，在該階層內根型別是抽象型別。 請注意 `IsOfType` **TypeName** 屬性的語法用法。

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

對應規格語言 (MSL 中的 **FunctionImportMapping** 元素) 會定義概念模型中的函式匯入與基礎資料庫中的預存程式或函數之間的對應。 函式匯入必須在概念模型中宣告，而預存程序則必須在儲存體模型中宣告。 如需詳細資訊，請參閱 (SSDL) 的 FunctionImport 元素 (CSDL) 和 Function 元素。

> [!NOTE]
> 根據預設，如果函式匯入傳回概念模型實體類型或複雜型別，則基礎預存程序所傳回的資料行名稱必須與概念模型類型上的屬性名稱完全相符。 如果資料行名稱與屬性名稱不完全相符，則必須在 ResultMapping 元素中定義對應。

**FunctionImportMapping**元素可以有下列子項目：

-   ResultMapping (零或多個) 

### <a name="applicable-attributes"></a>適用的屬性

下表描述適用于 **FunctionImportMapping** 元素的屬性：

| 屬性名稱         | 必要 | 值                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | 是         | 概念模型中要對應的函式匯入名稱。           |
| **FunctionName**       | 是         | 儲存體模型中要對應的函式之命名空間限定名稱。 |

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

下列範例顯示用來將函式和函式匯入對應至彼此的 **FunctionImportMapping** 專案：

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>InsertFunction 項目 (MSL)

對應規格語言 (MSL 中的 **InsertFunction** 專案) 將概念模型中之實體類型或關聯的插入函式對應至基礎資料庫中的預存程式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱 (SSDL) 的 Function 元素。

> [!NOTE]
> 如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。

**InsertFunction**元素可以是 ModificationFunctionMapping 專案的子系，並套用至 EntityTypeMapping 元素或 AssociationSetMapping 元素。

### <a name="insertfunction-applied-to-entitytypemapping"></a>套用至 EntityTypeMapping 的 InsertFunction

當套用至 EntityTypeMapping 元素時， **InsertFunction** 專案會將概念模型中實體類型的插入函式對應至預存程式。

**InsertFunction**元素套用至**EntityTypeMapping**專案時，可能會有下列子項目：

-   AssociationEnd (零或多個)
-   ComplexProperty (零或多個)
-   ResultBinding (零或一個)
-   ScarlarProperty (零或多個)

#### <a name="applicable-attributes"></a>適用的屬性

下表描述套用至**EntityTypeMapping**專案時，可套用至**InsertFunction**元素的屬性。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 插入函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例是以 School 模型為基礎，並顯示用來將 Person 實體類型的插入函式對應至**InsertPerson**預存程式的**InsertFunction**元素。 **InsertPerson**預存程式會在儲存體模型中宣告。

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

當套用至 AssociationSetMapping 元素時， **InsertFunction** 專案會將概念模型中關聯的插入函式對應至預存程式。

**InsertFunction**元素在套用至**AssociationSetMapping**專案時，可能會有下列子項目：

-   EndProperty

#### <a name="applicable-attributes"></a>適用的屬性

下表描述套用至**AssociationSetMapping**專案時，可套用至**InsertFunction**元素的屬性。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 插入函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

#### <a name="example"></a>範例

下列範例是以 School 模型為基礎，並顯示用來將**CourseInstructor**關聯的插入函式對應至**InsertCourseInstructor**預存程式的**InsertFunction**元素。 **InsertCourseInstructor**預存程式會在儲存體模型中宣告。

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

對應規格語言 (MSL 中的 **對應** 元素) 包含將概念模型中定義的物件對應至資料庫 (的資訊，如儲存體模型) 中所述。 如需詳細資訊，請參閱 CSDL 規格和 SSDL 規格。

**Mapping**元素是對應規格的根項目。 對應規格的 XML 命名空間為 https://schemas.microsoft.com/ado/2009/11/mapping/cs 。

對應項目可以擁有下列子項目 (依列出的順序)：

-   Alias (零或多個)
-   EntityContainerMapping (只有一個)

MSL 中所參考之概念及儲存模型類型的名稱必須以它們各自的命名空間名稱來限定。 如需概念模型命名空間名稱的詳細資訊，請參閱 (CSDL) 的架構元素。 如需儲存模型命名空間名稱的詳細資訊，請參閱 (SSDL) 的架構元素。 MSL 中所使用之命名空間的別名可以使用 Alias 項目加以定義。

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **對應** 專案的屬性。

| 屬性名稱 | 必要 | 值                                                 |
|:---------------|:------------|:------------------------------------------------------|
| **空間**      | 是         | **C-S**。 這是固定值，無法變更。 |

### <a name="example"></a>範例

下列範例顯示以 School 模型的一部分為基礎的 **對應** 元素。 如需 School 模型的詳細資訊，請參閱快速入門 (Entity Framework) ：

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

## <a name="mappingfragment-element-msl"></a>MappingFragment 項目 (MSL)

對應規格語言 (MSL 中的 **MappingFragment** 專案) 會定義概念模型實體類型的屬性和資料庫中的資料表或視圖之間的對應。 如需概念模型實體類型和基礎資料庫資料表或檢視的詳細資訊，請參閱 EntityType 項目 (CSDL) 和 EntitySet 項目 (SSDL)。 **MappingFragment**可以是 EntityTypeMapping 元素或 EntitySetMapping 元素的子項目。

**MappingFragment**元素可以有下列子項目：

-   ComplexType (零或多個)
-   ScalarProperty (零或多個)
-   Condition (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **MappingFragment** 元素的屬性。

| 屬性名稱          | 必要 | 值                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **中**      | 是         | 要對應的資料表或檢視之名稱。                                                                                                                                                                           |
| **MakeColumnsDistinct** | 否          | **True** 或 **False** ，取決於是否只傳回相異資料列。 <br/> 如果這個屬性設定為 **True**，則 EntityContainerMapping 專案的 **GenerateUpdateViews** 屬性必須設定為 **False**。 |

### <a name="example"></a>範例

下列範例顯示 **MappingFragment** 專案做為 **EntityTypeMapping** 元素的子系。 在此範例中，概念模型中 **課程** 類型的屬性會對應至資料庫中 **課程** 資料表的資料行。

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

下列範例顯示 **MappingFragment** 專案做為 **EntitySetMapping** 元素的子系。 如同上述範例，概念模型中 **課程** 類型的屬性會對應至資料庫中 **課程** 資料表的資料行。

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

對應規格語言 (MSL 中的 **ModificationFunctionMapping** 元素) 將概念模型實體類型的插入、更新和刪除函式對應至基礎資料庫中的預存程式。 **ModificationFunctionMapping**元素也可以將概念模型中多對多關聯的插入和刪除函數對應至基礎資料庫中的預存程式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱 (SSDL) 的 Function 元素。

> [!NOTE]
> 如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。


> [!NOTE]
> 如果繼承階層架構中某個實體的修改函式已對應至預存程序，則階層架構中所有類型的修改函式都必須對應至預存程序。

**ModificationFunctionMapping**元素可以是 EntityTypeMapping 元素或 AssociationSetMapping 元素的子系。

**ModificationFunctionMapping**元素可以有下列子項目：

-   DeleteFunction (零或一個)
-   InsertFunction (零或一個)
-   UpdateFunction (零或一個)

**ModificationFunctionMapping**元素沒有任何可用的屬性。

### <a name="example"></a>範例

下列範例顯示 School 模型中的 **人員** 實體集的實體集對應。 除了 **person** 實體類型的資料行對應之外，還會顯示 **person** 型別的插入、更新和刪除函式的對應。 對應至的函式會在儲存體模型中宣告。

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

下列範例顯示 School 模型中 **CourseInstructor** 關聯集的關聯集對應。 除了 **CourseInstructor** 關聯的資料行對應之外，還會顯示 **CourseInstructor** 關聯的插入和刪除函數對應。 對應至的函式會在儲存體模型中宣告。

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

對應規格語言 (MSL 中的 **QueryView** 專案) 會定義概念模型中的實體類型或關聯和基礎資料庫中的資料表之間的唯讀對應。 對應會使用針對儲存體模型評估的 Entity SQL 查詢來定義，而您會根據概念模型中的實體或關聯來表示結果集。 因為查詢檢視是唯讀的，無法使用標準更新命令來更新查詢檢視所定義的類型。 您可以使用修改函式來更新這些類型。 如需詳細資訊，請參閱如何：將修改函式對應至預存程式。

> [!NOTE]
> 在 **QueryView** 元素中，不支援包含 **GroupBy**、群組匯總或導覽屬性 Entity SQL 運算式。

 

**QueryView**元素可以是 EntitySetMapping 元素或 AssociationSetMapping 元素的子系。 若是前者，查詢檢視會定義概念模型中之實體的唯讀對應。 若是後者，查詢檢視會定義概念模型中之關聯的唯讀對應。

> [!NOTE]
> 如果 **AssociationSetMapping** 元素適用于與參考條件約束的關聯，則會忽略 **AssociationSetMapping** 元素。 如需詳細資訊，請參閱 (CSDL) 的 ReferentialConstraint 元素。

**QueryView**元素不能有任何子項目。

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **QueryView** 元素的屬性。

| 屬性名稱 | 必要 | 值                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **TypeName**   | 否          | 要由查詢檢視對應的概念模型類型名稱。 |

### <a name="example"></a>範例

下列範例會將 **QueryView** 元素顯示為 **EntitySetMapping** 專案的子系，並在 School 模型中定義 **部門** 實體類型的查詢檢視對應。

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

因為查詢只會傳回儲存體模型中 **部門** 類型的成員子集，所以學校模型中的 **部門** 類型已根據此對應進行修改，如下所示：

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

下一個範例顯示 **QueryView** 專案做為 **AssociationSetMapping** 專案的子系，並定義 `FK_Course_Department` School 模型中關聯的唯讀對應。

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

-   定義概念模型中的實體，其中未包含儲存體模型中實體的所有屬性。 這包括沒有預設值且不支援 **null** 值的屬性。
-   將儲存體模型中的計算資料行對應至概念模型中實體類型的屬性。
-   定義分割概念模型實體所用之條件不是以實體為依據的對應。 當您使用 **Condition** 元素指定條件式對應時，提供的條件必須等於指定的值。 如需詳細資訊，請參閱 Condition 元素 (MSL) 。
-   將儲存體模型中的相同資料行對應至概念模型中的多種類型。
-   將多種類型對應至相同資料表。
-   定義概念模型中不是以關聯式結構描述之外部索引鍵為依據的關聯。
-   使用自訂商務邏輯來設定概念模型中的屬性值。 例如，您可以將資料來源中的字串值 "T" 對應到概念模型中的 **true**值（布林值）。
-   為查詢結果定義條件篩選器。
-   對概念模型資料強加的限制比儲存體模型的少。 例如，您可以將概念模型中的屬性設為 null，即使它所對應的資料行不支援 **null**值也是如此。

下列考量適用於為實體定義查詢檢視的情況：

-   查詢檢視是唯讀的。 您只能使用修改函式來更新這些實體。
-   當您依據查詢檢視定義實體類型時，也必須依據查詢檢視定義所有的相關實體。
-   當您將多對多關聯對應到儲存模型中的實體（代表關聯式結構描述中的連結資料表）時，您必須在此連結資料表的**AssociationSetMapping**元素中定義**QueryView**專案。
-   必須為類型階層架構中的所有類型定義查詢檢視。 您可以透過下列方法完成這項作業：
-   -   具有單一 **QueryView** 元素，其指定會傳回階層中所有實體類型聯集的單一 Entity SQL 查詢。
    -   具有單一 **QueryView** 元素，此專案會指定使用 CASE 運算子的單一 Entity SQL 查詢，以根據特定條件傳回階層中的特定實體類型。
    -   具有階層中特定類型的其他 **QueryView** 元素。 在此情況下，請使用**QueryView**元素的**TypeName**屬性來指定每個視圖的實體類型。
-   當查詢檢視已定義時，您不能在**EntitySetMapping**元素上指定**StorageSetName**屬性。
-   當定義查詢檢視時， **EntitySetMapping**元素也不能包含 **屬性** 對應。

## <a name="resultbinding-element-msl"></a>ResultBinding 項目 (MSL)

當實體類型修改函式對應至基礎資料庫中的預存程式時，對應規格語言 (MSL 中的 **ResultBinding** 專案) 會將預存程式所傳回的資料行值對應至概念模型中的實體屬性。 例如，當 insert 預存程式傳回 identity 資料行的值時， **ResultBinding** 專案會將傳回的值對應至概念模型中的實體類型屬性。

**ResultBinding**元素可以是 InsertFunction 元素或 UpdateFunction 元素的子系。

**ResultBinding**元素不能有任何子項目。

### <a name="applicable-attributes"></a>適用的屬性

下表描述適用于 **ResultBinding** 元素的屬性：

| 屬性名稱 | 必要 | 值                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **名稱**       | 是         | 概念模型中要對應之實體屬性的名稱。 |
| **ColumnName** | 是         | 要對應的資料行名稱。                                          |

### <a name="example"></a>範例

下列範例是以 School 模型為基礎，並顯示用來將**Person**實體類型的插入函式對應至**InsertPerson**預存程式的**InsertFunction**元素。  (**InsertPerson** 預存程式如下所示，並在儲存體模型中宣告。 ) **ResultBinding** 元素可用來將預存程式所傳回的資料行值， (**NewPersonID**) 對應至實體類型屬性 (**PersonID**) 。

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

下列 Transact-sql 描述 **InsertPerson** 預存程式：

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

對應規格語言 (MSL 中的 **ResultMapping** 專案) 會在下列情況成立時，定義概念模型中的函式匯入與基礎資料庫中的預存程式之間的對應：

-   函式匯入會傳回概念模型實體類型或複雜型別。
-   預存程序所傳回之資料行名稱未與實體類型或複雜型別上的屬性名稱完全相符。

根據預設，預存程序所傳回之資料行與實體類型或複雜型別間的對應是以資料行和屬性名稱為基礎。 如果資料行名稱與屬性名稱不完全相符，您必須使用 **ResultMapping** 元素來定義對應。 如需預設對應的範例，請參閱 FunctionImportMapping 元素 (MSL) 。

**ResultMapping**元素是 FunctionImportMapping 元素的子項目。

**ResultMapping**元素可以有下列子項目：

-   EntityTypeMapping (零或多個)
-   ComplexTypeMapping

**ResultMapping**元素沒有任何可用的屬性。

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

為了建立會傳回前一個實體類型之實例的函式匯入，必須在 **ResultMapping** 專案中定義預存程式所傳回之資料行與實體類型之間的對應：

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

對應規格語言 (MSL 中的 **ScalarProperty** 專案) 將概念模型實體類型、複雜類型或關聯的屬性對應至基礎資料庫中的資料表資料行或預存程式參數。

> [!NOTE]
> 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱 (SSDL) 的 Function 元素。

**ScalarProperty**元素可以是下列元素的子系：

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

**ScalarProperty**專案是**MappingFragment**、 **ComplexProperty**或**EndProperty**元素的子系，會將概念模型中的屬性對應至資料庫中的資料行。 **ScalarProperty**專案是**InsertFunction**、 **UpdateFunction**或**DeleteFunction**元素的子系，會將概念模型中的屬性對應至預存程式參數。

**ScalarProperty**元素不能有任何子項目。

### <a name="applicable-attributes"></a>適用的屬性

適用于 **ScalarProperty** 元素的屬性會因專案的角色而有所不同。

下表描述當使用 **ScalarProperty** 元素將概念模型屬性對應至資料庫中的資料行時，適用的屬性：

| 屬性名稱 | 必要 | 值                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **名稱**       | 是         | 要對應的概念模型屬性名稱。 |
| **ColumnName** | 是         | 要對應的資料表資料行名稱。              |

下表描述當用來將概念模型屬性對應至預存程式參數時，適用于 **ScalarProperty** 元素的屬性：

| 屬性名稱    | 必要 | 值                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**          | 是         | 要對應的概念模型屬性名稱。                                                                                 |
| **ParameterName** | 是         | 要對應的參數名稱。                                                                                                 |
| **版本**       | 否          | [**目前**] 或 [**原始**]，取決於目前的值或屬性的原始值是否應該用於平行存取檢查。 |

### <a name="example"></a>範例

下列範例顯示以兩種方式使用的 **ScalarProperty** 元素：

-   將 **person** 實體類型的屬性對應到 **person**資料表的資料行。
-   將 **Person** 實體類型的屬性對應至 **UpdatePerson** 預存程式的參數。 預存程序已宣告於儲存模型中。

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

下一個範例顯示用來將概念模型關聯的插入和刪除函數對應至資料庫中預存程式的 **ScalarProperty** 專案。 預存程序已宣告於儲存模型中。

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

對應規格語言 (MSL 中的 **UpdateFunction** 元素) 將概念模型中實體類型的更新函式對應至基礎資料庫中的預存程式。 修改函式所對應的預存程序必須在儲存體模型中宣告。 如需詳細資訊，請參閱 (SSDL) 的 Function 元素。

> [!NOTE]
>  如果您沒有將實體類型的三個插入、更新或刪除作業對應至預存程式，則未對應的作業會在執行時間執行並擲回 UpdateException 時失敗。

**UpdateFunction**元素可以是 ModificationFunctionMapping 元素的子系，並套用至 EntityTypeMapping 元素。

**UpdateFunction**元素可以有下列子項目：

-   AssociationEnd (零或多個)
-   ComplexProperty (零或多個)
-   ResultBinding (零或一個)
-   ScarlarProperty (零或多個)

### <a name="applicable-attributes"></a>適用的屬性

下表說明可套用至 **UpdateFunction** 元素的屬性。

| 屬性名稱            | 必要 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 更新函式所對應至之預存程序的命名空間限定名稱。 預存程序必須已宣告於儲存模型中。 |
| **RowsAffectedParameter** | 否          | 會傳回受影響之資料列數的輸出參數名稱。                                                                               |

### <a name="example"></a>範例

下列範例是以 School 模型為基礎，並顯示用來將**Person**實體類型的更新函式對應至**UpdatePerson**預存程式的**UpdateFunction**元素。 **UpdatePerson**預存程式會在儲存體模型中宣告。

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
