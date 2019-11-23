---
title: 設計工具 CUD 預存程式-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
ms.openlocfilehash: bdb0df969c33d5ad3f103bfa9af6002c9c2bb9b3
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813554"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="f2d59-102">設計工具 CUD 預存程式</span><span class="sxs-lookup"><span data-stu-id="f2d59-102">Designer CUD Stored Procedures</span></span>

<span data-ttu-id="f2d59-103">此逐步解說示範如何使用 Entity Framework Designer （EF Designer），將實體類型的 create\\insert、update 和 delete （CUD）作業對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2d59-103">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="f2d59-104"> 根據預設，Entity Framework 會自動產生 CUD 作業的 SQL 語句，但您也可以將預存程式對應至這些作業。</span><span class="sxs-lookup"><span data-stu-id="f2d59-104"> By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="f2d59-105">請注意，該 Code First 不支援對應至預存程式或函數。</span><span class="sxs-lookup"><span data-stu-id="f2d59-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="f2d59-106">不過，您可以使用 DbSet. SqlQuery 方法來呼叫預存程式或函數。</span><span class="sxs-lookup"><span data-stu-id="f2d59-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="f2d59-107">例如：</span><span class="sxs-lookup"><span data-stu-id="f2d59-107">For example:</span></span>

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="f2d59-108">將 CUD 作業對應至預存程式時的考慮</span><span class="sxs-lookup"><span data-stu-id="f2d59-108">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="f2d59-109">將 CUD 作業對應至預存程式時，適用下列考慮事項：</span><span class="sxs-lookup"><span data-stu-id="f2d59-109">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span>

- <span data-ttu-id="f2d59-110">如果您要將其中一個 CUD 作業對應至預存程式，請將其對應。</span><span class="sxs-lookup"><span data-stu-id="f2d59-110">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="f2d59-111">如果您未對應這三個，未對應的作業會在執行時失敗，而且會擲回 **UpdateException** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-111">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
- <span data-ttu-id="f2d59-112">您必須將預存程式的每個參數對應至實體屬性。</span><span class="sxs-lookup"><span data-stu-id="f2d59-112">You must map every parameter of the stored procedure to entity properties.</span></span>
- <span data-ttu-id="f2d59-113">如果伺服器為插入的資料列產生主要金鑰值，您必須將此值對應回實體的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="f2d59-113">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="f2d59-114">在接下來的範例中， **InsertPerson** 預存程式會傳回新建立的主鍵做為預存程式之結果集的一部分。</span><span class="sxs-lookup"><span data-stu-id="f2d59-114">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="f2d59-115">主鍵會使用 EF Designer 的 **&lt;加入結果**系結&gt;  功能對應至實體索引鍵（**PersonID**）。</span><span class="sxs-lookup"><span data-stu-id="f2d59-115">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
- <span data-ttu-id="f2d59-116">預存程序呼叫會對應1:1 和概念模型中的實體。</span><span class="sxs-lookup"><span data-stu-id="f2d59-116">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="f2d59-117">例如，如果您在概念模型中執行繼承階層，然後對應**父**（基底）和**子**系（衍生）實體的 CUD 預存程式，則儲存**子**變更只會呼叫**子**系的預存程式，而不會觸發**父系**的預存程序呼叫。</span><span class="sxs-lookup"><span data-stu-id="f2d59-117">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f2d59-118">必要條件</span><span class="sxs-lookup"><span data-stu-id="f2d59-118">Prerequisites</span></span>

<span data-ttu-id="f2d59-119">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="f2d59-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="f2d59-120">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="f2d59-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="f2d59-121">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="f2d59-121">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="f2d59-122">設定專案</span><span class="sxs-lookup"><span data-stu-id="f2d59-122">Set up the Project</span></span>

- <span data-ttu-id="f2d59-123">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="f2d59-123">Open Visual Studio 2012.</span></span>
- <span data-ttu-id="f2d59-124">選取檔案 **&gt; 新&gt; 專案**</span><span class="sxs-lookup"><span data-stu-id="f2d59-124">Select **File-&gt; New -&gt; Project**</span></span>
- <span data-ttu-id="f2d59-125">在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台**] 範本。</span><span class="sxs-lookup"><span data-stu-id="f2d59-125">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
- <span data-ttu-id="f2d59-126">在 [名稱] 中輸入 **CUDSProcsSample** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-126">Enter **CUDSProcsSample** as the name.</span></span>
- <span data-ttu-id="f2d59-127">選取 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-127">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="f2d59-128">建立模型</span><span class="sxs-lookup"><span data-stu-id="f2d59-128">Create a Model</span></span>

