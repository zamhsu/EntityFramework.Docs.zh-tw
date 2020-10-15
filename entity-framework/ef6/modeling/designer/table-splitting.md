---
title: 設計工具資料表分割-EF6
description: Entity Framework 6 中的設計工具資料表分割
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 8f6db22913fb3ac8a4254c29b5f986eb6e14f18e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066040"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="ec55e-103">設計工具資料表分割</span><span class="sxs-lookup"><span data-stu-id="ec55e-103">Designer Table Splitting</span></span>
<span data-ttu-id="ec55e-104">本逐步解說示範如何使用 Entity Framework Designer (EF Designer) 修改模型，將多個實體類型對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="ec55e-104">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="ec55e-105">當您使用消極式載入來載入物件時，您可能會想要使用資料表分割來延遲載入某些屬性。</span><span class="sxs-lookup"><span data-stu-id="ec55e-105">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span><span data-ttu-id="ec55e-106">您可以將可能包含非常大量資料的屬性區隔成不同的實體，並且只在需要時才載入。</span><span class="sxs-lookup"><span data-stu-id="ec55e-106"> You can separate the properties that might contain very large amount of data into a separate entity and only load it when required.</span></span>

<span data-ttu-id="ec55e-107">下圖顯示使用 EF 設計工具時使用的主視窗。</span><span class="sxs-lookup"><span data-stu-id="ec55e-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="ec55e-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="ec55e-109">Prerequisites</span></span>

