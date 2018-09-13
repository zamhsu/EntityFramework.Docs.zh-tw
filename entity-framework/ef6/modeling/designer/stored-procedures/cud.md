---
title: 設計工具的 CUD 預存程序-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
ms.openlocfilehash: 35a00aa817c8643352c517c233977efd49e3baac
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489553"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="27146-102">設計工具的 CUD 預存程序</span><span class="sxs-lookup"><span data-stu-id="27146-102">Designer CUD Stored Procedures</span></span>
<span data-ttu-id="27146-103">此逐步解說顯示如何將對應建立\\插入、 更新和刪除 (CUD) 作業的實體類型使用 Entity Framework Designer （EF 設計工具） 的預存程序。</span><span class="sxs-lookup"><span data-stu-id="27146-103">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span>  <span data-ttu-id="27146-104">根據預設，Entity Framework 會自動產生 CUD 作業的 SQL 陳述式，但您也可以將預存程序對應至這些作業。</span><span class="sxs-lookup"><span data-stu-id="27146-104">By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="27146-105">請注意，Code First 不支援對應至預存程序或函式。</span><span class="sxs-lookup"><span data-stu-id="27146-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="27146-106">不過，您可以使用 System.Data.Entity.DbSet.SqlQuery 方法呼叫預存程序或函式。</span><span class="sxs-lookup"><span data-stu-id="27146-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="27146-107">例如: </span><span class="sxs-lookup"><span data-stu-id="27146-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="27146-108">預存程序的對應 CUD 作業時的考量</span><span class="sxs-lookup"><span data-stu-id="27146-108">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="27146-109">當將 CUD 作業對應至預存程序中，適用下列考量：</span><span class="sxs-lookup"><span data-stu-id="27146-109">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span> 

-   <span data-ttu-id="27146-110">如果您要對應 CUD 作業的其中一個預存程序，將對應所有人都。</span><span class="sxs-lookup"><span data-stu-id="27146-110">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="27146-111">如果您不會對應所有三個，對應的作業將會失敗，如果執行，而且**UpdateException**就會擲回。</span><span class="sxs-lookup"><span data-stu-id="27146-111">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
-   <span data-ttu-id="27146-112">您必須將預存程序的每個參數對應至實體屬性。</span><span class="sxs-lookup"><span data-stu-id="27146-112">You must map every parameter of the stored procedure to entity properties.</span></span>
-   <span data-ttu-id="27146-113">如果伺服器就會產生主索引鍵值插入的資料列，您必須將此值對應回實體的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="27146-113">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="27146-114">在範例中，如下所示**InsertPerson**預存程序會傳回新建立的主索引鍵做為預存程序的結果集的一部分。</span><span class="sxs-lookup"><span data-stu-id="27146-114">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="27146-115">主索引鍵對應到實體索引鍵 (**PersonID**) 使用**&lt;加入結果繫結&gt;** EF 設計工具的功能。</span><span class="sxs-lookup"><span data-stu-id="27146-115">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
-   <span data-ttu-id="27146-116">預存程序呼叫是對應與 1:1 概念模型中的實體。</span><span class="sxs-lookup"><span data-stu-id="27146-116">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="27146-117">例如，如果您實作在概念模型，然後對應中的繼承階層架構 CUD 預存程序**父代**（基本） 和**子**（衍生） 的實體，儲存**子系**變更都只會呼叫**子**的預存程序，就不會觸發**父**的預存程序呼叫。</span><span class="sxs-lookup"><span data-stu-id="27146-117">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="27146-118">必要條件</span><span class="sxs-lookup"><span data-stu-id="27146-118">Prerequisites</span></span>

<span data-ttu-id="27146-119">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="27146-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="27146-120">新版的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="27146-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="27146-121">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="27146-121">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="27146-122">設定專案</span><span class="sxs-lookup"><span data-stu-id="27146-122">Set up the Project</span></span>

