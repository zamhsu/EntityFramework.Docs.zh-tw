---
title: 關聯性-EF 設計工具-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
ms.openlocfilehash: d429c39dafbf183caabdc85748c188deb8dd6f66
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418242"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="e83df-102">關聯性-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="e83df-102">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="e83df-103">此頁面提供有關使用 EF 設計工具在模型中設定關聯性的資訊。</span><span class="sxs-lookup"><span data-stu-id="e83df-103">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="e83df-104">如需 EF 中關聯性和如何使用關聯性存取和運算元據的一般資訊，請參閱[& 導覽屬性的關聯](~/ef6/fundamentals/relationships.md)性。</span><span class="sxs-lookup"><span data-stu-id="e83df-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>

<span data-ttu-id="e83df-105">關聯會定義模型中實體類型之間的關係。</span><span class="sxs-lookup"><span data-stu-id="e83df-105">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="e83df-106">本主題說明如何使用 Entity Framework Designer （EF Designer）對應關聯。</span><span class="sxs-lookup"><span data-stu-id="e83df-106">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e83df-107">下圖顯示使用 EF 設計工具時的主要視窗。</span><span class="sxs-lookup"><span data-stu-id="e83df-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="e83df-109">建置概念模型時，與未對應之實體和關聯有關的警告可能會在 [錯誤清單] 中出現。</span><span class="sxs-lookup"><span data-stu-id="e83df-109">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="e83df-110">您可以忽略這些警告，因為在您選擇從模型產生資料庫之後，錯誤就會消失。</span><span class="sxs-lookup"><span data-stu-id="e83df-110">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="e83df-111">關聯總覽</span><span class="sxs-lookup"><span data-stu-id="e83df-111">Associations Overview</span></span>

