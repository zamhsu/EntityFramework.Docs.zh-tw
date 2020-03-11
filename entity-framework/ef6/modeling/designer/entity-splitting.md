---
title: 設計工具實體分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: ba1895ae491cec909ff88a8784eea82f1876f595
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418459"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="94ad8-102">設計工具實體分割</span><span class="sxs-lookup"><span data-stu-id="94ad8-102">Designer Entity Splitting</span></span>
<span data-ttu-id="94ad8-103">本逐步解說示範如何使用 Entity Framework Designer （EF Designer）修改模型，將實體類型對應至兩個數據表。</span><span class="sxs-lookup"><span data-stu-id="94ad8-103">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="94ad8-104">當多個資料表共用共同的索引鍵時，您可以將實體對應至多個資料表。</span><span class="sxs-lookup"><span data-stu-id="94ad8-104">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="94ad8-105">將實體類型對應到兩個數據表的概念，可以輕鬆地擴充，以將實體類型對應至兩個以上的資料表。</span><span class="sxs-lookup"><span data-stu-id="94ad8-105">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="94ad8-106">下圖顯示使用 EF 設計工具時的主要視窗。</span><span class="sxs-lookup"><span data-stu-id="94ad8-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="94ad8-108">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="94ad8-108">Prerequisites</span></span>

<span data-ttu-id="94ad8-109">Visual Studio 2012 或 Visual Studio 2010、旗艦版、Premium、Professional 或 Web Express edition。</span><span class="sxs-lookup"><span data-stu-id="94ad8-109">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="94ad8-110">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="94ad8-110">Create the Database</span></span>

<span data-ttu-id="94ad8-111">隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="94ad8-111">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="94ad8-112">如果您使用 Visual Studio 2012，則您將會建立 LocalDB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="94ad8-112">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="94ad8-113">如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="94ad8-113">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="94ad8-114">首先，我們要建立一個資料庫，其中包含兩個數據表，我們將結合成單一實體。</span><span class="sxs-lookup"><span data-stu-id="94ad8-114">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="94ad8-115">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94ad8-115">Open Visual Studio</span></span>
-   <span data-ttu-id="94ad8-116">**View&gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="94ad8-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="94ad8-117">以滑鼠右鍵按一下 **資料連線-&gt; 新增連接 ...**</span><span class="sxs-lookup"><span data-stu-id="94ad8-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="94ad8-118">如果您還沒有從伺服器總管連接到資料庫，則必須選取 [ **Microsoft SQL Server** ] 做為資料來源</span><span class="sxs-lookup"><span data-stu-id="94ad8-118">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="94ad8-119">連接到 LocalDB 或 SQL Express （視您安裝的版本而定）</span><span class="sxs-lookup"><span data-stu-id="94ad8-119">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="94ad8-120">輸入**EntitySplitting**做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="94ad8-120">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="94ad8-121">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**</span><span class="sxs-lookup"><span data-stu-id="94ad8-121">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="94ad8-122">新的資料庫現在會顯示在伺服器總管</span><span class="sxs-lookup"><span data-stu-id="94ad8-122">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="94ad8-123">如果您使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="94ad8-123">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="94ad8-124">以滑鼠右鍵按一下伺服器總管中的資料庫，然後選取 [追加**查詢**]</span><span class="sxs-lookup"><span data-stu-id="94ad8-124">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="94ad8-125">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="94ad8-126">如果您使用 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="94ad8-126">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="94ad8-127">選取**資料&gt; Transact-sql 編輯器-&gt; 新的查詢連接**。</span><span class="sxs-lookup"><span data-stu-id="94ad8-127">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="94ad8-128">輸入 **。\\SQLEXPRESS**做為伺服器名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="94ad8-128">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="94ad8-129">從 [查詢編輯器] 頂端的下拉式選中選取 [ **EntitySplitting** ] 資料庫</span><span class="sxs-lookup"><span data-stu-id="94ad8-129">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="94ad8-130">將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行 SQL** ]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-130">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

## <a name="create-the-project"></a><span data-ttu-id="94ad8-131">建立專案</span><span class="sxs-lookup"><span data-stu-id="94ad8-131">Create the Project</span></span>

-   <span data-ttu-id="94ad8-132">在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。</span><span class="sxs-lookup"><span data-stu-id="94ad8-132">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="94ad8-133">在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台應用程式**] 範本。</span><span class="sxs-lookup"><span data-stu-id="94ad8-133">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="94ad8-134">輸入**MapEntityToTablesSample**做為專案的名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="94ad8-134">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="94ad8-135">如果系統提示您儲存在第一節中建立的 SQL 查詢，請按一下 [**否**]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-135">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="94ad8-136">建立以資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="94ad8-136">Create a Model based on the Database</span></span>