-   <span data-ttu-id="27146-123">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="27146-123">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="27146-124">選取 **檔案&gt;新增-&gt;專案**</span><span class="sxs-lookup"><span data-stu-id="27146-124">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="27146-125">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。</span><span class="sxs-lookup"><span data-stu-id="27146-125">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="27146-126">請輸入**CUDSProcsSample**做為名稱。</span><span class="sxs-lookup"><span data-stu-id="27146-126">Enter **CUDSProcsSample** as the name.</span></span>
-   <span data-ttu-id="27146-127">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="27146-127">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="27146-128">建立模型</span><span class="sxs-lookup"><span data-stu-id="27146-128">Create a Model</span></span>

-   <span data-ttu-id="27146-129">以滑鼠右鍵按一下方案總管 中的專案名稱，然後選取**新增-&gt;新的項目**。</span><span class="sxs-lookup"><span data-stu-id="27146-129">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="27146-130">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。</span><span class="sxs-lookup"><span data-stu-id="27146-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="27146-131">請輸入**CUDSProcs.edmx**為檔案名稱，然後按一下**新增**。</span><span class="sxs-lookup"><span data-stu-id="27146-131">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="27146-132">在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。</span><span class="sxs-lookup"><span data-stu-id="27146-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="27146-133">按一下 **新的連接**。</span><span class="sxs-lookup"><span data-stu-id="27146-133">Click **New Connection**.</span></span> <span data-ttu-id="27146-134">在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。</span><span class="sxs-lookup"><span data-stu-id="27146-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="27146-135">[選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。</span><span class="sxs-lookup"><span data-stu-id="27146-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="27146-136">在 選擇您的資料庫物件對話方塊的 **資料表**節點中，選取**人員**資料表。</span><span class="sxs-lookup"><span data-stu-id="27146-136">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
-   <span data-ttu-id="27146-137">此外，請選取下的下列預存程序**預存程序和函式**節點： **DeletePerson**， **InsertPerson**，和**UpdatePerson**.</span><span class="sxs-lookup"><span data-stu-id="27146-137">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span> 
-   <span data-ttu-id="27146-138">從 Visual Studio 2012 EF 設計工具支援大量匯入預存程序。</span><span class="sxs-lookup"><span data-stu-id="27146-138">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="27146-139">**匯入選取的預存程序和函式的實體模型到**依預設會勾選。</span><span class="sxs-lookup"><span data-stu-id="27146-139">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="27146-140">在此範例中，我們有預存程序插入、 更新和刪除實體類型，因為我們不想要匯入它們，並將取消核取此核取方塊。</span><span class="sxs-lookup"><span data-stu-id="27146-140">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span> 

    ![匯入 S 程序](~/ef6/media/importsprocs.jpg)

-   <span data-ttu-id="27146-142">按一下 [ **完成**]。</span><span class="sxs-lookup"><span data-stu-id="27146-142">Click **Finish**.</span></span>
    <span data-ttu-id="27146-143">EF 設計工具中，可提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="27146-143">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="27146-144">將 Person 實體對應至預存程序</span><span class="sxs-lookup"><span data-stu-id="27146-144">Map the Person Entity to Stored Procedures</span></span>

-   <span data-ttu-id="27146-145">以滑鼠右鍵按一下**Person**實體類型，然後選取**預存程序對應**。</span><span class="sxs-lookup"><span data-stu-id="27146-145">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
-   <span data-ttu-id="27146-146">預存程序對應會出現在**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="27146-146">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="27146-147">按一下  **&lt;選取 插入函數&gt;**。</span><span class="sxs-lookup"><span data-stu-id="27146-147">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="27146-148">此欄位會變成儲存模型中預存程序的下拉式清單，可以對應到概念模型中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="27146-148">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="27146-149">選取  **InsertPerson**從下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="27146-149">Select **InsertPerson** from the drop-down list.</span></span>
-   <span data-ttu-id="27146-150">預存程序參數與實體屬性之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="27146-150">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="27146-151">請注意，箭頭表示對應方向：為預存程序參數提供屬性值。</span><span class="sxs-lookup"><span data-stu-id="27146-151">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
-   <span data-ttu-id="27146-152">按一下  **&lt;加入結果繫結&gt;**。</span><span class="sxs-lookup"><span data-stu-id="27146-152">Click **&lt;Add Result Binding&gt;**.</span></span>
-   <span data-ttu-id="27146-153">型別**NewPersonID**，所傳回的參數名稱**InsertPerson**預存程序。</span><span class="sxs-lookup"><span data-stu-id="27146-153">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="27146-154">請確定輸入開頭或尾端空格。</span><span class="sxs-lookup"><span data-stu-id="27146-154">Make sure not to type leading or trailing spaces.</span></span>
-   <span data-ttu-id="27146-155">按 **Enter** 鍵。</span><span class="sxs-lookup"><span data-stu-id="27146-155">Press **Enter**.</span></span>
-   <span data-ttu-id="27146-156">根據預設， **NewPersonID**對應到實體索引鍵**PersonID**。</span><span class="sxs-lookup"><span data-stu-id="27146-156">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="27146-157">請注意，箭頭表示對應方向：為屬性提供結果資料行的值。</span><span class="sxs-lookup"><span data-stu-id="27146-157">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![對應詳細資料](~/ef6/media/mappingdetails.png)

