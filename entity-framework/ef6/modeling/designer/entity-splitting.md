---
title: 設計工具實體分割-EF6
description: Entity Framework 6 中的設計工具實體分割
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: 8fdb9037fc49045089016e6630316d05c8726ad8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064896"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="2944d-103">設計工具實體分割</span><span class="sxs-lookup"><span data-stu-id="2944d-103">Designer Entity Splitting</span></span>
<span data-ttu-id="2944d-104">本逐步解說示範如何使用 Entity Framework Designer (EF Designer) 修改模型，將實體類型對應至兩個數據表。</span><span class="sxs-lookup"><span data-stu-id="2944d-104">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="2944d-105">當多個資料表共用共同的索引鍵時，您可以將實體對應至多個資料表。</span><span class="sxs-lookup"><span data-stu-id="2944d-105">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="2944d-106">適用於將實體類型對應至兩個資料表的概念，很容易擴大為將實體類型對應至兩個以上的資料表。</span><span class="sxs-lookup"><span data-stu-id="2944d-106">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="2944d-107">下圖顯示使用 EF 設計工具時使用的主視窗。</span><span class="sxs-lookup"><span data-stu-id="2944d-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="2944d-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="2944d-109">Prerequisites</span></span>

<span data-ttu-id="2944d-110">Visual Studio 2012 或 Visual Studio 2010、旗艦版、Premium、Professional 或 Web Express 版。</span><span class="sxs-lookup"><span data-stu-id="2944d-110">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="2944d-111">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="2944d-111">Create the Database</span></span>

<span data-ttu-id="2944d-112">隨 Visual Studio 安裝的資料庫伺服器會根據您所安裝的 Visual Studio 版本而有所不同：</span><span class="sxs-lookup"><span data-stu-id="2944d-112">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="2944d-113">如果您使用 Visual Studio 2012，則會建立 LocalDB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="2944d-113">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="2944d-114">如果您使用 Visual Studio 2010，將會建立 SQL Express 資料庫。</span><span class="sxs-lookup"><span data-stu-id="2944d-114">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="2944d-115">首先我們要建立一個資料庫，其中包含兩個數據表，我們會將它們合併成單一實體。</span><span class="sxs-lookup"><span data-stu-id="2944d-115">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="2944d-116">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2944d-116">Open Visual Studio</span></span>
-   <span data-ttu-id="2944d-117">**View- &gt; 伺服器總管**</span><span class="sxs-lookup"><span data-stu-id="2944d-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="2944d-118">以滑鼠右鍵按一下 [**資料連線- &gt; 新增連接 ...** ]</span><span class="sxs-lookup"><span data-stu-id="2944d-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="2944d-119">如果您還沒有從伺服器總管連接到資料庫，則必須選取 **Microsoft SQL Server** 作為資料來源</span><span class="sxs-lookup"><span data-stu-id="2944d-119">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="2944d-120">連接至 LocalDB 或 SQL Express （視您已安裝的版本而定）</span><span class="sxs-lookup"><span data-stu-id="2944d-120">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="2944d-121">輸入 **EntitySplitting** 做為資料庫名稱</span><span class="sxs-lookup"><span data-stu-id="2944d-121">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="2944d-122">選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。</span><span class="sxs-lookup"><span data-stu-id="2944d-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="2944d-123">新資料庫現在會出現在伺服器總管</span><span class="sxs-lookup"><span data-stu-id="2944d-123">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="2944d-124">如果您使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="2944d-124">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="2944d-125">以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="2944d-125">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="2944d-126">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行**]</span><span class="sxs-lookup"><span data-stu-id="2944d-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="2944d-127">如果您使用 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="2944d-127">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="2944d-128">選取 **資料- &gt; 交易 SQL 編輯器- &gt; 新增查詢連接 ...**</span><span class="sxs-lookup"><span data-stu-id="2944d-128">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="2944d-129">輸入 **。 \\SQLEXPRESS** 作為伺服器名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="2944d-129">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="2944d-130">從查詢編輯器頂端的下拉式清單中選取 **EntitySplitting** 資料庫</span><span class="sxs-lookup"><span data-stu-id="2944d-130">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="2944d-131">將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行 SQL** ]。</span><span class="sxs-lookup"><span data-stu-id="2944d-131">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

## <a name="create-the-project"></a><span data-ttu-id="2944d-132">建立專案</span><span class="sxs-lookup"><span data-stu-id="2944d-132">Create the Project</span></span>