-   <span data-ttu-id="94ad8-137">以滑鼠右鍵按一下方案總管中的專案名稱，指向 [**加入**]，然後按一下 [**新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-137">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="94ad8-138">從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-138">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="94ad8-139">在 [檔案名] 中輸入**MapEntityToTablesModel** ，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-139">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="94ad8-140">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]。**</span><span class="sxs-lookup"><span data-stu-id="94ad8-140">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="94ad8-141">從下拉式選中選取 [ **EntitySplitting** ] 連接，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="94ad8-141">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="94ad8-142">在 [選擇您的資料庫物件] 對話方塊中，勾選 [**資料表** ] 節點旁的方塊。</span><span class="sxs-lookup"><span data-stu-id="94ad8-142">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="94ad8-143">這會將**EntitySplitting**資料庫中的所有資料表加入至模型。</span><span class="sxs-lookup"><span data-stu-id="94ad8-143">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="94ad8-144">按一下 **[完成]** 。</span><span class="sxs-lookup"><span data-stu-id="94ad8-144">Click **Finish**.</span></span>

<span data-ttu-id="94ad8-145">會顯示 Entity Designer （提供編輯模型的設計介面）。</span><span class="sxs-lookup"><span data-stu-id="94ad8-145">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="94ad8-146">將實體對應至兩個數據表</span><span class="sxs-lookup"><span data-stu-id="94ad8-146">Map an Entity to Two Tables</span></span>

<span data-ttu-id="94ad8-147">在此步驟中，我們將更新**person**實體類型，以結合**person**和**PersonInfo**資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="94ad8-147">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="94ad8-148">選取 **PersonInfo **實體的 **電子郵件** 和**電話**屬性，然後按**Ctrl + X**鍵。</span><span class="sxs-lookup"><span data-stu-id="94ad8-148">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="94ad8-149">選取 **Person **實體，然後按**Ctrl + V**鍵。</span><span class="sxs-lookup"><span data-stu-id="94ad8-149">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="94ad8-150">在設計介面上，選取 [ **PersonInfo** ] 實體，然後按鍵盤上的 [**刪除**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="94ad8-150">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="94ad8-151">當系統詢問您是否要從模型中移除**PersonInfo**資料表時，請按一下 [**否**]，我們即將將其對應至**Person**實體。</span><span class="sxs-lookup"><span data-stu-id="94ad8-151">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![刪除資料表](~/ef6/media/deletetables.png)

<span data-ttu-id="94ad8-153">後續步驟需要  視窗的  **對應詳細資料**。</span><span class="sxs-lookup"><span data-stu-id="94ad8-153">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="94ad8-154">如果您看不到這個視窗，請以滑鼠右鍵按一下設計介面，然後選取 [**對應詳細資料**]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-154">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="94ad8-155">選取  **Person**  實體類型，然後按一下  **對應詳細資料**  視窗中的  **&lt;新增資料表或視圖&gt;**  。</span><span class="sxs-lookup"><span data-stu-id="94ad8-155">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="94ad8-156">從下拉式清單中選取 [ **PersonInfo ** ]。</span><span class="sxs-lookup"><span data-stu-id="94ad8-156">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="94ad8-157"> **對應詳細資料**  視窗會以預設資料行對應進行更新，這在我們的案例中是正常的。</span><span class="sxs-lookup"><span data-stu-id="94ad8-157">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="94ad8-158"> 實體類型的 **person**現在會對應至 **Person** 並 **PersonInfo** 資料表。</span><span class="sxs-lookup"><span data-stu-id="94ad8-158">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![對應2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="94ad8-160">使用模型</span><span class="sxs-lookup"><span data-stu-id="94ad8-160">Use the Model</span></span>

-   <span data-ttu-id="94ad8-161">在 Main 方法中貼上下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="94ad8-161">Paste the following code in the Main method.</span></span>

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

-   <span data-ttu-id="94ad8-162">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="94ad8-162">Compile and run the application.</span></span>

<span data-ttu-id="94ad8-163">執行此應用程式後，已對資料庫執行下列 T-sql 語句。</span><span class="sxs-lookup"><span data-stu-id="94ad8-163">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="94ad8-164">執行內容時，會執行下列兩個**INSERT**語句。SaveChanges （）。</span><span class="sxs-lookup"><span data-stu-id="94ad8-164">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="94ad8-165">他們會從**person**實體接受資料，並將其分割在**person**和**PersonInfo**資料表之間。</span><span class="sxs-lookup"><span data-stu-id="94ad8-165">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![插入1](~/ef6/media/insert1.png)

    ![插入2](~/ef6/media/insert2.png)
-   <span data-ttu-id="94ad8-168">下列**SELECT**是由於列舉資料庫中的人員而執行。</span><span class="sxs-lookup"><span data-stu-id="94ad8-168">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="94ad8-169">它會結合**Person**和**PersonInfo**資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="94ad8-169">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![Select](~/ef6/media/select.png)
