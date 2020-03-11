---
title: 定義查詢-EF 設計工具-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
ms.openlocfilehash: b1589dc12ccb50754c2e950932a2d82bc4869f6b
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418795"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="7bd81-102">定義查詢-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="7bd81-102">Defining Query - EF Designer</span></span>
<span data-ttu-id="7bd81-103">本逐步解說示範如何使用 EF 設計工具，將定義查詢和對應的實體類型加入至模型。</span><span class="sxs-lookup"><span data-stu-id="7bd81-103">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="7bd81-104">定義查詢通常用來提供類似資料庫檢視所提供的功能，但此視圖定義于模型中，而不是資料庫。</span><span class="sxs-lookup"><span data-stu-id="7bd81-104">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="7bd81-105">定義查詢可讓您執行 .edmx 檔案之 **DefiningQuery** 元素中所指定的 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="7bd81-105">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="7bd81-106">如需詳細資訊，請參閱[SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)中的 **DefiningQuery** 。</span><span class="sxs-lookup"><span data-stu-id="7bd81-106">For more information, see **DefiningQuery** in the [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="7bd81-107">使用定義查詢時，您也必須在模型中定義實體類型。</span><span class="sxs-lookup"><span data-stu-id="7bd81-107">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="7bd81-108">實體類型是用來呈現由定義查詢所公開的資料。</span><span class="sxs-lookup"><span data-stu-id="7bd81-108">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="7bd81-109">請注意，透過此實體類型呈現的資料是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="7bd81-109">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="7bd81-110">您不能將參數化查詢做為定義查詢來執行。</span><span class="sxs-lookup"><span data-stu-id="7bd81-110">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="7bd81-111">不過，將用於呈現資料的實體類型之 Insert、Update 和 Delete 等函式對應至預存程序，即可更新資料。</span><span class="sxs-lookup"><span data-stu-id="7bd81-111">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="7bd81-112">如需詳細資訊，請參閱 [使用預存程式來插入、更新和刪除](~/ef6/modeling/designer/stored-procedures/cud.md)。</span><span class="sxs-lookup"><span data-stu-id="7bd81-112">For more information, see [Insert, Update, and Delete with Stored Procedures](~/ef6/modeling/designer/stored-procedures/cud.md).</span></span>

<span data-ttu-id="7bd81-113">本主題說明如何執行下列工作。</span><span class="sxs-lookup"><span data-stu-id="7bd81-113">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="7bd81-114">加入定義查詢</span><span class="sxs-lookup"><span data-stu-id="7bd81-114">Add a Defining Query</span></span>
-   <span data-ttu-id="7bd81-115">將實體類型新增至模型</span><span class="sxs-lookup"><span data-stu-id="7bd81-115">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="7bd81-116">將定義查詢對應至實體類型</span><span class="sxs-lookup"><span data-stu-id="7bd81-116">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7bd81-117">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="7bd81-117">Prerequisites</span></span>

<span data-ttu-id="7bd81-118">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="7bd81-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="7bd81-119">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="7bd81-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="7bd81-120">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="7bd81-120">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="7bd81-121">設定專案</span><span class="sxs-lookup"><span data-stu-id="7bd81-121">Set up the Project</span></span>

<span data-ttu-id="7bd81-122">本逐步解說使用 Visual Studio 2012 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="7bd81-122">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="7bd81-123">開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="7bd81-123">Open Visual Studio.</span></span>
-   <span data-ttu-id="7bd81-124">在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。</span><span class="sxs-lookup"><span data-stu-id="7bd81-124">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="7bd81-125">在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台應用程式**] 範本。</span><span class="sxs-lookup"><span data-stu-id="7bd81-125">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="7bd81-126">輸入**DefiningQuerySample**做為專案的名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="7bd81-126">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="7bd81-127">建立以 School 資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="7bd81-127">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="7bd81-128">以滑鼠右鍵按一下方案總管中的專案名稱，指向 [**加入**]，然後按一下 [**新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-128">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="7bd81-129">從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="7bd81-130">在 [檔案名] 中輸入**DefiningQueryModel** ，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-130">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="7bd81-131">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="7bd81-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="7bd81-132">按一下 [新增連接]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-132">Click New Connection.</span></span> <span data-ttu-id="7bd81-133">在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="7bd81-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="7bd81-134">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="7bd81-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="7bd81-135">在 [選擇您的資料庫物件] 對話方塊中，檢查 [**資料表** ] 節點。</span><span class="sxs-lookup"><span data-stu-id="7bd81-135">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="7bd81-136">這會將所有資料表加入**School**模型。</span><span class="sxs-lookup"><span data-stu-id="7bd81-136">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="7bd81-137">按一下 **[完成]** 。</span><span class="sxs-lookup"><span data-stu-id="7bd81-137">Click **Finish**.</span></span>
-   <span data-ttu-id="7bd81-138">在方案總管中，以滑鼠右鍵按一下**DefiningQueryModel .edmx**檔案，然後選取 [**開啟方式**...]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-138">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="7bd81-139">選取 **[XML （文字）編輯器**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-139">Select **XML (Text) Editor**.</span></span>

    ![XML 編輯器](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="7bd81-141">如果出現下列訊息的提示，請按一下 **[是]** ：</span><span class="sxs-lookup"><span data-stu-id="7bd81-141">Click **Yes** if prompted with the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="7bd81-143">加入定義查詢</span><span class="sxs-lookup"><span data-stu-id="7bd81-143">Add a Defining Query</span></span>

<span data-ttu-id="7bd81-144">在此步驟中，我們將使用 XML 編輯器，將定義查詢和實體類型新增至 .edmx 檔案的 SSDL 區段。</span><span class="sxs-lookup"><span data-stu-id="7bd81-144">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="7bd81-145">將 **EntitySet** 元素新增至 .edmx 檔案的 SSDL 區段（第5行到13）。</span><span class="sxs-lookup"><span data-stu-id="7bd81-145">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="7bd81-146">指定下列項目：</span><span class="sxs-lookup"><span data-stu-id="7bd81-146">Specify the following:</span></span>
    -   <span data-ttu-id="7bd81-147">只會指定 **EntitySet** 元素的 **名稱** 和 **EntityType** 屬性。</span><span class="sxs-lookup"><span data-stu-id="7bd81-147">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="7bd81-148">實體類型的完整名稱會在 **EntityType** 屬性中使用。</span><span class="sxs-lookup"><span data-stu-id="7bd81-148">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="7bd81-149">要執行的 SQL 語句是在 **DefiningQuery** 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="7bd81-149">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

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

-   <span data-ttu-id="7bd81-150">將**EntityType**元素新增至 .EDMX 的 SSDL 區段。</span><span class="sxs-lookup"><span data-stu-id="7bd81-150">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="7bd81-151">檔案，如下所示。</span><span class="sxs-lookup"><span data-stu-id="7bd81-151">file as shown below.</span></span> <span data-ttu-id="7bd81-152">請注意：</span><span class="sxs-lookup"><span data-stu-id="7bd81-152">Note the following:</span></span>
    -   <span data-ttu-id="7bd81-153">雖然在**entitytype**屬性中使用實體類型的完整名稱，但**Name**屬性的值會對應到**EntitySet**元素中的**EntityType**屬性值。</span><span class="sxs-lookup"><span data-stu-id="7bd81-153">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="7bd81-154">屬性名稱會對應至**DefiningQuery**元素（如上）中 SQL 語句所傳回的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="7bd81-154">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="7bd81-155">在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="7bd81-155">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

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
> <span data-ttu-id="7bd81-156">如果您稍後執行 [**更新模型嚮導**] 對話方塊，對儲存體模型所做的任何變更（包括定義查詢）都會遭到覆寫。</span><span class="sxs-lookup"><span data-stu-id="7bd81-156">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="7bd81-157">將實體類型新增至模型</span><span class="sxs-lookup"><span data-stu-id="7bd81-157">Add an Entity Type to the Model</span></span>

<span data-ttu-id="7bd81-158">在此步驟中，我們會使用 EF 設計工具，將實體類型新增至概念模型。</span><span class="sxs-lookup"><span data-stu-id="7bd81-158">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="7bd81-159"> 請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="7bd81-159"> Note the following:</span></span>

-   <span data-ttu-id="7bd81-160">實體的**名稱**會對應至上述**EntitySet**元素中**EntityType**屬性的值。</span><span class="sxs-lookup"><span data-stu-id="7bd81-160">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="7bd81-161">屬性名稱會對應至上述**DefiningQuery**元素中 SQL 語句所傳回的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="7bd81-161">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="7bd81-162">在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="7bd81-162">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="7bd81-163">在 EF 設計工具中開啟模型。</span><span class="sxs-lookup"><span data-stu-id="7bd81-163">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="7bd81-164">按兩下 [DefiningQueryModel]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-164">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="7bd81-165">在下列訊息中說「**是**」：</span><span class="sxs-lookup"><span data-stu-id="7bd81-165">Say **Yes** to the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

<span data-ttu-id="7bd81-167">會顯示 Entity Designer （提供編輯模型的設計介面）。</span><span class="sxs-lookup"><span data-stu-id="7bd81-167">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="7bd81-168">以滑鼠右鍵按一下設計工具介面，然後選取 [**加入新**的-] &gt;[**實體**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-168">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="7bd81-169">針對 [機構名稱] 和 [索引鍵]**屬性**的**CourseID**指定**GradeReport** 。</span><span class="sxs-lookup"><span data-stu-id="7bd81-169">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="7bd81-170">以滑鼠右鍵按一下 [ **GradeReport** ] 實體，然後選取 [**加入新**的-] &gt; 純量**屬性**。</span><span class="sxs-lookup"><span data-stu-id="7bd81-170">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="7bd81-171">將屬性的預設名稱變更為**FirstName**。</span><span class="sxs-lookup"><span data-stu-id="7bd81-171">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="7bd81-172">新增另一個純量屬性，並指定**LastName**做為名稱。</span><span class="sxs-lookup"><span data-stu-id="7bd81-172">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="7bd81-173">新增另一個純量屬性，並指定名稱的**等級**。</span><span class="sxs-lookup"><span data-stu-id="7bd81-173">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="7bd81-174">在 [**屬性**] 視窗中，將 [**年級**的**類型**] 屬性變更為 [**十進位**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-174">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="7bd81-175">選取 [ **FirstName** ] 和 [ **LastName** ] 屬性。</span><span class="sxs-lookup"><span data-stu-id="7bd81-175">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="7bd81-176">在 [**屬性**] 視窗中，將 [ **EntityKey** ] 屬性值變更為 [ **True**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-176">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="7bd81-177">因此，下列專案已新增至 .edmx 檔案的**CSDL**區段中。</span><span class="sxs-lookup"><span data-stu-id="7bd81-177">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="7bd81-178">將定義查詢對應至實體類型</span><span class="sxs-lookup"><span data-stu-id="7bd81-178">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="7bd81-179">在此步驟中，我們將使用 [對應詳細資料] 視窗來對應概念和儲存體實體類型。</span><span class="sxs-lookup"><span data-stu-id="7bd81-179">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="7bd81-180">以滑鼠右鍵按一下設計介面上的 [ **GradeReport** ] 實體，然後選取 [**資料表對應**]。</span><span class="sxs-lookup"><span data-stu-id="7bd81-180">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="7bd81-181">[**對應詳細資料**] 視窗隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="7bd81-181">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="7bd81-182">從 [ **&lt;加入資料表] 或 [&gt;** ] 下拉式清單中選取 [ **GradeReport** ] （位於 [**資料表**s] 底下）。</span><span class="sxs-lookup"><span data-stu-id="7bd81-182">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="7bd81-183">[概念] 和 [儲存體**GradeReport** ] 實體類型之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="7bd81-183">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="7bd81-184">![對應 Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="7bd81-184">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="7bd81-185">因此， **EntitySetMapping** 元素會新增至 .edmx 檔的 mapping 區段。</span><span class="sxs-lookup"><span data-stu-id="7bd81-185">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

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

-   <span data-ttu-id="7bd81-186">編譯應用程式。</span><span class="sxs-lookup"><span data-stu-id="7bd81-186">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="7bd81-187">在您的程式碼中呼叫定義查詢</span><span class="sxs-lookup"><span data-stu-id="7bd81-187">Call the Defining Query in your Code</span></span>

<span data-ttu-id="7bd81-188">您現在可以使用**GradeReport**實體類型來執行定義查詢。</span><span class="sxs-lookup"><span data-stu-id="7bd81-188">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