<span data-ttu-id="e83df-112">當您使用 EF 設計工具設計模型時，.edmx 檔案會代表您的模型。</span><span class="sxs-lookup"><span data-stu-id="e83df-112">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="e83df-113">在 .edmx 檔案中，Association 元素會定義兩個實體類型之間的**關聯**性。</span><span class="sxs-lookup"><span data-stu-id="e83df-113">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="e83df-114">關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。</span><span class="sxs-lookup"><span data-stu-id="e83df-114">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="e83df-115">關聯端點的多重性可以有一（1）、零或一（0 ..1），或多個（\*）的值。</span><span class="sxs-lookup"><span data-stu-id="e83df-115">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="e83df-116">這項資訊是在兩個子系**End**元素中指定。</span><span class="sxs-lookup"><span data-stu-id="e83df-116">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="e83df-117">在執行時間，可以透過導覽屬性或外鍵來存取關聯一端的實體類型實例（如果您選擇要公開實體中的外鍵）。</span><span class="sxs-lookup"><span data-stu-id="e83df-117">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="e83df-118">公開外鍵後，實體之間的關聯性會使用**ReferentialConstraint**專案（ **Association**元素的子項目）來管理。</span><span class="sxs-lookup"><span data-stu-id="e83df-118">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="e83df-119">建議您一律針對實體中的關聯性公開外鍵。</span><span class="sxs-lookup"><span data-stu-id="e83df-119">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="e83df-120">在多對多（\*：\*）中，您無法將外鍵加入至實體。</span><span class="sxs-lookup"><span data-stu-id="e83df-120">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="e83df-121">在 \*：\* 關聯性中，會使用獨立物件來管理關聯資訊。</span><span class="sxs-lookup"><span data-stu-id="e83df-121">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="e83df-122">如需 CSDL 元素（**ReferentialConstraint**、**關聯**等）的相關資訊，請參閱[csdl 規格](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="e83df-122">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="e83df-123">建立和刪除關聯</span><span class="sxs-lookup"><span data-stu-id="e83df-123">Create and Delete Associations</span></span>

<span data-ttu-id="e83df-124">使用 EF Designer 建立關聯會更新 .edmx 檔案的模型內容。</span><span class="sxs-lookup"><span data-stu-id="e83df-124">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="e83df-125">建立關聯之後，您必須建立關聯的對應（本主題稍後會討論）。</span><span class="sxs-lookup"><span data-stu-id="e83df-125">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="e83df-126">本節假設您已經加入您想要在模型之間建立關聯的實體。</span><span class="sxs-lookup"><span data-stu-id="e83df-126">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="e83df-127">建立關聯</span><span class="sxs-lookup"><span data-stu-id="e83df-127">To create an association</span></span>

1.  <span data-ttu-id="e83df-128">以滑鼠右鍵按一下設計介面的空白區域，指向 [ **加入新**的]，然後選取 [ **關聯**]。</span><span class="sxs-lookup"><span data-stu-id="e83df-128">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="e83df-129">在 [**加入關聯**] 對話方塊中，填入關聯的設定。</span><span class="sxs-lookup"><span data-stu-id="e83df-129">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![新增關聯](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="e83df-131">您可以藉由清除 **導覽屬性 **並 **將外鍵屬性加入&gt; 實體 核取方塊的 &lt;實體類型名稱 **，選擇不要將導覽屬性或外鍵屬性加入至關聯結尾的實體。</span><span class="sxs-lookup"><span data-stu-id="e83df-131">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="e83df-132">如果您只加入一個導覽屬性，則關聯只能單向周遊。</span><span class="sxs-lookup"><span data-stu-id="e83df-132">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="e83df-133">如果不加入任何導覽屬性，您必須選擇加入外部索引鍵屬性，才能存取關聯 End 的實體。</span><span class="sxs-lookup"><span data-stu-id="e83df-133">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="e83df-134">按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="e83df-134">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="e83df-135">若要刪除關聯</span><span class="sxs-lookup"><span data-stu-id="e83df-135">To delete an association</span></span>

<span data-ttu-id="e83df-136">若要刪除關聯，請執行下列其中一項動作：</span><span class="sxs-lookup"><span data-stu-id="e83df-136">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="e83df-137">在 EF 設計工具介面上，以滑鼠右鍵按一下關聯，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="e83df-137">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="e83df-138">或 -</span><span class="sxs-lookup"><span data-stu-id="e83df-138">OR -</span></span>

-   <span data-ttu-id="e83df-139">選取一個或多個關聯，然後按下 DELETE 鍵。</span><span class="sxs-lookup"><span data-stu-id="e83df-139">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="e83df-140">在您的實體中包含外鍵屬性（參考條件約束）</span><span class="sxs-lookup"><span data-stu-id="e83df-140">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="e83df-141">建議您一律針對實體中的關聯性公開外鍵。</span><span class="sxs-lookup"><span data-stu-id="e83df-141">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="e83df-142">Entity Framework 使用參考條件約束來識別屬性是否做為關聯性的外鍵。</span><span class="sxs-lookup"><span data-stu-id="e83df-142">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="e83df-143">如果您在建立關聯性時，核取了 [***將外鍵屬性加入至 &lt;實體類型名稱&gt; 實體***] 核取方塊，就會為您新增此參考條件約束。</span><span class="sxs-lookup"><span data-stu-id="e83df-143">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="e83df-144">當您使用 EF 設計工具來加入或編輯參考條件約束時，EF 設計工具會在 .edmx 檔案的 CSDL 內容中加入或修改**ReferentialConstraint** 元素。</span><span class="sxs-lookup"><span data-stu-id="e83df-144">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="e83df-145">按兩下您要編輯的關聯。</span><span class="sxs-lookup"><span data-stu-id="e83df-145">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="e83df-146">[ **參考條件約束** ] 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="e83df-146">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="e83df-147">從 [ **主體** ] 下拉式清單中，選取參考條件約束中的 [主體] 實體。</span><span class="sxs-lookup"><span data-stu-id="e83df-147">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="e83df-148">實體的索引鍵屬性會加入至對話方塊的 [ **主體索引鍵** ] 清單中。</span><span class="sxs-lookup"><span data-stu-id="e83df-148">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="e83df-149">從 [ **相依** ] 下拉式清單中，選取參考條件約束中的相依實體。</span><span class="sxs-lookup"><span data-stu-id="e83df-149">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="e83df-150">針對具有相依索引鍵的每個主體索引鍵，從 [ **相依索引鍵** ] 資料行的下拉式清單中選取對應的相依索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e83df-150">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref 條件約束](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="e83df-152">按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="e83df-152">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="e83df-153">建立和編輯關聯對應</span><span class="sxs-lookup"><span data-stu-id="e83df-153">Create and Edit Association Mappings</span></span>

<span data-ttu-id="e83df-154">您可以在 EF 設計工具的 **對應詳細資料**  視窗中，指定關聯對應至資料庫的方式。</span><span class="sxs-lookup"><span data-stu-id="e83df-154">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="e83df-155">您只能對應未指定參考條件約束之關聯的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="e83df-155">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="e83df-156">如果指定了參考條件約束，則實體中會包含外鍵屬性，而且您可以使用實體的對應詳細資料，來控制外鍵要對應至哪個資料行。</span><span class="sxs-lookup"><span data-stu-id="e83df-156">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="e83df-157">建立關聯對應</span><span class="sxs-lookup"><span data-stu-id="e83df-157">Create an association mapping</span></span>

-   <span data-ttu-id="e83df-158">以滑鼠右鍵按一下設計介面中的關聯，然後選取 [ **資料表對應**]。</span><span class="sxs-lookup"><span data-stu-id="e83df-158">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="e83df-159">這會在  **對應詳細資料**  視窗中顯示關聯對應。</span><span class="sxs-lookup"><span data-stu-id="e83df-159">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="e83df-160">按一下 [ **加入資料表或視圖**]。</span><span class="sxs-lookup"><span data-stu-id="e83df-160">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="e83df-161">包含儲存體模型中所有資料表的下拉式清單便會出現。</span><span class="sxs-lookup"><span data-stu-id="e83df-161">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="e83df-162">選取此關聯將要對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="e83df-162">Select the table to which the association will map.</span></span>
    <span data-ttu-id="e83df-163">[ **對應詳細資料** ] 視窗會顯示關聯的兩端，以及每**一端**之實體類型的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="e83df-163">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="e83df-164">針對每個索引鍵屬性，按一下資料 **行**  欄位，然後選取屬性將對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="e83df-164">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![對應詳細資料4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="e83df-166">編輯關聯對應</span><span class="sxs-lookup"><span data-stu-id="e83df-166">Edit an association mapping</span></span>

-   <span data-ttu-id="e83df-167">以滑鼠右鍵按一下設計介面中的關聯，然後選取 [ **資料表對應**]。</span><span class="sxs-lookup"><span data-stu-id="e83df-167">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="e83df-168">這會在  **對應詳細資料**  視窗中顯示關聯對應。</span><span class="sxs-lookup"><span data-stu-id="e83df-168">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="e83df-169">按一下 [ **對應] &lt;資料表名稱&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="e83df-169">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="e83df-170">包含儲存體模型中所有資料表的下拉式清單便會出現。</span><span class="sxs-lookup"><span data-stu-id="e83df-170">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="e83df-171">選取此關聯將要對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="e83df-171">Select the table to which the association will map.</span></span>
    <span data-ttu-id="e83df-172">[ **對應詳細資料** ] 視窗會顯示關聯的兩端，以及每一端之實體類型的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="e83df-172">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="e83df-173">針對每個索引鍵屬性，按一下資料 **行**  欄位，然後選取屬性將對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="e83df-173">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="e83df-174">編輯和刪除導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e83df-174">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="e83df-175">導覽屬性是用來在模型中的關聯端點上尋找實體的快捷方式屬性。</span><span class="sxs-lookup"><span data-stu-id="e83df-175">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="e83df-176">當您在兩個實體類型之間建立關聯時，可以建立導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e83df-176">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="e83df-177">編輯導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e83df-177">To edit navigation properties</span></span>

-   <span data-ttu-id="e83df-178">選取 EF 設計工具介面上的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e83df-178">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="e83df-179">導覽屬性的相關資訊會顯示在 Visual Studio **屬性**  視窗中。</span><span class="sxs-lookup"><span data-stu-id="e83df-179">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="e83df-180">變更  **屬性**  視窗中的屬性設定。</span><span class="sxs-lookup"><span data-stu-id="e83df-180">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="e83df-181">刪除導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e83df-181">To delete navigation properties</span></span>

-   <span data-ttu-id="e83df-182">如果未在概念模型中的實體類型上公開外部索引鍵，刪除導覽屬性可能會使對應的關聯只能單向周遊，或者完全不能周遊。</span><span class="sxs-lookup"><span data-stu-id="e83df-182">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="e83df-183">以滑鼠右鍵按一下 EF 設計工具介面上的導覽屬性，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="e83df-183">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
