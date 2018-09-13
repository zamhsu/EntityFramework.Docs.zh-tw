---
title: 設計工具的資料表分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: 8b0ca6778a06ed43b1365d2e5969ff15948f8004
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490690"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="34df5-102">分割的資料表設計工具</span><span class="sxs-lookup"><span data-stu-id="34df5-102">Designer Table Splitting</span></span>
<span data-ttu-id="34df5-103">本逐步解說示範如何將多個實體類型對應至單一資料表中，透過修改模型使用 Entity Framework Designer （EF 設計工具）。</span><span class="sxs-lookup"><span data-stu-id="34df5-103">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="34df5-104">使用消極式載入來載入您的物件時，您可能想要使用資料表分割的其中一個原因延遲載入的某些屬性。</span><span class="sxs-lookup"><span data-stu-id="34df5-104">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span> <span data-ttu-id="34df5-105">您可以分隔可能包含非常大量的資料分成個別的實體，並只載入時所需的屬性。</span><span class="sxs-lookup"><span data-stu-id="34df5-105">You can separate the properties that might contain very large amount of data into a seperate entity and only load it when required.</span></span>

<span data-ttu-id="34df5-106">下圖顯示使用 EF 設計工具時所使用的主視窗。</span><span class="sxs-lookup"><span data-stu-id="34df5-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="34df5-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="34df5-108">Prerequisites</span></span>

<span data-ttu-id="34df5-109">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="34df5-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="34df5-110">新版的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="34df5-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="34df5-111">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="34df5-111">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="34df5-112">設定專案</span><span class="sxs-lookup"><span data-stu-id="34df5-112">Set up the Project</span></span>

<span data-ttu-id="34df5-113">本逐步解說使用 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="34df5-113">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="34df5-114">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="34df5-114">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="34df5-115">在 [檔案]  功能表中，指向 [新增] ，然後按一下 [專案] 。</span><span class="sxs-lookup"><span data-stu-id="34df5-115">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="34df5-116">在左窗格中，按一下 Visual C\#，然後選取 主控台應用程式範本。</span><span class="sxs-lookup"><span data-stu-id="34df5-116">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="34df5-117">請輸入**TableSplittingSample**做為名稱的專案，然後按一下**確定**。</span><span class="sxs-lookup"><span data-stu-id="34df5-117">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="34df5-118">建立 School 資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="34df5-118">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="34df5-119">以滑鼠右鍵按一下方案總管 中的專案名稱，指向**新增**，然後按一下**新項目**。</span><span class="sxs-lookup"><span data-stu-id="34df5-119">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="34df5-120">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。</span><span class="sxs-lookup"><span data-stu-id="34df5-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="34df5-121">請輸入**TableSplittingModel.edmx**為檔案名稱，然後按一下**新增**。</span><span class="sxs-lookup"><span data-stu-id="34df5-121">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="34df5-122">在 [選擇模型內容] 對話方塊中，選取**從資料庫產生**，然後按一下 [**下一步]。**</span><span class="sxs-lookup"><span data-stu-id="34df5-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="34df5-123">按一下 新增連接。</span><span class="sxs-lookup"><span data-stu-id="34df5-123">Click New Connection.</span></span> <span data-ttu-id="34df5-124">在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。</span><span class="sxs-lookup"><span data-stu-id="34df5-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="34df5-125">[選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。</span><span class="sxs-lookup"><span data-stu-id="34df5-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="34df5-126">在 [選擇您的資料庫物件] 對話方塊中，展開**資料表**節點，並檢查**人員**資料表。</span><span class="sxs-lookup"><span data-stu-id="34df5-126">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="34df5-127">這會新增至指定的資料表**學校**模型。</span><span class="sxs-lookup"><span data-stu-id="34df5-127">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="34df5-128">按一下 [ **完成**]。</span><span class="sxs-lookup"><span data-stu-id="34df5-128">Click **Finish**.</span></span>

