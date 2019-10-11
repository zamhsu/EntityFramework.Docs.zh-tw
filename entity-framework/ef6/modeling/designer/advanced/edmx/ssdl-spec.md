---
title: SSDL 規格-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: b20d1f99f1da9c53a8a164fccc461e07d19c879d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182538"
---
# <a name="ssdl-specification"></a>SSDL 規格
存放結構定義語言 (SSDL) 是一種 XML 架構語言，會描述 Entity Framework 應用程式的儲存體模型。

在 Entity Framework 應用程式中，儲存體模型中繼資料是從 ssdl 檔案（以 SSDL 撰寫）載入至 StoreItemCollection 的實例，而且可以使用中的方法來存取。System.string. system.string 類別。 Entity Framework 會使用儲存體模型中繼資料，將針對概念模型的查詢轉譯為存放區特有的命令。

Entity Framework Designer （EF Designer）會在設計階段將儲存模型資訊儲存在 .edmx 檔案中。 在組建期間，Entity Designer 會使用 .edmx 檔案中的資訊，建立在執行時間 Entity Framework 所需的 ssdl 檔案。

SSDL 的版本可藉由 XML 命名空間來區別。

| SSDL 版本 | XML 命名空間                                     |
|:-------------|:--------------------------------------------------|
| SSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL v2      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Association 項目 (SSDL)

存放結構定義語言（SSDL）中的**Association**元素會指定參與基礎資料庫外鍵條件約束的資料表資料行。 兩個必要的子專案 End 元素會在關聯的兩端指定資料表，並在每個端的多重性處。 選擇性的 ReferentialConstraint 元素會指定關聯的主體和相依端點，以及參與的資料行。 如果沒有**ReferentialConstraint**元素存在，則必須使用 AssociationSetMapping 專案來指定關聯的資料行對應。

**Association**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個）
-   結束（剛好兩個）
-   ReferentialConstraint （零或一個）
-   Annotation 元素（零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**Association**元素的屬性。

| 屬性名稱 | 必要 | 值                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **名稱**       | 是         | 基礎資料庫中對應之外部索引鍵條件約束的名稱。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Association**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示的**Association**元素，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-3CustomerOrders** foreign key 條件約束的資料行：

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

存放結構定義語言（SSDL）中的**AssociationSet**元素代表基礎資料庫中兩個數據表之間的外鍵條件約束。 參與外鍵條件約束的資料表資料行是在 Association 元素中指定。 對應至指定**associationset**元素的**association**專案，是在**AssociationSet**元素的**Association**屬性中指定。

SSDL 關聯集會透過 AssociationSetMapping 元素對應到 CSDL 關聯集。 不過，如果指定之 CSDL 關聯集的 CSDL 關聯是使用 ReferentialConstraint 元素所定義，則不需要對應的**AssociationSetMapping**元素。 在此情況下，如果**AssociationSetMapping**元素存在，則**ReferentialConstraint**元素會覆寫它所定義的對應。

**AssociationSet**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個）
-   結束（零或兩個）
-   Annotation 元素（零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**AssociationSet**元素的屬性。

| 屬性名稱  | 必要 | 值                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **名稱**        | 是         | 關聯集所表示之外部索引鍵條件約束的名稱。                          |
| **技術協會** | 是         | 定義參與外部索引鍵條件約束之資料行的關聯名稱。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**AssociationSet**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示**AssociationSet**元素，此專案代表基礎資料庫中的 @no__t 1 外鍵條件約束：

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>CollectionType 元素（SSDL）

存放結構定義語言（SSDL）中的**CollectionType**元素會指定函式的傳回型別為集合。 **CollectionType**元素是 ReturnType 元素的子系。 集合的類型是使用 RowType 子項目所指定：

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**CollectionType**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示的函式會使用**CollectionType**專案來指定該函數傳回資料列集合。

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

存放結構定義語言（SSDL）中的**CommandText**專案是 Function 元素的子系，可讓您定義在資料庫上執行的 SQL 語句。 **CommandText**元素可讓您加入與資料庫中的預存程式類似的功能，但您會在儲存模型中定義**CommandText**元素。

**CommandText**元素不能有子項目。 **CommandText**元素的主體必須是基礎資料庫的有效 SQL 語句。