-   <span data-ttu-id="2944d-133">在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。</span><span class="sxs-lookup"><span data-stu-id="2944d-133">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="2944d-134">在左窗格中，按一下 [ **Visual \# C**]，然後選取 [**主控台應用程式**] 範本。</span><span class="sxs-lookup"><span data-stu-id="2944d-134">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="2944d-135">輸入 **MapEntityToTablesSample** 做為專案的名稱，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="2944d-135">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="2944d-136">如果系統提示您儲存在第一節中建立的 SQL 查詢，請按一下 [ **否** ]。</span><span class="sxs-lookup"><span data-stu-id="2944d-136">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="2944d-137">建立以資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="2944d-137">Create a Model based on the Database</span></span>

-   <span data-ttu-id="2944d-138">在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [ **加入**]，然後按一下 [ **新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="2944d-138">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="2944d-139">從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。</span><span class="sxs-lookup"><span data-stu-id="2944d-139">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="2944d-140">輸入 **MapEntityToTablesModel** 的檔案名，然後按一下 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="2944d-140">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="2944d-141">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]。**</span><span class="sxs-lookup"><span data-stu-id="2944d-141">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="2944d-142">從下拉式清單中選取 **EntitySplitting** 連接，然後按 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="2944d-142">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="2944d-143">在 [選擇您的資料庫物件] 對話方塊中，核取 [**資料表**] 節點旁的核取方塊   。</span><span class="sxs-lookup"><span data-stu-id="2944d-143">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="2944d-144">這會將 **EntitySplitting** 資料庫中的所有資料表加入至模型。</span><span class="sxs-lookup"><span data-stu-id="2944d-144">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="2944d-145">按一下 **[完成]**。</span><span class="sxs-lookup"><span data-stu-id="2944d-145">Click **Finish**.</span></span>

<span data-ttu-id="2944d-146">隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。</span><span class="sxs-lookup"><span data-stu-id="2944d-146">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="2944d-147">將實體對應至兩個數據表</span><span class="sxs-lookup"><span data-stu-id="2944d-147">Map an Entity to Two Tables</span></span>

<span data-ttu-id="2944d-148">在此步驟中，我們將更新 **person** 實體類型，以結合 **person** 和 **PersonInfo** 資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="2944d-148">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="2944d-149">選取 PersonInfo 實體的 **電子郵件**   和**電話**屬性，然後按**Ctrl + X**鍵。 \*\*  \*\*</span><span class="sxs-lookup"><span data-stu-id="2944d-149">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="2944d-150">選取 **Person **實體，然後按 **Ctrl + V** 鍵。</span><span class="sxs-lookup"><span data-stu-id="2944d-150">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="2944d-151">在設計介面上，選取 [ **PersonInfo**]   實體，然後按鍵盤上的 [**刪除**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="2944d-151">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="2944d-152">當系統詢問您是否要從模型中移除**PersonInfo**資料表時，請按一下 [**否**]，我們即將將其對應至**Person**實體。</span><span class="sxs-lookup"><span data-stu-id="2944d-152">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![刪除資料表](~/ef6/media/deletetables.png)

<span data-ttu-id="2944d-154">接下來的步驟需要 [ **對應詳細資料**   ] 視窗。</span><span class="sxs-lookup"><span data-stu-id="2944d-154">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="2944d-155">如果您看不到這個視窗，請以滑鼠右鍵按一下設計介面，然後選取 [ **對應詳細資料**]。</span><span class="sxs-lookup"><span data-stu-id="2944d-155">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="2944d-156">選取 [ **Person**   ] 實體類型，然後按一下 [對應詳細資料] 視窗中的 [ \*\* &lt; 加入資料表或視圖 &gt; \*\*    **Mapping Details**   ]。</span><span class="sxs-lookup"><span data-stu-id="2944d-156">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="2944d-157"> **PersonInfo **   從下拉式清單中選取 [PersonInfo]。</span><span class="sxs-lookup"><span data-stu-id="2944d-157">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="2944d-158">[ **對應詳細資料**   ] 視窗會更新成預設資料行對應，在我們的案例中，這些都沒問題。</span><span class="sxs-lookup"><span data-stu-id="2944d-158">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="2944d-159"> **Person**   實體類型現在會對應到 **person**   和 **PersonInfo**   資料表。</span><span class="sxs-lookup"><span data-stu-id="2944d-159">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![對應2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="2944d-161">使用模型</span><span class="sxs-lookup"><span data-stu-id="2944d-161">Use the Model</span></span>

-   <span data-ttu-id="2944d-162">在 Main 方法中貼上下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="2944d-162">Paste the following code in the Main method.</span></span>

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

-   <span data-ttu-id="2944d-163">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="2944d-163">Compile and run the application.</span></span>

<span data-ttu-id="2944d-164">執行此應用程式後，會對資料庫執行下列 T-sql 語句。</span><span class="sxs-lookup"><span data-stu-id="2944d-164">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="2944d-165">執行內容的結果會執行下列兩個 **INSERT** 語句。SaveChanges ( # A1。</span><span class="sxs-lookup"><span data-stu-id="2944d-165">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="2944d-166">他們會取用 **person** 實體的資料，並在 **person** 和 **PersonInfo** 資料表之間分割資料。</span><span class="sxs-lookup"><span data-stu-id="2944d-166">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![插入1](~/ef6/media/insert1.png)

    ![插入2](~/ef6/media/insert2.png)
-   <span data-ttu-id="2944d-169">下列 **SELECT** 是由於列舉資料庫中的人員而執行。</span><span class="sxs-lookup"><span data-stu-id="2944d-169">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="2944d-170">它會結合 **Person** 和 **PersonInfo** 資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="2944d-170">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![選取結合 Person 和 PersonInfo 資料](~/ef6/media/select.png)
