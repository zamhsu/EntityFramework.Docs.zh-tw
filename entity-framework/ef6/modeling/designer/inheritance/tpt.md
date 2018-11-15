---
title: 設計工具的 TPT 繼承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
ms.openlocfilehash: 84330fba4807620aa242a70cd8ac76a60284416d
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489449"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="3899e-102">設計工具的 TPT 繼承</span><span class="sxs-lookup"><span data-stu-id="3899e-102">Designer TPT Inheritance</span></span>
<span data-ttu-id="3899e-103">此逐步解說示範如何使用 Entity Framework Designer （EF 設計工具） 在模型中實作每一類一表 (TPT) 繼承。</span><span class="sxs-lookup"><span data-stu-id="3899e-103">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="3899e-104">一類一表 (Table-Per-Type) 繼承會在資料庫中使用個別資料表來維護繼承階層架構 (Inheritance Hierarchy) 中每一個類型的非繼承屬性和索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="3899e-104">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="3899e-105">我們將在本逐步解說對應**課程**（基底型別）， **OnlineCourse** （衍生自課程），並**OnsiteCourse** (衍生自**課程**)具有相同名稱的資料表實體。</span><span class="sxs-lookup"><span data-stu-id="3899e-105">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="3899e-106">我們將從資料庫建立模型，並再改變實作 TPT 繼承模型。</span><span class="sxs-lookup"><span data-stu-id="3899e-106">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="3899e-107">您可以也使用 Model First 啟動，並接著從模型產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="3899e-107">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="3899e-108">EF 設計工具預設會使用 TPT 策略，因此任何繼承模型中的將會對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="3899e-108">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="3899e-109">其他的繼承選項</span><span class="sxs-lookup"><span data-stu-id="3899e-109">Other Inheritance Options</span></span>

<span data-ttu-id="3899e-110">每個階層的資料表 (TPH) 是繼承的另一種類型中哪一個資料庫資料表繼承的用來維護繼承階層架構中的實體類型的所有資料。</span><span class="sxs-lookup"><span data-stu-id="3899e-110">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span>  <span data-ttu-id="3899e-111">如需如何將對應 Entity Designer 中的每個階層的資料表繼承資訊，請參閱[EF 設計工具 TPH 繼承](~/ef6/modeling/designer/inheritance/tph.md)。</span><span class="sxs-lookup"><span data-stu-id="3899e-111">For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](~/ef6/modeling/designer/inheritance/tph.md).</span></span> 

<span data-ttu-id="3899e-112">請注意，每個實體資料表類型 (TPC) 繼承和混合的繼承模型支援 Entity Framework 執行階段，但不是會受到 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="3899e-112">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="3899e-113">如果您想要使用的 TPC 或混合的繼承，您會有兩個選項： 使用 Code First，或以手動方式編輯 EDMX 檔。</span><span class="sxs-lookup"><span data-stu-id="3899e-113">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="3899e-114">如果您選擇使用 EDMX 檔案，對應的 [詳細資料] 視窗將會進入 「 安全模式 」，您將無法使用設計工具來變更對應。</span><span class="sxs-lookup"><span data-stu-id="3899e-114">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3899e-115">必要條件</span><span class="sxs-lookup"><span data-stu-id="3899e-115">Prerequisites</span></span>

<span data-ttu-id="3899e-116">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="3899e-116">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="3899e-117">新版的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="3899e-117">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="3899e-118">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="3899e-118">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="3899e-119">設定專案</span><span class="sxs-lookup"><span data-stu-id="3899e-119">Set up the Project</span></span>

-   <span data-ttu-id="3899e-120">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="3899e-120">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="3899e-121">選取 **檔案&gt;新增-&gt;專案**</span><span class="sxs-lookup"><span data-stu-id="3899e-121">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="3899e-122">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。</span><span class="sxs-lookup"><span data-stu-id="3899e-122">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="3899e-123">請輸入**TPTDBFirstSample**做為名稱。</span><span class="sxs-lookup"><span data-stu-id="3899e-123">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="3899e-124">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="3899e-124">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="3899e-125">建立模型</span><span class="sxs-lookup"><span data-stu-id="3899e-125">Create a Model</span></span>