**CommandText**元素沒有適用的屬性。

### <a name="example"></a>範例

下列範例顯示具有子**CommandText**元素的**Function**元素。 藉由將**UpdateProductInOrder**函式匯入到概念模型中，將其公開為 ObjectCoNtext 的方法。  

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

存放結構定義語言（SSDL）中的**DefiningQuery**元素可讓您直接在基礎資料庫中執行 SQL 語句。 **DefiningQuery**元素常用於資料庫檢視，但此視圖定義于儲存體模型中，而不是資料庫中。 **DefiningQuery**元素中定義的視圖可以透過 EntitySetMapping 專案對應到概念模型中的實體類型。 這些對應是唯讀的。  

下列 SSDL 語法顯示**EntitySet**的宣告，後面接著**DefiningQuery**元素，其中包含用來抓取視圖的查詢。

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

您可以使用 Entity Framework 中的預存程式，透過 views 啟用讀寫案例。 您可以使用資料來源 view 或 Entity SQL view，做為用來抓取資料的基表和預存程式的變更處理。

您可以使用**DefiningQuery**元素，以 Microsoft SQL Server Compact 3.5 為目標。 雖然 SQL Server Compact 3.5 不支援預存程式，但您可以使用**DefiningQuery**元素來執行類似的功能。 該項目的另一個用處是可建立預存程序，克服程式設計語言中所使用之資料類型與資料來源之資料類型間不相符的問題。 您可以撰寫一個**DefiningQuery** ，它會接受一組特定的參數，然後使用一組不同的參數來呼叫預存程式，例如，刪除資料的預存程式。

## <a name="dependent-element-ssdl"></a>Dependent 項目 (SSDL)

存放結構定義語言（SSDL）中的**相依**專案是 ReferentialConstraint 元素的子專案，可定義外鍵條件約束（也稱為參考條件約束）的相依端點。 **Dependent**元素會在參考主鍵資料行（或資料行）的資料表中指定資料行（或資料行）。 **PropertyRef**元素會指定要參考的資料行。 Principal 元素會指定**相依**專案中所指定之資料行所參考的主鍵資料行。

相依元素可以具有下列子專案（**依**列出的順序）：

-   PropertyRef （一或多個）
-   Annotation 元素（零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**相依**元素的屬性。

| 屬性名稱 | 必要 | 值                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **角色**       | 是         | 與對應之 End 專案的**Role**屬性（如果使用的話）相同的值。否則，就是包含參考資料行之資料表的名稱。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**相依**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示的 Association 專案，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-2CustomerOrders** foreign key 條件約束的資料行。 **Dependent**元素會將**Order**資料表的**CustomerId**資料行指定為條件約束的相依端點。

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

存放結構定義語言（SSDL）中的**檔**專案可以用來提供父元素中所定義之物件的相關資訊。

**檔**元素可以具有下列子專案（依列出的順序）：

-   **摘要**：父元素的簡短描述。 (零或一個項目)
-   **LongDescription**：父元素的詳細描述。 (零或一個項目)

### <a name="applicable-attributes"></a>適用屬性

可以將任何數目的注釋屬性（自訂 XML 屬性）套用至**檔**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示**檔**元素做為 EntityType 元素的子專案。

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

存放結構定義語言（SSDL）中的**End**元素會指定基礎資料庫中外鍵條件約束一端的資料表和資料列數目。 **End**元素可以是 Association 元素或 AssociationSet 專案的子系。 在這兩種情況下，可能的子項目和適用的屬性都不相同。

### <a name="end-element-as-a-child-of-the-association-element"></a>End 項目為 Association 項目的子項目

**End**專案（做為**Association**專案的子系）會指定 foreign key 條件約束結尾的資料表和資料列數目，分別具有**Type**和**多重性**屬性。 外部索引鍵條件約束的端點是定義為 SSDL 關聯的一部分；SSDL 關聯必須擁有兩個端點。

**End**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個元素）
-   OnDelete （零或一個元素）
-   Annotation 元素（零或多個元素）

#### <a name="applicable-attributes"></a>適用屬性

下表描述當屬性為**Association**元素的子系時，可以套用至**結尾**專案的屬性。

