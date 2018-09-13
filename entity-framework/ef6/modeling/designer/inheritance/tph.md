---
title: 設計工具的 TPH 繼承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
ms.openlocfilehash: 43ba34a98c3960a7a3052a00e2ed2751c2f2b121
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490107"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="dfd6f-102">設計工具的 TPH 繼承</span><span class="sxs-lookup"><span data-stu-id="dfd6f-102">Designer TPH Inheritance</span></span>
<span data-ttu-id="dfd6f-103">此逐步解說示範如何實作 Entity Framework Designer （EF 設計工具） 的概念模型中的每個階層的資料表 (TPH) 繼承。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-103">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="dfd6f-104">TPH 繼承會使用一個資料庫資料表來維護繼承階層架構中的實體類型的所有資料。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-104">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="dfd6f-105">在本逐步解說我們會將 Person 資料表對應至三個實體類型： Person （基底類型）、 （衍生自 Person） 的學生和講師 （衍生自 Person）。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-105">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="dfd6f-106">我們將從資料庫 (Database First) 建立概念模型，並再來改變模型來實作 TPH 繼承的使用 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-106">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="dfd6f-107">它是無法對應到使用 Model First TPH 繼承，但您必須撰寫您自己也就是複雜的資料庫產生工作流程。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-107">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="dfd6f-108">然後，您就會指派此工作流程**資料庫產生工作流程**EF 設計工具中的屬性。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-108">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="dfd6f-109">簡單的替代方法是使用 Code First。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-109">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="dfd6f-110">其他的繼承選項</span><span class="sxs-lookup"><span data-stu-id="dfd6f-110">Other Inheritance Options</span></span>

<span data-ttu-id="dfd6f-111">每一類一表 (TPT) 是繼承的另一種類型所在資料庫中的個別資料表會對應到參與繼承的實體。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-111">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span>  <span data-ttu-id="dfd6f-112">如需如何將對應表 (Table-per-type) 繼承與 EF 設計工具的資訊，請參閱[EF 設計工具 TPT 繼承](~/ef6/modeling/designer/inheritance/tpt.md)。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-112">For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](~/ef6/modeling/designer/inheritance/tpt.md).</span></span>

<span data-ttu-id="dfd6f-113">資料表每個實體類型的繼承 (TPC) 和混合的繼承模型支援 Entity Framework 執行階段，但不是會受到 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-113">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="dfd6f-114">如果您想要使用的 TPC 或混合的繼承，您會有兩個選項： 使用 Code First，或以手動方式編輯 EDMX 檔。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="dfd6f-115">如果您選擇使用 EDMX 檔案，對應的 [詳細資料] 視窗將會進入 「 安全模式 」，您將無法使用設計工具來變更對應。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dfd6f-116">必要條件</span><span class="sxs-lookup"><span data-stu-id="dfd6f-116">Prerequisites</span></span>

<span data-ttu-id="dfd6f-117">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="dfd6f-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="dfd6f-118">新版的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="dfd6f-119">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-119">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="dfd6f-120">設定專案</span><span class="sxs-lookup"><span data-stu-id="dfd6f-120">Set up the Project</span></span>

-   <span data-ttu-id="dfd6f-121">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="dfd6f-122">選取 **檔案&gt;新增-&gt;專案**</span><span class="sxs-lookup"><span data-stu-id="dfd6f-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="dfd6f-123">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="dfd6f-124">請輸入**TPHDBFirstSample**做為名稱。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-124">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="dfd6f-125">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="dfd6f-126">建立模型</span><span class="sxs-lookup"><span data-stu-id="dfd6f-126">Create a Model</span></span>