-   <span data-ttu-id="3899e-126">以滑鼠右鍵按一下方案總管 中的專案，然後選取**新增-&gt;新的項目**。</span><span class="sxs-lookup"><span data-stu-id="3899e-126">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="3899e-127">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。</span><span class="sxs-lookup"><span data-stu-id="3899e-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="3899e-128">請輸入**TPTModel.edmx**為檔案名稱，然後按一下**新增**。</span><span class="sxs-lookup"><span data-stu-id="3899e-128">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="3899e-129">在 [選擇模型內容] 對話方塊中，選取 [產生資料庫 \* \* 從然後**下一步]**。</span><span class="sxs-lookup"><span data-stu-id="3899e-129">In the Choose Model Contents dialog box, select\*\* Generate from database\*\*, and then click **Next**.</span></span>
-   <span data-ttu-id="3899e-130">按一下 **新的連接**。</span><span class="sxs-lookup"><span data-stu-id="3899e-130">Click **New Connection**.</span></span>
    <span data-ttu-id="3899e-131">在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。</span><span class="sxs-lookup"><span data-stu-id="3899e-131">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="3899e-132">[選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。</span><span class="sxs-lookup"><span data-stu-id="3899e-132">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="3899e-133">在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取**部門**，**課程、 OnlineCourse 和 OnsiteCourse**資料表。</span><span class="sxs-lookup"><span data-stu-id="3899e-133">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="3899e-134">按一下 [ **完成**]。</span><span class="sxs-lookup"><span data-stu-id="3899e-134">Click **Finish**.</span></span>

<span data-ttu-id="3899e-135">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="3899e-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="3899e-136">您在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都加入至模型。</span><span class="sxs-lookup"><span data-stu-id="3899e-136">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="3899e-137">實作單表 (Table-per-type) 繼承</span><span class="sxs-lookup"><span data-stu-id="3899e-137">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="3899e-138">在設計介面中，以滑鼠右鍵按一下**OnlineCourse**實體類型，然後選取**屬性**。</span><span class="sxs-lookup"><span data-stu-id="3899e-138">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="3899e-139">在 [**屬性**] 視窗中，會將基底類型屬性設定為**課程**。</span><span class="sxs-lookup"><span data-stu-id="3899e-139">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="3899e-140">以滑鼠右鍵按一下**OnsiteCourse**實體類型，然後選取**屬性**。</span><span class="sxs-lookup"><span data-stu-id="3899e-140">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="3899e-141">在 [**屬性**] 視窗中，會將基底類型屬性設定為**課程**。</span><span class="sxs-lookup"><span data-stu-id="3899e-141">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="3899e-142">以滑鼠右鍵按一下之間的關聯 （列） **OnlineCourse**並**課程**實體類型。</span><span class="sxs-lookup"><span data-stu-id="3899e-142">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="3899e-143">選取 **從模型刪除**。</span><span class="sxs-lookup"><span data-stu-id="3899e-143">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="3899e-144">以滑鼠右鍵按一下之間的關聯**OnsiteCourse**並**課程**實體類型。</span><span class="sxs-lookup"><span data-stu-id="3899e-144">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="3899e-145">選取 **從模型刪除**。</span><span class="sxs-lookup"><span data-stu-id="3899e-145">Select **Delete from Model**.</span></span>

<span data-ttu-id="3899e-146">我們現在將會刪除**CourseID**屬性從**OnlineCourse**並**OnsiteCourse**因為這些類別會繼承**CourseID**從**課程**基底型別。</span><span class="sxs-lookup"><span data-stu-id="3899e-146">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="3899e-147">以滑鼠右鍵按一下**CourseID**屬性**OnlineCourse**實體類型，然後選取**從模型刪除**。</span><span class="sxs-lookup"><span data-stu-id="3899e-147">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="3899e-148">以滑鼠右鍵按一下**CourseID**屬性**OnsiteCourse**實體類型，然後選取**從模型刪除**</span><span class="sxs-lookup"><span data-stu-id="3899e-148">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="3899e-149">現已實作一類一表 (Table-Per-Type) 繼承。</span><span class="sxs-lookup"><span data-stu-id="3899e-149">Table-per-type inheritance is now implemented.</span></span>

![TPT](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="3899e-151">使用模型</span><span class="sxs-lookup"><span data-stu-id="3899e-151">Use the Model</span></span>

<span data-ttu-id="3899e-152">開啟**Program.cs**檔**Main**方法定義。</span><span class="sxs-lookup"><span data-stu-id="3899e-152">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="3899e-153">貼上下列程式碼插入**Main**函式。</span><span class="sxs-lookup"><span data-stu-id="3899e-153">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="3899e-154">三個查詢執行的程式碼。</span><span class="sxs-lookup"><span data-stu-id="3899e-154">The code executes three queries.</span></span> <span data-ttu-id="3899e-155">第一個查詢會傳回所有**課程**指定部門產生關聯。</span><span class="sxs-lookup"><span data-stu-id="3899e-155">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="3899e-156">第二個查詢會使用**OfType**方法，以傳回**OnlineCourses**指定部門產生關聯。</span><span class="sxs-lookup"><span data-stu-id="3899e-156">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="3899e-157">第三個查詢會傳回**OnsiteCourses**。</span><span class="sxs-lookup"><span data-stu-id="3899e-157">The third query returns **OnsiteCourses**.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
