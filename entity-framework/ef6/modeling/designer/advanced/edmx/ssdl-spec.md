---
title: SSDL 規格-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: a8b1f844a34c44d283982a52cef3bf80afd7e679
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490346"
---
# <a name="ssdl-specification"></a>SSDL 規格
存放結構定義語言 (SSDL) 是一種 XML 架構語言，會描述 Entity Framework 應用程式的儲存體模型。

Entity Framework 應用程式中，儲存體模型中繼資料載入來自.ssdl 檔 （以 SSDL 撰寫） System.Data.Metadata.Edm.StoreItemCollection 的執行個體，則可存取使用中的方法System.Data.Metadata.Edm.MetadataWorkspace 類別。 Entity Framework 會使用儲存體模型中繼資料，以針對概念模型存放區特有的命令以將查詢轉譯。

Entity Framework Designer （EF 設計工具） 會在設計階段，將儲存體模型資訊儲存在.edmx 檔案。 在建置階段 Entity Designer 會使用資訊來建立.ssdl 檔案所需的 Entity Framework 在執行階段在.edmx 檔案中。

SSDL 的版本可藉由 XML 命名空間來區別。

| SSDL 的版本 | XML 命名空間                                     |
|:-------------|:--------------------------------------------------|
| SSDL v1      | http://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL v2      | http://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL v3      | http://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Association 項目 (SSDL)

**關聯**存放結構定義語言 (SSDL) 中的項目會指定參與的資料表資料行中的基礎資料庫中的外部索引鍵條件約束。 兩個必要的子端項目會指定在此關聯各端的資料表和每一端的多重性。 選擇性的 ReferentialConstraint 項目會指定主體和相依端點的關聯，以及參與的資料行。 如果沒有**ReferentialConstraint**項目不存在、 AssociationSetMapping 項目必須用來指定關聯的資料行對應。

**關聯**元素可能具有下列子項目 （列出的順序）：

-   （零或一個） 的文件
-   結束 （正好兩個）
-   ReferentialConstraint （零或一個）
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**關聯**項目。

| 屬性名稱 | 必要 | 值                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **名稱**       | 是         | 基礎資料庫中對應之外部索引鍵條件約束的名稱。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**關聯**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**關聯**使用的項目**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders** foreign key 條件約束：

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a>AssociationSet 項目 (SSDL)

**AssociationSet**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中的兩個資料表之間的外部索引鍵條件約束。 Association 項目中，會指定參與外部索引鍵條件約束的資料表資料行。 **關聯**項目，對應至給定**AssociationSet**中所指定的項目**關聯**屬性**AssociationSet**項目。

SSDL 關聯集會對應至 CSDL 關聯集 AssociationSetMapping 項目。 不過，如果指定之 CSDL 關聯的 CSDL 關聯設定會定義使用 ReferentialConstraint 項目，沒有對應的**AssociationSetMapping**項目是必要。 在此情況下，如果**AssociationSetMapping**項目出現時，它會定義的對應會覆寫**ReferentialConstraint**項目。

**AssociationSet**元素可能具有下列子項目 （列出的順序）：

-   （零或一個） 的文件
-   結束 （零或兩個）
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**AssociationSet**項目。

| 屬性名稱  | 必要 | 值                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **名稱**        | 是         | 關聯集所表示之外部索引鍵條件約束的名稱。                          |
| **關聯** | 是         | 定義參與外部索引鍵條件約束之資料行的關聯名稱。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**AssociationSet**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**AssociationSet**項目，表示`FK_CustomerOrders`基礎資料庫中的外部索引鍵條件約束：

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>CollectionType 項目 (SSDL)

**CollectionType**存放結構定義語言 (SSDL) 中的項目會指定函式的傳回型別是集合。 **CollectionType**項目是子系的 ReturnType 項目。 集合型別是由使用資料列型別子項目指定：

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**CollectionType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例示範使用的函式**CollectionType**項目來指定此函數會傳回資料列集合。

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a>CommandText 項目 (SSDL)

**CommandText**存放結構定義語言 (SSDL) 中的項目是可讓您定義在資料庫上執行的 SQL 陳述式的函式項目子系。 **CommandText**項目可讓您新增類似於資料庫中的預存程序的功能，但您定義**CommandText**儲存模型中的項目。