- <span data-ttu-id="f2d59-129">以滑鼠右鍵按一下方案總管中的專案名稱，然後選取 [**新增-&gt; 新專案**]。</span><span class="sxs-lookup"><span data-stu-id="f2d59-129">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
- <span data-ttu-id="f2d59-130">從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。</span><span class="sxs-lookup"><span data-stu-id="f2d59-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
- <span data-ttu-id="f2d59-131">在 [檔案名] 中輸入**CUDSProcs** ，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="f2d59-131">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
- <span data-ttu-id="f2d59-132">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
- <span data-ttu-id="f2d59-133">按一下 [ **新增連接**]。</span><span class="sxs-lookup"><span data-stu-id="f2d59-133">Click **New Connection**.</span></span> <span data-ttu-id="f2d59-134">在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="f2d59-135">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="f2d59-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
- <span data-ttu-id="f2d59-136">在 [選擇您的資料庫物件] 對話方塊的 [ **資料表** ] 節點底下，選取 [ **Person** ] 資料表。</span><span class="sxs-lookup"><span data-stu-id="f2d59-136">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
- <span data-ttu-id="f2d59-137">此外，請在 [**預存程式和函數**] 節點底下選取下列預存程式： **DeletePerson**、 **InsertPerson**和**UpdatePerson**。</span><span class="sxs-lookup"><span data-stu-id="f2d59-137">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span>
- <span data-ttu-id="f2d59-138">從 Visual Studio 2012 開始，EF 設計工具支援大量匯入預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2d59-138">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="f2d59-139">預設會核取 [將**選取的預存程式和函數匯入至實體模型**]。</span><span class="sxs-lookup"><span data-stu-id="f2d59-139">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="f2d59-140">因為在此範例中，我們有一個插入、更新和刪除實體類型的預存程式，所以我們不想要匯入它們，而且會取消核取此核取方塊。</span><span class="sxs-lookup"><span data-stu-id="f2d59-140">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span>

    ![匯入 S 的過程](~/ef6/media/importsprocs.jpg)

- <span data-ttu-id="f2d59-142">按一下 **[完成]** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-142">Click **Finish**.</span></span>
    <span data-ttu-id="f2d59-143">[EF Designer] （提供編輯模型的設計介面）隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="f2d59-143">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="f2d59-144">將 Person 實體對應至預存程式</span><span class="sxs-lookup"><span data-stu-id="f2d59-144">Map the Person Entity to Stored Procedures</span></span>

- <span data-ttu-id="f2d59-145">以滑鼠右鍵按一下 [ **Person** ] 實體類型，然後選取 [ **預存程式對應**]。</span><span class="sxs-lookup"><span data-stu-id="f2d59-145">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
- <span data-ttu-id="f2d59-146">預存程式對應會顯示在  **對應詳細資料**  視窗中。</span><span class="sxs-lookup"><span data-stu-id="f2d59-146">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
- <span data-ttu-id="f2d59-147">按一下  **&lt;選取 插入函數&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-147">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="f2d59-148">此欄位會變成儲存模型中預存程序的下拉式清單，可以對應到概念模型中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="f2d59-148">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="f2d59-149">從下拉式清單中選取 [ **InsertPerson** ]。</span><span class="sxs-lookup"><span data-stu-id="f2d59-149">Select **InsertPerson** from the drop-down list.</span></span>
- <span data-ttu-id="f2d59-150">預存程序參數與實體屬性之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="f2d59-150">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="f2d59-151">請注意，箭頭表示對應方向：為預存程序參數提供屬性值。</span><span class="sxs-lookup"><span data-stu-id="f2d59-151">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
- <span data-ttu-id="f2d59-152">按一下 [ **&lt;加入結果**系結]&gt;。</span><span class="sxs-lookup"><span data-stu-id="f2d59-152">Click **&lt;Add Result Binding&gt;**.</span></span>
- <span data-ttu-id="f2d59-153">輸入 **NewPersonID**，這是 **InsertPerson** 預存程式所傳回的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="f2d59-153">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="f2d59-154">請確定不輸入前置或尾端空格。</span><span class="sxs-lookup"><span data-stu-id="f2d59-154">Make sure not to type leading or trailing spaces.</span></span>
- <span data-ttu-id="f2d59-155">按 **enter**鍵。</span><span class="sxs-lookup"><span data-stu-id="f2d59-155">Press **Enter**.</span></span>
- <span data-ttu-id="f2d59-156">根據預設， **NewPersonID** 會對應至實體索引鍵 **PersonID**。</span><span class="sxs-lookup"><span data-stu-id="f2d59-156">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="f2d59-157">請注意，箭頭表示對應方向：為屬性提供結果資料行的值。</span><span class="sxs-lookup"><span data-stu-id="f2d59-157">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![對應詳細資料](~/ef6/media/mappingdetails.png)

