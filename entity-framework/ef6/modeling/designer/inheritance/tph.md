---
title: 設計工具 TPH 繼承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
ms.openlocfilehash: 43ba34a98c3960a7a3052a00e2ed2751c2f2b121
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418424"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="7885e-102">設計工具 TPH 繼承</span><span class="sxs-lookup"><span data-stu-id="7885e-102">Designer TPH Inheritance</span></span>
<span data-ttu-id="7885e-103">此逐步解說會示範如何使用 Entity Framework Designer （EF Designer），在概念模型中執行每個階層的資料表（TPH）繼承。</span><span class="sxs-lookup"><span data-stu-id="7885e-103">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="7885e-104">TPH 繼承會使用一個資料庫資料表來維護繼承階層架構中所有實體類型的資料。</span><span class="sxs-lookup"><span data-stu-id="7885e-104">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="7885e-105">在此逐步解說中，我們會將 Person 資料表對應至三個實體類型： Person （基底類型）、Student （衍生自 Person）和講師（衍生自 Person）。</span><span class="sxs-lookup"><span data-stu-id="7885e-105">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="7885e-106">我們將從資料庫建立概念模型（Database First），然後使用 EF 設計工具來改變模型，以執行 TPH 繼承。</span><span class="sxs-lookup"><span data-stu-id="7885e-106">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="7885e-107">您可以使用 Model First 對應到 TPH 繼承，但是您必須撰寫自己的資料庫產生工作流程，這是很複雜的。</span><span class="sxs-lookup"><span data-stu-id="7885e-107">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="7885e-108">接著，您會將此工作流程指派給 EF 設計工具中的 [**資料庫產生工作流程**] 屬性。</span><span class="sxs-lookup"><span data-stu-id="7885e-108">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="7885e-109">較簡單的替代方法是使用 Code First。</span><span class="sxs-lookup"><span data-stu-id="7885e-109">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="7885e-110">其他繼承選項</span><span class="sxs-lookup"><span data-stu-id="7885e-110">Other Inheritance Options</span></span>

<span data-ttu-id="7885e-111">每一類型的資料表（TPT）是另一種繼承類型，在其中，資料庫中的個別資料表會對應至參與繼承的實體。</span><span class="sxs-lookup"><span data-stu-id="7885e-111">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="7885e-112"> 如需如何使用 EF Designer 來對應每一類型的資料表繼承的相關資訊，請參閱[EF DESIGNER TPT 繼承](~/ef6/modeling/designer/inheritance/tpt.md)。</span><span class="sxs-lookup"><span data-stu-id="7885e-112"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](~/ef6/modeling/designer/inheritance/tpt.md).</span></span>

<span data-ttu-id="7885e-113">Entity Framework 執行時間支援單表類型繼承（TPC）和混合繼承模型，但 EF 設計工具並不支援。</span><span class="sxs-lookup"><span data-stu-id="7885e-113">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="7885e-114">如果您想要使用 TPC 或混合繼承，您有兩個選項： [使用 Code First] 或 [手動編輯 EDMX 檔案]。</span><span class="sxs-lookup"><span data-stu-id="7885e-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="7885e-115">如果您選擇使用 EDMX 檔案，[對應詳細資料] 視窗將會進入「安全模式」，而且您將無法使用設計工具來變更對應。</span><span class="sxs-lookup"><span data-stu-id="7885e-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7885e-116">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="7885e-116">Prerequisites</span></span>

<span data-ttu-id="7885e-117">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="7885e-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="7885e-118">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="7885e-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="7885e-119">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="7885e-119">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="7885e-120">設定專案</span><span class="sxs-lookup"><span data-stu-id="7885e-120">Set up the Project</span></span>

-   <span data-ttu-id="7885e-121">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="7885e-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="7885e-122">選取檔案 **&gt; 新&gt; 專案**</span><span class="sxs-lookup"><span data-stu-id="7885e-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="7885e-123">在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台**] 範本。</span><span class="sxs-lookup"><span data-stu-id="7885e-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="7885e-124">在 [名稱] 中輸入 **TPHDBFirstSample** 。</span><span class="sxs-lookup"><span data-stu-id="7885e-124">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="7885e-125">選取 [確定] \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="7885e-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="7885e-126">建立模型</span><span class="sxs-lookup"><span data-stu-id="7885e-126">Create a Model</span></span>

-   <span data-ttu-id="7885e-127">以滑鼠右鍵按一下方案總管中的專案名稱，然後選取 [**新增-&gt; 新專案**]。</span><span class="sxs-lookup"><span data-stu-id="7885e-127">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="7885e-128">從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。</span><span class="sxs-lookup"><span data-stu-id="7885e-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="7885e-129">在 [檔案名] 中輸入**TPHModel** ，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="7885e-129">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="7885e-130">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="7885e-130">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="7885e-131">按一下 [ **新增連接**]。</span><span class="sxs-lookup"><span data-stu-id="7885e-131">Click **New Connection**.</span></span>
    <span data-ttu-id="7885e-132">在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="7885e-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="7885e-133">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="7885e-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="7885e-134">在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取 [ **Person** ] 資料表。</span><span class="sxs-lookup"><span data-stu-id="7885e-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="7885e-135">按一下 **[完成]** 。</span><span class="sxs-lookup"><span data-stu-id="7885e-135">Click **Finish**.</span></span>