**CommandText**項目不能有子項目。 主體**CommandText**項目必須是有效的 SQL 陳述式為基礎的資料庫。

任何屬性都適用於**CommandText**項目。

### <a name="example"></a>範例

下列範例所示**函式**項目具有子系**CommandText**項目。 公開**UpdateProductInOrder**藉由將它匯入概念模型函式做為方法，藉由對 ObjectContext。  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a>DefiningQuery 項目 (SSDL)

**DefiningQuery**存放結構定義語言 (SSDL) 中的項目可讓您直接在基礎資料庫中執行 SQL 陳述式。 **DefiningQuery**資料庫檢視的方式常用項目，但該檢視定義在儲存體模型中，而非資料庫。 中所定義的檢視**DefiningQuery**項目可以對應至概念模型，透過 EntitySetMapping 項目中的實體類型。 這些對應是唯讀的。  

下列 SSDL 語法顯示的宣告**EntitySet**後面**DefiningQuery**元素，其中包含用來擷取檢視的查詢。

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

您可以使用 Entity Framework 中的預存程序，可透過檢視的讀寫案例。 您可以使用資料來源檢視或 Entity SQL 檢視的基底資料表擷取資料及進行變更處理預存程序。

您可以使用**DefiningQuery**來以 Microsoft SQL Server Compact 3.5 為目標的項目。 雖然 SQL Server Compact 3.5 不支援預存程序，您可以實作類似的功能與**DefiningQuery**項目。 該項目的另一個用處是可建立預存程序，克服程式設計語言中所使用之資料類型與資料來源之資料類型間不相符的問題。 您可以撰寫**DefiningQuery**採用一組特定的參數，並接著呼叫具有一組不同的參數，例如，預存程序會刪除資料的預存程序。

## <a name="dependent-element-ssdl"></a>Dependent 項目 (SSDL)

**相依**存放結構定義語言 (SSDL) 中的項目是 ReferentialConstraint 項目，定義 （也稱為參考條件約束） 的外部索引鍵條件約束的相依端點的子項目。 **相依**項目會指定資料行 （或資料行） 在資料表中參考的主索引鍵資料行 （或資料行）。 **PropertyRef**項目會指定哪些資料行參考。 主體項目會指定中所指定的資料行所參考的主索引鍵資料行**相依**項目。

**相依**元素可能具有下列子項目 （列出的順序）：

-   PropertyRef （一或多個）
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**相依**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **角色**       | 是         | 相同的值**角色**屬性對應的結束項目 （如果使用）; 否則資料表的名稱，包含參考的資料行。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**相依**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例示範會使用 Association 元素**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders**外部索引鍵條件約束。 **相依**項目會指定**CustomerId**資料行**順序**做為條件約束的相依端的資料表。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a>Documentation 項目 (SSDL)

**文件**存放結構定義語言 (SSDL) 中的項目可以用來提供父元素中定義之物件的相關資訊。

**文件**元素可能具有下列子項目 （列出的順序）：

-   **摘要**： 父元素的簡短描述。 (零或一個項目)
-   **LongDescription**： 父項目的詳細描述。 (零或一個項目)

### <a name="applicable-attributes"></a>適用屬性

附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**文件**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**文件**為 EntityType 元素的子元素的項目。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a>End 項目 (SSDL)

**結束**存放結構定義語言 (SSDL) 中的項目指定基礎資料庫中的資料表和外部索引鍵條件約束的其中一端的資料列數目。 **結束**項目可以是子系的 Association 元素或 AssociationSet 項目。 在這兩種情況下，可能的子項目和適用的屬性都不相同。

### <a name="end-element-as-a-child-of-the-association-element"></a>End 項目為 Association 項目的子項目

**結束**項目 (為子系**關聯**項目) 指定的資料表和外部索引鍵條件約束與結尾的資料列數目**型別**和**多重性**分別屬性。 外部索引鍵條件約束的端點是定義為 SSDL 關聯的一部分；SSDL 關聯必須擁有兩個端點。

**結束**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   OnDelete （零或一個項目）
-   Annotation 項目 （零或多個項目）

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**結束**項目時的子系**關聯**項目。

