---
title: 定義查詢-EF Designer-EF6
description: 在 Entity Framework 6 中定義查詢-EF 設計工具
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: b5d24b9f6c047eb8681a0343c768d1ffa70ca6b8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070337"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="53ad6-103">定義查詢-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="53ad6-103">Defining Query - EF Designer</span></span>
<span data-ttu-id="53ad6-104">本逐步解說示範如何使用 EF 設計工具將定義查詢和對應的實體類型加入至模型。</span><span class="sxs-lookup"><span data-stu-id="53ad6-104">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="53ad6-105">定義查詢通常用來提供類似于資料庫檢視所提供的功能，但該視圖是在模型中定義，而不是在資料庫中定義。</span><span class="sxs-lookup"><span data-stu-id="53ad6-105">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="53ad6-106">定義查詢可讓您執行在 .edmx 檔的 **DefiningQuery**元素中指定的 SQL 語句   。</span><span class="sxs-lookup"><span data-stu-id="53ad6-106">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="53ad6-107">如需詳細資訊，請參閱[SSDL 規格](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)中的 **DefiningQuery** 。</span><span class="sxs-lookup"><span data-stu-id="53ad6-107">For more information, see **DefiningQuery** in the [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="53ad6-108">使用定義查詢時，您也必須在模型中定義實體類型。</span><span class="sxs-lookup"><span data-stu-id="53ad6-108">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="53ad6-109">實體類型是用來呈現定義查詢所公開的資料。</span><span class="sxs-lookup"><span data-stu-id="53ad6-109">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="53ad6-110">請注意，透過此實體類型呈現的資料是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="53ad6-110">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="53ad6-111">您不能將參數化查詢做為定義查詢來執行。</span><span class="sxs-lookup"><span data-stu-id="53ad6-111">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="53ad6-112">不過，將用於呈現資料的實體類型之 Insert、Update 和 Delete 等函式對應至預存程序，即可更新資料。</span><span class="sxs-lookup"><span data-stu-id="53ad6-112">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="53ad6-113">如需詳細資訊，請參閱 [使用預存程式插入、更新及刪除](xref:ef6/modeling/designer/stored-procedures/cud)。</span><span class="sxs-lookup"><span data-stu-id="53ad6-113">For more information, see [Insert, Update, and Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).</span></span>

<span data-ttu-id="53ad6-114">本主題說明如何執行下列工作。</span><span class="sxs-lookup"><span data-stu-id="53ad6-114">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="53ad6-115">新增定義查詢</span><span class="sxs-lookup"><span data-stu-id="53ad6-115">Add a Defining Query</span></span>
-   <span data-ttu-id="53ad6-116">將實體類型加入至模型</span><span class="sxs-lookup"><span data-stu-id="53ad6-116">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="53ad6-117">將定義查詢對應至實體類型</span><span class="sxs-lookup"><span data-stu-id="53ad6-117">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="53ad6-118">必要條件</span><span class="sxs-lookup"><span data-stu-id="53ad6-118">Prerequisites</span></span>

<span data-ttu-id="53ad6-119">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="53ad6-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="53ad6-120">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="53ad6-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="53ad6-121">[School 範例資料庫](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="53ad6-121">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="53ad6-122">設定專案</span><span class="sxs-lookup"><span data-stu-id="53ad6-122">Set up the Project</span></span>

<span data-ttu-id="53ad6-123">本逐步解說使用 Visual Studio 2012 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="53ad6-123">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="53ad6-124">開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="53ad6-124">Open Visual Studio.</span></span>
-   <span data-ttu-id="53ad6-125">在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。</span><span class="sxs-lookup"><span data-stu-id="53ad6-125">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="53ad6-126">在左窗格中，按一下 [ **Visual \# C**]，然後選取 [**主控台應用程式**] 範本。</span><span class="sxs-lookup"><span data-stu-id="53ad6-126">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="53ad6-127">輸入 **DefiningQuerySample** 做為專案的名稱，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="53ad6-127">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="53ad6-128">建立以 School 資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="53ad6-128">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="53ad6-129">在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [ **加入**]，然後按一下 [ **新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="53ad6-130">從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="53ad6-131">輸入 **DefiningQueryModel** 的檔案名，然後按一下 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-131">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="53ad6-132">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="53ad6-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="53ad6-133">按一下 [新增連接]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-133">Click New Connection.</span></span> <span data-ttu-id="53ad6-134">在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如\*\* (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School\*\*   作為資料庫名稱，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="53ad6-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="53ad6-135">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="53ad6-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="53ad6-136">在 [選擇您的資料庫物件] 對話方塊中，檢查 [**資料表**]   節點。</span><span class="sxs-lookup"><span data-stu-id="53ad6-136">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="53ad6-137">這會將所有的資料表新增至 **School** 模型。</span><span class="sxs-lookup"><span data-stu-id="53ad6-137">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="53ad6-138">按一下 **[完成]**。</span><span class="sxs-lookup"><span data-stu-id="53ad6-138">Click **Finish**.</span></span>
-   <span data-ttu-id="53ad6-139">在方案總管中，以滑鼠右鍵按一下 **DefiningQueryModel .edmx** 檔案，然後選取 [ **開啟方式**...]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-139">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="53ad6-140">選取 [ **XML (Text) 編輯器**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-140">Select **XML (Text) Editor**.</span></span>

    ![XML 編輯器](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="53ad6-142">如果出現下列訊息的提示，請按一下 **[是]** ：</span><span class="sxs-lookup"><span data-stu-id="53ad6-142">Click **Yes** if prompted with the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="53ad6-144">新增定義查詢</span><span class="sxs-lookup"><span data-stu-id="53ad6-144">Add a Defining Query</span></span>

<span data-ttu-id="53ad6-145">在這個步驟中，我們將使用 XML 編輯器，將定義查詢和實體類型加入 .edmx 檔的 SSDL 區段。</span><span class="sxs-lookup"><span data-stu-id="53ad6-145">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="53ad6-146">將 **EntitySet**   元素加入至 .EDMX 檔案的 SSDL 區段， (第5行到 13) 。</span><span class="sxs-lookup"><span data-stu-id="53ad6-146">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="53ad6-147">指定下列項目：</span><span class="sxs-lookup"><span data-stu-id="53ad6-147">Specify the following:</span></span>
    -   <span data-ttu-id="53ad6-148">只 **Name**    **EntityType**   會指定 **EntitySet**元素的 Name 和 EntityType 屬性   。</span><span class="sxs-lookup"><span data-stu-id="53ad6-148">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="53ad6-149">在 **EntityType**屬性中，會使用實體類型的完整名稱   。</span><span class="sxs-lookup"><span data-stu-id="53ad6-149">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="53ad6-150">要執行的 SQL 語句是在 **DefiningQuery**   元素中指定。</span><span class="sxs-lookup"><span data-stu-id="53ad6-150">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

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

-   <span data-ttu-id="53ad6-151">將 **EntityType** 元素加入 .EDMX 的 SSDL 區段。</span><span class="sxs-lookup"><span data-stu-id="53ad6-151">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="53ad6-152">檔案，如下所示。</span><span class="sxs-lookup"><span data-stu-id="53ad6-152">file as shown below.</span></span> <span data-ttu-id="53ad6-153">請注意：</span><span class="sxs-lookup"><span data-stu-id="53ad6-153">Note the following:</span></span>
    -   <span data-ttu-id="53ad6-154">**Name**屬性的值會對應到上述**EntitySet**元素中的**EntityType**屬性值，雖然在**EntityType**屬性中使用實體類型的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="53ad6-154">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="53ad6-155">屬性名稱會對應到上述)  (**DefiningQuery** 元素中 SQL 語句所傳回的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="53ad6-155">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="53ad6-156">在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="53ad6-156">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

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
> <span data-ttu-id="53ad6-157">如果您稍後執行 [ **更新模型嚮導** ] 對話方塊，將會覆寫對儲存體模型所做的任何變更，包括定義查詢。</span><span class="sxs-lookup"><span data-stu-id="53ad6-157">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="53ad6-158">將實體類型加入至模型</span><span class="sxs-lookup"><span data-stu-id="53ad6-158">Add an Entity Type to the Model</span></span>

<span data-ttu-id="53ad6-159">在這個步驟中，我們會使用 EF 設計工具將實體類型加入至概念模型。</span><span class="sxs-lookup"><span data-stu-id="53ad6-159">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="53ad6-160">請注意：</span><span class="sxs-lookup"><span data-stu-id="53ad6-160"> Note the following:</span></span>

-   <span data-ttu-id="53ad6-161">實體的**名稱**會對應至上述**EntitySet**元素中的**EntityType**屬性值。</span><span class="sxs-lookup"><span data-stu-id="53ad6-161">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="53ad6-162">屬性名稱會對應到上述 **DefiningQuery** 元素中 SQL 語句所傳回的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="53ad6-162">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="53ad6-163">在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="53ad6-163">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="53ad6-164">在 EF 設計工具中開啟模型。</span><span class="sxs-lookup"><span data-stu-id="53ad6-164">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="53ad6-165">按兩下 DefiningQueryModel。</span><span class="sxs-lookup"><span data-stu-id="53ad6-165">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="53ad6-166">在下列訊息中說出 **Yes** ：</span><span class="sxs-lookup"><span data-stu-id="53ad6-166">Say **Yes** to the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

<span data-ttu-id="53ad6-168">隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。</span><span class="sxs-lookup"><span data-stu-id="53ad6-168">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="53ad6-169">以滑鼠右鍵按一下設計工具介面，然後選取 [**加入新**的 - &gt; **實體 ...**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-169">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="53ad6-170">針對 [索引**鍵] 屬性**指定 [ **GradeReport** ] 做為 [機構名稱] 和 [ **CourseID** ]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-170">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="53ad6-171">以滑鼠右鍵按一下 [ **GradeReport** ] 實體，然後選取 [**加入新**的純量 - &gt; **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-171">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="53ad6-172">將屬性的預設名稱變更為 **FirstName**。</span><span class="sxs-lookup"><span data-stu-id="53ad6-172">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="53ad6-173">加入其他純量屬性，並指定 **LastName** 作為名稱。</span><span class="sxs-lookup"><span data-stu-id="53ad6-173">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="53ad6-174">新增另一個純量屬性，並指定名稱的 **等級** 。</span><span class="sxs-lookup"><span data-stu-id="53ad6-174">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="53ad6-175">在 [ **屬性** ] 視窗中，將 [ **等級**的 **類型** ] 屬性變更為 [ **十進位**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-175">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="53ad6-176">選取 [ **FirstName** ] 和 [ **LastName** ] 屬性。</span><span class="sxs-lookup"><span data-stu-id="53ad6-176">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="53ad6-177">在 [ **屬性** ] 視窗中，將 [ **EntityKey** ] 屬性值變更為 [ **True**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-177">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="53ad6-178">因此，已將下列專案加入 .edmx 檔的 **CSDL** 區段。</span><span class="sxs-lookup"><span data-stu-id="53ad6-178">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="53ad6-179">將定義查詢對應至實體類型</span><span class="sxs-lookup"><span data-stu-id="53ad6-179">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="53ad6-180">在這個步驟中，我們將使用 [對應詳細資料] 視窗來對應概念和儲存實體類型。</span><span class="sxs-lookup"><span data-stu-id="53ad6-180">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="53ad6-181">以滑鼠右鍵按一下設計介面上的 [ **GradeReport** ] 實體，然後選取 [ **資料表對應**]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-181">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="53ad6-182">[ **對應詳細資料** ] 視窗隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="53ad6-182">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="53ad6-183">從 [**資料表**s) ] 下的 [ \*\* &lt; 加入資料表或 &gt; 視圖\*\*] 下拉式清單 (選取 [ **GradeReport** ]。</span><span class="sxs-lookup"><span data-stu-id="53ad6-183">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="53ad6-184">概念和儲存 **GradeReport** 實體類型之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="53ad6-184">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="53ad6-185">![對應 Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="53ad6-185">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="53ad6-186">如此一來，就會將**EntitySetMapping**   元素加入至 .edmx 檔案的 mapping 區段。</span><span class="sxs-lookup"><span data-stu-id="53ad6-186">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

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

-   <span data-ttu-id="53ad6-187">編譯應用程式。</span><span class="sxs-lookup"><span data-stu-id="53ad6-187">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="53ad6-188">在您的程式碼中呼叫定義查詢</span><span class="sxs-lookup"><span data-stu-id="53ad6-188">Call the Defining Query in your Code</span></span>

<span data-ttu-id="53ad6-189">您現在可以使用 **GradeReport** 實體類型來執行定義查詢。</span><span class="sxs-lookup"><span data-stu-id="53ad6-189">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