- <span data-ttu-id="f2d59-159">按一下  **&lt;選取 更新**函式&gt; ，然後從產生的下拉式清單中選取  **UpdatePerson** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-159">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="f2d59-160">預存程序參數與實體屬性之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="f2d59-160">Default mappings between stored procedure parameters and entity properties appear.</span></span>
- <span data-ttu-id="f2d59-161">按一下  **&lt;選取 刪除**函式&gt; 然後從產生的下拉式清單中選取  **DeletePerson** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-161">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="f2d59-162">預存程序參數與實體屬性之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="f2d59-162">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="f2d59-163"> **Person** 實體類型的插入、更新和刪除作業現在會對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="f2d59-163">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="f2d59-164">如果您想要在使用預存程式來更新或刪除實體時啟用並行檢查，請使用下列其中一個選項：</span><span class="sxs-lookup"><span data-stu-id="f2d59-164">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

- <span data-ttu-id="f2d59-165">使用**OUTPUT**參數從預存程式傳回受影響的資料列數目，然後檢查參數名稱旁邊的 **受影響**的資料列 參數  核取方塊。</span><span class="sxs-lookup"><span data-stu-id="f2d59-165">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="f2d59-166">如果在呼叫作業時傳回的值為零，則會擲回  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-166">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
- <span data-ttu-id="f2d59-167">核取您要用於並行檢查的屬性旁的 [**使用原始值**] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="f2d59-167">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="f2d59-168">嘗試進行更新時，將資料寫回資料庫時，會使用原本從資料庫讀取的屬性值。</span><span class="sxs-lookup"><span data-stu-id="f2d59-168">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="f2d59-169">如果值不符合資料庫中的值，則會擲回**OptimisticConcurrencyException** 。</span><span class="sxs-lookup"><span data-stu-id="f2d59-169">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="f2d59-170">使用模型</span><span class="sxs-lookup"><span data-stu-id="f2d59-170">Use the Model</span></span>

<span data-ttu-id="f2d59-171">開啟定義**Main**方法的**Program.cs**檔案。</span><span class="sxs-lookup"><span data-stu-id="f2d59-171">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="f2d59-172">將下列程式碼新增至 Main 函式中。</span><span class="sxs-lookup"><span data-stu-id="f2d59-172">Add the following code into the Main function.</span></span>

<span data-ttu-id="f2d59-173">此程式碼會建立新的**Person**物件，然後更新物件，最後刪除物件。</span><span class="sxs-lookup"><span data-stu-id="f2d59-173">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- <span data-ttu-id="f2d59-174">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="f2d59-174">Compile and run the application.</span></span> <span data-ttu-id="f2d59-175">程式會產生下列輸出 \*</span><span class="sxs-lookup"><span data-stu-id="f2d59-175">The program produces the following output \*</span></span>

> [!NOTE]
> <span data-ttu-id="f2d59-176">PersonID 是由伺服器自動產生的，因此您很有可能會看到不同的數位 \*</span><span class="sxs-lookup"><span data-stu-id="f2d59-176">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="f2d59-177">如果您使用的是 Visual Studio 的旗艦版，您可以使用 Intellitrace 搭配偵錯工具來查看執行的 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="f2d59-177">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Intellitrace](~/ef6/media/intellitrace.png)
