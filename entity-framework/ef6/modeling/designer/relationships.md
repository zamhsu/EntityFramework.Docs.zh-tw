---
title: 關聯性-EF 設計工具-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
ms.openlocfilehash: 72efe76956c930a787449e6cce453ab0317adc7c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994644"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="d725d-102">關聯性-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="d725d-102">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="d725d-103">此頁面會提供設定模型使用 EF 設計工具中的關聯性相關資訊。</span><span class="sxs-lookup"><span data-stu-id="d725d-103">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="d725d-104">如需 EF 以及如何存取和操作資料使用關聯性的關聯性的一般資訊，請參閱[關聯性和導覽屬性](~/ef6/fundamentals/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="d725d-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>

<span data-ttu-id="d725d-105">關聯會定義模型中的實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="d725d-105">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="d725d-106">本主題說明如何將對應關聯與 Entity Framework Designer （EF 設計工具）。</span><span class="sxs-lookup"><span data-stu-id="d725d-106">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="d725d-107">下圖顯示使用 EF 設計工具時所使用的主視窗。</span><span class="sxs-lookup"><span data-stu-id="d725d-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EFDesigner](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="d725d-109">當您建置概念模型時，未對應的實體和關聯有關的警告可能會出現在 錯誤清單。</span><span class="sxs-lookup"><span data-stu-id="d725d-109">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="d725d-110">您可以忽略這些警告，因為錯誤您選擇從模型產生資料庫之後，就會消失。</span><span class="sxs-lookup"><span data-stu-id="d725d-110">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="d725d-111">關聯概觀</span><span class="sxs-lookup"><span data-stu-id="d725d-111">Associations Overview</span></span>