<span data-ttu-id="ec55e-110">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="ec55e-110">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="ec55e-111">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="ec55e-111">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="ec55e-112">[School 範例資料庫](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="ec55e-112">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="ec55e-113">設定專案</span><span class="sxs-lookup"><span data-stu-id="ec55e-113">Set up the Project</span></span>

<span data-ttu-id="ec55e-114">本逐步解說使用 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="ec55e-114">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="ec55e-115">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="ec55e-115">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="ec55e-116">在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。</span><span class="sxs-lookup"><span data-stu-id="ec55e-116">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="ec55e-117">在左窗格中，按一下 [Visual C] \# ，然後選取 [主控台應用程式] 範本。</span><span class="sxs-lookup"><span data-stu-id="ec55e-117">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="ec55e-118">輸入 **TableSplittingSample** 做為專案的名稱，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="ec55e-118">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="ec55e-119">建立以 School 資料庫為基礎的模型</span><span class="sxs-lookup"><span data-stu-id="ec55e-119">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="ec55e-120">在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [ **加入**]，然後按一下 [ **新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-120">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="ec55e-121">從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-121">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="ec55e-122">輸入 **TableSplittingModel** 的檔案名，然後按一下 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-122">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="ec55e-123">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]。**</span><span class="sxs-lookup"><span data-stu-id="ec55e-123">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="ec55e-124">按一下 [新增連接]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-124">Click New Connection.</span></span> <span data-ttu-id="ec55e-125">在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如\*\* (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School\*\*   作為資料庫名稱，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="ec55e-125">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="ec55e-126">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="ec55e-126">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="ec55e-127">在 [選擇您的資料庫物件] 對話方塊中，展開 [**資料表**]   節點，並檢查**Person**資料表。</span><span class="sxs-lookup"><span data-stu-id="ec55e-127">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="ec55e-128">這會將指定的資料表新增至 **School** 模型。</span><span class="sxs-lookup"><span data-stu-id="ec55e-128">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="ec55e-129">按一下 **[完成]**。</span><span class="sxs-lookup"><span data-stu-id="ec55e-129">Click **Finish**.</span></span>

<span data-ttu-id="ec55e-130">隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。</span><span class="sxs-lookup"><span data-stu-id="ec55e-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="ec55e-131">在 [ **選擇您的資料庫物件**] 對話方塊中選取的所有物件   都會加入至模型中。</span><span class="sxs-lookup"><span data-stu-id="ec55e-131">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="ec55e-132">將兩個實體對應至單一資料表</span><span class="sxs-lookup"><span data-stu-id="ec55e-132">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="ec55e-133">在本節中，您會將 **Person** 實體分割成兩個實體，然後將它們對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="ec55e-133">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="ec55e-134">**Person**實體未包含任何可能包含大量資料的屬性;它僅作為範例使用。</span><span class="sxs-lookup"><span data-stu-id="ec55e-134">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="ec55e-135">以滑鼠右鍵按一下設計介面的空白區域、指向 [ **加入新**的]，然後按一下 [ **實體**]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-135">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="ec55e-136">[ **新增實體**   ] 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="ec55e-136">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="ec55e-137">針對 **HireInfo**[    **機構名稱**] 輸入 HireInfo，並針對索引**鍵屬性**名稱輸入**PersonID** 。</span><span class="sxs-lookup"><span data-stu-id="ec55e-137">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="ec55e-138">按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="ec55e-138">Click **OK**.</span></span>
-   <span data-ttu-id="ec55e-139">在設計介面上便會建立並顯示新的實體類型。</span><span class="sxs-lookup"><span data-stu-id="ec55e-139">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="ec55e-140">選取 Person 實體類型的 [ **雇用**日期]   屬性 **Person**   ，然後按**Ctrl + X**鍵。</span><span class="sxs-lookup"><span data-stu-id="ec55e-140">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="ec55e-141">選取 [ **HireInfo**]   實體，然後按**Ctrl + V**鍵。</span><span class="sxs-lookup"><span data-stu-id="ec55e-141">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="ec55e-142">建立 **Person** 與 **HireInfo**之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="ec55e-142">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="ec55e-143">若要這樣做，請以滑鼠右鍵按一下設計介面的空白區域、指向 [ **加入新**的]，然後按一下 [ **關聯**]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-143">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="ec55e-144">[ **新增關聯**   ] 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="ec55e-144">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="ec55e-145">預設會提供 **PersonHireInfo** 名稱。</span><span class="sxs-lookup"><span data-stu-id="ec55e-145">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="ec55e-146">指定多重性 **1 (** 關聯性兩端的一個) 。</span><span class="sxs-lookup"><span data-stu-id="ec55e-146">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="ec55e-147">按下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="ec55e-147">Press **OK**.</span></span>

<span data-ttu-id="ec55e-148">下一個步驟需要 [ **對應詳細資料**   ] 視窗。</span><span class="sxs-lookup"><span data-stu-id="ec55e-148">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="ec55e-149">如果您看不到這個視窗，請以滑鼠右鍵按一下設計介面，然後選取 [ **對應詳細資料**]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-149">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="ec55e-150">選取 [ **HireInfo**   ] 實體類型，然後按一下 [對應詳細資料] 視窗中的 [ \*\* &lt; 加入資料表或視圖 &gt; \*\*    **Mapping Details**   ]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-150">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="ec55e-151">從 [ \*\* &lt; 加入資料表或視圖 &gt; **欄位] 下拉式清單中選取 [**人員\*\*]   。</span><span class="sxs-lookup"><span data-stu-id="ec55e-151">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="ec55e-152">此清單包含選取的實體可以對應的資料表或視圖。</span><span class="sxs-lookup"><span data-stu-id="ec55e-152">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="ec55e-153">根據預設，應該對應適當的屬性。</span><span class="sxs-lookup"><span data-stu-id="ec55e-153">The appropriate properties should be mapped by default.</span></span>

    ![對應屬性](~/ef6/media/mapping.png)

-   <span data-ttu-id="ec55e-155">在設計介面上選取 [ **PersonHireInfo** ] 關聯。</span><span class="sxs-lookup"><span data-stu-id="ec55e-155">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="ec55e-156">以滑鼠右鍵按一下設計介面上的關聯，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-156">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="ec55e-157">在 [ **屬性** ] 視窗中，選取 [ **參考條件約束** ] 屬性，然後按一下省略號按鈕。</span><span class="sxs-lookup"><span data-stu-id="ec55e-157">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="ec55e-158">從 [**主體**] 下拉式清單中選取 [**人員**]。</span><span class="sxs-lookup"><span data-stu-id="ec55e-158">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="ec55e-159">按下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="ec55e-159">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="ec55e-160">使用模型</span><span class="sxs-lookup"><span data-stu-id="ec55e-160">Use the Model</span></span>

-   <span data-ttu-id="ec55e-161">在 Main 方法中貼上下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="ec55e-161">Paste the following code in the Main method.</span></span>

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
-   <span data-ttu-id="ec55e-162">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec55e-162">Compile and run the application.</span></span>

<span data-ttu-id="ec55e-163">執行此應用程式後，會針對 **School** 資料庫執行下列 t-sql 語句。</span><span class="sxs-lookup"><span data-stu-id="ec55e-163">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="ec55e-164">執行內容的結果會執行下列 **插入** 。SaveChanges ( # A1 並結合 **Person** 和 **HireInfo** 實體的資料</span><span class="sxs-lookup"><span data-stu-id="ec55e-164">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![插入合併人員和 HireInfo 資料](~/ef6/media/insert.png)

-   <span data-ttu-id="ec55e-166">執行內容的結果會執行下列**SELECT** 。FirstOrDefault ( # A1 並只選取與**Person**對應的資料行</span><span class="sxs-lookup"><span data-stu-id="ec55e-166">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![選取 [1]](~/ef6/media/select1.png)

-   <span data-ttu-id="ec55e-168">下列**SELECT**是因為存取導覽屬性 existingPerson 而執行的結果，並只選取對應至**HireInfo**的資料行。</span><span class="sxs-lookup"><span data-stu-id="ec55e-168">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![選取2](~/ef6/media/select2.png)