| 屬性名稱   | 必要 | 值                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | 是         | 外部索引鍵條件約束結尾 SSDL 實體集的完整的名稱。                                                                                                                                                                                                                                                                                          |
| **角色**         | 否          | 值**角色**對應 ReferentialConstraint 項目 主體 或 相依項目中的屬性 （如果使用的話）。                                                                                                                                                                                                                                             |
| **多重性** | 是         | **1**， **0..1**，或**\*** 可以是結尾的外部索引鍵條件約束的資料列數目而定。 <br/> **1**表示外部索引鍵條件約束端點上存在該只有一個資料列。 <br/> **0..1**表示外部索引鍵條件約束端點上存在零個或一個資料列。 <br/> **\*** 表示零個、 一個或多個資料列存在外部索引鍵條件約束結尾。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

#### <a name="example"></a>範例

下列範例所示**關聯**項目，定義**FK\_CustomerOrders** foreign key 條件約束。 **多重性**值，指定在每個**端**項目表示，多個資料列**訂單**表可以與中的資料列相關聯**客戶**資料表，但只有一個資料列中的**客戶**表可以與中的資料列相關聯**訂單**資料表。 此外， **OnDelete**項目表示的所有資料列中**訂單**參考中的特定資料列的資料表**客戶**如果，就會刪除資料表中的資料列**客戶**刪除資料表。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a>End 項目為 AssociationSet 項目的子項目

**結束**項目 (為子系**AssociationSet**項目) 指定基礎資料庫中的其中一端的外部索引鍵條件約束的資料表。

**結束**元素可能具有下列子項目 （列出的順序）：

-   （零或一個） 的文件
-   Annotation 項目 （零或多個）

#### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**結束**項目時的子系**AssociationSet**項目。

| 屬性名稱 | 必要 | 值                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | 是         | 外部索引鍵條件約束結尾的 SSDL 實體集名稱。                                      |
| **角色**       | 否          | 其中的值**角色**上其中一個指定的屬性**結束**相對應的關聯項目的項目。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

#### <a name="example"></a>範例

下列範例所示**EntityContainer**項目**AssociationSet**具有兩個項目**結束**項目：

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a>EntityContainer 項目 (SSDL)

**EntityContainer**存放結構定義語言 (SSDL) 中的項目會描述 Entity Framework 應用程式的基礎資料來源的結構： SSDL 實體集 （EntitySet 項目中定義） 表示中的資料表資料庫中，SSDL 實體類型 （EntityType 項目中定義） 表示的資料表，資料列和關聯集 （定義於 AssociationSet 項目） 代表在資料庫中的 foreign key 條件約束。 儲存體模型實體容器對應到概念模型實體容器透過 EntityContainerMapping 項目。

**EntityContainer**項目可以有零個或一個文件項目。 如果**文件**項目已存在，它必須在所有其他子項目。

**EntityContainer**項目可以有零或多個下列子項目 （依列出的順序）：

-   EntitySet
-   AssociationSet
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntityContainer**項目。

| 屬性名稱 | 必要 | 值                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **名稱**       | 是         | 實體容器的名稱。 此名稱不得包含任何句號 (.)。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityContainer**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**EntityContainer**定義兩個實體集和一個關聯集的項目。 請注意實體類型和關聯類型名稱要以概念模型命名空間名稱限定。

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a>EntitySet 項目 (SSDL)

**EntitySet**存放結構定義語言 (SSDL) 中的項目代表資料表或檢視基礎資料庫中的。 在 SSDL 中的 EntityType 項目代表資料表或檢視表中的資料列。 **EntityType**屬性**EntitySet**項目會指定表示 SSDL 實體集中的資料列的特定 SSDL 實體類型。 EntitySetMapping 項目中指定的 CSDL 實體集和 SSDL 實體集之間的對應。

**EntitySet**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   DefiningQuery （零或一個項目）
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntitySet**項目。

> [!NOTE]
> （這裡未列出） 的某些屬性可能限定**儲存**別名。 更新模型時，[更新模型精靈] 會使用這些屬性。

| 屬性名稱 | 必要 | 值                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **名稱**       | 是         | 實體集的名稱。                                                              |
| **EntityType** | 是         | 實體類型 (實體集包含其執行個體) 的完整名稱。 |
| **結構描述**     | 否          | 資料庫結構描述。                                                                     |
| **資料表**      | 否          | 資料庫資料表。                                                                      |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntitySet**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**EntityContainer**具有兩個項目**EntitySet**項目和一個**AssociationSet**項目：

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a>EntityType 項目 (SSDL)