<span data-ttu-id="7885e-136">會顯示 Entity Designer （提供編輯模型的設計介面）。</span><span class="sxs-lookup"><span data-stu-id="7885e-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="7885e-137">您在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都會加入至模型。</span><span class="sxs-lookup"><span data-stu-id="7885e-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="7885e-138">這就是**Person**資料表在資料庫中的外觀。</span><span class="sxs-lookup"><span data-stu-id="7885e-138">That is how the **Person** table looks in the database.</span></span>

![Person 資料表](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="7885e-140">執行每個階層的資料表繼承</span><span class="sxs-lookup"><span data-stu-id="7885e-140">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="7885e-141"> **Person**資料表具有**鑒別**子資料行，它可以有兩個值的其中一個： "Student" 和 "講師"。</span><span class="sxs-lookup"><span data-stu-id="7885e-141">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="7885e-142">根據值， **Person**資料表會對應到**Student**實體或**講師**實體。</span><span class="sxs-lookup"><span data-stu-id="7885e-142">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="7885e-143"> **Person**資料表也有兩個數據行，也就是 **雇用** 和 **EnrollmentDate**，這必須是可**為 null** ，因為一個人不能同時是學生和講師（至少在本逐步解說中）。</span><span class="sxs-lookup"><span data-stu-id="7885e-143">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="7885e-144">加入新的實體</span><span class="sxs-lookup"><span data-stu-id="7885e-144">Add new Entities</span></span>

-   <span data-ttu-id="7885e-145">加入新的實體。</span><span class="sxs-lookup"><span data-stu-id="7885e-145">Add a new entity.</span></span>
    <span data-ttu-id="7885e-146">若要這麼做，請以滑鼠右鍵按一下 Entity Framework Designer 設計介面的空白空間，然後選取 [**加入&gt;實體**]。</span><span class="sxs-lookup"><span data-stu-id="7885e-146">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="7885e-147">在  **機構名稱** 輸入 **講師** ，然後從  **基底類型** 的下拉式清單中選取  **Person** 。</span><span class="sxs-lookup"><span data-stu-id="7885e-147">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="7885e-148">按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="7885e-148">Click **OK**.</span></span>
-   <span data-ttu-id="7885e-149">新增另一個新實體。</span><span class="sxs-lookup"><span data-stu-id="7885e-149">Add another new entity.</span></span> <span data-ttu-id="7885e-150">輸入 **Student** 作為 [ **機構名稱**] 然後從 [ **基底類型**] 的下拉式清單中選取 [ **Person** ]。</span><span class="sxs-lookup"><span data-stu-id="7885e-150">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="7885e-151">在設計介面中加入了兩個新的實體類型。</span><span class="sxs-lookup"><span data-stu-id="7885e-151">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="7885e-152">箭號會從新的實體類型指向 **Person** 實體類型;這表示 **Person** 是新實體類型的基底類型。</span><span class="sxs-lookup"><span data-stu-id="7885e-152">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="7885e-153">以滑鼠右鍵按一下 Person 實體的 [ **雇用** **者** ] 屬性。</span><span class="sxs-lookup"><span data-stu-id="7885e-153">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="7885e-154">選取 [ **剪**下] （或使用 Ctrl X 鍵）。</span><span class="sxs-lookup"><span data-stu-id="7885e-154">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="7885e-155">以滑鼠右鍵按一下 [ **講師** ] 實體，然後選取 [ **貼**上] （或使用 Ctrl + V 鍵）。</span><span class="sxs-lookup"><span data-stu-id="7885e-155">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="7885e-156">以滑鼠右鍵按一下 [ **雇用**日期 ] 屬性，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="7885e-156">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="7885e-157">在 [ **屬性** ] 視窗中，將 **可為 null**的 屬性設定為 **false**。</span><span class="sxs-lookup"><span data-stu-id="7885e-157">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="7885e-158">以滑鼠右鍵按一下 **Person** 實體的 [ **EnrollmentDate** ] 屬性。</span><span class="sxs-lookup"><span data-stu-id="7885e-158">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="7885e-159">選取 [ **剪**下] （或使用 Ctrl X 鍵）。</span><span class="sxs-lookup"><span data-stu-id="7885e-159">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="7885e-160">以滑鼠右鍵按一下 [ **Student** ] 實體，然後選取 [貼上] **（或使用 Ctrl + V 鍵）。**</span><span class="sxs-lookup"><span data-stu-id="7885e-160">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="7885e-161">選取 [ **EnrollmentDate** ] 屬性，並將 **可為 null**的 屬性設定為 **false**。</span><span class="sxs-lookup"><span data-stu-id="7885e-161">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="7885e-162">選取 [ **Person** ] 實體類型。</span><span class="sxs-lookup"><span data-stu-id="7885e-162">Select the **Person** entity type.</span></span> <span data-ttu-id="7885e-163">在 [ **屬性** ] 視窗中，將其 [ **抽象** ] 屬性設為 [ **true**]。</span><span class="sxs-lookup"><span data-stu-id="7885e-163">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="7885e-164">刪除**Person**的**鑒別**子屬性。</span><span class="sxs-lookup"><span data-stu-id="7885e-164">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="7885e-165">下一節將說明應刪除的原因。</span><span class="sxs-lookup"><span data-stu-id="7885e-165">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="7885e-166">對應實體</span><span class="sxs-lookup"><span data-stu-id="7885e-166">Map the entities</span></span>

-   <span data-ttu-id="7885e-167">以滑鼠右鍵按一下 **講師**，然後選取 [**資料表對應]。**</span><span class="sxs-lookup"><span data-stu-id="7885e-167">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="7885e-168">[對應詳細資料] 視窗中已選取講師實體。</span><span class="sxs-lookup"><span data-stu-id="7885e-168">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="7885e-169">在  **對應詳細資料** 視窗中，按一下  **&lt;新增資料表或 View&gt;**  。</span><span class="sxs-lookup"><span data-stu-id="7885e-169">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="7885e-170"> **&lt;加入資料表或視圖&gt;**   欄位會變成可對應所選取實體之資料表或視圖的下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="7885e-170">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="7885e-171">從下拉式清單中選取 [ **Person** ]。</span><span class="sxs-lookup"><span data-stu-id="7885e-171">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="7885e-172"> **對應詳細資料**  視窗會以預設資料行對應進行更新，以及新增條件的選項。</span><span class="sxs-lookup"><span data-stu-id="7885e-172">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="7885e-173">按一下 [ **&lt;新增條件&gt;** ]。</span><span class="sxs-lookup"><span data-stu-id="7885e-173">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="7885e-174"> **&lt;新增條件&gt;**   欄位會變成可設定條件的下拉式資料行清單。</span><span class="sxs-lookup"><span data-stu-id="7885e-174">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="7885e-175">從下拉式清單中選取 [ **鑒別**子 ]。</span><span class="sxs-lookup"><span data-stu-id="7885e-175">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="7885e-176">在 [ **對應詳細資料** ] 視窗的 [ **運算子** ] 欄位中，從下拉式清單中選取 [=]。</span><span class="sxs-lookup"><span data-stu-id="7885e-176">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="7885e-177">在 [ **值/屬性**] 資料行中，輸入 **講師**。</span><span class="sxs-lookup"><span data-stu-id="7885e-177">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="7885e-178">最後的結果應該如下所示：</span><span class="sxs-lookup"><span data-stu-id="7885e-178">The end result should look like this:</span></span>

    ![對應詳細資料](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="7885e-180">針對 **student** 實體類型重複上述步驟，但讓條件等於**student**值。</span><span class="sxs-lookup"><span data-stu-id="7885e-180">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="7885e-181">*我們想要移除**鑒別**子屬性的原因，是因為您無法多次對應資料表資料行。此資料行會用於條件式對應，因此也無法用於屬性對應。如果條件使用 **Is null** 或 **Not null** 比較，則可用於兩者的唯一方法。*</span><span class="sxs-lookup"><span data-stu-id="7885e-181">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="7885e-182">現已實作每個階層的資料表繼承。</span><span class="sxs-lookup"><span data-stu-id="7885e-182">Table-per-hierarchy inheritance is now implemented.</span></span>

![最終的 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="7885e-184">使用模型</span><span class="sxs-lookup"><span data-stu-id="7885e-184">Use the Model</span></span>

<span data-ttu-id="7885e-185">開啟定義**Main**方法的**Program.cs**檔案。</span><span class="sxs-lookup"><span data-stu-id="7885e-185">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="7885e-186">將下列程式碼貼入**Main**函式中。</span><span class="sxs-lookup"><span data-stu-id="7885e-186">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="7885e-187">程式碼會執行三個查詢。</span><span class="sxs-lookup"><span data-stu-id="7885e-187">The code executes three queries.</span></span> <span data-ttu-id="7885e-188">第一個查詢會傳回所有**Person**物件。</span><span class="sxs-lookup"><span data-stu-id="7885e-188">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="7885e-189">第二個查詢會使用**OfType**方法來傳回**講師**物件。</span><span class="sxs-lookup"><span data-stu-id="7885e-189">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="7885e-190">第三個查詢使用**OfType**方法來傳回**Student**物件。</span><span class="sxs-lookup"><span data-stu-id="7885e-190">The third query uses the **OfType** method to return **Student** objects.</span></span>

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
