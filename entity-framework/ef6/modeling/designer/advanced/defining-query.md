---
title: 定義查詢-EF 設計工具-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
ms.openlocfilehash: 60d5310589bb9bc3fdb971673422e80537357e55
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996303"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="4bae3-102">定義查詢-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="4bae3-102">Defining Query - EF Designer</span></span>
<span data-ttu-id="4bae3-103">本逐步解說示範如何將定義要使用 EF 設計工具的模型類型的查詢和對應的實體。</span><span class="sxs-lookup"><span data-stu-id="4bae3-103">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="4bae3-104">定義查詢通常用來提供資料庫檢視，提供類似功能，但該檢視定義在模型中，不是資料庫。</span><span class="sxs-lookup"><span data-stu-id="4bae3-104">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="4bae3-105">定義查詢可讓您執行 SQL 陳述式中指定**DefiningQuery** .edmx 檔案的項目。</span><span class="sxs-lookup"><span data-stu-id="4bae3-105">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="4bae3-106">如需詳細資訊，請參閱 < **DefiningQuery**中[SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="4bae3-106">For more information, see **DefiningQuery** in the [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="4bae3-107">使用定義查詢時，您也必須在模型中定義實體類型。</span><span class="sxs-lookup"><span data-stu-id="4bae3-107">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="4bae3-108">實體類型用來呈現所定義的查詢公開的資料。</span><span class="sxs-lookup"><span data-stu-id="4bae3-108">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="4bae3-109">請注意，透過這個實體型別顯示的資料是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="4bae3-109">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="4bae3-110">您不能將參數化查詢做為定義查詢來執行。</span><span class="sxs-lookup"><span data-stu-id="4bae3-110">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="4bae3-111">不過，將用於呈現資料的實體類型之 Insert、Update 和 Delete 等函式對應至預存程序，即可更新資料。</span><span class="sxs-lookup"><span data-stu-id="4bae3-111">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="4bae3-112">如需詳細資訊，請參閱 <<c0> [ 插入、 更新和刪除預存程序與](~/ef6/modeling/designer/stored-procedures/cud.md)。</span><span class="sxs-lookup"><span data-stu-id="4bae3-112">For more information, see [Insert, Update, and Delete with Stored Procedures](~/ef6/modeling/designer/stored-procedures/cud.md).</span></span>

<span data-ttu-id="4bae3-113">本主題說明如何執行下列工作。</span><span class="sxs-lookup"><span data-stu-id="4bae3-113">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="4bae3-114">加入定義查詢</span><span class="sxs-lookup"><span data-stu-id="4bae3-114">Add a Defining Query</span></span>
-   <span data-ttu-id="4bae3-115">將實體類型加入模型</span><span class="sxs-lookup"><span data-stu-id="4bae3-115">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="4bae3-116">對應至實體類型定義的查詢</span><span class="sxs-lookup"><span data-stu-id="4bae3-116">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4bae3-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="4bae3-117">Prerequisites</span></span>

<span data-ttu-id="4bae3-118">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="4bae3-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="4bae3-119">新版的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="4bae3-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="4bae3-120">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="4bae3-120">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="4bae3-121">設定專案</span><span class="sxs-lookup"><span data-stu-id="4bae3-121">Set up the Project</span></span>

<span data-ttu-id="4bae3-122">本逐步解說使用 Visual Studio 2012 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="4bae3-122">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="4bae3-123">開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="4bae3-123">Open Visual Studio.</span></span>
-   <span data-ttu-id="4bae3-124">在 [檔案]  功能表中，指向 [新增] ，然後按一下 [專案] 。</span><span class="sxs-lookup"><span data-stu-id="4bae3-124">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="4bae3-125">在左窗格中，按一下**Visual C\#**，然後選取**主控台應用程式**範本。</span><span class="sxs-lookup"><span data-stu-id="4bae3-125">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="4bae3-126">請輸入**DefiningQuerySample**做為名稱的專案，然後按一下**確定**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-126">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="4bae3-127">建立 School 資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="4bae3-127">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="4bae3-128">以滑鼠右鍵按一下方案總管 中的專案名稱，指向**新增**，然後按一下**新項目**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-128">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="4bae3-129">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。</span><span class="sxs-lookup"><span data-stu-id="4bae3-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="4bae3-130">請輸入**DefiningQueryModel.edmx**為檔案名稱，然後按一下**新增**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-130">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="4bae3-131">在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="4bae3-132">按一下 新增連接。</span><span class="sxs-lookup"><span data-stu-id="4bae3-132">Click New Connection.</span></span> <span data-ttu-id="4bae3-133">在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="4bae3-134">[選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。</span><span class="sxs-lookup"><span data-stu-id="4bae3-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="4bae3-135">在 [選擇您的資料庫物件] 對話方塊中，檢查**資料表**節點。</span><span class="sxs-lookup"><span data-stu-id="4bae3-135">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="4bae3-136">這會新增至所有資料表**學校**模型。</span><span class="sxs-lookup"><span data-stu-id="4bae3-136">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="4bae3-137">按一下 [ **完成**]。</span><span class="sxs-lookup"><span data-stu-id="4bae3-137">Click **Finish**.</span></span>
-   <span data-ttu-id="4bae3-138">在 [方案總管] 中，以滑鼠右鍵按一下**DefiningQueryModel.edmx**檔案，然後選取**開啟方式...**.</span><span class="sxs-lookup"><span data-stu-id="4bae3-138">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="4bae3-139">選取  **XML （文字） 編輯器**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-139">Select **XML (Text) Editor**.</span></span>

    ![XMLEditor](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="4bae3-141">按一下 **是**出現提示時出現下列訊息：</span><span class="sxs-lookup"><span data-stu-id="4bae3-141">Click **Yes** if prompted with the following message:</span></span>

    ![Warning2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="4bae3-143">加入定義查詢</span><span class="sxs-lookup"><span data-stu-id="4bae3-143">Add a Defining Query</span></span>

<span data-ttu-id="4bae3-144">在此步驟中，我們將使用 XML 編輯器，若要將定義查詢並的實體類型加入.edmx 檔的 SSDL 區段。</span><span class="sxs-lookup"><span data-stu-id="4bae3-144">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="4bae3-145">新增**EntitySet**項目加入.edmx 檔 （行 5 到 13） 的 SSDL 區段。</span><span class="sxs-lookup"><span data-stu-id="4bae3-145">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="4bae3-146">指定下列項目：</span><span class="sxs-lookup"><span data-stu-id="4bae3-146">Specify the following:</span></span>
    -   <span data-ttu-id="4bae3-147">只有**名稱**並**EntityType**屬性**EntitySet**指定項目。</span><span class="sxs-lookup"><span data-stu-id="4bae3-147">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="4bae3-148">實體類型的完整限定名稱用於**EntityType**屬性。</span><span class="sxs-lookup"><span data-stu-id="4bae3-148">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="4bae3-149">若要執行的 SQL 陳述式中指定**DefiningQuery**項目。</span><span class="sxs-lookup"><span data-stu-id="4bae3-149">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

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

-   <span data-ttu-id="4bae3-150">新增**EntityType**項目加入.edmx SSDL 區段。</span><span class="sxs-lookup"><span data-stu-id="4bae3-150">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="4bae3-151">底下的檔如下所示。</span><span class="sxs-lookup"><span data-stu-id="4bae3-151">file as shown below.</span></span> <span data-ttu-id="4bae3-152">請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="4bae3-152">Note the following:</span></span>
    -   <span data-ttu-id="4bae3-153">值**名稱**屬性的值會對應**EntityType**屬性中**EntitySet**項目上方，雖然的完整限定名稱實體類型中使用**EntityType**屬性。</span><span class="sxs-lookup"><span data-stu-id="4bae3-153">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="4bae3-154">屬性名稱對應中的 SQL 陳述式所傳回的資料行名稱**DefiningQuery**項目 （請見上方）。</span><span class="sxs-lookup"><span data-stu-id="4bae3-154">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="4bae3-155">在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="4bae3-155">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

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
> <span data-ttu-id="4bae3-156">如果您執行的更新版本**更新模型精靈** 對話方塊中，儲存體模型，包括定義查詢，所做的變更將會覆寫。</span><span class="sxs-lookup"><span data-stu-id="4bae3-156">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="4bae3-157">將實體類型加入模型</span><span class="sxs-lookup"><span data-stu-id="4bae3-157">Add an Entity Type to the Model</span></span>

<span data-ttu-id="4bae3-158">在此步驟中，我們會將實體類型加入概念模型使用 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="4bae3-158">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span>  <span data-ttu-id="4bae3-159">請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="4bae3-159">Note the following:</span></span>

-   <span data-ttu-id="4bae3-160">**名稱**實體的對應至值**EntityType**屬性中**EntitySet**上述項目。</span><span class="sxs-lookup"><span data-stu-id="4bae3-160">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="4bae3-161">屬性名稱對應中的 SQL 陳述式所傳回的資料行名稱**DefiningQuery**上述項目。</span><span class="sxs-lookup"><span data-stu-id="4bae3-161">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="4bae3-162">在本範例中，實體索引鍵由三個屬性組成，以確保唯一的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="4bae3-162">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="4bae3-163">在 EF 設計工具中開啟模型。</span><span class="sxs-lookup"><span data-stu-id="4bae3-163">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="4bae3-164">按兩下 DefiningQueryModel.edmx。</span><span class="sxs-lookup"><span data-stu-id="4bae3-164">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="4bae3-165">假設**是**出現下列訊息：</span><span class="sxs-lookup"><span data-stu-id="4bae3-165">Say **Yes** to the following message:</span></span>

    ![Warning2](~/ef6/media/warning2.png)

 

<span data-ttu-id="4bae3-167">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="4bae3-167">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="4bae3-168">以滑鼠右鍵按一下設計工具介面，然後選取**加入新**-&gt;**實體...**.</span><span class="sxs-lookup"><span data-stu-id="4bae3-168">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="4bae3-169">指定**GradeReport**為 實體名稱並**CourseID** for **Key 屬性**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-169">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="4bae3-170">以滑鼠右鍵按一下**GradeReport**實體，然後選取**加入新**- &gt; **純量屬性**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-170">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="4bae3-171">變更預設名稱的屬性**FirstName**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-171">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="4bae3-172">新增另一個純量屬性，並指定**LastName**的名稱。</span><span class="sxs-lookup"><span data-stu-id="4bae3-172">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="4bae3-173">新增另一個純量屬性，並指定**級**的名稱。</span><span class="sxs-lookup"><span data-stu-id="4bae3-173">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="4bae3-174">在 **屬性**視窗中，變更**級**的**類型**屬性設**十進位**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-174">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="4bae3-175">選取  **FirstName**並**LastName**屬性。</span><span class="sxs-lookup"><span data-stu-id="4bae3-175">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="4bae3-176">在 **屬性**視窗中，變更**EntityKey**屬性值 **，則為 True**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-176">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="4bae3-177">如此一來，下列項目已新增至**CSDL** .edmx 檔案的區段。</span><span class="sxs-lookup"><span data-stu-id="4bae3-177">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="4bae3-178">對應至實體類型定義的查詢</span><span class="sxs-lookup"><span data-stu-id="4bae3-178">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="4bae3-179">在此步驟中，我們將使用的對應詳細資料視窗中將對應的概念和儲存體實體類型。</span><span class="sxs-lookup"><span data-stu-id="4bae3-179">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="4bae3-180">以滑鼠右鍵按一下**GradeReport**設計介面，然後選取實體**資料表對應**。</span><span class="sxs-lookup"><span data-stu-id="4bae3-180">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="4bae3-181">**對應詳細資料** 視窗隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="4bae3-181">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="4bae3-182">選取  **GradeReport**從**&lt;將資料表或檢視&gt;** 下拉式清單中 (位於**資料表**s)。</span><span class="sxs-lookup"><span data-stu-id="4bae3-182">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="4bae3-183">預設對應之間的概念和儲存體**GradeReport**實體類型會出現。</span><span class="sxs-lookup"><span data-stu-id="4bae3-183">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="4bae3-184">![MappingDetails3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="4bae3-184">![MappingDetails3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="4bae3-185">如此一來， **EntitySetMapping**項目加入.edmx 檔的 mapping 區段。</span><span class="sxs-lookup"><span data-stu-id="4bae3-185">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

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

-   <span data-ttu-id="4bae3-186">編譯應用程式。</span><span class="sxs-lookup"><span data-stu-id="4bae3-186">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="4bae3-187">在您的程式碼中呼叫定義的查詢</span><span class="sxs-lookup"><span data-stu-id="4bae3-187">Call the Defining Query in your Code</span></span>

<span data-ttu-id="4bae3-188">您現在可以使用，執行定義查詢**GradeReport**實體類型。</span><span class="sxs-lookup"><span data-stu-id="4bae3-188">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
