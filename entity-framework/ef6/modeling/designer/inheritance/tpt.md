---
title: 設計工具 TPT 繼承-EF6
description: Entity Framework 6 中的設計工具 TPT 繼承
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: f8e4198278fb44309916d57b44fb9af41d572108
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066170"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="e6543-103">設計工具 TPT 繼承</span><span class="sxs-lookup"><span data-stu-id="e6543-103">Designer TPT Inheritance</span></span>
<span data-ttu-id="e6543-104">本逐步解說將示範如何使用 Entity Framework Designer (EF Designer) ，在您的模型中執行每一類型的資料表 (TPT) 繼承。</span><span class="sxs-lookup"><span data-stu-id="e6543-104">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e6543-105">一類一表 (Table-Per-Type) 繼承會在資料庫中使用個別資料表來維護繼承階層架構 (Inheritance Hierarchy) 中每一個類型的非繼承屬性和索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="e6543-105">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="e6543-106">在這個逐步解說中，我們會將**課程** (基底類型) 、 **OnlineCourse** (衍生自課程) ，而 **OnsiteCourse**   (衍生自**課程**) 實體到具有相同名稱的資料表。</span><span class="sxs-lookup"><span data-stu-id="e6543-106">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="e6543-107">我們會從資料庫建立模型，然後改變模型以執行 TPT 繼承。</span><span class="sxs-lookup"><span data-stu-id="e6543-107">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="e6543-108">您也可以從 Model First 開始，然後從模型產生資料庫。</span><span class="sxs-lookup"><span data-stu-id="e6543-108">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="e6543-109">EF Designer 預設會使用 TPT 策略，因此模型中的任何繼承都會對應到不同的資料表。</span><span class="sxs-lookup"><span data-stu-id="e6543-109">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="e6543-110">其他繼承選項</span><span class="sxs-lookup"><span data-stu-id="e6543-110">Other Inheritance Options</span></span>

<span data-ttu-id="e6543-111">每個階層的資料表 (TPH) 是另一種繼承類型，也就是使用一個資料庫資料表來維護繼承階層中所有實體類型的資料。</span><span class="sxs-lookup"><span data-stu-id="e6543-111">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span><span data-ttu-id="e6543-112">如需有關如何使用 Entity Designer 來對應每個階層的資料表繼承的詳細資訊，請參閱 [EF DESIGNER TPH 繼承](xref:ef6/modeling/designer/inheritance/tph)。</span><span class="sxs-lookup"><span data-stu-id="e6543-112">  For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](xref:ef6/modeling/designer/inheritance/tph).</span></span> 

<span data-ttu-id="e6543-113">請注意， (TPC) 和混合式繼承模型的資料表個別具象型別繼承會受到 Entity Framework 執行時間支援，但 EF Designer 並不支援。</span><span class="sxs-lookup"><span data-stu-id="e6543-113">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="e6543-114">如果您想要使用 TPC 或混合的繼承，您有兩個選項： [使用 Code First] 或 [手動編輯 EDMX 檔案]。</span><span class="sxs-lookup"><span data-stu-id="e6543-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="e6543-115">如果您選擇使用 EDMX 檔案，[對應詳細資料] 視窗將會進入「安全模式」，而您將無法使用設計工具來變更對應。</span><span class="sxs-lookup"><span data-stu-id="e6543-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6543-116">必要條件</span><span class="sxs-lookup"><span data-stu-id="e6543-116">Prerequisites</span></span>