**EntityType**存放結構定義語言 (SSDL) 中的項目代表資料表或檢視的基礎資料庫中的資料列。 在 SSDL 中的 EntitySet 項目代表資料表或檢視的資料列會發生。 **EntityType**屬性**EntitySet**項目會指定表示 SSDL 實體集中的資料列的特定 SSDL 實體類型。 EntityTypeMapping 項目中指定的 SSDL 實體類型和 CSDL 實體類型之間的對應。

**EntityType**元素可能具有下列子項目 （列出的順序）：

-   文件 （零或一個項目）
-   索引鍵 （零或一個項目）
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**EntityType**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**       | 是         | 實體類型的名稱。 此值通常與資料表名稱相同，在資料表中實體類型表示資料列。 此值不可以包含句號 (.)。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**EntityType**具有兩個屬性的項目：

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a>Function 屬性 (SSDL)

**函式**存放結構定義語言 (SSDL) 中的項目會指定是否存在的預存程序基礎資料庫中。

**函式**元素可能具有下列子項目 （列出的順序）：

-   （零或一個） 的文件
-   參數 （零或多個）
-   CommandText （零或一個）
-   ReturnType （零或多個）
-   Annotation 項目 （零或多個）

傳回類型函式必須指定使用**ReturnType**項目或有**ReturnType**屬性 （如下所示），但非兩者。

儲存體模型中所指定的預存程序可以匯入應用程式的概念模型中。 如需詳細資訊，請參閱 <<c0> [ 預存程序使用查詢](~/ef6/modeling/designer/stored-procedures/query.md)。 **函式**項目也可用來在儲存體模型中定義自訂函式。  

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**函式**項目。

> [!NOTE]
> （這裡未列出） 的某些屬性可能限定**儲存**別名。 更新模型時，[更新模型精靈] 會使用這些屬性。

| 屬性名稱             | 必要 | 值                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                   | 是         | 預存程序 (Stored Procedure) 的名稱。                                                                                                                                                                                  |
| **ReturnType**             | 否          | 預存程序的傳回型別。                                                                                                                                                                           |
| **Aggregate**              | 否          | **真**如果預存程序傳回彙總的值; 否則為**False**。                                                                                                                                  |
| **內建**                | 否          | **真**內建函式是否<sup>1</sup>函式，否則**False**。                                                                                                                                  |
| **StoreFunctionName**      | 否          | 預存程序 (Stored Procedure) 的名稱。                                                                                                                                                                                  |
| **NiladicFunction**        | 否          | **真**niladic 函數是否<sup>2</sup>函式，**False**否則。                                                                                                                                   |
| **IsComposable**           | 否          | **真**如果函式是可組合<sup>3</sup>函式，**False**否則。                                                                                                                                |
| **ParameterTypeSemantics** | 否          | 列舉型別，可定義用來解決函式多載的型別語意。 列舉型別會定義在每個函式定義的提供者資訊清單。 預設值是**AllowImplicitConversion**。 |
| **結構描述**                 | 否          | 結構描述的名稱，可在其中定義預存程序。                                                                                                                                                   |

<sup>1</sup>內建函式是在資料庫中定義的函式。 如需儲存體模型中定義的函式的詳細資訊，請參閱 CommandText 項目 (SSDL)。

<sup>2</sup> niladic 函數會不接受任何參數，並呼叫時，不需要括號。

<sup>3</sup>兩個函式是可組合，如果有一個函式的輸出可以是其他函式的輸入。

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**函式**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**函式**對應至項目**UpdateOrderQuantity**預存程序。 預存程序可接受兩個參數且不傳回值。

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a>Key 項目 (SSDL)

**金鑰**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中資料表的主索引鍵。 **索引鍵**是 EntityType 項目，表示資料表中的資料列的子元素。 中定義的主索引鍵**金鑰**藉由參考一或多個屬性項目上所定義的項目**EntityType**項目。

**金鑰**元素可能具有下列子項目 （列出的順序）：

-   PropertyRef （一或多個）
-   Annotation 項目

任何屬性都適用於**金鑰**項目。

### <a name="example"></a>範例

下列範例所示**EntityType**參考一個屬性的索引鍵的項目：

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a>OnDelete 項目 (SSDL)

