---
title: CSDL 規格-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
caps.latest.revision: 3
ms.openlocfilehash: 0ece73a19fe7ea244905bccb728ab2a104c5179f
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120378"
---
# <a name="csdl-specification"></a>CSDL 規格
概念結構定義語言 (CSDL) 是 XML架構語言，可描述組成資料驅動應用程式之概念模型的實體、關聯性和函式。 此概念模型可供 Entity Framework 或 WCF Data Services。 以 CSDL 描述的中繼資料由 Entity Framework 用來對應實體和資料來源的概念模型中定義的關聯性。 如需詳細資訊，請參閱 [SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)並[MSL 規格](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)。

CSDL 是實體資料模型的 Entity Framework 的實作。

Entity Framework 應用程式中，概念模型中繼資料載入來自.csdl 檔 （於 CSDL 中寫入） System.Data.Metadata.Edm.EdmItemCollection 的執行個體，則可存取使用中的方法System.Data.Metadata.Edm.MetadataWorkspace 類別。 Entity Framework 會使用概念模型中繼資料，以針對概念模型資料來源特有的命令以將查詢轉譯。

EF 設計工具會在設計階段，將概念模型資訊儲存在.edmx 檔案。 在建置時，EF 設計工具會使用資訊來建立由 Entity Framework 在執行階段所需的.csdl 檔案.edmx 檔案中。

CSDL 的版本可藉由 XML 命名空間來區別。

| CSDL 的版本 | XML 命名空間                                |
|:-------------|:---------------------------------------------|
| CSDL v1      | http://schemas.microsoft.com/ado/2006/04/edm |
| CSDL v2      | http://schemas.microsoft.com/ado/2008/09/edm |
| CSDL v3      | http://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Association 項目 (CSDL)

**關聯**項目會定義兩個實體類型之間的關聯性。 關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。 關聯 end 的多重性可以有值為一 (1)、 零或一個 (0..1)，或多個 (\*)。 這項資訊是在兩個子結束項目中指定。

關聯其中一端的實體類型執行個體可透過導覽屬性或外部索引鍵來存取 (若在實體類型上公開)。

在應用程式中，關聯的執行個體代表實體類型之間的特定關聯。 關聯執行個體會在邏輯上群組於關聯集中。

**關聯**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   結束 （2 個項目）
-   ReferentialConstraint （零或一個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**關聯**項目。

| 屬性名稱 | 必要 | 值                        |
|:---------------|:------------|:-----------------------------|
| **名稱**       | [是]         | 關聯的名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**關聯**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**關聯**項目，定義**CustomerOrders**時不上公開外部索引鍵的關聯**客戶**並**順序**實體類型。 **多重性**每個值**端**關聯的指示，許多**訂單**可以與相關聯**客戶**，但只有一個**客戶**可以與相關聯**順序**。 此外， **OnDelete**項目表示所有**訂單**與相關的特定**客戶**和已載入至 ObjectContext 會被刪除。如果**客戶**會被刪除。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

下列範例所示**關聯**項目，定義**CustomerOrders**時已經上公開外部索引鍵的關聯**客戶**並**順序**實體類型。 公開外部索引鍵，使用管理實體之間的關聯性**ReferentialConstraint**項目。 對應的 AssociationSetMapping 項目不需要將此關聯對應至資料來源。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a>AssociationSet 項目 (CSDL)

**AssociationSet**概念結構定義語言 (CSDL) 中的項目是相同類型的關聯執行個體的邏輯容器。 關聯集提供群組關聯執行個體的定義，執行個體才能對應至資料來源。  

**AssociationSet**元素可能具有下列子項目 （列出的順序）：

-   文件 （允許零或一個元素）
-   結束 （所需的兩個項目）
-   Annotation 項目 （允許零或多個元素）

**關聯**屬性會指定關聯集中包含的關聯型別。 指定組成關聯集的兩端的實體集只由兩個子**結束**項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**AssociationSet**項目。

| 屬性名稱  | 必要 | 值                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**        | [是]         | 實體集的名稱。 值**名稱**屬性不能為的值相同**關聯**屬性。                                 |
| **關聯** | [是]         | 關聯執行個體 (由關聯集包含) 之關聯的完整名稱。 關聯必須存在與關聯集相同的命名空間中。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**AssociationSet**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**EntityContainer**具有兩個項目**AssociationSet**項目：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a>CollectionType 項目 (CSDL)

**CollectionType**概念結構定義語言 (CSDL) 中的項目會指定函式參數或函式傳回類型是集合。 **CollectionType**項目可以是 Parameter 元素或 ReturnType （函式） 元素的子系。 可以使用其中一種指定集合的型別**型別**屬性或其中一個下列子元素：

-   **CollectionType**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> 如果指定的集合類型，但兩者不會驗證模型**型別**屬性和子元素。

 

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**CollectionType**項目。 請注意， **DefaultValue**， **MaxLength**， **FixedLength**，**精確度**，**擴展**， **Unicode**，並**定序**屬性僅適用於集合**EDMSimpleTypes**。

| 屬性名稱                                                          | 必要 | 值                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                                | 否          | 集合的型別。                                                                                                                                                                                                      |
| **可為 null**                                                            | 否          | **真**（預設值） 或**False**根據屬性是否有 null 值。 <br/> [!NOTE]                                                                                                                 |
| > 在 CSDL v1 中，複雜類型屬性必須有`Nullable="False"`。 |             |                                                                                                                                                                                                                                  |
| **DefaultValue**                                                        | 否          | 屬性的預設值。                                                                                                                                                                                               |
| **MaxLength**                                                           | 否          | 屬性值的最大長度。                                                                                                                                                                                        |
| **FixedLength**                                                         | 否          | **真**或是**False**根據屬性值是否會儲存為固定的長度的字串。                                                                                                                           |
| **整數位數**                                                           | 否          | 屬性值的有效位數。                                                                                                                                                                                             |
| **縮放**                                                               | 否          | 屬性值的小數位數。                                                                                                                                                                                                 |
| **SRID**                                                                | 否          | 系統的空間參考識別碼。 僅適用於空間類型的屬性。   如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)和[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) |
| **Unicode**                                                             | 否          | **真**或是**False**根據屬性值是否會儲存為 Unicode 字串。                                                                                                                                |
| **定序**                                                           | 否          | 指定資料來源中使用之定序順序的字串。                                                                                                                                                    |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**CollectionType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例示範模型定義函式使用**CollectionType**項目來指定函式傳回的集合**人員**實體類型 (為指定的**ElementType**屬性)。

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

下列範例示範使用的模型定義函式**CollectionType**項目來指定此函數會傳回資料列集合 (如同在中指定**RowType**項目)。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

下列範例示範使用的模型定義函式**CollectionType**項目來指定，此函式會接受做為參數的集合**部門**實體類型。

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a>ComplexType 項目 (CSDL)

A **ComplexType**項目會定義所組成的資料結構**EdmSimpleType**屬性或其他複雜型別。  複雜類型可以是實體類型的屬性或另一個複雜類型的屬性。 複雜類型與實體類型相似之處在於複雜類型會定義資料。 不過，複雜型別和實體類型之間還是有些重大的差異：

