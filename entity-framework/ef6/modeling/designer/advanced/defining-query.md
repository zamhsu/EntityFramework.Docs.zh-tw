---
title: 定義查詢-EF 設計工具-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
ms.openlocfilehash: b1589dc12ccb50754c2e950932a2d82bc4869f6b
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489468"
---
# <a name="defining-query---ef-designer"></a>定義查詢-EF 設計工具
本逐步解說示範如何將定義要使用 EF 設計工具的模型類型的查詢和對應的實體。 定義查詢通常用來提供資料庫檢視，提供類似功能，但該檢視定義在模型中，不是資料庫。 定義查詢可讓您執行 SQL 陳述式中指定**DefiningQuery** .edmx 檔案的項目。 如需詳細資訊，請參閱 < **DefiningQuery**中[SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)。

使用定義查詢時，您也必須在模型中定義實體類型。 實體類型用來呈現所定義的查詢公開的資料。 請注意，透過這個實體型別顯示的資料是唯讀的。

您不能將參數化查詢做為定義查詢來執行。 不過，將用於呈現資料的實體類型之 Insert、Update 和 Delete 等函式對應至預存程序，即可更新資料。 如需詳細資訊，請參閱 <<c0> [ 插入、 更新和刪除預存程序與](~/ef6/modeling/designer/stored-procedures/cud.md)。

本主題說明如何執行下列工作。

-   加入定義查詢
-   將實體類型加入模型
-   對應至實體類型定義的查詢

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- 新版的 Visual Studio。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

本逐步解說使用 Visual Studio 2012 或更新版本。

-   開啟 Visual Studio。
-   在 [檔案]  功能表中，指向 [新增] ，然後按一下 [專案] 。
-   在左窗格中，按一下**Visual C\#**，然後選取**主控台應用程式**範本。
-   請輸入**DefiningQuerySample**做為名稱的專案，然後按一下**確定**。

 

## <a name="create-a-model-based-on-the-school-database"></a>建立 School 資料庫為基礎的模型

-   以滑鼠右鍵按一下方案總管 中的專案名稱，指向**新增**，然後按一下**新項目**。
-   選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。
-   請輸入**DefiningQueryModel.edmx**為檔案名稱，然後按一下**新增**。
-   在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。
-   按一下 新增連接。 在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。
    [選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。
-   在 [選擇您的資料庫物件] 對話方塊中，檢查**資料表**節點。 這會新增至所有資料表**學校**模型。
-   按一下 [ **完成**]。
-   在 [方案總管] 中，以滑鼠右鍵按一下**DefiningQueryModel.edmx**檔案，然後選取**開啟方式...**.
-   選取  **XML （文字） 編輯器**。

    ![XML 編輯器](~/ef6/media/xmleditor.png)

-   按一下 **是**出現提示時出現下列訊息：

    ![警告 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>加入定義查詢

在此步驟中，我們將使用 XML 編輯器，若要將定義查詢並的實體類型加入.edmx 檔的 SSDL 區段。 

-   新增**EntitySet**項目加入.edmx 檔 （行 5 到 13） 的 SSDL 區段。 指定下列項目：
    -   只有**名稱**並**EntityType**屬性**EntitySet**指定項目。
    -   實體類型的完整限定名稱用於**EntityType**屬性。
    -   若要執行的 SQL 陳述式中指定**DefiningQuery**項目。

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

-   新增**EntityType**項目加入.edmx SSDL 區段。 底下的檔如下所示。 請注意下列事項：
    -   值**名稱**屬性的值會對應**EntityType**屬性中**EntitySet**項目上方，雖然的完整限定名稱實體類型中使用**EntityType**屬性。
    -   屬性名稱對應中的 SQL 陳述式所傳回的資料行名稱**DefiningQuery**項目 （請見上方）。
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
> 如果您執行的更新版本**更新模型精靈** 對話方塊中，儲存體模型，包括定義查詢，所做的變更將會覆寫。

 

## <a name="add-an-entity-type-to-the-model"></a>將實體類型加入模型

在此步驟中，我們會將實體類型加入概念模型使用 EF 設計工具。  請注意下列事項：

-   **名稱**實體的對應至值**EntityType**屬性中**EntitySet**上述項目。
-   屬性名稱對應中的 SQL 陳述式所傳回的資料行名稱**DefiningQuery**上述項目。
-   在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。

在 EF 設計工具中開啟模型。

-   按兩下 DefiningQueryModel.edmx。
-   假設**是**出現下列訊息：

    ![警告 2](~/ef6/media/warning2.png)

 

Entity Designer 中，提供用於編輯模型的設計介面，會顯示。

-   以滑鼠右鍵按一下設計工具介面，然後選取**加入新**-&gt;**實體...**.
-   指定**GradeReport**為 實體名稱並**CourseID** for **Key 屬性**。
-   以滑鼠右鍵按一下**GradeReport**實體，然後選取**加入新**- &gt; **純量屬性**。
-   變更預設名稱的屬性**FirstName**。
-   新增另一個純量屬性，並指定**LastName**的名稱。
-   新增另一個純量屬性，並指定**級**的名稱。
-   在 **屬性**視窗中，變更**級**的**類型**屬性設**十進位**。
-   選取  **FirstName**並**LastName**屬性。
-   在 **屬性**視窗中，變更**EntityKey**屬性值 **，則為 True**。

如此一來，下列項目已新增至**CSDL** .edmx 檔案的區段。

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>對應至實體類型定義的查詢

在此步驟中，我們將使用的對應詳細資料視窗中將對應的概念和儲存體實體類型。

-   以滑鼠右鍵按一下**GradeReport**設計介面，然後選取實體**資料表對應**。  
    **對應詳細資料** 視窗隨即顯示。
-   選取  **GradeReport**從**&lt;將資料表或檢視&gt;** 下拉式清單中 (位於**資料表**s)。  
    預設對應之間的概念和儲存體**GradeReport**實體類型會出現。  
    ![對應 Details3](~/ef6/media/mappingdetails.png)

如此一來， **EntitySetMapping**項目加入.edmx 檔的 mapping 區段。 

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

 

## <a name="call-the-defining-query-in-your-code"></a>在您的程式碼中呼叫定義的查詢

您現在可以使用，執行定義查詢**GradeReport**實體類型。 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
