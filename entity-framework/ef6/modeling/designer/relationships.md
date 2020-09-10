---
title: 關聯性-EF Designer-EF6
description: 關聯性-Entity Framework 6 中的 EF 設計工具
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: f7c801dd8f8ae81cfe44283b7575cfe869ce26d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620419"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="9860c-103">關聯性-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="9860c-103">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="9860c-104">此頁面提供使用 EF 設計工具在模型中設定關聯性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="9860c-104">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="9860c-105">如需 EF 中關聯性的一般資訊，以及如何使用關聯性存取和運算元據，請參閱 [關聯性 & 導覽屬性](xref:ef6/fundamentals/relationships)。</span><span class="sxs-lookup"><span data-stu-id="9860c-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>

<span data-ttu-id="9860c-106">關聯會定義模型中實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="9860c-106">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="9860c-107">本主題說明如何將關聯與 Entity Framework Designer (EF 設計工具) 對應。</span><span class="sxs-lookup"><span data-stu-id="9860c-107">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="9860c-108">下圖顯示使用 EF 設計工具時使用的主視窗。</span><span class="sxs-lookup"><span data-stu-id="9860c-108">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="9860c-110">建置概念模型時，與未對應之實體和關聯有關的警告可能會在 [錯誤清單] 中出現。</span><span class="sxs-lookup"><span data-stu-id="9860c-110">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="9860c-111">您可以忽略這些警告，因為在您選擇從模型產生資料庫之後，錯誤就會消失。</span><span class="sxs-lookup"><span data-stu-id="9860c-111">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="9860c-112">關聯總覽</span><span class="sxs-lookup"><span data-stu-id="9860c-112">Associations Overview</span></span>

<span data-ttu-id="9860c-113">當您使用 EF 設計工具來設計模型時，.edmx 檔案會代表您的模型。</span><span class="sxs-lookup"><span data-stu-id="9860c-113">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="9860c-114">在 .edmx 檔案中，Association 元素會定義兩個實體類型之間的 **關聯** 性。</span><span class="sxs-lookup"><span data-stu-id="9860c-114">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="9860c-115">關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。</span><span class="sxs-lookup"><span data-stu-id="9860c-115">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="9860c-116">關聯 end 的多重性可以有一個 (1) 、零個或一個 (0 ..1) 或許多 () 中的值。 \*</span><span class="sxs-lookup"><span data-stu-id="9860c-116">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="9860c-117">這項資訊是在兩個 **子項目** 中指定。</span><span class="sxs-lookup"><span data-stu-id="9860c-117">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="9860c-118">在執行時間，您可以透過導覽屬性或外鍵來存取位於關聯某一端的實體類型實例， (如果您選擇在實體中公開外鍵) 。</span><span class="sxs-lookup"><span data-stu-id="9860c-118">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="9860c-119">公開外鍵之後，實體之間的關聯性會以 **ReferentialConstraint** 專案來管理， (**Association** 元素) 的子項目。</span><span class="sxs-lookup"><span data-stu-id="9860c-119">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="9860c-120">建議您一律公開實體中關聯性的外鍵。</span><span class="sxs-lookup"><span data-stu-id="9860c-120">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="9860c-121">在多對多 (\* ： \*) 您無法將外鍵加入至實體。</span><span class="sxs-lookup"><span data-stu-id="9860c-121">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="9860c-122">在 \* ：關聯性中 \* ，會使用獨立物件來管理關聯資訊。</span><span class="sxs-lookup"><span data-stu-id="9860c-122">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="9860c-123">如需 CSDL 元素 (**ReferentialConstraint**、 **關聯**等的詳細資訊，) 查看 [csdl 規格](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)。</span><span class="sxs-lookup"><span data-stu-id="9860c-123">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="9860c-124">建立和刪除關聯</span><span class="sxs-lookup"><span data-stu-id="9860c-124">Create and Delete Associations</span></span>

<span data-ttu-id="9860c-125">使用 EF 設計工具建立關聯會更新 .edmx 檔案的模型內容。</span><span class="sxs-lookup"><span data-stu-id="9860c-125">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="9860c-126">建立關聯之後，您必須為此主題稍後討論的關聯建立對應 () 。</span><span class="sxs-lookup"><span data-stu-id="9860c-126">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="9860c-127">本節假設您已新增您想要在模型之間建立關聯的實體。</span><span class="sxs-lookup"><span data-stu-id="9860c-127">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="9860c-128">建立關聯</span><span class="sxs-lookup"><span data-stu-id="9860c-128">To create an association</span></span>