<span data-ttu-id="d725d-112">當您設計您的模型使用 EF 設計工具時，.edmx 檔案會代表您的模型。</span><span class="sxs-lookup"><span data-stu-id="d725d-112">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="d725d-113">在.edmx 檔案中，**關聯**項目會定義兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="d725d-113">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="d725d-114">關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。</span><span class="sxs-lookup"><span data-stu-id="d725d-114">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="d725d-115">關聯 end 的多重性可以有值為一 (1)、 零或一個 (0..1)，或多個 (\*)。</span><span class="sxs-lookup"><span data-stu-id="d725d-115">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="d725d-116">這項資訊會指定在兩個子**結束**項目。</span><span class="sxs-lookup"><span data-stu-id="d725d-116">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="d725d-117">在執行階段，位於關聯某一端的實體類型執行個體可以存取透過導覽屬性或外部索引鍵 （如果您選擇要公開 （expose） 實體中的外部索引鍵）。</span><span class="sxs-lookup"><span data-stu-id="d725d-117">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="d725d-118">公開外部索引鍵，使用管理實體之間的關聯性**ReferentialConstraint**項目 (的子元素**關聯**項目)。</span><span class="sxs-lookup"><span data-stu-id="d725d-118">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="d725d-119">建議您一律公開實體中的外部索引鍵關聯性。</span><span class="sxs-lookup"><span data-stu-id="d725d-119">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="d725d-120">在多對多 (\*:\*) 您無法將外部索引鍵新增至實體。</span><span class="sxs-lookup"><span data-stu-id="d725d-120">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="d725d-121">在  \*:\*關聯性，關聯資訊管理與獨立的物件。</span><span class="sxs-lookup"><span data-stu-id="d725d-121">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="d725d-122">如需 CSDL 元素 (**ReferentialConstraint**，**關聯**等等) 請參閱[CSDL 規格](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="d725d-122">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="d725d-123">建立和刪除關聯</span><span class="sxs-lookup"><span data-stu-id="d725d-123">Create and Delete Associations</span></span>

<span data-ttu-id="d725d-124">建立關聯的 EF 設計工具更新.edmx 檔案的模型內容。</span><span class="sxs-lookup"><span data-stu-id="d725d-124">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="d725d-125">建立關聯之後, 您必須建立關聯 （在本主題稍後討論） 的對應。</span><span class="sxs-lookup"><span data-stu-id="d725d-125">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="d725d-126">本節假設您已經新增您想要建立您的模型之間的關聯的實體。</span><span class="sxs-lookup"><span data-stu-id="d725d-126">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="d725d-127">建立關聯</span><span class="sxs-lookup"><span data-stu-id="d725d-127">To create an association</span></span>

1.  <span data-ttu-id="d725d-128">以滑鼠右鍵按一下設計介面的空白區域，指向**加入新**，然後選取**關聯...**.</span><span class="sxs-lookup"><span data-stu-id="d725d-128">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="d725d-129">中的關聯的設定中填滿**新增關聯**對話方塊。</span><span class="sxs-lookup"><span data-stu-id="d725d-129">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![AddAssociation](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="d725d-131">您可以選擇不導覽屬性或外部索引鍵屬性加入至位於關聯兩端之實體的藉由清除 * * 導覽屬性 * * 和 * * 加入外部索引鍵的屬性，來&lt;實體類型名稱&gt;實體 * *核取方塊。</span><span class="sxs-lookup"><span data-stu-id="d725d-131">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="d725d-132">如果您只加入一個導覽屬性，則關聯只能單向周遊。</span><span class="sxs-lookup"><span data-stu-id="d725d-132">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="d725d-133">如果不加入任何導覽屬性，您必須選擇加入外部索引鍵屬性，才能存取關聯 End 的實體。</span><span class="sxs-lookup"><span data-stu-id="d725d-133">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="d725d-134">按一下 [確定 **Deploying Office Solutions**]。</span><span class="sxs-lookup"><span data-stu-id="d725d-134">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="d725d-135">若要刪除關聯</span><span class="sxs-lookup"><span data-stu-id="d725d-135">To delete an association</span></span>

<span data-ttu-id="d725d-136">若要刪除關聯，執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="d725d-136">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="d725d-137">以滑鼠右鍵按一下介面，然後選取 EF 設計工具上的關聯**刪除**。</span><span class="sxs-lookup"><span data-stu-id="d725d-137">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="d725d-138">或-</span><span class="sxs-lookup"><span data-stu-id="d725d-138">OR -</span></span>

-   <span data-ttu-id="d725d-139">選取一個或多個關聯，然後按下 DELETE 鍵。</span><span class="sxs-lookup"><span data-stu-id="d725d-139">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="d725d-140">在您的實體 （參考條件約束） 包含外部索引鍵屬性</span><span class="sxs-lookup"><span data-stu-id="d725d-140">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="d725d-141">建議您一律公開實體中的外部索引鍵關聯性。</span><span class="sxs-lookup"><span data-stu-id="d725d-141">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="d725d-142">Entity Framework 會使用參考條件約束，來識別屬性，做為外部索引鍵關聯性。</span><span class="sxs-lookup"><span data-stu-id="d725d-142">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="d725d-143">如果您已核取***新增至外部索引鍵屬性&lt;實體類型名稱&gt;實體***核取方塊為您建立關聯時，已加入這個參考條件約束。</span><span class="sxs-lookup"><span data-stu-id="d725d-143">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="d725d-144">當您使用 EF 設計工具來新增或編輯參考條件約束時，EF 設計工具加入或修改**ReferentialConstraint** .edmx 檔的 CSDL 內容中的項目。</span><span class="sxs-lookup"><span data-stu-id="d725d-144">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="d725d-145">按兩下您要編輯的關聯。</span><span class="sxs-lookup"><span data-stu-id="d725d-145">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="d725d-146">**參考條件約束** 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="d725d-146">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="d725d-147">從**主體**下拉式清單中，選取 條件約束中的 主要實體。</span><span class="sxs-lookup"><span data-stu-id="d725d-147">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="d725d-148">實體索引鍵屬性加入**主體金鑰**在對話方塊中的清單。</span><span class="sxs-lookup"><span data-stu-id="d725d-148">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="d725d-149">從**相依**下拉式清單中，選取相依的實體條件約束中。</span><span class="sxs-lookup"><span data-stu-id="d725d-149">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="d725d-150">針對每個具有相依索引鍵的主體金鑰，請從中的下拉式清單中選取對應的相依索引鍵**相依索引鍵**資料行。</span><span class="sxs-lookup"><span data-stu-id="d725d-150">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![RefConstraint](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="d725d-152">按一下 [確定 **Deploying Office Solutions**]。</span><span class="sxs-lookup"><span data-stu-id="d725d-152">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="d725d-153">建立和編輯關聯對應</span><span class="sxs-lookup"><span data-stu-id="d725d-153">Create and Edit Association Mappings</span></span>

<span data-ttu-id="d725d-154">您可以指定如何將關聯對應至資料庫中**對應詳細資料**的 EF 設計工具視窗。</span><span class="sxs-lookup"><span data-stu-id="d725d-154">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="d725d-155">您只能對應沒有指定參考條件約束的關聯的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="d725d-155">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="d725d-156">如果指定了參考條件約束，在實體中包含外部索引鍵屬性，以及您可以使用對應的詳細資料，來控制哪些資料行的外部索引鍵對應至實體。</span><span class="sxs-lookup"><span data-stu-id="d725d-156">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="d725d-157">建立關聯對應</span><span class="sxs-lookup"><span data-stu-id="d725d-157">Create an association mapping</span></span>

-   <span data-ttu-id="d725d-158">以滑鼠右鍵按一下設計介面，並選取中的關聯**的資料表對應**。</span><span class="sxs-lookup"><span data-stu-id="d725d-158">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="d725d-159">這會顯示在關聯對應**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="d725d-159">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="d725d-160">按一下 **將資料表或檢視**。</span><span class="sxs-lookup"><span data-stu-id="d725d-160">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="d725d-161">包含儲存體模型中所有資料表的下拉式清單便會出現。</span><span class="sxs-lookup"><span data-stu-id="d725d-161">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="d725d-162">選取此關聯將要對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="d725d-162">Select the table to which the association will map.</span></span>
    <span data-ttu-id="d725d-163">**對應詳細資料** 視窗會顯示在每個的關聯和實體類型的索引鍵屬性的兩端**結束**。</span><span class="sxs-lookup"><span data-stu-id="d725d-163">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="d725d-164">針對每個索引鍵的屬性，按一下**資料行**欄位，然後選取此屬性將要對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="d725d-164">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![MappingDetails4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="d725d-166">編輯關聯對應</span><span class="sxs-lookup"><span data-stu-id="d725d-166">Edit an association mapping</span></span>

-   <span data-ttu-id="d725d-167">以滑鼠右鍵按一下設計介面，並選取中的關聯**的資料表對應**。</span><span class="sxs-lookup"><span data-stu-id="d725d-167">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="d725d-168">這會顯示在關聯對應**對應詳細資料**視窗。</span><span class="sxs-lookup"><span data-stu-id="d725d-168">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="d725d-169">按一下 **對應至&lt;資料表名稱&gt;**。</span><span class="sxs-lookup"><span data-stu-id="d725d-169">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="d725d-170">包含儲存體模型中所有資料表的下拉式清單便會出現。</span><span class="sxs-lookup"><span data-stu-id="d725d-170">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="d725d-171">選取此關聯將要對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="d725d-171">Select the table to which the association will map.</span></span>
    <span data-ttu-id="d725d-172">**對應詳細資料**視窗會顯示在每一端的關聯和實體類型的索引鍵屬性的兩端。</span><span class="sxs-lookup"><span data-stu-id="d725d-172">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="d725d-173">針對每個索引鍵的屬性，按一下**資料行**欄位，然後選取此屬性將要對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="d725d-173">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="d725d-174">編輯和刪除導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d725d-174">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="d725d-175">導覽屬性是用來尋找位於關聯兩端之實體模型中的捷徑屬性。</span><span class="sxs-lookup"><span data-stu-id="d725d-175">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="d725d-176">當您在兩個實體類型之間建立關聯時，可以建立導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d725d-176">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="d725d-177">若要編輯導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d725d-177">To edit navigation properties</span></span>

-   <span data-ttu-id="d725d-178">選取在 EF 設計工具介面上的瀏覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d725d-178">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="d725d-179">導覽屬性的相關資訊會顯示在 Visual Studio**屬性**視窗。</span><span class="sxs-lookup"><span data-stu-id="d725d-179">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="d725d-180">變更中的屬性設定**屬性**視窗。</span><span class="sxs-lookup"><span data-stu-id="d725d-180">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="d725d-181">若要刪除導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d725d-181">To delete navigation properties</span></span>

-   <span data-ttu-id="d725d-182">如果未在概念模型中的實體類型上公開外部索引鍵，刪除導覽屬性可能會使對應的關聯只能單向周遊，或者完全不能周遊。</span><span class="sxs-lookup"><span data-stu-id="d725d-182">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="d725d-183">以滑鼠右鍵按一下 瀏覽屬性在 EF 設計工具介面，然後選取**刪除**。</span><span class="sxs-lookup"><span data-stu-id="d725d-183">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