-   複雜型別不具有識別 (或索引鍵)，因此無法獨立存在。 複雜型別只能以實體類型或其他複雜型別的屬性形式存在。
-   複雜型別不能參與關聯。 既不關聯 end 的可以是複雜類型，並因此不能定義導覽屬性的複雜型別。
-   雖然複雜型別的純量屬性可能每個都設為 null，但複雜型別屬性不可以有 null 值。

A **ComplexType**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   屬性 （零或多個項目）
-   Annotation 項目 （零或多個項目）

下表描述可套用至的屬性**ComplexType**項目。

| 屬性名稱                                                                                                 | 必要 | 值                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱                                                                                                           | [是]         | 複雜類型的名稱。 複雜類型的名稱不可與其他複雜類型、實體類型或模型範圍內之關聯的名稱相同。 |
| BaseType                                                                                                       | 否          | 其他複雜類型的名稱是即將定義之複雜類型的基底型別。 <br/> [!NOTE]                                                                     |
| > 這個屬性並不適用於 CSDL v1。 該版本不支援複雜類型的繼承。 |             |                                                                                                                                                                                     |
| 抽象                                                                                                       | 否          | **True**或是**False** （預設值） 根據複雜的型別是抽象型別。 <br/> [!NOTE]                                                                  |
| > 這個屬性並不適用於 CSDL v1。 該版本的複雜類型不可以是抽象型別。         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ComplexType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例示範複雜型別**地址**，以**EdmSimpleType**屬性**StreetAddress**，**縣 （市)**， **StateOrProvince**，**國家/地區**，以及**PostalCode**。

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

若要定義的複雜型別**地址**（上方），您也必須在實體類型，屬性宣告中的實體類型定義的屬性類型。 下列範例所示**地址**複雜型別的實體類型上的屬性 (**發行者**):

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a>DefiningExpression 項目 (CSDL)

**DefiningExpression**概念結構定義語言 (CSDL) 中的項目包含概念模型中定義的函式的 Entity SQL 運算式。  

> [!NOTE]
> 進行驗證**DefiningExpression**元素可以包含任意內容。 不過，Entity Framework 將會擲回例外狀況在執行階段**DefiningExpression**元素未包含有效的 Entity SQL。

 

### <a name="applicable-attributes"></a>適用屬性

附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**DefiningExpression**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例會使用**DefiningExpression**項目來定義發行活頁簿之後，傳回的年數的函式。 內容**DefiningExpression**項目會寫入 Entity sql。

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Dependent 項目 (CSDL)

**相依**概念結構定義語言 (CSDL) 中的項目是 ReferentialConstraint 項目子元素和定義參考條件約束的相依端。 A **ReferentialConstraint**項目會定義類似於關聯式資料庫中的參考完整性條件約束的功能。 同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。 參考的實體類型稱為*主體端點*條件約束。 參考主要端點的實體類型稱為*相依端點*條件約束。 **PropertyRef**元素用來指定參考主要端點的索引鍵。

**相依**元素可能具有下列子項目 （列出的順序）：

-   PropertyRef （一或多個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**相依**項目。

| 屬性名稱 | 必要 | 值                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **角色**       | [是]         | 位於關聯之相依端點的實體類型名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**相依**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**ReferentialConstraint**做為定義的一部分的項目**PublishedBy**關聯。 **PublisherId**屬性**活頁簿**構成參考條件約束的相依端點的實體類型。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a>Documentation 項目 (CSDL)

**文件**概念結構定義語言 (CSDL) 中的項目可以用來提供父元素中定義之物件的相關資訊。 在.edmx 檔案中，當**文件**項目是顯示為物件 （例如實體、 關聯或屬性），在 EF 設計工具的設計介面上的內容項目的子系**文件**項目會出現在 Visual Studio**屬性**視窗物件。

**文件**元素可能具有下列子項目 （列出的順序）：

-   **摘要**： 父元素的簡短描述。 (零或一個項目)
-   **LongDescription**： 父項目的詳細描述。 (零或一個項目)
-   Annotation 項目。 (零或多個項目)

### <a name="applicable-attributes"></a>適用屬性

附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**文件**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**文件**為 EntityType 元素的子元素的項目。 如果是在 CSDL 中的下列程式碼片段內容的.edmx 檔案的內容**摘要**並**LongDescription**項目會出現在 Visual Studio**屬性**當您按一下視窗`Customer`實體類型。

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a>End 項目 (CSDL)

**結束**概念結構定義語言 (CSDL) 中的項目可以是子系的 Association 元素或 AssociationSet 項目。 在每個案例中，所扮演的角色**結束**項目不同，且適用的屬性會不同。

### <a name="end-element-as-a-child-of-the-association-element"></a>End 項目為 Association 項目的子項目

**結束**項目 (為子系**關聯**項目) 識別關聯的一端和可存在於該關聯 end 的實體類型執行個體數目的實體型別。 關聯 End 會定義為關聯的部分。關聯必須具有兩個關聯 End。 關聯其中一端的實體型別執行個體可透過巡覽屬性或外部索引鍵來存取 (若在實體型別上公開)。  

**結束**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   OnDelete （零或一個項目）
-   Annotation 項目 （零或多個項目）

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**結束**項目時的子系**關聯**項目。

| 屬性名稱   | 必要 | 值                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | [是]         | 其中一個關聯 End 的實體類型名稱。                                                                                                                                                                                                                                                                                                                                                         |
| **角色**         | 否          | 關聯 End 的名稱。 如果未提供任何名稱，則會使用關聯 End 上之實體類型的名稱。                                                                                                                                                                                                                                                                                           |
| **多重性** | [是]         | **1**， **0..1**，或**\*** 根據可在關聯一端的實體類型執行個體的數目。 <br/> **1**指出該只有一個實體類型執行個體存在於關聯 end。 <br/> **0..1**表示零個或一個實體類型執行個體存在於關聯 end。 <br/> **\*** 表示零個、 一個或多個實體類型執行個體存在於關聯 end。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

#### <a name="example"></a>範例

下列範例所示**關聯**項目，定義**CustomerOrders**關聯。 **多重性**每個值**端**關聯的指示，許多**訂單**可以與相關聯**客戶**，但只有一個**客戶**可以與相關聯**順序**。 此外， **OnDelete**項目表示所有**訂單**與相關的特定**客戶**和，已載入至 ObjectContext 會已刪除的 if**客戶**會被刪除。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>End 項目為 AssociationSet 項目的子項目

**結束**項目會指定一個關聯集的結尾。 **AssociationSet**項目必須包含兩個**結束**項目。 中包含的資訊**結束**項目用於資料集與資料來源對應。

**結束**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   Annotation 項目 （零或多個項目）

> [!NOTE]
> Annotation 項目必須出現在所有其他子項目之後。 Annotation 項目才允許在 CSDL v2 和更新版本。

 

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**結束**項目時的子系**AssociationSet**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | [是]         | 名稱**EntitySet**項目，定義某一端之父代**AssociationSet**項目。 **EntitySet**項目必須定義在相同的實體容器當成父系**AssociationSet**項目。 |
| **角色**       | 否          | 關聯集 End 的名稱。 如果**角色**不使用屬性、 關聯集 end 的名稱會是實體集的名稱。                                                                   |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

#### <a name="example"></a>範例

下列範例所示**EntityContainer**具有兩個項目**AssociationSet**項目，各有兩個**結束**項目：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a>EntityContainer 項目 (CSDL)