**OnDelete**參與的外部索引鍵條件約束的資料列刪除時，存放結構定義語言 (SSDL) 中的項目會反映資料庫行為。 如果動作設定為**Cascade**，則也會刪除參考正在刪除的資料列的資料列。 如果動作設定為**無**，則不會一併刪除參考正在刪除的資料列的資料列。 **OnDelete**項目是子元素的結束項目。

**OnDelete**元素可能具有下列子項目 （列出的順序）：

-   （零或一個） 的文件
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**OnDelete**項目。

| 屬性名稱 | 必要 | 值                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **動作**     | 是         | **Cascade**或是**None**。 (值**Restricted**有效，但具有相同的行為**無**。) |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**OnDelete**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**關聯**項目，定義**FK\_CustomerOrders** foreign key 條件約束。 **OnDelete**項目表示的所有資料列中**訂單**參考中的特定資料列的資料表**客戶**將刪除資料表，如果中的資料列**客戶**刪除資料表。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a>Parameter 項目 (SSDL)

**參數**存放結構定義語言 (SSDL) 中的項目是預存程序的參數會指定資料庫中的函式項目子系。

**參數**元素可能具有下列子項目 （列出的順序）：

-   （零或一個） 的文件
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**參數**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**       | 是         | 參數名稱。                                                                                                                                                                                                      |
| **Type**       | 是         | 參數型別。                                                                                                                                                                                                             |
| **模式**       | 否          | **在 **， **Out**，或**InOut**取決於參數是輸入、 輸出或輸入/輸出參數。                                                                                                                |
| **MaxLength**  | 否          | 參數的長度上限。                                                                                                                                                                                            |
| **整數位數**  | 否          | 參數的精確度。                                                                                                                                                                                                 |
| **縮放**      | 否          | 參數的小數位數。                                                                                                                                                                                                     |
| **SRID**       | 否          | 系統的空間參考識別碼。 僅適用於空間類型的參數。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**參數**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**函式**具有兩個項目**參數**指定輸入的參數的項目：

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a>Principal 項目 (SSDL)

**主體**存放結構定義語言 (SSDL) 中的項目是 ReferentialConstraint 項目，定義 （也稱為參考條件約束） 的外部索引鍵條件約束的主體端點的子項目。 **主體**元素指定另一個資料行 （或資料行） 所參考的資料表中的主索引鍵資料行 （或資料行）。 **PropertyRef**項目會指定哪些資料行參考。 相依的項目會指定參考主索引鍵資料行中所指定的資料行**主體**項目。

**主體**元素可能具有下列子項目 （列出的順序）：

-   PropertyRef （一或多個）
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**主體**項目。

| 屬性名稱 | 必要 | 值                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **角色**       | 是         | 相同的值**角色**屬性對應的結束項目 （如果使用）; 否則資料表的名稱，包含參考的資料行。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**主體**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例示範會使用 Association 元素**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders**外部索引鍵條件約束。 **主體**項目會指定**CustomerId**資料行**客戶**做為條件約束的主體端的資料表。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a>Property 項目 (SSDL)

**屬性**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中的資料表中的資料行。 **屬性**項目是 EntityType 項目，代表資料表中的資料列的子系。 每個**屬性**上定義的項目**EntityType**項目表示的資料行。

A**屬性**項目不能有任何子項目。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**屬性**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                  | 是         | 對應資料行的名稱。                                                                                                                                                                                           |
| **Type**                  | 是         | 對應資料行的類型。                                                                                                                                                                                           |
| **可為 null**              | 否          | **真**（預設值） 或**False**取決於對應的資料行是否可以有 null 值。                                                                                                                  |
| **DefaultValue**          | 否          | 對應資料行的預設值。                                                                                                                                                                                  |
| **MaxLength**             | 否          | 對應資料行的長度上限。                                                                                                                                                                                 |
| **FixedLength**           | 否          | **True**或是**False**取決於對應的資料行值是否會儲存為固定的長度的字串。                                                                                                              |
| **整數位數**             | 否          | 對應資料行的精確度。                                                                                                                                                                                      |
| **縮放**                 | 否          | 對應資料行的小數位數。                                                                                                                                                                                          |
| **Unicode**               | 否          | **True**或是**False**取決於對應的資料行值是否會儲存為 Unicode 字串。                                                                                                                   |
| **定序**             | 否          | 指定資料來源中使用之定序順序的字串。                                                                                                                                                   |
| **SRID**                  | 否          | 系統的空間參考識別碼。 僅適用於空間類型的屬性。 如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **StoreGeneratedPattern** | 否          | **無**，**身分識別**（如果對應的資料行值是在資料庫中產生的身分識別），或**計算**（如果對應的資料行值會計算資料庫中）。 沒有適用於資料列型別屬性。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**屬性**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**EntityType**具有兩個子項目**屬性**項目：

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a>PropertyRef 項目 (SSDL)