-   <span data-ttu-id="27146-159">按一下  **&lt;選取更新函式&gt;** ，然後選取**UpdatePerson**從產生的下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="27146-159">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
-   <span data-ttu-id="27146-160">預存程序參數與實體屬性之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="27146-160">Default mappings between stored procedure parameters and entity properties appear.</span></span>
-   <span data-ttu-id="27146-161">按一下  **&lt;選取刪除函式&gt;** ，然後選取**DeletePerson**從產生的下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="27146-161">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
-   <span data-ttu-id="27146-162">預存程序參數與實體屬性之間的預設對應隨即出現。</span><span class="sxs-lookup"><span data-stu-id="27146-162">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="27146-163">插入、 更新和刪除作業**人員**實體類型現在會對應至預存程序。</span><span class="sxs-lookup"><span data-stu-id="27146-163">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="27146-164">如果您想要啟用並行檢查更新或刪除實體的預存程序時，請使用下列選項之一：</span><span class="sxs-lookup"><span data-stu-id="27146-164">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

-   <span data-ttu-id="27146-165">使用**輸出**參數來傳回的數目受影響的資料列的預存程序和核取**資料列受影響的參數**參數名稱旁的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="27146-165">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="27146-166">如果傳回的值為零，呼叫作業時， [ **OptimisticConcurrencyException** ](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)就會擲回。</span><span class="sxs-lookup"><span data-stu-id="27146-166">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
-   <span data-ttu-id="27146-167">請檢查**使用原始值**您想要用於並行存取檢查屬性旁的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="27146-167">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="27146-168">當嘗試更新時，資料寫回至資料庫時將使用原本從資料庫讀取屬性的值。</span><span class="sxs-lookup"><span data-stu-id="27146-168">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="27146-169">如果值不符合資料庫中的值**OptimisticConcurrencyException**就會擲回。</span><span class="sxs-lookup"><span data-stu-id="27146-169">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="27146-170">使用模型</span><span class="sxs-lookup"><span data-stu-id="27146-170">Use the Model</span></span>

<span data-ttu-id="27146-171">開啟**Program.cs**檔**Main**方法定義。</span><span class="sxs-lookup"><span data-stu-id="27146-171">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="27146-172">將下列程式碼新增至 Main 函式。</span><span class="sxs-lookup"><span data-stu-id="27146-172">Add the following code into the Main function.</span></span>

<span data-ttu-id="27146-173">程式碼會建立新**人員**物件，然後更新物件，而最後會刪除物件。</span><span class="sxs-lookup"><span data-stu-id="27146-173">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>         

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

-   <span data-ttu-id="27146-174">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="27146-174">Compile and run the application.</span></span> <span data-ttu-id="27146-175">此程式會產生下列輸出 \*</span><span class="sxs-lookup"><span data-stu-id="27146-175">The program produces the following output \*</span></span>
    >[!NOTE]
> <span data-ttu-id="27146-176">PersonID 是自動產生的伺服器，因此您很可能會看到不同的號碼 \*</span><span class="sxs-lookup"><span data-stu-id="27146-176">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

```
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="27146-177">如果您正在使用的 Visual Studio Ultimate 的版本，您可以偵錯工具使用 Intellitrace，查看執行的 SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="27146-177">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Intellitrace](~/ef6/media/intellitrace.png)