**EntityContainer**概念結構定義語言 (CSDL) 中的項目是實體集、 關聯集和函式匯入的邏輯容器。 概念模型實體容器對應到儲存體模型實體容器透過 EntityContainerMapping 項目。 儲存體模型實體容器描述資料的結構：實體集描述資料表、關聯集描述外部索引建條件約束，而函式匯入則描述資料庫中的預存程序。

**EntityContainer**項目可以有零個或一個文件項目。 如果**文件**項目已存在，它必須在所有**EntitySet**， **AssociationSet**，以及**FunctionImport**項目。

**EntityContainer**項目可以有零或多個下列子項目 （依列出的順序）：

-   EntitySet
-   AssociationSet
-   FunctionImport
-   Annotation 項目

您可以延伸**EntityContainer**項目包含的另一個內容**EntityContainer** ，位於相同的命名空間。 若要包含的另一個內容**EntityContainer**，在參考**EntityContainer**項目，來設定值**擴充**名稱屬性**EntityContainer**您想要包含的項目。 包含之所有子項目**EntityContainer**項目將會被視為子項目的參考**EntityContainer**項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**Using**項目。

| 屬性名稱 | 必要 | 值                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **名稱**       | [是]         | 實體容器的名稱。                               |
| **擴充**    | 否          | 相同命名空間中另一個實體容器的名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityContainer**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**EntityContainer**定義三個實體集和兩個關聯集的項目。

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a>EntitySet 項目 (CSDL)

**EntitySet**中概念結構定義語言項目是實體類型的執行個體和衍生自該實體類型的任何類型的執行個體的邏輯容器。 實體類型和實體集之間的關聯性，類似於關聯式資料庫中資料列與資料表的關係。 實體類型和資料列一樣可以定義相關的資料集，而實體集則和資料表一樣可以包含該定義的執行個體。 實體集提供群組實體類型執行個體的建構，以便將它們對應至資料來源中的相關資料結構。  

您可以為特定的實體類型定義多個實體集。

> [!NOTE]
> EF 設計工具不支援包含每個類型的多個實體集的概念模型。

 

**EntitySet**元素可能具有下列子項目 （列出的順序）：

-   Documentation 項目 （允許零或一個元素）
-   Annotation 項目 （允許零或多個元素）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntitySet**項目。

| 屬性名稱 | 必要 | 值                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **名稱**       | [是]         | 實體集的名稱。                                                              |
| **EntityType** | [是]         | 實體類型 (實體集包含其執行個體) 的完整名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntitySet**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**EntityContainer**具有三個項目**EntitySet**項目：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

您可以定義每個類型的多重實體集 (MEST)。 下列範例會定義的實體容器具有兩個實體集，如**活頁簿**實體類型：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a>EntityType 項目 (CSDL)

**EntityType**項目代表最上層概念，例如客戶或訂單，概念模型中的結構。 實體類型是應用程式中實體類型之執行個體的範本。 每個範本包含下列資訊：

-   唯一名稱。 (必要項。)
-   實體索引鍵是由一個或多個屬性定義。 (必要項。)
-   包含資料的屬性。 (選擇性。)
-   導覽屬性允許從關聯的一端巡覽至另一端。 (選擇性。)

在應用程式中，實體類型的執行個體代表特定的物件 (例如特定的客戶或訂單)。 實體類型的每一個執行個體都必須在實體集中有唯一的實體索引鍵。

如果兩個實體類型執行個體屬於相同類型，而且索引鍵的值也相同，則會將這兩個執行個體視為相等。

**EntityType**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   索引鍵 （零或一個項目）
-   屬性 （零或多個項目）
-   NavigationProperty （零或多個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntityType**項目。

| 屬性名稱                                                                                                                                  | 必要 | 值                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **名稱**                                                                                                                                        | [是]         | 實體類型的名稱。                                                                     |
| **BaseType**                                                                                                                                    | 否          | 其他實體類型的名稱是即將定義之實體類型的基底類型。  |
| **抽象**                                                                                                                                    | 否          | **True**或是**False**，取決於實體類型是否為抽象型別。                 |
| **OpenType**                                                                                                                                    | 否          | **真**或是**False**根據實體類型是否為開放實體類型。 <br/> [!NOTE] |
| > **OpenType**屬性僅適用於使用 ADO.NET Data Services 使用的概念模型中所定義的實體類型。 |             |                                                                                                  |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**EntityType**具有三個項目**屬性**項目和兩個**NavigationProperty**項目：

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a>EnumType 項目 (CSDL)

**EnumType**項目代表列舉型別。

**EnumType**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   成員 （零或多個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EnumType**項目。

| 屬性名稱     | 必要 | 值                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**           | [是]         | 實體類型的名稱。                                                                                                                                                                  |
| **IsFlags**        | 否          | **True**或是**False**，取決於是否列舉型別可用來當做一組旗標。 預設值是 **，則為 False。**                                                                     |
| **UnderlyingType** | 否          | **Edm.Byte**， **Edm.Int16**， **Edm.Int32**， **Edm.Int64**或是**Edm.SByte**定義類型的值的範圍。   預設基礎列舉項目類型是**Edm.Int32**。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EnumType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**EnumType**具有三個項目**成員**項目：

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Function 屬性 (CSDL)

**函式**概念結構定義語言 (CSDL) 中的項目用以定義或宣告概念模型中的函式。 函式是使用 DefiningExpression 項目所定義。  

A**函式**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   參數 （零或多個項目）
-   DefiningExpression （零或一個項目）
-   ReturnType （函式） （零或一個項目）
-   Annotation 項目 （零或多個項目）

傳回類型函式必須指定使用**ReturnType** (Function) 項目或有**ReturnType**屬性 （如下所示），但非兩者。 可能的傳回型別包括任何 EdmSimpleType、實體類型、複雜類型、資料列型別或 ref 型別 (或這些類型其中之一的集合)。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**函式**項目。

| 屬性名稱 | 必要 | 值                              |
|:---------------|:------------|:-----------------------------------|
| **名稱**       | [是]         | 函式的名稱。          |
| **ReturnType** | 否          | 此函式傳回的型別。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**函式**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例會使用**函式**項目來定義傳回講師受雇之後的年數的函式。

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>FunctionImport 項目 (CSDL)

**FunctionImport**概念結構定義語言 (CSDL) 中的項目代表已定義資料來源中，但可透過概念模型物件的函式。 例如，儲存體模型中的函式項目可用來代表在資料庫中的預存程序。 A **FunctionImport**概念模型中的項目代表 Entity Framework 應用程式中對應的函式，並使用 FunctionImportMapping 項目對應至儲存體模型函式。 在應用程式中呼叫函式時，對應的預存程序會在資料庫中執行。

**FunctionImport**元素可能具有下列子項目 （列出的順序）：

-   文件 （允許零或一個元素）
-   參數 （允許零或多個元素）
-   Annotation 項目 （允許零或多個元素）
-   ReturnType (FunctionImport) （允許零或多個元素）

一**參數**項目應定義為每個函數可接受的參數。

傳回使用，就必須指定函式的類型**ReturnType** (FunctionImport) 項目或有**ReturnType**屬性 （如下所示），但非兩者。 傳回型別值必須是 EdmSimpleType、 EntityType 或 ComplexType 的集合。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**FunctionImport**項目。

