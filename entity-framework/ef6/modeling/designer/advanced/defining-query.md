---
title: 定義查詢-EF Designer-EF6
description: 在 Entity Framework 6 中定義查詢-EF 設計工具
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: e1962a80c3a94fb17bacf28969629ce949b117a0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616838"
---
# <a name="defining-query---ef-designer"></a>定義查詢-EF 設計工具
本逐步解說示範如何使用 EF 設計工具將定義查詢和對應的實體類型加入至模型。 定義查詢通常用來提供類似于資料庫檢視所提供的功能，但該視圖是在模型中定義，而不是在資料庫中定義。 定義查詢可讓您執行在 .edmx 檔的 **DefiningQuery**元素中指定的 SQL 語句   。 如需詳細資訊，請參閱[SSDL 規格](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)中的 **DefiningQuery** 。

使用定義查詢時，您也必須在模型中定義實體類型。 實體類型是用來呈現定義查詢所公開的資料。 請注意，透過此實體類型呈現的資料是唯讀的。

您不能將參數化查詢做為定義查詢來執行。 不過，將用於呈現資料的實體類型之 Insert、Update 和 Delete 等函式對應至預存程序，即可更新資料。 如需詳細資訊，請參閱 [使用預存程式插入、更新及刪除](xref:ef6/modeling/designer/stored-procedures/cud)。

本主題說明如何執行下列工作。

-   新增定義查詢
-   將實體類型加入至模型
-   將定義查詢對應至實體類型

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>設定專案

本逐步解說使用 Visual Studio 2012 或更新版本。

-   開啟 Visual Studio。
-   在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。
-   在左窗格中，按一下 [ **Visual \# C**]，然後選取 [**主控台應用程式**] 範本。
-   輸入 **DefiningQuerySample** 做為專案的名稱，然後按一下 **[確定]**。

 

## <a name="create-a-model-based-on-the-school-database"></a>建立以 School 資料庫為基礎的模型

-   在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [ **加入**]，然後按一下 [ **新增專案**]。
-   從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。
-   輸入 **DefiningQueryModel** 的檔案名，然後按一下 [ **新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]**。
-   按一下 [新增連接]。 在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如** (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School**   作為資料庫名稱，然後按一下 **[確定]**。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊中，檢查 [**資料表**]   節點。 這會將所有的資料表新增至 **School** 模型。
-   按一下 **[完成]**。
-   在方案總管中，以滑鼠右鍵按一下 **DefiningQueryModel .edmx** 檔案，然後選取 [ **開啟方式**...]。
-   選取 [ **XML (Text) 編輯器**]。

    ![XML 編輯器](~/ef6/media/xmleditor.png)

-   如果出現下列訊息的提示，請按一下 **[是]** ：

    ![警告2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>新增定義查詢

在這個步驟中，我們將使用 XML 編輯器，將定義查詢和實體類型加入 .edmx 檔的 SSDL 區段。 

-   將 **EntitySet**   元素加入至 .EDMX 檔案的 SSDL 區段， (第5行到 13) 。 指定下列項目：
    -   只 **Name**    **EntityType**   會指定 **EntitySet**元素的 Name 和 EntityType 屬性   。
    -   在 **EntityType**屬性中，會使用實體類型的完整名稱   。
    -   要執行的 SQL 語句是在 **DefiningQuery**   元素中指定。

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   將 **EntityType** 元素加入 .EDMX 的 SSDL 區段。 檔案，如下所示。 請注意：
    -   **Name**屬性的值會對應到上述**EntitySet**元素中的**EntityType**屬性值，雖然在**EntityType**屬性中使用實體類型的完整名稱。
    -   屬性名稱會對應到上述)  (**DefiningQuery** 元素中 SQL 語句所傳回的資料行名稱。
    -   在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> 如果您稍後執行 [ **更新模型嚮導** ] 對話方塊，將會覆寫對儲存體模型所做的任何變更，包括定義查詢。

 

## <a name="add-an-entity-type-to-the-model"></a>將實體類型加入至模型

在這個步驟中，我們會使用 EF 設計工具將實體類型加入至概念模型。 請注意：

-   實體的**名稱**會對應至上述**EntitySet**元素中的**EntityType**屬性值。
-   屬性名稱會對應到上述 **DefiningQuery** 元素中 SQL 語句所傳回的資料行名稱。
-   在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。

在 EF 設計工具中開啟模型。

-   按兩下 DefiningQueryModel。
-   在下列訊息中說出 **Yes** ：

    ![警告2](~/ef6/media/warning2.png)

 

隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。

-   以滑鼠右鍵按一下設計工具介面，然後選取 [**加入新**的 - &gt; **實體 ...**]。
-   針對 [索引**鍵] 屬性**指定 [ **GradeReport** ] 做為 [機構名稱] 和 [ **CourseID** ]。
-   以滑鼠右鍵按一下 [ **GradeReport** ] 實體，然後選取 [**加入新**的純量 - &gt; **屬性**]。
-   將屬性的預設名稱變更為 **FirstName**。
-   加入其他純量屬性，並指定 **LastName** 作為名稱。
-   新增另一個純量屬性，並指定名稱的 **等級** 。
-   在 [ **屬性** ] 視窗中，將 [ **等級**的 **類型** ] 屬性變更為 [ **十進位**]。
-   選取 [ **FirstName** ] 和 [ **LastName** ] 屬性。
-   在 [ **屬性** ] 視窗中，將 [ **EntityKey** ] 屬性值變更為 [ **True**]。

因此，已將下列專案加入 .edmx 檔的 **CSDL** 區段。

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>將定義查詢對應至實體類型

在這個步驟中，我們將使用 [對應詳細資料] 視窗來對應概念和儲存實體類型。

-   以滑鼠右鍵按一下設計介面上的 [ **GradeReport** ] 實體，然後選取 [ **資料表對應**]。  
    [ **對應詳細資料** ] 視窗隨即顯示。
-   從 [**資料表**s) ] 下的 [ ** &lt; 加入資料表或 &gt; 視圖**] 下拉式清單 (選取 [ **GradeReport** ]。  
    概念和儲存 **GradeReport** 實體類型之間的預設對應隨即出現。  
    ![對應 Details3](~/ef6/media/mappingdetails.png)

如此一來，就會將**EntitySetMapping**   元素加入至 .edmx 檔案的 mapping 區段。 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   編譯應用程式。

 

## <a name="call-the-defining-query-in-your-code"></a>在您的程式碼中呼叫定義查詢

您現在可以使用 **GradeReport** 實體類型來執行定義查詢。 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
