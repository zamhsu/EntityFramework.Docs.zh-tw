---
title: 設計工具的實體分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: ba1895ae491cec909ff88a8784eea82f1876f595
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490842"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="ea02c-102">分割的實體設計工具</span><span class="sxs-lookup"><span data-stu-id="ea02c-102">Designer Entity Splitting</span></span>
<span data-ttu-id="ea02c-103">本逐步解說示範如何透過修改模型使用 Entity Framework Designer （EF 設計工具），將實體類型對應至兩個資料表。</span><span class="sxs-lookup"><span data-stu-id="ea02c-103">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="ea02c-104">當多個資料表共用共同的索引鍵時，您可以將實體對應至多個資料表。</span><span class="sxs-lookup"><span data-stu-id="ea02c-104">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="ea02c-105">適用於將實體類型對應至兩個資料表的概念，很容易擴大為將實體類型對應至兩個以上的資料表。</span><span class="sxs-lookup"><span data-stu-id="ea02c-105">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="ea02c-106">下圖顯示使用 EF 設計工具時所使用的主視窗。</span><span class="sxs-lookup"><span data-stu-id="ea02c-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="ea02c-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="ea02c-108">Prerequisites</span></span>

<span data-ttu-id="ea02c-109">Visual Studio 2012 或 Visual Studio 2010、 Ultimate、 Premium、 Professional 或 Web Express edition。</span><span class="sxs-lookup"><span data-stu-id="ea02c-109">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="ea02c-110">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="ea02c-110">Create the Database</span></span>

<span data-ttu-id="ea02c-111">Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="ea02c-111">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="ea02c-112">如果您使用 Visual Studio 2012，則您將會建立新的 LocalDB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="ea02c-112">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="ea02c-113">如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="ea02c-113">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="ea02c-114">第一次我們將建立一個資料庫具有兩個資料表，我們要結合成單一的實體。</span><span class="sxs-lookup"><span data-stu-id="ea02c-114">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="ea02c-115">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea02c-115">Open Visual Studio</span></span>
-   <span data-ttu-id="ea02c-116">**檢視-&gt;伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="ea02c-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="ea02c-117">以滑鼠右鍵按一下**資料連線-&gt;新增連接...**</span><span class="sxs-lookup"><span data-stu-id="ea02c-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="ea02c-118">如果您尚未連線至資料庫從伺服器總管之前您必須選取**Microsoft SQL Server**做為資料來源</span><span class="sxs-lookup"><span data-stu-id="ea02c-118">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="ea02c-119">連接到 LocalDB 或 SQL Express，何者而定，您已安裝</span><span class="sxs-lookup"><span data-stu-id="ea02c-119">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="ea02c-120">請輸入**EntitySplitting**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="ea02c-120">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="ea02c-121">選取  **確定**而且會要求您想要建立新資料庫，請選取**是**</span><span class="sxs-lookup"><span data-stu-id="ea02c-121">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="ea02c-122">在 伺服器總管現在會顯示新的資料庫</span><span class="sxs-lookup"><span data-stu-id="ea02c-122">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="ea02c-123">如果您使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="ea02c-123">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="ea02c-124">以滑鼠右鍵按一下伺服器總管 中的資料庫，然後選取**新查詢**</span><span class="sxs-lookup"><span data-stu-id="ea02c-124">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="ea02c-125">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="ea02c-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="ea02c-126">如果您使用 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="ea02c-126">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="ea02c-127">選取 **資料集&gt;Transact SQL 編輯器-&gt;新增查詢連接...**</span><span class="sxs-lookup"><span data-stu-id="ea02c-127">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="ea02c-128">請輸入 **。\\SQLEXPRESS**作為伺服器名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="ea02c-128">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="ea02c-129">選取  **EntitySplitting**資料庫從下拉式清單頂端的查詢編輯器</span><span class="sxs-lookup"><span data-stu-id="ea02c-129">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="ea02c-130">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**執行 SQL**</span><span class="sxs-lookup"><span data-stu-id="ea02c-130">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a><span data-ttu-id="ea02c-131">建立專案</span><span class="sxs-lookup"><span data-stu-id="ea02c-131">Create the Project</span></span>

-   <span data-ttu-id="ea02c-132">在 [檔案]  功能表中，指向 [新增] ，然後按一下 [專案] 。</span><span class="sxs-lookup"><span data-stu-id="ea02c-132">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="ea02c-133">在左窗格中，按一下**Visual C\#**，然後選取**主控台應用程式**範本。</span><span class="sxs-lookup"><span data-stu-id="ea02c-133">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="ea02c-134">請輸入**MapEntityToTablesSample**做為名稱的專案，然後按一下**確定**。</span><span class="sxs-lookup"><span data-stu-id="ea02c-134">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="ea02c-135">按一下  **No**如果系統提示您儲存在第一節中建立的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="ea02c-135">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="ea02c-136">建立資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="ea02c-136">Create a Model based on the Database</span></span>