| 屬性名稱   | 必要 | 值                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **型別**         | 是         | 位於外鍵條件約束結尾之 SSDL 實體集的完整名稱。                                                                                                                                                                                                                                                                                          |
| **角色**         | 否          | 在對應之 ReferentialConstraint 專案的 Principal 或 Dependent 元素中， **Role**屬性的值（如果有使用的話）。                                                                                                                                                                                                                                             |
| **數** | 是         | **1**、 **0 ..1**或 **\*** ，視外鍵條件約束結尾的資料列數目而定。 <br/> **1**表示外鍵條件約束結尾只存在一個資料列。 <br/> **0 ..1**表示外鍵條件約束結尾處有零或一個資料列。 <br/> **\*** 表示外鍵條件約束結尾有零個、一個或多個資料列。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**End**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

#### <a name="example"></a>範例

下列範例顯示定義**FK @ no__t-2CustomerOrders** foreign key 條件約束的**Association**元素。 在每個**End**專案上指定的**多重性**值表示**Orders**資料表中的許多資料列都可以與**customers**資料表中的資料列相關聯，但是**customers**資料表中只有一個資料列可以與資料列相關聯在**Orders**資料表中。 此外， **OnDelete**元素表示如果**customers**資料表中的資料列遭到刪除，則會刪除**Orders**資料表中，參考**customers**資料表中之特定資料列的所有資料列。

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

**End**專案（做為**AssociationSet**專案的子專案）會指定基礎資料庫中外鍵條件約束一端的資料表。

**End**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個）
-   Annotation 元素（零或多個）

#### <a name="applicable-attributes"></a>適用屬性

下表描述當元素是**AssociationSet**專案的子系時，可以套用至**結尾**專案的屬性。

| 屬性名稱 | 必要 | 值                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | 是         | 位於外鍵條件約束結尾之 SSDL 實體集的名稱。                                      |
| **角色**       | 否          | 在對應關聯專案的一個**End**專案上指定的其中一個**角色**屬性的值。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**End**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

#### <a name="example"></a>範例

下列範例顯示具有**AssociationSet**元素並具有兩個**End**元素的**EntityContainer**元素：

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

存放結構定義語言（SSDL）中的**EntityContainer**元素會描述 Entity Framework 應用程式中基礎資料來源的結構：SSDL 實體集（定義于 EntitySet 專案中）表示資料庫中的資料表、SSDL 實體類型（定義于 EntityType 元素中）代表資料表中的資料列，以及關聯集（定義于 AssociationSet 專案中）代表中的外鍵條件約束資料. 儲存體模型實體容器會透過 EntityContainerMapping 元素對應至概念模型實體容器。

**EntityContainer**元素可以有零個或一個檔元素。 如果**檔**元素存在，則必須在所有其他子專案之前。

**EntityContainer**元素可以有零或多個下列子專案（依列出的順序）：

-   EntitySet
-   AssociationSet
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**EntityContainer**元素的屬性。

| 屬性名稱 | 必要 | 值                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **名稱**       | 是         | 實體容器的名稱。 此名稱不得包含任何句號 (.)。 |

> [!NOTE]
> 可以將任何數目的注釋屬性（自訂 XML 屬性）套用至**EntityContainer**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示的**EntityContainer**元素會定義兩個實體集和一個關聯集。 請注意實體類型和關聯類型名稱要以概念模型命名空間名稱限定。

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

存放結構定義語言（SSDL）中的**EntitySet**元素代表基礎資料庫中的資料表或視圖。 SSDL 中的 EntityType 元素代表資料表或視圖中的資料列。 **EntitySet**元素的**EntityType**屬性會指定代表 ssdl 實體集內資料列的特定 SSDL 實體類型。 CSDL 實體集與 SSDL 實體集之間的對應是在 EntitySetMapping 元素中指定。

**EntitySet**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個元素）
-   DefiningQuery （零或一個元素）
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**EntitySet**元素的屬性。

> [!NOTE]
> 某些屬性（此處未列出）可能會以**存放區**別名來限定。 更新模型時會使用這些屬性。