-   <span data-ttu-id="dfd6f-127">以滑鼠右鍵按一下方案總管 中的專案名稱，然後選取**新增-&gt;新的項目**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-127">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="dfd6f-128">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="dfd6f-129">請輸入**TPHModel.edmx**為檔案名稱，然後按一下**新增**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-129">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="dfd6f-130">在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-130">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="dfd6f-131">按一下 **新的連接**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-131">Click **New Connection**.</span></span>
    <span data-ttu-id="dfd6f-132">在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="dfd6f-133">[選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="dfd6f-134">在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取**人員**資料表。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="dfd6f-135">按一下 [ **完成**]。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-135">Click **Finish**.</span></span>

<span data-ttu-id="dfd6f-136">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="dfd6f-137">您在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都加入至模型。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="dfd6f-138">也就是如何**人員**資料表會尋找資料庫中。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-138">That is how the **Person** table looks in the database.</span></span>

![Person 資料表](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="dfd6f-140">實作每個階層的資料表繼承</span><span class="sxs-lookup"><span data-stu-id="dfd6f-140">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="dfd6f-141">**Person**資料表有**鑑別子**資料行，它可以有兩個值之一:"Student"和"Instructor"。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-141">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="dfd6f-142">值而定**Person**資料表將會對應到**學生**實體或**講師**實體。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-142">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="dfd6f-143">**Person**資料表也有兩個資料行， **HireDate**並**EnrollmentDate**，這必須是**可為 null**因為人員不能是student 和 instructor 一次 （至少不在本逐步解說）。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-143">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="dfd6f-144">加入新的實體</span><span class="sxs-lookup"><span data-stu-id="dfd6f-144">Add new Entities</span></span>

-   <span data-ttu-id="dfd6f-145">加入新的實體。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-145">Add a new entity.</span></span>
    <span data-ttu-id="dfd6f-146">若要這樣做，請在 Entity Framework Designer 中，設計介面的空白處上按一下滑鼠右鍵，然後選取**Add-&gt;實體**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-146">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="dfd6f-147">型別**講師**for**實體名稱**，然後選取**人員**從下拉式清單**基底型別**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-147">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="dfd6f-148">按一下 [確定 **Deploying Office Solutions**]。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-148">Click **OK**.</span></span>
-   <span data-ttu-id="dfd6f-149">加入另一個新的實體。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-149">Add another new entity.</span></span> <span data-ttu-id="dfd6f-150">型別**學生**如**實體名稱**，然後選取**人員**的下拉式清單從**基底型別**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-150">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="dfd6f-151">兩個新的實體類型已新增至設計介面。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-151">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="dfd6f-152">箭號指向新的實體類型，以從**Person**實體類型，這表示**人員**是新的實體類型的基底類型。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-152">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="dfd6f-153">以滑鼠右鍵按一下**HireDate**屬性**人員**實體。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-153">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="dfd6f-154">選取 **剪下**（或使用 Ctrl-X 鍵）。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-154">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="dfd6f-155">以滑鼠右鍵按一下**講師**實體，然後選取**貼上**（或使用 Ctrl-V 鍵）。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-155">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="dfd6f-156">以滑鼠右鍵按一下**HireDate**屬性，並選取**屬性**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-156">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="dfd6f-157">在 **屬性**視窗中，將**Nullable**屬性設**false**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-157">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="dfd6f-158">以滑鼠右鍵按一下**EnrollmentDate**屬性**人員**實體。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-158">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="dfd6f-159">選取 **剪下**（或使用 Ctrl-X 鍵）。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-159">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="dfd6f-160">以滑鼠右鍵按一下**學生**實體，然後選取**貼上 （或使用 Ctrl-V 鍵）。**</span><span class="sxs-lookup"><span data-stu-id="dfd6f-160">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="dfd6f-161">選取  **EnrollmentDate**屬性並設定**Nullable**屬性設**false**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-161">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="dfd6f-162">選取 **人員**實體類型。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-162">Select the **Person** entity type.</span></span> <span data-ttu-id="dfd6f-163">在 **屬性**視窗中，將其**抽象**屬性設 **，則為 true**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-163">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="dfd6f-164">刪除**鑑別子**屬性從**人員**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-164">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="dfd6f-165">應刪除它的原因是下列一節所述。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-165">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="dfd6f-166">對應實體</span><span class="sxs-lookup"><span data-stu-id="dfd6f-166">Map the entities</span></span>

-   <span data-ttu-id="dfd6f-167">以滑鼠右鍵按一下**講師**，然後選取**資料表對應。**</span><span class="sxs-lookup"><span data-stu-id="dfd6f-167">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="dfd6f-168">對應詳細資料視窗中選取 [Instructor] 實體。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-168">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="dfd6f-169">按一下  **&lt;新增資料表或檢視表&gt;** 中**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-169">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="dfd6f-170">**&lt;新增資料表或檢視表&gt;** 欄位會變成下拉式清單的資料表或檢視選取的實體可以對應。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-170">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="dfd6f-171">選取 **人員**從下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-171">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="dfd6f-172">**對應詳細資料**視窗會更新成預設資料行對應及加入條件的選項。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-172">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="dfd6f-173">按一下  **&lt;新增條件&gt;**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-173">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="dfd6f-174">**&lt;新增條件&gt;** 欄位會變成可以設定條件的資料行的下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-174">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="dfd6f-175">選取 **鑑別子**從下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-175">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="dfd6f-176">在 **運算子**資料行**對應詳細資料**視窗中，選取 = 從下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-176">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="dfd6f-177">在 **值/屬性**資料行中輸入**講師**。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-177">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="dfd6f-178">最後的結果應該如下所示：</span><span class="sxs-lookup"><span data-stu-id="dfd6f-178">The end result should look like this:</span></span>

    ![對應詳細資料](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="dfd6f-180">重複這些步驟**學生**實體類型，但請相等條件**學生**值。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-180">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="dfd6f-181">*我們想要移除的原因**鑑別子**屬性，是因為您不能超過一次對應資料表資料行。本專欄將用於條件式對應，因此無法用於屬性以及對應。它可以用於兩者，如果條件使用的唯一方式**Is Null**或是**Is Not Null**比較。*</span><span class="sxs-lookup"><span data-stu-id="dfd6f-181">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="dfd6f-182">現已實作每個階層的資料表繼承。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-182">Table-per-hierarchy inheritance is now implemented.</span></span>

![最終 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="dfd6f-184">使用模型</span><span class="sxs-lookup"><span data-stu-id="dfd6f-184">Use the Model</span></span>

<span data-ttu-id="dfd6f-185">開啟**Program.cs**檔**Main**方法定義。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-185">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="dfd6f-186">貼上下列程式碼插入**Main**函式。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-186">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="dfd6f-187">三個查詢執行的程式碼。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-187">The code executes three queries.</span></span> <span data-ttu-id="dfd6f-188">第一個查詢會傳回所有**人員**物件。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-188">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="dfd6f-189">第二個查詢會使用**OfType**方法，以傳回**講師**物件。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-189">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="dfd6f-190">第三個查詢會使用**OfType**方法，以傳回**學生**物件。</span><span class="sxs-lookup"><span data-stu-id="dfd6f-190">The third query uses the **OfType** method to return **Student** objects.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