<span data-ttu-id="e6543-117">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="e6543-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="e6543-118">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="e6543-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="e6543-119">[School 範例資料庫](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="e6543-119">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="e6543-120">設定專案</span><span class="sxs-lookup"><span data-stu-id="e6543-120">Set up the Project</span></span>

-   <span data-ttu-id="e6543-121">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="e6543-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="e6543-122">選取檔案 **- &gt; 新增- &gt; 專案**</span><span class="sxs-lookup"><span data-stu-id="e6543-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="e6543-123">在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本。</span><span class="sxs-lookup"><span data-stu-id="e6543-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="e6543-124">輸入 **TPTDBFirstSample**   作為名稱。</span><span class="sxs-lookup"><span data-stu-id="e6543-124">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="e6543-125">選取 [確定] \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="e6543-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="e6543-126">建立模型</span><span class="sxs-lookup"><span data-stu-id="e6543-126">Create a Model</span></span>

-   <span data-ttu-id="e6543-127">以滑鼠右鍵按一下方案總管中的專案，然後選取 [ **加入- &gt; 新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-127">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="e6543-128">從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。</span><span class="sxs-lookup"><span data-stu-id="e6543-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="e6543-129">輸入 **TPTModel** 的檔案名，然後按一下 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-129">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="e6543-130">在 [選擇模型內容] 對話方塊中，選取 [\*\*   從資料庫產生**]，然後按 **[下一步]\*\*。</span><span class="sxs-lookup"><span data-stu-id="e6543-130">In the Choose Model Contents dialog box, select** Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="e6543-131">按一下 [ **新增連接**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-131">Click **New Connection**.</span></span>
    <span data-ttu-id="e6543-132">在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如\*\* (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School\*\*   作為資料庫名稱，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="e6543-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="e6543-133">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="e6543-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="e6543-134">在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取 [ **部門**]、[ **課程]、[OnlineCourse] 和 [OnsiteCourse** ] 資料表。</span><span class="sxs-lookup"><span data-stu-id="e6543-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="e6543-135">按一下 **[完成]**。</span><span class="sxs-lookup"><span data-stu-id="e6543-135">Click **Finish**.</span></span>

<span data-ttu-id="e6543-136">隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。</span><span class="sxs-lookup"><span data-stu-id="e6543-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="e6543-137">在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都會加入至模型中。</span><span class="sxs-lookup"><span data-stu-id="e6543-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="e6543-138">執行每一類型的資料表繼承</span><span class="sxs-lookup"><span data-stu-id="e6543-138">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="e6543-139">在設計介面上，以滑鼠右鍵按一下 [ **OnlineCourse** ] 實體類型，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-139">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e6543-140">在 [ **屬性** ] 視窗中，將 [基底類型] 屬性設定為 [ **課程**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-140">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e6543-141">以滑鼠右鍵按一下 [ **OnsiteCourse** ] 實體類型，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-141">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e6543-142">在 [ **屬性** ] 視窗中，將 [基底類型] 屬性設定為 [ **課程**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-142">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e6543-143">以滑鼠右鍵按一下 [ **OnlineCourse** ] 和 [ **課程** ] 實體類型之間的關聯 (行) 。</span><span class="sxs-lookup"><span data-stu-id="e6543-143">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e6543-144">選取 [ **從模型刪除**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-144">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="e6543-145">以滑鼠右鍵按一下 [ **OnsiteCourse** ] 和 [ **課程** ] 實體類型之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="e6543-145">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e6543-146">選取 [ **從模型刪除**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-146">Select **Delete from Model**.</span></span>

<span data-ttu-id="e6543-147">我們現在將刪除**OnlineCourse**和**OnsiteCourse**中的**CourseID**屬性，因為這些類別會從**課程**基底類型繼承**CourseID** 。</span><span class="sxs-lookup"><span data-stu-id="e6543-147">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="e6543-148">以滑鼠右鍵按一下 [ **OnlineCourse** ] 實體類型的 [ **CourseID** ] 屬性，然後選取 [**從模型刪除**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-148">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="e6543-149">以滑鼠右鍵按一下 [ **OnsiteCourse** ] 實體類型的 [ **CourseID** ] 屬性，然後選取 [**從模型刪除**]。</span><span class="sxs-lookup"><span data-stu-id="e6543-149">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="e6543-150">現已實作一類一表 (Table-Per-Type) 繼承。</span><span class="sxs-lookup"><span data-stu-id="e6543-150">Table-per-type inheritance is now implemented.</span></span>

![每一類型的資料表](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="e6543-152">使用模型</span><span class="sxs-lookup"><span data-stu-id="e6543-152">Use the Model</span></span>

<span data-ttu-id="e6543-153">開啟定義**Main**方法的**Program.cs**檔案。</span><span class="sxs-lookup"><span data-stu-id="e6543-153">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="e6543-154">將下列程式碼貼入 **Main** 函數中。</span><span class="sxs-lookup"><span data-stu-id="e6543-154">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="e6543-155">程式碼會執行三個查詢。</span><span class="sxs-lookup"><span data-stu-id="e6543-155">The code executes three queries.</span></span> <span data-ttu-id="e6543-156">第一個查詢會傳回與指定部門相關的所有 **課程** 。</span><span class="sxs-lookup"><span data-stu-id="e6543-156">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="e6543-157">第二個查詢會使用 **OfType** 方法來傳回與指定部門相關的 **OnlineCourses** 。</span><span class="sxs-lookup"><span data-stu-id="e6543-157">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="e6543-158">第三個查詢會傳回 **OnsiteCourses**。</span><span class="sxs-lookup"><span data-stu-id="e6543-158">The third query returns **OnsiteCourses**.</span></span>

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