| 屬性名稱 | 必要 | 值                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **名稱**       | 是         | 實體集的名稱。                                                              |
| **EntityType** | 是         | 實體類型 (實體集包含其執行個體) 的完整名稱。 |
| **結構描述**     | 否          | 資料庫結構描述。                                                                     |
| **資料表**      | 否          | 資料庫資料表。                                                                      |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**EntitySet**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示具有兩個**EntitySet**元素和一個**AssociationSet**元素的**EntityContainer**元素：

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

存放結構定義語言（SSDL）中的**EntityType**元素代表基礎資料庫之資料表或視圖中的資料列。 SSDL 中的 EntitySet 元素代表資料列發生所在的資料表或視圖。 **EntitySet**元素的**EntityType**屬性會指定代表 ssdl 實體集內資料列的特定 SSDL 實體類型。 SSDL 實體類型和 CSDL 實體類型之間的對應是在 EntityTypeMapping 元素中指定。

**EntityType**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個元素）
-   索引鍵（零或一個元素）
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**EntityType**元素的屬性。

| 屬性名稱 | 必要 | 值                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**       | 是         | 實體類型的名稱。 此值通常與資料表名稱相同，在資料表中實體類型表示資料列。 此值不可以包含句號 (.)。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**EntityType**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示具有兩個屬性的**EntityType**元素：

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

存放結構定義語言（SSDL）中的**Function**元素會指定存在於基礎資料庫中的預存程式。

**Function**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個）
-   參數（零或多個）
-   CommandText （零或一個）
-   ReturnType （零或多個）
-   Annotation 元素（零或多個）

函式的傳回型別必須使用**returntype**元素或**returntype**屬性（請參閱下文）來指定，但不能同時指定。

儲存體模型中所指定的預存程序可以匯入應用程式的概念模型中。 如需詳細資訊，請參閱[使用預存程式進行查詢](~/ef6/modeling/designer/stored-procedures/query.md)。 **Function**元素也可以用來定義儲存體模型中的自訂函數。  

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**Function**元素的屬性。

> [!NOTE]
> 某些屬性（此處未列出）可能會以**存放區**別名來限定。 更新模型時會使用這些屬性。

| 屬性名稱             | 必要 | 值                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                   | 是         | 預存程序 (Stored Procedure) 的名稱。                                                                                                                                                                                  |
| **ReturnType**             | 否          | 預存程序的傳回型別。                                                                                                                                                                           |
| **Aggregate**              | 否          | 如果預存程式傳回匯總值，則**為 True** ;否則**為 False**。                                                                                                                                  |
| **BuiltIn**                | 否          | 如果函式是內建的<sup>1</sup>函數，則為**True** ;否則**為 False**。                                                                                                                                  |
| **StoreFunctionName**      | 否          | 預存程序 (Stored Procedure) 的名稱。                                                                                                                                                                                  |
| **且具有 niladicfunction**        | 否          | 如果函數是 niladic<sup>2</sup>函式，則為**True** ;否則**為 False** 。                                                                                                                                   |
| **IsComposable**           | 否          | 如果函式是可組合的<sup>3</sup>個函數，則為**True** ;否則**為 False** 。                                                                                                                                |
| **ParameterTypeSemantics** | 否          | 列舉型別，可定義用來解決函式多載的型別語意。 列舉型別會定義在每個函式定義的提供者資訊清單。 預設值為**AllowImplicitConversion**。 |
| **結構描述**                 | 否          | 結構描述的名稱，可在其中定義預存程序。                                                                                                                                                   |

<sup>1</sup>內建函數是在資料庫中定義的函數。 如需儲存體模型中所定義之函式的詳細資訊，請參閱 CommandText 元素（SSDL）。

<sup>2</sup> niladic 函式是不接受任何參數的函式，呼叫時，不需要括弧。

如果一個函式的輸出可以是另一個函式的輸入，<sup>則兩個</sup>函式是可組合的。

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Function**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例會顯示對應至**UpdateOrderQuantity**預存程式的**Function**元素。 預存程序可接受兩個參數且不傳回值。

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

存放結構定義語言（SSDL）中的**Key**元素代表基礎資料庫中資料表的主鍵。 **Key**是 EntityType 元素的子項目，代表資料表中的資料列。 主要索引鍵是藉由參考**EntityType**元素上定義的一個或多個屬性專案，定義在**key**元素中。

**Key**元素可以具有下列子專案（依列出的順序）：