**PropertyRef**存放結構定義語言 (SSDL) 中的項目會參考上的 EntityType 項目，表示屬性將執行下列角色的其中一個定義的屬性：

-   資料表的主索引鍵的一部分， **EntityType**表示。 一或多個**PropertyRef**項目可以用來定義主索引鍵。 如需詳細資訊，請參閱索引鍵的項目。
-   做為參考條件約束的相依端點和主要端點。 如需詳細資訊，請參閱 ReferentialConstraint 項目。

**PropertyRef**元素只能有下列子項目：

-   （零或一個） 的文件
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至的屬性**PropertyRef**項目。

| 屬性名稱 | 必要 | 值                                |
|:---------------|:------------|:-------------------------------------|
| **名稱**       | 是         | 參考屬性的名稱。 |

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**PropertyRef**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**PropertyRef**用來定義主索引鍵所參考屬性上所定義的項目**EntityType**項目。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a>ReferentialConstraint 項目 (SSDL)

**ReferentialConstraint**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中的 （也稱為參考完整性條件約束） 的外部索引鍵條件約束。 會分別由主體和相依的子系項目，指定條件約束的主體和相依端點。 參與主體端和相依端資料行是使用 PropertyRef 元素參考。

**ReferentialConstraint**項目是關聯項目的選用子項目。 如果**ReferentialConstraint**項目不用來對應中指定的外部索引鍵條件約束**關聯**項目，AssociationSetMapping 項目必須用來執行這項操作。

**ReferentialConstraint**元素可能具有下列子元素：

-   （零或一個） 的文件
-   （只有一個） 的主體
-   相依 （只有一個）
-   Annotation 項目 （零或多個）

### <a name="applicable-attributes"></a>適用屬性

附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReferentialConstraint**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例所示**關聯**使用的項目**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders** foreign key 條件約束：

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a>ReturnType 項目 (SSDL)

**ReturnType**存放結構定義語言 (SSDL) 中的項目會指定在定義的函式的傳回型別**函式**項目。 傳回型別也可以使用指定的函式**ReturnType**屬性。

使用指定的函式的傳回型別**型別**屬性或**ReturnType**項目。

**ReturnType**元素可能具有下列子元素：

- CollectionType （一）  

> [!NOTE]
> 附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReturnType**項目。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例會使用**函式**所傳回的資料列集合。

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a>RowType 項目 (SSDL)

A **RowType**存放結構定義語言 (SSDL) 中的項目會定義未命名的結構做為傳回存放中所定義的函式的型別。

A **RowType**項目是子元素**CollectionType**項目：

A **RowType**元素可能具有下列子元素：

- 屬性 （一或多個）  

### <a name="example"></a>範例

下列範例示範使用的存放區函式**CollectionType**項目來指定此函數會傳回資料列集合 (如同在中指定**RowType**項目)。


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a>Schema 項目 (SSDL)

**結構描述**存放結構定義語言 (SSDL) 中的項目是儲存模型定義的根項目。 其中包含組成儲存模型的物件、函式和容器等定義。

**結構描述**項目可能包含零或多個下列子元素：

-   關聯
-   EntityType
-   EntityContainer
-   功能

**結構描述**項目會使用**命名空間**屬性來定義儲存體模型中的實體類型和關聯物件的命名空間。 在命名空間中，兩個物件不能有相同的名稱。

儲存模型命名空間是 XML 命名空間的不同**結構描述**項目。 儲存模型命名空間 (如所定義**命名空間**屬性) 是實體類型和關聯型別的邏輯容器。 XML 命名空間 (由**xmlns**屬性) 的**結構描述**項目是子元素和屬性的預設命名空間**結構描述**項目。 XML 命名空間的表單 http://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl （其中 YYYY 和 MM 表示年和月分別） 是保留供 SSDL。 自訂項目和屬性不能出現在擁有此格式的命名空間中。