| 屬性名稱   | 必要 | 值                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**         | [是]         | 匯入函式的名稱。                                                                                                                                                                    |
| **ReturnType**   | 否          | 函式傳回的型別。 如果函式不會傳回值，請不要使用這個屬性。 否則，值必須是 ComplexType、 EntityType 或 EDMSimpleType 的集合。        |
| **EntitySet**    | 否          | 如果函數傳回實體集合類型的值**EntitySet**必須實體集所屬的集合。 否則，請**EntitySet**屬性不能使用。 |
| **IsComposable** | 否          | 如果值設為 true，函式是可組合 （資料表值函式） 且可以用於 LINQ 查詢中。  預設值為 **false**。                                                           |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**FunctionImport**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**FunctionImport**接受一個參數，並傳回的實體類型集合的項目：

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>Key 項目 (CSDL)

**金鑰**項目是 EntityType 元素的子項目，並定義*實體索引鍵*（的屬性或一組實體類型的屬性，可判斷識別）。 構成實體索引鍵的屬性是在設計階段選取的。 實體索引鍵屬性的值必須在執行階段的實體集中，單獨識別實體類型執行個體。 您應選取構成實體索引鍵的屬性，以保證執行個體在實體集中的唯一性。 **金鑰**項目會定義實體索引鍵參考一或多個實體類型的屬性。

**金鑰**元素可能具有下列子元素：

-   PropertyRef （一或多個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**金鑰**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

以下範例定義名為實體型別**活頁簿**。 藉由參考定義的實體索引鍵**ISBN**之實體類型的屬性。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

**ISBN**屬性是實體索引鍵的理想選擇，因為國際標準書號 (ISBN) 可唯一識別一本書。

下列範例顯示的實體類型 (**作者**)，有包含兩個屬性，實體索引鍵**名稱**並**位址**。

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

使用**名稱**並**地址**實體索引鍵是合理的選擇，因為同名的兩位作者不太可能住在相同的位址。 不過，針對實體索引鍵所做的這個選擇不能絕對保證實體集中的唯一實體索引鍵。 新增屬性，例如**AuthorId**，無法用來唯一識別作者就會在此情況下建議使用。

 

## <a name="member-element-csdl"></a>Member 元素 (CSDL)

**成員**項目是 EnumType 元素的子項目，並定義列舉類型的成員。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**FunctionImport**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**       | [是]         | 成員的名稱。                                                                                                                                                                  |
| **值**      | 否          | 成員的值。 根據預設，第一個成員具有值為 0，而且每個連續的列舉值的值會遞增 1。 可能存在多個成員具有相同的值。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**FunctionImport**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**EnumType**具有三個項目**成員**項目：

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>NavigationProperty 項目 (CSDL)

A **NavigationProperty**項目定義導覽屬性，提供關聯另一端的參考。 不像與屬性的項目定義的屬性，瀏覽屬性不會定義圖案和特性的資料。 他們提供巡覽兩個實體類型間之關聯的方式。

請注意，在關聯各端點的實體類型上，導覽屬性是選擇性的。 如果您在關聯其中一個端點的實體類型上定義導覽屬性，就不必在關聯另一個端點的實體類型上定義導覽屬性。

導覽屬性傳回的資料類型是由其遠端關聯端點的多重性所判斷。 例如，假設一個導覽屬性**OrdersNavProp**，存在於**客戶**實體類型並瀏覽一對多關聯**客戶**和**順序**。 因為導覽屬性的遠端關聯 end 具有多重性許多 (\*)，其資料型別是集合 (的**順序**)。 同樣地，如果導覽屬性， **CustomerNavProp**，存在於**順序**實體型別，其資料類型會是**客戶**因為遠端 end 的多重性是一 (1)。

A **NavigationProperty**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**NavigationProperty**項目。

| 屬性名稱   | 必要 | 值                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**         | [是]         | 導覽屬性的名稱。                                                                                                                                                                                                             |
| **關聯性** | [是]         | 關聯的名稱在模型的範圍之內。                                                                                                                                                                                |
| **ToRole**       | [是]         | 位於導覽端點的關聯端點。 值**ToRole**屬性必須是其中一個的值相同**角色**某個關聯端點 （定義於 AssociationEnd 項目） 所定義的屬性。       |
| **FromRole**     | [是]         | 開始導覽的關聯端點。 值**FromRole**屬性必須是其中一個的值相同**角色**某個關聯端點 （定義於 AssociationEnd 項目） 所定義的屬性。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**NavigationProperty**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例會定義實體類型 (**活頁簿**) 具有兩個導覽屬性 (**PublishedBy**並**WrittenBy**):

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a>OnDelete 項目 (CSDL)

**OnDelete**概念結構定義語言 (CSDL) 中的項目會定義連接關聯的行為。 如果**動作**屬性設為**Cascade**上關聯的一端，相關的刪除第一個端點上的實體類型時，會刪除關聯另一端的實體類型。 如果兩個實體類型之間的關聯是主索引鍵對主索引鍵關聯性，則不論刪除關聯另一端的主體物件時，會刪除載入的相依物件**OnDelete**規格。  

> [!NOTE]
> **OnDelete**項目只會影響應用程式的執行階段行為，不會影響資料來源中的行為。 資料來源中定義的行為應與應用程式中定義的行為相同。

 

**OnDelete**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**OnDelete**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **動作**     | [是]         | **Cascade**或是**None**。 如果**Cascade**，刪除主要實體類型時，將會刪除相依實體類型。 如果**無**，刪除主要實體類型時，將不會刪除相依實體類型。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**關聯**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**關聯**項目，定義**CustomerOrders**關聯。 **OnDelete**項目表示所有**訂單**與相關的特定**客戶**和已載入至 ObjectContext 會被刪除時**客戶**會被刪除。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Parameter 項目 (CSDL)

**參數**概念結構定義語言 (CSDL) 中的項目可以在 FunctionImport 項目或 Function 項目子系。

### <a name="functionimport-element-application"></a>FunctionImport 項目的應用

A**參數**項目 (為子系**FunctionImport**項目) 用來在 CSDL 中定義宣告的函式匯入的輸入和輸出參數。

**參數**元素可能具有下列子項目 （列出的順序）：

-   文件 （允許零或一個元素）
-   Annotation 項目 （允許零或多個元素）

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**參數**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**       | [是]         | 參數名稱。                                                                                                                                                                                                      |
| **Type**       | [是]         | 參數型別。 此值必須是**EDMSimpleType**或是模型範圍內的複雜類型。                                                                                                             |
| **模式**       | 否          | **在 **， **Out**，或**InOut**取決於參數是輸入、 輸出或輸入/輸出參數。                                                                                                                |
| **MaxLength**  | 否          | 可允許的最大參數長度。                                                                                                                                                                                    |
| **整數位數**  | 否          | 參數的精確度。                                                                                                                                                                                                 |
| **縮放**      | 否          | 參數的小數位數。                                                                                                                                                                                                     |
| **SRID**       | 否          | 系統的空間參考識別碼。 僅適用於空間類型的參數。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**參數**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

#### <a name="example"></a>範例

下列範例所示**FunctionImport**項目包含一個**參數**子項目。 函式接受一個輸入參數，然後傳回實體類型的集合。

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Function 項目的應用