-   PropertyRef （一或多個）
-   Annotation 項目

索引**鍵**元素沒有適用的屬性。

### <a name="example"></a>範例

下列範例顯示的**EntityType**元素具有參考一個屬性的索引鍵：

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

當參與外鍵條件約束的資料列遭到刪除時，存放結構定義語言（SSDL）中的**OnDelete**元素會反映資料庫行為。 如果動作設定為 [ **Cascade**]，則會一併刪除參考正在刪除之資料列的資料列。 如果動作設定為 [**無**]，則參考所刪除之資料列的資料列也不會被刪除。 **OnDelete**元素是 End 元素的子項目。

**OnDelete**元素可以有下列子專案（依列出的順序）：

-   檔（零或一個）
-   Annotation 元素（零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**OnDelete**元素的屬性。

| 屬性名稱 | 必要 | 值                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **動作**     | 是         | **Cascade**或**None**。 （**限制**的值是有效的，但其行為與 [**無**] 相同）。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**OnDelete**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示定義**FK @ no__t-2CustomerOrders** foreign key 條件約束的**Association**元素。 **OnDelete**元素表示如果**customers**資料表中的資料列被刪除，則會刪除**Orders**資料表中，參考**customers**資料表中之特定資料列的所有資料列。

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

存放結構定義語言（SSDL）中的**Parameter**專案是 Function 元素的子系，可指定資料庫中預存程式的參數。

**Parameter**元素可以具有下列子專案（依列出的順序）：

-   檔（零或一個）
-   Annotation 元素（零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**參數**元素的屬性。

| 屬性名稱 | 必要 | 值                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**       | 是         | 參數名稱。                                                                                                                                                                                                      |
| **型別**       | 是         | 參數型別。                                                                                                                                                                                                             |
| **下**       | 否          | **In**、 **Out**或**InOut** ，取決於參數是輸入、輸出或輸入/輸出參數。                                                                                                                |
| **MaxLength**  | 否          | 參數的長度上限。                                                                                                                                                                                            |
| **整數位數**  | 否          | 參數的精確度。                                                                                                                                                                                                 |
| **縮放**      | 否          | 參數的小數位數。                                                                                                                                                                                                     |
| **SRID**       | 否          | 空間系統參考識別碼。 僅對空間類型的參數有效。 如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Parameter**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例**顯示的函式元素具有**兩個指定輸入參數的**參數**元素：

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

存放結構定義語言（SSDL）中的**Principal**元素是 ReferentialConstraint 元素的子專案，可定義外鍵條件約束（也稱為參考條件約束）的主要端點。 **Principal**元素會在另一個資料行（或資料行）所參考的資料表中，指定主鍵資料行（或資料行）。 **PropertyRef**元素會指定要參考的資料行。 Dependent 元素會指定參考**Principal**元素中所指定之主鍵資料行的資料行。

**Principal**元素可以具有下列子專案（依列出的順序）：

-   PropertyRef （一或多個）
-   Annotation 元素（零或多個）

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**主體**元素的屬性。

| 屬性名稱 | 必要 | 值                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **角色**       | 是         | 與對應之 End 專案的**Role**屬性（如果使用的話）相同的值。否則，就是包含參考資料行之資料表的名稱。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**主體**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示的 Association 專案，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-2CustomerOrders** foreign key 條件約束的資料行。 **Principal**元素會將**Customer**資料表的**CustomerId**資料行指定為條件約束的主要端點。

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

存放結構定義語言（SSDL）中的**屬性**專案代表基礎資料庫中資料表的資料行。 **屬性**元素是 EntityType 元素的子系，代表資料表中的資料列。 **EntityType**元素上定義的每個**屬性**專案都代表一個資料行。

**Property**元素不能有任何子專案。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**Property**元素的屬性。