### <a name="applicable-attributes"></a>適用屬性

下表描述的屬性可以套用至**結構描述**項目。

| 屬性名稱            | 必要 | 值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **命名空間**             | 是         | 儲存模型的命名空間。 值**命名空間**屬性用來構成型別的完整的名稱。 例如，如果**EntityType**名為*客戶*處於 ExampleModel.Store 命名空間中，則完整限定的名稱**EntityType**是ExampleModel.Store.Customer。 <br/> 下列字串不能做為值**命名空間**屬性：**系統**，**暫時性**，或**Edm**。 值**命名空間**屬性不能做為值相同**命名空間**CSDL 結構描述項目中的屬性。 |
| **Alias**                 | 否          | 用來取代命名空間名稱的識別項。 例如，如果**EntityType**名為*客戶*ExampleModel.Store 命名空間和值**別名**屬性是*StorageModel*，則您可以使用 StorageModel.Customer 做為完整格式名稱**EntityType。**                                                                                                                                                                                                                                                                                    |
| **提供者**              | 是         | 資料提供者。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | 是         | 向提供者表示要傳回哪個提供者資訊清單的語彙基元。 未定義此語彙基元的格式。 語彙基元的值是由提供者定義。 如需 SQL Server 提供者資訊清單語彙基元資訊，請參閱 Entity framework 的 SqlClient。                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>範例

下列範例所示**結構描述**包含的項目**EntityContainer**元素中，兩個**EntityType**項目和一個**關聯**項目。

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a>註釋屬性

存放結構定義語言 (SSDL) 中的 Annotation 屬性是儲存體模型中的自訂 XML 屬性，它們可提供與儲存體模型中之項目相關的額外中繼資料。 除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 屬性：

-   Annotation 屬性不能在任何 XML 命名空間之中，這是保留供 SSDL 之用。
-   任兩個 Annotation 屬性的完整名稱不能相同。

可以將一個以上的 Annotation 屬性套用至指定的 SSDL 項目。 可以使用 System.Data.Metadata.Edm 命名空間中的類別，在執行階段存取 annotation 項目中包含的中繼資料。

### <a name="example"></a>範例

下列範例示範具有 annotation 屬性套用至的 EntityType 項目**OrderId**屬性。 該範例也顯示註釋項目加入至**EntityType**項目。

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a>Annotation 項目 (SSDL)

存放結構定義語言 (SSDL) 中的 Annotation 項目是儲存體模型中的自訂 XML 項目，它們可提供與儲存體模型相關的額外中繼資料。 除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 項目：

-   Annotation 項目不能存在於保留供 SSDL 使用的任何 XML 命名空間中。
-   任兩個 Annotation 項目的完整名稱不能相同。
-   Annotation 項目必須出現在所指定 SSDL 項目的所有其他子項目之後。

多個 Annotation 項目可以同時做為所指定 SSDL 項目的子系。 從.NET Framework 第 4 版開始，中繼資料包含在 annotation 項目可以存取在執行階段使用 System.Data.Metadata.Edm 命名空間中類別。

### <a name="example"></a>範例

下列範例示範具有 annotation 項目的 EntityType 項目 (**CustomElement**)。 此範例也會示範套用至的註釋屬性**OrderId**屬性。

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a>Facet (SSDL)

存放結構定義語言 (SSDL) 中的 facet 表示屬性項目中指定的資料行型別條件約束。 Facet 會實作為 XML 屬性上**屬性**項目。

下表說明 SSDL 中支援的 Facet：

| Facet           | 描述                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **定序**   | 在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。                                                                                                             |
| **FixedLength** | 指定資料行值的長度是否可以變更。                                                                                                                                                                                                  |
| **MaxLength**   | 指定資料行值的最大長度。                                                                                                                                                                                                           |
| **整數位數**   | 屬性的型別**十進位**，指定屬性值的數字數目。 屬性的型別**時間**， **DateTime**，並**DateTimeOffset**，指定資料行值秒數的小數部分的位數。 |
| **縮放**       | 指定資料行值小數點右邊的位數。                                                                                                                                                                      |
| **Unicode**     | 指定資料行值是否儲存為 Unicode。                                                                                                                                                                                                    |