<span data-ttu-id="34df5-129">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="34df5-129">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="34df5-130">您在選取的所有物件**選擇您的資料庫物件**對話方塊加入至模型。</span><span class="sxs-lookup"><span data-stu-id="34df5-130">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="34df5-131">將兩個實體對應至單一資料表</span><span class="sxs-lookup"><span data-stu-id="34df5-131">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="34df5-132">在本節中，您將分割**人員**成兩個實體的實體，然後將它們對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="34df5-132">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="34df5-133">**人員**實體不包含任何可能包含大量資料的屬性; 它只會使用做為範例。</span><span class="sxs-lookup"><span data-stu-id="34df5-133">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="34df5-134">以滑鼠右鍵按一下設計介面的空白區域，指向**加入新**，然後按一下**實體**。</span><span class="sxs-lookup"><span data-stu-id="34df5-134">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="34df5-135">**新的實體** 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="34df5-135">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="34df5-136">型別**HireInfo**的**實體名稱**並**PersonID**如**Key 屬性**名稱。</span><span class="sxs-lookup"><span data-stu-id="34df5-136">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="34df5-137">按一下 [確定 **Deploying Office Solutions**]。</span><span class="sxs-lookup"><span data-stu-id="34df5-137">Click **OK**.</span></span>
-   <span data-ttu-id="34df5-138">在設計介面上便會建立並顯示新的實體類型。</span><span class="sxs-lookup"><span data-stu-id="34df5-138">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="34df5-139">選取  **HireDate**屬性**人員**實體類型，然後按**Ctrl + X**索引鍵。</span><span class="sxs-lookup"><span data-stu-id="34df5-139">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="34df5-140">選取  **HireInfo**實體，然後按**Ctrl + V**索引鍵。</span><span class="sxs-lookup"><span data-stu-id="34df5-140">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="34df5-141">建立之間的關聯**Person**並**HireInfo**。</span><span class="sxs-lookup"><span data-stu-id="34df5-141">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="34df5-142">若要這樣做，請以滑鼠右鍵按一下設計介面的空白區域，指向**加入新**，然後按一下**關聯**。</span><span class="sxs-lookup"><span data-stu-id="34df5-142">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="34df5-143">**新增關聯** 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="34df5-143">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="34df5-144">**PersonHireInfo**預設指定名稱。</span><span class="sxs-lookup"><span data-stu-id="34df5-144">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="34df5-145">指定多重性**1(One)** 關聯性的兩端。</span><span class="sxs-lookup"><span data-stu-id="34df5-145">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="34df5-146">按下**確定**。</span><span class="sxs-lookup"><span data-stu-id="34df5-146">Press **OK**.</span></span>

<span data-ttu-id="34df5-147">下一個步驟需要**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="34df5-147">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="34df5-148">如果看不到這個視窗，以滑鼠右鍵按一下設計介面，然後選取**對應詳細資料**。</span><span class="sxs-lookup"><span data-stu-id="34df5-148">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="34df5-149">選取  **HireInfo**實體類型，然後按一下**&lt;將資料表或檢視&gt;** 中**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="34df5-149">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="34df5-150">選取  **Person**從**&lt;將資料表或檢視&gt;** 欄位下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="34df5-150">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="34df5-151">清單包含資料表或檢視選取的實體可以對應。</span><span class="sxs-lookup"><span data-stu-id="34df5-151">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="34df5-152">根據預設值，就應該對應適當的屬性。</span><span class="sxs-lookup"><span data-stu-id="34df5-152">The appropriate properties should be mapped by default.</span></span>

    ![對應](~/ef6/media/mapping.png)

-   <span data-ttu-id="34df5-154">選取  **PersonHireInfo**關聯的設計介面上。</span><span class="sxs-lookup"><span data-stu-id="34df5-154">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="34df5-155">以滑鼠右鍵按一下設計介面，然後選取的關聯**屬性**。</span><span class="sxs-lookup"><span data-stu-id="34df5-155">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="34df5-156">在 **屬性**視窗中，選取**參考條件約束**屬性，然後按一下省略符號按鈕。</span><span class="sxs-lookup"><span data-stu-id="34df5-156">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="34df5-157">選取  **Person**從**主體**下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="34df5-157">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="34df5-158">按下**確定**。</span><span class="sxs-lookup"><span data-stu-id="34df5-158">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="34df5-159">使用模型</span><span class="sxs-lookup"><span data-stu-id="34df5-159">Use the Model</span></span>

-   <span data-ttu-id="34df5-160">在 Main 方法中，貼上下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="34df5-160">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   <span data-ttu-id="34df5-161">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="34df5-161">Compile and run the application.</span></span>

<span data-ttu-id="34df5-162">針對執行下列 T-SQL 陳述式**學校**執行此應用程式最後的資料庫。</span><span class="sxs-lookup"><span data-stu-id="34df5-162">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="34df5-163">下列**插入**由於執行內容而執行。Savechanges （） 及結合資料是從**Person**並**HireInfo**實體</span><span class="sxs-lookup"><span data-stu-id="34df5-163">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![Insert](~/ef6/media/insert.png)

-   <span data-ttu-id="34df5-165">下列**選取**由於執行內容而執行。People.FirstOrDefault() 和選取的資料行對應至**人員**</span><span class="sxs-lookup"><span data-stu-id="34df5-165">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![選取 [1]](~/ef6/media/select1.png)

-   <span data-ttu-id="34df5-167">下列**選取 **因為存取導覽屬性 existingPerson.Instructor 執行，並選取 只是資料行對應至**HireInfo**</span><span class="sxs-lookup"><span data-stu-id="34df5-167">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![選取 [2]](~/ef6/media/select2.png)