| 屬性名稱            | 必要 | 值                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名稱**                  | 是         | 對應資料行的名稱。                                                                                                                                                                                           |
| **型別**                  | 是         | 對應資料行的類型。                                                                                                                                                                                           |
| **Null**              | 否          | **True** （預設值）或**False** ，取決於對應的資料行是否可以有 null 值。                                                                                                                  |
| **DefaultValue**          | 否          | 對應資料行的預設值。                                                                                                                                                                                  |
| **MaxLength**             | 否          | 對應資料行的長度上限。                                                                                                                                                                                 |
| **FixedLength**           | 否          | **True**或**False** ，取決於對應的資料行值是否會儲存為固定長度的字串。                                                                                                              |
| **整數位數**             | 否          | 對應資料行的精確度。                                                                                                                                                                                      |
| **縮放**                 | 否          | 對應資料行的小數位數。                                                                                                                                                                                          |
| **Unicode**               | 否          | **True**或**False** ，取決於對應的資料行值是否會儲存為 Unicode 字串。                                                                                                                   |
| **定序**             | 否          | 指定資料來源中使用之定序順序的字串。                                                                                                                                                   |
| **SRID**                  | 否          | 空間系統參考識別碼。 僅對空間類型的屬性有效。 如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **StoreGeneratedPattern** | 否          | **無**、**識別**（如果對應的資料行值是在資料庫中產生的身分識別），或**計算**（如果對應的資料行值是在資料庫中計算）。 對 RowType 屬性無效。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**屬性**專案。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示具有兩個子**屬性**元素的**EntityType**元素：

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

存放結構定義語言（SSDL）中的**PropertyRef**元素會參考 EntityType 元素上定義的屬性，以指出屬性將會執行下列其中一個角色：

-   屬於**EntityType**所代表之資料表的主鍵。 一或多個**PropertyRef**元素可以用來定義主鍵。 如需詳細資訊，請參閱 Key 元素。
-   做為參考條件約束的相依端點和主要端點。 如需詳細資訊，請參閱 ReferentialConstraint 元素。

**PropertyRef**元素只能有下列子項目：

-   檔（零或一個）
-   Annotation 項目

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**PropertyRef**元素的屬性。

| 屬性名稱 | 必要 | 值                                |
|:---------------|:------------|:-------------------------------------|
| **名稱**       | 是         | 參考屬性的名稱。 |

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**PropertyRef**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示的**PropertyRef**專案，是用來藉由參考**EntityType**元素上定義的屬性來定義主鍵。

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

存放結構定義語言（SSDL）中的**ReferentialConstraint**元素代表基礎資料庫中的外鍵條件約束（也稱為參考完整性條件約束）。 條件約束的主體和相依端點會分別由主體和相依子項目指定。 參與主體和相依端點的資料行會以 PropertyRef 元素來參考。

**ReferentialConstraint**元素是 Association 元素的選擇性子項目。 如果**ReferentialConstraint**元素不是用來對應**Association**元素中所指定的 foreign key 條件約束，就必須使用 AssociationSetMapping 專案來執行此動作。

**ReferentialConstraint**元素可以有下列子項目：

-   檔（零或一個）
-   Principal （只有一個）
-   相依（只有一個）
-   Annotation 元素（零或多個）

### <a name="applicable-attributes"></a>適用屬性

任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ReferentialConstraint**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例顯示的**Association**元素，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-3CustomerOrders** foreign key 條件約束的資料行：

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

## <a name="returntype-element-ssdl"></a>ReturnType 元素（SSDL）

存放結構定義語言（SSDL）中的**ReturnType**元素會指定在**function**元素中定義之函式的傳回類型。 函式傳回型別也可以使用**ReturnType**屬性來指定。

函式的傳回型別是使用**type**屬性或**ReturnType**元素所指定。

**ReturnType**元素可以具有下列子項目：

- CollectionType （一）  

> [!NOTE]
> 任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ReturnType**元素。 不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。 任兩個自訂屬性的完整名稱不能相同。

### <a name="example"></a>範例

下列範例會使用傳回資料列集合的**函數**。

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


## <a name="rowtype-element-ssdl"></a>RowType 元素（SSDL）

存放結構定義語言（SSDL）中的**RowType**專案會將未命名的結構定義為存放區中定義之函式的傳回型別。

**RowType**元素是**CollectionType**元素的子項目：

**RowType**元素可以有下列子項目：

- 屬性（一或多個）  

### <a name="example"></a>範例

下列範例顯示使用**CollectionType**元素的存放區函式，以指定函式傳回資料列集合（如**RowType**元素中所指定）。


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