A**參數**項目 (為子系**函式**項目) 概念模型中定義之函式的定義或宣告的參數。

**參數**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   CollectionType （零或一個項目）
-   ReferenceType （零或一個項目）
-   資料列型別 （零或一個項目）

> [!NOTE]
> 只有其中一個**CollectionType**， **ReferenceType**，或**RowType**項目可以是子項目**屬性**項目。

 

-   Annotation 項目 （允許零或多個元素）

> [!NOTE]
> Annotation 項目必須出現在所有其他子項目之後。 Annotation 項目才允許在 CSDL v2 和更新版本。

 

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**參數**項目。

| 屬性名稱   | 必要 | 值                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**         | [是]         | 參數名稱。                                                                                                                                                                                                      |
| **Type**         | 否          | 參數型別。 參數可以是下列任何一種型別 (或這些型別的集合)： <br/> **EdmSimpleType** <br/> Entity Type - 實體類型 <br/> 複雜類型 <br/> 資料列型別 <br/> 參考類型                             |
| **可為 null**     | 否          | **真**（預設值） 或**False**根據屬性是否可以有**null**值。                                                                                                                          |
| **DefaultValue** | 否          | 屬性的預設值。                                                                                                                                                                                              |
| **MaxLength**    | 否          | 屬性值的最大長度。                                                                                                                                                                                       |
| **FixedLength**  | 否          | **真**或是**False**根據屬性值是否會儲存為固定的長度的字串。                                                                                                                          |
| **整數位數**    | 否          | 屬性值的有效位數。                                                                                                                                                                                            |
| **縮放**        | 否          | 屬性值的小數位數。                                                                                                                                                                                                |
| **SRID**         | 否          | 系統的空間參考識別碼。 僅適用於空間類型的屬性。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**      | 否          | **真**或是**False**根據屬性值是否會儲存為 Unicode 字串。                                                                                                                               |
| **定序**    | 否          | 指定資料來源中使用之定序順序的字串。                                                                                                                                                   |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**參數**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

#### <a name="example"></a>範例