-   <span data-ttu-id="ea02c-137">以滑鼠右鍵按一下方案總管 中的專案名稱，指向**新增**，然後按一下**新項目**。</span><span class="sxs-lookup"><span data-stu-id="ea02c-137">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="ea02c-138">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。</span><span class="sxs-lookup"><span data-stu-id="ea02c-138">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="ea02c-139">請輸入**MapEntityToTablesModel.edmx**為檔案名稱，然後按一下**新增**。</span><span class="sxs-lookup"><span data-stu-id="ea02c-139">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="ea02c-140">在 [選擇模型內容] 對話方塊中，選取**從資料庫產生**，然後按一下 [**下一步]。**</span><span class="sxs-lookup"><span data-stu-id="ea02c-140">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="ea02c-141">選取 [ **EntitySplitting**連線，從下拉式清單，然後按一下**下一步]**。</span><span class="sxs-lookup"><span data-stu-id="ea02c-141">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="ea02c-142">在 [選擇您的資料庫物件] 對話方塊中，核取方塊旁**資料表**節點。</span><span class="sxs-lookup"><span data-stu-id="ea02c-142">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="ea02c-143">這會新增中的所有資料表**EntitySplitting**模型的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ea02c-143">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="ea02c-144">按一下 [ **完成**]。</span><span class="sxs-lookup"><span data-stu-id="ea02c-144">Click **Finish**.</span></span>

<span data-ttu-id="ea02c-145">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="ea02c-145">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="ea02c-146">將實體對應至兩個資料表</span><span class="sxs-lookup"><span data-stu-id="ea02c-146">Map an Entity to Two Tables</span></span>

<span data-ttu-id="ea02c-147">在此步驟中，我們將會更新**人員**將資料從實體型別**人員**並**PersonInfo**資料表。</span><span class="sxs-lookup"><span data-stu-id="ea02c-147">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="ea02c-148">選取**電子郵件**並**Phone**的屬性 \* \* PersonInfo \* \* 實體，然後按**Ctrl + X**索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ea02c-148">Select the **Email** and **Phone** properties of the \*\*PersonInfo \*\*entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="ea02c-149">選取 \* \* 人員 \* \* 實體，然後按**Ctrl + V**索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ea02c-149">Select the \*\*Person \*\*entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="ea02c-150">在設計介面上選取**PersonInfo**實體，然後按**刪除**鍵盤上的按鈕。</span><span class="sxs-lookup"><span data-stu-id="ea02c-150">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="ea02c-151">按一下 **否**詢問您是否要移除時**PersonInfo**資料表從模型中，我們即將將其對應至**人員**實體。</span><span class="sxs-lookup"><span data-stu-id="ea02c-151">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![刪除資料表](~/ef6/media/deletetables.png)

<span data-ttu-id="ea02c-153">接下來的步驟需要**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="ea02c-153">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="ea02c-154">如果看不到這個視窗，以滑鼠右鍵按一下設計介面，然後選取**對應詳細資料**。</span><span class="sxs-lookup"><span data-stu-id="ea02c-154">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="ea02c-155">選取  **Person**實體類型，然後按一下**&lt;將資料表或檢視&gt;** 中**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="ea02c-155">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="ea02c-156">選取 \* \* PersonInfo \* \* 從下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="ea02c-156">Select \*\*PersonInfo \*\* from the drop-down list.</span></span>
    <span data-ttu-id="ea02c-157">**對應詳細資料**視窗會更新成預設資料行對應，而是適合我們的案例。</span><span class="sxs-lookup"><span data-stu-id="ea02c-157">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="ea02c-158">**Person**實體類型現在已對應至**人員**並**PersonInfo**資料表。</span><span class="sxs-lookup"><span data-stu-id="ea02c-158">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![對應 2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="ea02c-160">使用模型</span><span class="sxs-lookup"><span data-stu-id="ea02c-160">Use the Model</span></span>

-   <span data-ttu-id="ea02c-161">在 Main 方法中，貼上下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="ea02c-161">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   <span data-ttu-id="ea02c-162">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ea02c-162">Compile and run the application.</span></span>

<span data-ttu-id="ea02c-163">針對資料庫執行此應用程式最後執行下列 T-SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="ea02c-163">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="ea02c-164">下列兩個**插入**陳述式所執行的結果執行內容。Savechanges （)。</span><span class="sxs-lookup"><span data-stu-id="ea02c-164">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="ea02c-165">他們所採取的資料**Person**實體並將它之間分割**人員**並**PersonInfo**資料表。</span><span class="sxs-lookup"><span data-stu-id="ea02c-165">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![插入 1](~/ef6/media/insert1.png)

    ![插入 2](~/ef6/media/insert2.png)
-   <span data-ttu-id="ea02c-168">下列**選取**執行結果列舉資料庫中的人員。</span><span class="sxs-lookup"><span data-stu-id="ea02c-168">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="ea02c-169">它結合了將資料從**Person**並**PersonInfo**資料表。</span><span class="sxs-lookup"><span data-stu-id="ea02c-169">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![選用版](~/ef6/media/select.png)