存放結構定義語言（SSDL）中的**架構**元素是儲存模型定義的根項目。 其中包含組成儲存模型的物件、函式和容器等定義。

**Schema**元素可以包含零個或多個下列子項目：

-   關聯
-   EntityType
-   EntityContainer
-   函數

**Schema**元素會使用**namespace**屬性來定義儲存模型中實體類型和關聯物件的命名空間。 在命名空間中，兩個物件不能有相同的名稱。

儲存模型命名空間與**架構**元素的 XML 命名空間不同。 儲存模型命名空間（如**命名空間**屬性所定義）是實體類型和關聯類型的邏輯容器。 **Schema**元素的 XML 命名空間（由**xmlns**屬性所表示）是**schema**專案之子專案和屬性的預設命名空間。 XML 命名空間的表單 https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl （其中 YYYY 和 MM 表示年和月分別） 是保留供 SSDL。 自訂項目和屬性不能出現在擁有此格式的命名空間中。

### <a name="applicable-attributes"></a>適用屬性

下表描述可套用至**架構**元素的屬性。

| 屬性名稱            | 必要 | 值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **命名空間**             | 是         | 儲存模型的命名空間。 **Namespace**屬性的值是用來形成類型的完整名稱。 例如，如果名為*Customer*的**EntityType**是在 examplemodel.store.customer 命名空間中，則**entitytype**的完整限定名稱為 examplemodel.store.customer。 <br/> 下列字串不能當做**Namespace**屬性的值使用：**系統**、**暫時性**或**Edm**。 **Namespace**屬性的值不能與 CSDL Schema 元素中**namespace**屬性的值相同。 |
| **Alias**                 | 否          | 用來取代命名空間名稱的識別項。 例如，如果名為*Customer*的**EntityType**是在 Examplemodel.store.customer 中，而且**Alias**屬性的值是*StorageModel*，則您可以使用 StorageModel 做為的完整名稱 **。EntityType。**                                                                                                                                                                                                                                                                                    |
| **提供者**              | 是         | 資料提供者。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | 是         | 向提供者表示要傳回哪個提供者資訊清單的語彙基元。 未定義此語彙基元的格式。 語彙基元的值是由提供者定義。 如需有關 SQL Server 提供者資訊清單標記的詳細資訊，請參閱 SqlClient for Entity Framework。                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>範例

下列範例顯示包含**EntityContainer**元素、兩個**EntityType**專案和一個**Association**元素的**Schema**專案。

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
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

可以將一個以上的 Annotation 屬性套用至指定的 SSDL 項目。 注釋專案中包含的中繼資料可在執行時間使用 system.string. 中繼資料命名空間中的類別來存取。

### <a name="example"></a>範例

下列範例顯示的 EntityType 專案，具有套用至 [**訂單**] 屬性的 annotation 屬性。 此範例也會顯示加入至**EntityType**元素的 annotation 專案。

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

多個 Annotation 項目可以同時做為所指定 SSDL 項目的子系。 從 .NET Framework 第4版開始，您可以在執行時間使用 system.servicemodel 命名空間中的類別來存取 annotation 元素中包含的中繼資料。

### <a name="example"></a>範例

下列範例顯示具有 annotation 元素（**CustomElement**）的 EntityType 專案。 此範例也會顯示套用至 [**訂單**] 屬性的 annotation 屬性。

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

存放結構定義語言（SSDL）中的 facet 代表在屬性專案中指定之資料行類型的條件約束。 Facet 會實作為**屬性**元素上的 XML 屬性。

下表說明 SSDL 中支援的 Facet：

| Facet           | 描述                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **定序**   | 在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。                                                                                                             |
| **FixedLength** | 指定資料行值的長度是否可以變更。                                                                                                                                                                                                  |
| **MaxLength**   | 指定資料行值的最大長度。                                                                                                                                                                                                           |
| **整數位數**   | 針對**Decimal**類型的屬性，指定屬性值可以擁有的位數。 針對**Time**、 **DateTime**和**DateTimeOffset**類型的屬性，指定資料行值的秒數小數部分的位數。 |
| **縮放**       | 指定資料行值小數點右邊的位數。                                                                                                                                                                      |
| **Unicode**     | 指定資料行值是否儲存為 Unicode。                                                                                                                                                                                                    |