下列範例所示**函式**項目，會使用其中一個**參數**子項目定義的函式參數。

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
``` 

 

## <a name="principal-element-csdl"></a>Principal 項目 (CSDL)

**主體**概念結構定義語言 (CSDL) 中的項目是 ReferentialConstraint 項目，定義參考條件約束的主體端點的子項目。 A **ReferentialConstraint**項目會定義類似於關聯式資料庫中的參考完整性條件約束的功能。 同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。 參考的實體類型稱為*主體端點*條件約束。 參考主要端點的實體類型稱為*相依端點*條件約束。 **PropertyRef**元素用來指定索引鍵所參考的相依端點。

**主體**元素可能具有下列子項目 （列出的順序）：

-   PropertyRef （一或多個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**主體**項目。

| 屬性名稱 | 必要 | 值                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **角色**       | [是]         | 位於關聯之主要端點的實體類型名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**主體**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**ReferentialConstraint**項目所定義的一部分**PublishedBy**關聯。 **識別碼**屬性**發行者**構成參考條件約束的主體端點的實體類型。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a>Property 項目 (CSDL)

**屬性**概念結構定義語言 (CSDL) 中的項目可以是 EntityType 項目、 ComplexType 項目或 RowType 項目子系。

### <a name="entitytype-and-complextype-element-applications"></a>EntityType 和 ComplexType 項目的應用程式

**屬性**項目 (為的子系**EntityType**或是**ComplexType**項目) 定義的圖形和特性的實體類型執行個體或複雜類型執行個體將包含的資料. 概念模型中的屬性類似類別中定義的屬性。 如同類別上的屬性可定義類別的圖形並包含關於物件的資訊，概念模型的屬性可定義實體類別的圖形，並包含關於實體類型執行個體的資訊。

**屬性**元素可能具有下列子項目 （列出的順序）：

-   Documentation 項目 （允許零或一個元素）
-   Annotation 項目 （允許零或多個元素）

下列 facet 可套用至**屬性**項目： **Nullable**， **DefaultValue**， **MaxLength**， **FixedLength**，**精確度**，**擴展**， **Unicode**，**定序**， **ConcurrencyMode**。 Facet 是 XML 屬性 (attribute)，提供關於屬性 (property) 值如何儲存在資料存放區資訊。

> [!NOTE]
> Facet 只能套用至屬性的型別**EDMSimpleType**。

 

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**屬性**項目。

| 屬性名稱                                                         | 必要 | 值                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                                                               | [是]         | 屬性的名稱。                                                                                                                                                                                                       |
| **Type**                                                               | [是]         | 屬性值的型別。 屬性值類型必須是**EDMSimpleType**或複雜類型 （以完整的名稱） 之模型的範圍內。                                                 |
| **可為 null**                                                           | 否          | **真**（預設值） 或<strong>False</strong>根據屬性是否有 null 值。 <br/> [!NOTE]                                                                                                   |
| > 在 CSDL v1 的複雜類型屬性必須有`Nullable="False"`。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                       | 否          | 屬性的預設值。                                                                                                                                                                                              |
| **MaxLength**                                                          | 否          | 屬性值的最大長度。                                                                                                                                                                                       |
| **FixedLength**                                                        | 否          | **真**或是**False**根據屬性值是否會儲存為固定的長度的字串。                                                                                                                          |
| **整數位數**                                                          | 否          | 屬性值的有效位數。                                                                                                                                                                                            |
| **縮放**                                                              | 否          | 屬性值的小數位數。                                                                                                                                                                                                |
| **SRID**                                                               | 否          | 系統的空間參考識別碼。 僅適用於空間類型的屬性。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**                                                            | 否          | **真**或是**False**根據屬性值是否會儲存為 Unicode 字串。                                                                                                                               |
| **定序**                                                          | 否          | 指定資料來源中使用之定序順序的字串。                                                                                                                                                   |
| **ConcurrencyMode**                                                    | 否          | **無**（預設值） 或**Fixed**。 如果值設定為**Fixed**，屬性值將用於開放式並行存取檢查。                                                                                  |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**屬性**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

#### <a name="example"></a>範例

下列範例所示**EntityType**具有三個項目**屬性**項目：

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

下列範例所示**ComplexType**具有五個項目**屬性**項目：

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a>RowType 項目的應用程式

**屬性**項目 (為的子系**RowType**項目) 定義的圖案和特性，可以傳遞至或從模型定義函式傳回的資料。  

**屬性**項目可以有一個下列子元素：

-   CollectionType
-   ReferenceType
-   RowType

**屬性**項目可以有任何數目的子系 annotation 項目。

> [!NOTE]
> Annotation 項目才允許在 CSDL v2 和更新版本。

 

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**屬性**項目。

| 屬性名稱                                                     | 必要 | 值                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                                                           | [是]         | 屬性的名稱。                                                                                                                                                                                                       |
| **Type**                                                           | [是]         | 屬性值的型別。                                                                                                                                                                                                 |
| **可為 null**                                                       | 否          | **真**（預設值） 或**False**根據屬性是否有 null 值。 <br/> [!NOTE]                                                                                                                |
| > 在 CSDL v1 的複雜類型屬性必須有`Nullable="False"`。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | 否          | 屬性的預設值。                                                                                                                                                                                              |
| **MaxLength**                                                      | 否          | 屬性值的最大長度。                                                                                                                                                                                       |
| **FixedLength**                                                    | 否          | **真**或是**False**根據屬性值是否會儲存為固定的長度的字串。                                                                                                                          |
| **整數位數**                                                      | 否          | 屬性值的有效位數。                                                                                                                                                                                            |
| **縮放**                                                          | 否          | 屬性值的小數位數。                                                                                                                                                                                                |
| **SRID**                                                           | 否          | 系統的空間參考識別碼。 僅適用於空間類型的屬性。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**                                                        | 否          | **真**或是**False**根據屬性值是否會儲存為 Unicode 字串。                                                                                                                               |
| **定序**                                                      | 否          | 指定資料來源中使用之定序順序的字串。                                                                                                                                                   |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**屬性**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

#### <a name="example"></a>範例

下列範例所示**屬性**用來定義模型定義函式的傳回類型的圖形項目。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a>PropertyRef 項目 (CSDL)

**PropertyRef**概念結構定義語言 (CSDL) 中的項目會參考的實體類型表示的屬性將執行下列角色的其中一個屬性：

-   實體索引鍵的一部分 (可判斷識別之實體類型的屬性或屬性集)。 一或多個**PropertyRef**項目可以用來定義實體索引鍵。
-   參考條件約束的相依端點和主要端點。

**PropertyRef**元素只能有 annotation 項目 （零或多個） 做為子系項目。

> [!NOTE]
> Annotation 項目才允許在 CSDL v2 和更新版本。

 

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**PropertyRef**項目。

| 屬性名稱 | 必要 | 值                                |
|:---------------|:------------|:-------------------------------------|
| **名稱**       | [是]         | 參考屬性的名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**PropertyRef**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

以下範例定義實體類型 (**活頁簿**)。 藉由參考定義的實體索引鍵**ISBN**之實體類型的屬性。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

在下一個範例中，兩個**PropertyRef**項目用來表示兩個屬性 (**識別碼**並**PublisherId**) 是主體端和相依端的參考條件約束。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a>ReferenceType 項目 (CSDL)

**ReferenceType**概念結構定義語言 (CSDL) 中的項目會指定實體類型的參考。 **ReferenceType**項目可以是下列項目子系：

-   ReturnType （函式）
-   參數
-   CollectionType

**ReferenceType**定義函式的參數或傳回類型時，會使用項目。

A **ReferenceType**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**ReferenceType**項目。

| 屬性名稱 | 必要 | 值                                         |
|:---------------|:------------|:----------------------------------------------|
| **Type**       | [是]         | 參考之實體類型的名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReferenceType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**ReferenceType**當做子系的項目**參數**接受參考的模型定義函式中的項目**人員**實體類型：

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

下列範例所示**ReferenceType**當做子系的項目**ReturnType** （函數） 傳回的參考的模型定義函式中的項目**人員**實體類型：

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a>ReferentialConstraint 項目 (CSDL)

A **ReferentialConstraint**概念結構定義語言 (CSDL) 中的項目會定義類似於關聯式資料庫中的參考完整性條件約束的功能。 同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。 參考的實體類型稱為*主體端點*條件約束。 參考主要端點的實體類型稱為*相依端點*條件約束。

如果一個實體類型公開外部索引鍵參考另一個的實體類型上的屬性**ReferentialConstraint**項目會定義兩個實體類型之間的關聯。 因為**ReferentialConstraint**項目會提供如何在兩個實體類型的相關資訊與相關，沒有對應的 AssociationSetMapping 項目是對應規格語言 (MSL) 中的必要。 沒有公開外部索引鍵的兩個實體類型之間的關聯必須有對應**AssociationSetMapping**項目，以便將關聯資訊對應至資料來源。

如果實體型別上沒有公開外部索引鍵**ReferentialConstraint**項目只能定義實體類型與另一個實體類型之間的主索引鍵對主索引鍵限制。

A **ReferentialConstraint**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   主體 （只有一個項目）
-   相依 （只有一個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

**ReferentialConstraint**項目可以有任意數目的附註屬性 （自訂 XML 屬性）。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**ReferentialConstraint**做為定義的一部分的項目**PublishedBy**關聯。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a>ReturnType （函式） 項目 (CSDL)

**ReturnType** （函式） 在概念結構定義語言 (CSDL) 中的項目會指定函式項目中定義的函式的傳回型別。 傳回型別也可以使用指定的函式**ReturnType**屬性。

傳回類型可以是任何**EdmSimpleType**，實體類型、 複雜型別、 資料列型別、 ref 型別或其中一種類型的集合。

可以使用其中一個指定的函式的傳回型別**型別**屬性**ReturnType** (Function) 的項目，或使用其中一個下列子元素：

-   CollectionType
-   ReferenceType
-   RowType

> [!NOTE]
> 如果您指定函式的傳回型別都不會驗證模型**型別**屬性**ReturnType** (Function) 項目，以及其中一個子項目。

 

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**ReturnType** (Function) 項目。

| 屬性名稱 | 必要 | 值                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | 否          | 此函式傳回的型別。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReturnType** (Function) 項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例會使用**函式**項目來定義傳回一本書已在列印中的年數的函式。 請注意，所指定的傳回型別**型別**屬性**ReturnType** (Function) 項目。

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a>ReturnType (FunctionImport) 項目 (CSDL)

**ReturnType** (FunctionImport) 在概念結構定義語言 (CSDL) 中的項目指定的 FunctionImport 項目中定義的函式的傳回型別。 傳回型別也可以使用指定的函式**ReturnType**屬性。

傳回型別可以是實體類型、 複雜類型的任何集合或**EdmSimpleType**，

使用指定的函式的傳回型別**型別**屬性**ReturnType** (FunctionImport) 項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**ReturnType** (FunctionImport) 項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**       | 否          | 函式傳回的型別。 值必須是 ComplexType、 EntityType 或 EDMSimpleType 的集合。                                                                                      |
| **EntitySet**  | 否          | 如果函數傳回實體集合類型的值**EntitySet**必須實體集所屬的集合。 否則，請**EntitySet**屬性不能使用。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReturnType** (FunctionImport) 項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例會使用**FunctionImport**傳回書籍和發行者。 請注意，此函數會傳回兩個結果集，因此兩個**ReturnType** (FunctionImport) 項目指定。

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>RowType 項目 (CSDL)

A **RowType**概念結構定義語言 (CSDL) 中的項目做為參數或傳回型別定義概念模型中的函式定義未命名的結構。

A **RowType**項目可以是下列項目子系：

-   CollectionType
-   參數
-   ReturnType （函式）

A **RowType**元素可能具有下列子項目 （列出的順序）：

-   屬性 （一或多個）
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**RowType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例示範使用的模型定義函式**CollectionType**項目來指定此函數會傳回資料列集合 (如同在中指定**RowType**項目)。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a>Schema 項目 (CSDL)

**結構描述**項目是概念模型定義的根項目。 其中包含組成概念模型的物件、函式和容器等定義。

**結構描述**項目可能包含零或多個下列子元素：

-   Using
-   EntityContainer
-   EntityType
-   EnumType
-   關聯
-   ComplexType
-   功能

A**結構描述**項目可以包含零個或一個註釋項目。

> [!NOTE]
> **函式**項目和註釋項目才允許在 CSDL v2 和更新版本。

 

**結構描述**項目會使用**命名空間**屬性來定義概念模型中的實體類型、 複雜型別和關聯物件的命名空間。 在命名空間中，兩個物件不能有相同的名稱。 命名空間可以跨多個**結構描述**項目和多個.csdl 檔案。

概念模型命名空間是 XML 命名空間的不同**結構描述**項目。 概念模型命名空間 (如所定義**命名空間**屬性) 是實體類型、 複雜型別和關聯型別的邏輯容器。 XML 命名空間 (由**xmlns**屬性) 的**結構描述**項目是子元素和屬性的預設命名空間**結構描述**項目。 XML 命名空間的表單 http://schemas.microsoft.com/ado/YYYY/MM/edm（其中 YYYY 和 MM 表示年和月分別） 是保留供 CSDL。 自訂項目和屬性不能出現在擁有此格式的命名空間中。

### <a name="applicable-attributes"></a>適用屬性

下表描述的屬性可以套用至**結構描述**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **命名空間**  | [是]         | 概念模型的命名空間。 值**命名空間**屬性用來構成型別的完整的名稱。 例如，如果**EntityType**名為*客戶*在 Simple.Example.Model 命名空間，則完整限定的名稱是**EntityType**是SimpleExampleModel.Customer。 <br/> 下列字串不能做為值**命名空間**屬性：**系統**，**暫時性**，或**Edm**。 值**命名空間**屬性不能做為值相同**命名空間**SSDL 結構描述項目中的屬性。 |
| **Alias**      | 否          | 用來取代命名空間名稱的識別項。 例如，如果**EntityType**名為*客戶*在 Simple.Example.Model 命名空間和值**別名**屬性是*模型*，然後您可以使用 Model.Customer 做為完整格式名稱**EntityType。**                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結構描述**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例所示**結構描述**包含的項目**EntityContainer**元素中，兩個**EntityType**項目和一個**關聯**項目。

``` xml
 <Schema xmlns="http://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="http://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="http://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a>TypeRef 項目 (CSDL)