1.  <span data-ttu-id="9860c-129">以滑鼠右鍵按一下設計介面的空白區域，指向 [ **加入新**的]，然後選取 [ **關聯 ...**]。</span><span class="sxs-lookup"><span data-stu-id="9860c-129">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="9860c-130">在 [ **加入關聯** ] 對話方塊中，填入關聯的設定。</span><span class="sxs-lookup"><span data-stu-id="9860c-130">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![加入關聯](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="9860c-132">您可以藉由清除 **導覽屬性 **，並 **將外鍵屬性加入至 &lt; 實體類型名稱 &gt; 實體 **核取方塊，來選擇不要將導覽屬性或外鍵屬性加入至關聯兩端的實體。</span><span class="sxs-lookup"><span data-stu-id="9860c-132">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="9860c-133">如果您只加入一個導覽屬性，則關聯只能單向周遊。</span><span class="sxs-lookup"><span data-stu-id="9860c-133">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="9860c-134">如果不加入任何導覽屬性，您必須選擇加入外部索引鍵屬性，才能存取關聯 End 的實體。</span><span class="sxs-lookup"><span data-stu-id="9860c-134">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="9860c-135">按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="9860c-135">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="9860c-136">若要刪除關聯</span><span class="sxs-lookup"><span data-stu-id="9860c-136">To delete an association</span></span>

<span data-ttu-id="9860c-137">若要刪除關聯，請執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="9860c-137">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="9860c-138">以滑鼠右鍵按一下 EF 設計工具介面上的關聯，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="9860c-138">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="9860c-139">或 -</span><span class="sxs-lookup"><span data-stu-id="9860c-139">OR -</span></span>

-   <span data-ttu-id="9860c-140">選取一個或多個關聯，然後按下 DELETE 鍵。</span><span class="sxs-lookup"><span data-stu-id="9860c-140">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="9860c-141">在您的實體中包含外鍵屬性 (參考條件約束) </span><span class="sxs-lookup"><span data-stu-id="9860c-141">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="9860c-142">建議您一律公開實體中關聯性的外鍵。</span><span class="sxs-lookup"><span data-stu-id="9860c-142">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="9860c-143">Entity Framework 使用參考條件約束來識別某個屬性做為關聯性的外鍵。</span><span class="sxs-lookup"><span data-stu-id="9860c-143">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="9860c-144">如果您在建立關聯性時，核取 [ ***將外鍵屬性加入至 &lt; 實體類型名稱 &gt; 實體*** ] 核取方塊，就會為您加入這個參考條件約束。</span><span class="sxs-lookup"><span data-stu-id="9860c-144">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="9860c-145">當您使用 EF Designer 加入或編輯參考條件約束時，EF 設計工具會在 .edmx 檔的 CSDL 內容中加入或修改**ReferentialConstraint**   元素。</span><span class="sxs-lookup"><span data-stu-id="9860c-145">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="9860c-146">按兩下您要編輯的關聯。</span><span class="sxs-lookup"><span data-stu-id="9860c-146">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="9860c-147">[ **參考條件約束**   ] 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="9860c-147">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="9860c-148">從 [ **主體**]   下拉式清單中，選取參考條件約束中的主體實體。</span><span class="sxs-lookup"><span data-stu-id="9860c-148">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="9860c-149">實體的索引鍵屬性會加入至對話方塊中的 [ **主要索引鍵**   ] 清單。</span><span class="sxs-lookup"><span data-stu-id="9860c-149">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="9860c-150">從 **相依**的   下拉式清單中，選取參考條件約束中的相依實體。</span><span class="sxs-lookup"><span data-stu-id="9860c-150">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="9860c-151">針對每個具有相依索引鍵的主體索引鍵，從 **相依索引鍵**資料行的下拉式清單中選取對應的相依索引鍵   。</span><span class="sxs-lookup"><span data-stu-id="9860c-151">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref 條件約束](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="9860c-153">按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="9860c-153">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="9860c-154">建立和編輯關聯對應</span><span class="sxs-lookup"><span data-stu-id="9860c-154">Create and Edit Association Mappings</span></span>

<span data-ttu-id="9860c-155">您可以在 EF Designer 的 [**對應詳細資料**] 視窗中，指定關聯如何對應到資料庫   。</span><span class="sxs-lookup"><span data-stu-id="9860c-155">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="9860c-156">您只能對應未指定參考條件約束之關聯的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="9860c-156">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="9860c-157">如果指定了參考條件約束，則實體中包含外鍵屬性，而且您可以使用實體的對應詳細資料來控制外鍵所對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="9860c-157">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="9860c-158">建立關聯對應</span><span class="sxs-lookup"><span data-stu-id="9860c-158">Create an association mapping</span></span>

-   <span data-ttu-id="9860c-159">以滑鼠右鍵按一下設計介面中的關聯，然後選取 [ **資料表對應**]。</span><span class="sxs-lookup"><span data-stu-id="9860c-159">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="9860c-160">這會在 [ **對應詳細資料**] 視窗中顯示關聯對應   。</span><span class="sxs-lookup"><span data-stu-id="9860c-160">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="9860c-161">按一下 [ **加入資料表或視圖**]。</span><span class="sxs-lookup"><span data-stu-id="9860c-161">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="9860c-162">包含儲存體模型中所有資料表的下拉式清單便會出現。</span><span class="sxs-lookup"><span data-stu-id="9860c-162">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="9860c-163">選取此關聯將要對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="9860c-163">Select the table to which the association will map.</span></span>
    <span data-ttu-id="9860c-164">[ **對應詳細資料**   ] 視窗會顯示關聯的兩端，以及每**一端**實體類型的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="9860c-164">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="9860c-165">針對每個索引鍵屬性，按一下 [資料 **行**]   欄位，然後選取屬性將對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="9860c-165">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![對應詳細資料4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="9860c-167">編輯關聯對應</span><span class="sxs-lookup"><span data-stu-id="9860c-167">Edit an association mapping</span></span>

-   <span data-ttu-id="9860c-168">以滑鼠右鍵按一下設計介面中的關聯，然後選取 [ **資料表對應**]。</span><span class="sxs-lookup"><span data-stu-id="9860c-168">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="9860c-169">這會在 [ **對應詳細資料**] 視窗中顯示關聯對應   。</span><span class="sxs-lookup"><span data-stu-id="9860c-169">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="9860c-170">按一下 [ **對應至 &lt; 資料表 &gt; 名稱**]。</span><span class="sxs-lookup"><span data-stu-id="9860c-170">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="9860c-171">包含儲存體模型中所有資料表的下拉式清單便會出現。</span><span class="sxs-lookup"><span data-stu-id="9860c-171">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="9860c-172">選取此關聯將要對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="9860c-172">Select the table to which the association will map.</span></span>
    <span data-ttu-id="9860c-173">[ **對應詳細資料**   ] 視窗會顯示關聯的兩端，以及每一端實體類型的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="9860c-173">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="9860c-174">針對每個索引鍵屬性，按一下 [資料 **行**]   欄位，然後選取屬性將對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="9860c-174">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="9860c-175">編輯和刪除導覽屬性</span><span class="sxs-lookup"><span data-stu-id="9860c-175">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="9860c-176">導覽屬性是用來在模型中的關聯端點上找出實體的快捷方式屬性。</span><span class="sxs-lookup"><span data-stu-id="9860c-176">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="9860c-177">當您在兩個實體類型之間建立關聯時，可以建立導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="9860c-177">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="9860c-178">編輯導覽屬性</span><span class="sxs-lookup"><span data-stu-id="9860c-178">To edit navigation properties</span></span>

-   <span data-ttu-id="9860c-179">選取 EF 設計工具介面上的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="9860c-179">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="9860c-180">導覽屬性的相關資訊會顯示在 [Visual Studio **屬性**   ] 視窗中。</span><span class="sxs-lookup"><span data-stu-id="9860c-180">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="9860c-181">變更 [ **屬性**] 視窗中的屬性設定   。</span><span class="sxs-lookup"><span data-stu-id="9860c-181">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="9860c-182">刪除導覽屬性</span><span class="sxs-lookup"><span data-stu-id="9860c-182">To delete navigation properties</span></span>

-   <span data-ttu-id="9860c-183">如果未在概念模型中的實體類型上公開外部索引鍵，刪除導覽屬性可能會使對應的關聯只能單向周遊，或者完全不能周遊。</span><span class="sxs-lookup"><span data-stu-id="9860c-183">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="9860c-184">以滑鼠右鍵按一下 EF 設計工具介面上的導覽屬性，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="9860c-184">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