**TypeRef**概念結構定義語言 (CSDL) 中的項目提供現有具名型別的參考。 **TypeRef**項目可以是 CollectionType 項目，這用來指定函式已為參數或傳回型別集合的子系。

A **TypeRef**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   Annotation 項目 （零或多個項目）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**TypeRef**項目。 請注意， **DefaultValue**， **MaxLength**， **FixedLength**，**精確度**，**擴展**， **Unicode**，並**定序**屬性僅適用於**EDMSimpleTypes**。

| 屬性名稱                                                     | 必要 | 值                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                           | 否          | 所參考的型別名稱。                                                                                                                                                                                          |
| **可為 null**                                                       | 否          | **真**（預設值） 或**False**根據屬性是否有 null 值。 <br/> [!NOTE]                                                                                                                |
| > 在 CSDL v1 的複雜類型屬性必須有`Nullable="False"`。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | 否          | 屬性的預設值。                                                                                                                                                                                              |
| **MaxLength**                                                      | 否          | 屬性值的最大長度。                                                                                                                                                                                       |
| **FixedLength**                                                    | 否          | **真**或是**False**根據屬性值是否會儲存為固定的長度的字串。                                                                                                                          |
| **整數位數**                                                      | 否          | 屬性值的有效位數。                                                                                                                                                                                            |
| **縮放**                                                          | 否          | 屬性值的小數位數。                                                                                                                                                                                                |
| **SRID**                                                           | 否          | 系統的空間參考識別碼。 僅適用於空間類型的屬性。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**                                                        | 否          | **真**或是**False**根據屬性值是否會儲存為 Unicode 字串。                                                                                                                               |
| **定序**                                                      | 否          | 指定資料來源中使用之定序順序的字串。                                                                                                                                                   |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**CollectionType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例示範使用的模型定義函式**TypeRef**項目 (為子系**CollectionType**項目) 來指定函數可接受的集合**部門**實體類型。

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a>Using 項目 (CSDL)

**Using**概念結構定義語言 (CSDL) 中的項目會匯入存在於不同的命名空間的概念模型的內容。 藉由設定的值**命名空間**屬性，您可以參考實體類型、 複雜型別和關聯型別。 另一個概念模型中定義。 多個**Using**項目可以是子系**結構描述**項目。

> [!NOTE]
> **Using** CSDL 中的項目無法運作完全相同**使用**程式設計語言中的陳述式。 藉由匯入的命名空間**使用**陳述式在程式設計語言中，您不會影響原始命名空間中的物件。 在 CSDL 中，匯入的命名空間可以包含實體類型，該實體類型是衍生自原始命名空間中的實體類型。 這會影響在原始命名空間中宣告的實體集。

 

**Using**元素可能具有下列子元素：

-   文件 （允許零或一個元素）
-   Annotation 項目 （允許零或多個元素）

### <a name="applicable-attributes"></a>適用屬性

下表描述的屬性可以套用至**Using**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **命名空間**  | [是]         | 匯入的命名空間名稱。                                                                                                                                                |
| **Alias**      | [是]         | 用來取代命名空間名稱的識別項。 雖然這個屬性是必要的，但是不必使用此屬性取代命名空間名稱，來限定物件名稱。 |

 

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**Using**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

 

### <a name="example"></a>範例

下列範例示範**Using**用來匯入的命名空間的項目定義其他位置。 請注意，命名空間**結構描述**所示的項目是`BooksModel`。 `Address`上的屬性`Publisher` **EntityType**是複雜類型中定義`ExtendedBooksModel`命名空間 (使用匯入**Using**項目)。

``` xml
 <Schema xmlns="http://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="http://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="http://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a>註釋屬性 (CSDL)

概念結構描述定義語言 (CSDL) 中的附註屬性是概念模型中自訂的 XML 屬性。 除了擁有有效的 XML 結構外，下列附註屬性的條件必須成立：

-   附註屬性不能在任何 XML 命名空間之中，這是保留供 CSDL 之用。
-   超過一個以上的附註屬性可以套用至給定的 CSDL 項目。
-   任兩個 Annotation 屬性的完整名稱不能相同。

附註屬性可用來提供與概念模型中之項目有關的額外中繼資料。 可以使用 System.Data.Metadata.Edm 命名空間中的類別，在執行階段存取 annotation 項目中包含的中繼資料。

### <a name="example"></a>範例

下列範例所示**EntityType**具有註釋屬性項目 (**atribut customattribute s**)。 下列範例也顯示套用至實體類型項目的 Annotation 項目。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="http://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="http://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

下列程式碼會擷取附註屬性中的中繼資料，並且將它撰寫至主控台：

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

以上程式碼假設 `School.csdl` 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a>註釋項目 (CSDL)

概念結構定義語言 (CSDL) 中的 Annotation 項目是概念模型中的自訂 XML 項目。 除了擁有有效的 XML 結構外，下列 Annotation 項目的條件也必須成立：

-   Annotation 項目不能存在於保留供 CSDL 使用的任何 XML 命名空間中。
-   多個 Annotation 項目可以同時為指定 CSDL 項目的子系。
-   任兩個 Annotation 項目的完整名稱不能相同。
-   Annotation 項目必須出現在指定 CSDL 項目的所有其他子項目之後。

Annotation 項目可用來提供與概念模型中之項目有關的額外中繼資料。 從.NET Framework 第 4 版開始，中繼資料包含在 annotation 項目可以存取在執行階段使用 System.Data.Metadata.Edm 命名空間中類別。

### <a name="example"></a>範例

下列範例所示**EntityType** annotation 項目具有項目 (**CustomElement**)。 該範例也顯示套用至實體類型項目的 annotation 屬性。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="http://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="http://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

下列程式碼會擷取 annotation 項目中的中繼資料，並且將它撰寫至主控台：

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

以上程式碼假設 School.csdl 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a>概念模型類型 (CSDL)

概念結構定義語言 (CSDL) 支援一組的抽象基本資料型別，稱為**EDMSimpleTypes**，可定義概念模型中的屬性。 **EDMSimpleTypes**是儲存體或裝載環境中所支援的基本資料型別的 proxy。

下表列出 CSDL 所支援的基本資料型別。 這個表格也列出可以套用至每個 facet **EDMSimpleType**。

| EDMSimpleType                    | 描述                                                | 適用的 Facet                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm.Binary**                   | 包含二進位資料。                                      | MaxLength、FixedLength、Nullable、Default                                |
| **Edm.Boolean**                  | 包含值**真**或是**false**。                  | Nullable、Default                                                        |
| **Edm.Byte**                     | 包含不帶正負號的 8 位元整數值。                  | Precision、Nullable、Default                                             |
| **以 Edm.DateTime**                 | 表示日期和時間。                                | Precision、Nullable、Default                                             |
| **Edm.DateTimeOffset**           | 包含與 GMT 的日期和時間時差 (以分鐘為單位)。 | Precision、Nullable、Default                                             |
| **Edm.Decimal**                  | 包含固定有效位數和小數位數的數值。   | Precision、Nullable、Default                                             |
| **Edm.Double**                   | 包含浮點數具有 15 位數精確度   | Precision、Nullable、Default                                             |
| **Edm.Float**                    | 包含具有 7 位數精確度的浮點數。   | Precision、Nullable、Default                                             |
| **Edm.Guid**                     | 包含 16 位元組的唯一識別碼。                      | Precision、Nullable、Default                                             |
| **Edm.Int16**                    | 包含帶正負號的 16 位元整數值。                    | Precision、Nullable、Default                                             |
| **Edm.Int32**                    | 包含帶正負號的 32 位元整數值。                    | Precision、Nullable、Default                                             |
| **Edm.Int64**                    | 包含帶正負號的 64 位元整數值。                    | Precision、Nullable、Default                                             |
| **Edm.SByte**                    | 包含帶正負號的 8 位元整數值。                     | Precision、Nullable、Default                                             |
| **Edm.String**                   | 包含字元資料。                                   | Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default |
| **Edm.Time**                     | 包含一天的時間。                                    | Precision、Nullable、Default                                             |
| **Edm.Geography**                |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeographyLineString**      |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeographyPolygon**         |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeographyMultiPoint**      |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeographyMultiLineString** |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeographyMultiPolygon**    |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeographyCollection**      |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.Geometry**                 |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeometryPoint**            |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeometryLineString**       |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeometryPolygon**          |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeometryMultiPoint**       |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeometryMultiLineString**  |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeometryMultiPolygon**     |                                                            | 可為 null，預設的 SRID                                                  |
| **Edm.GeometryCollection**       |                                                            | 可為 null，預設的 SRID                                                  |

## <a name="facets-csdl"></a>Facet (CSDL)

概念結構定義語言 (CSDL) 中的 Facet 表示實體型別和複雜型別屬性上的條件約束。 Facet 在下列 CSDL 元素上會以 XML 屬性的形式出現：

-   屬性
-   TypeRef
-   參數

下表說明 CSDL 中支援的 Facet。 所有的 Facet 都是選擇性的。 從概念模型產生資料庫時，Entity Framework 會使用下面所列的某些 facet。

> [!NOTE]
> 如需概念模型中的資料型別資訊，請參閱概念模型型別 (CSDL)。

| Facet               | 描述                                                                                                                                                                                                                                                   | 適用於                                                                                                                                                                                                                                                                                                                                                                           | 用於產生資料庫 | 由執行階段所使用 |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **定序**       | 在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | [是]                              | 否                  |
| **ConcurrencyMode** | 表示屬性值應用於開放式並行存取檢查。                                                                                                                                                                    | 所有**EDMSimpleType**屬性                                                                                                                                                                                                                                                                                                                                                     | 否                               | [是]                 |
| **Default**         | 執行個體化時如果沒有提供值，請指定屬性的預設值。                                                                                                                                                                       | 所有**EDMSimpleType**屬性                                                                                                                                                                                                                                                                                                                                                     | [是]                              | [是]                 |
| **FixedLength**     | 指定屬性值的長度是否可以變更。                                                                                                                                                                                                  | **Edm.Binary**， **Edm.String**                                                                                                                                                                                                                                                                                                                                                       | [是]                              | 否                  |
| **MaxLength**       | 指定屬性值的最大長度。                                                                                                                                                                                                           | **Edm.Binary**， **Edm.String**                                                                                                                                                                                                                                                                                                                                                       | [是]                              | 否                  |
| **可為 null**        | 指定屬性是否可以有**null**值。                                                                                                                                                                                                     | 所有**EDMSimpleType**屬性                                                                                                                                                                                                                                                                                                                                                     | [是]                              | [是]                 |
| **整數位數**       | 屬性的型別**十進位**，指定屬性值的數字數目。 屬性的型別**時間**， **DateTime**，並**DateTimeOffset**，指定屬性值秒數的小數部分的位數。 | **以 Edm.DateTime**， **Edm.DateTimeOffset**， **Edm.Decimal**， **Edm.Time**                                                                                                                                                                                                                                                                                                              | [是]                              | 否                  |
| **縮放**           | 指定屬性值小數點右邊的位數。                                                                                                                                                                      | **Edm.Decimal**                                                                                                                                                                                                                                                                                                                                                                      | [是]                              | 否                  |
| **SRID**            | 指定系統的空間參考系統識別碼。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。                                                              | **Edm.Geography、 Edm.GeographyPoint、 Edm.GeographyLineString、 Edm.GeographyPolygon、 Edm.GeographyMultiPoint、 Edm.GeographyMultiLineString、 Edm.GeographyMultiPolygon、 Edm.GeographyCollection、 Edm.Geometry、 Edm.GeometryPoint，Edm.GeometryLineString、 Edm.GeometryPolygon、 Edm.GeometryMultiPoint、 Edm.GeometryMultiLineString、 Edm.GeometryMultiPolygon、 Edm.GeometryCollection** | 否                               | [是]                 |
| **Unicode**         | 指出屬性值是否儲存為 Unicode。                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | [是]                              | [是]                 |

>[!NOTE]
> 從概念模型產生資料庫時, 產生資料庫精靈會辨識的值**StoreGeneratedPattern**屬性上**屬性**如果是在下列的項目命名空間： http://schemas.microsoft.com/ado/2009/02/edm/annotation。 屬性支援的值為**身分識別**並**計算**。 值為**識別**會產生識別值在資料庫中產生的資料庫資料行。 值為**計算**會產生資料庫計算所得值的資料行。

### <a name="example"></a>範例

下列範例顯示 Facet 套用至實體類型的屬性：

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="http://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
