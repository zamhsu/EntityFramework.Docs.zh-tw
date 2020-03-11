---
title: 複雜類型-EF 設計工具-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
ms.openlocfilehash: a3c5578acee23688c04772d2dd0a2221779af562
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418648"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="2d446-102">複雜類型-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="2d446-102">Complex Types - EF Designer</span></span>
<span data-ttu-id="2d446-103">本主題說明如何使用 Entity Framework Designer （EF Designer）對應複雜型別，以及如何查詢包含複雜類型屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="2d446-103">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="2d446-104">下圖顯示使用 EF 設計工具時的主要視窗。</span><span class="sxs-lookup"><span data-stu-id="2d446-104">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="2d446-106">建置概念模型時，與未對應之實體和關聯有關的警告可能會在 [錯誤清單] 中出現。</span><span class="sxs-lookup"><span data-stu-id="2d446-106">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="2d446-107">您可以忽略這些警告，因為在您選擇從模型產生資料庫之後，錯誤就會消失。</span><span class="sxs-lookup"><span data-stu-id="2d446-107">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="2d446-108">什麼是複雜型別</span><span class="sxs-lookup"><span data-stu-id="2d446-108">What is a Complex Type</span></span>

<span data-ttu-id="2d446-109">複雜型別是實體型別的非純量屬性，可讓純量屬性得以在實體內組織。</span><span class="sxs-lookup"><span data-stu-id="2d446-109">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="2d446-110">如同實體，複雜類型包含純量屬性或其他複雜類型屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-110">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="2d446-111">當您使用代表複雜類型的物件時，請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="2d446-111">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="2d446-112">複雜型別沒有索引鍵，因此無法獨立存在。</span><span class="sxs-lookup"><span data-stu-id="2d446-112">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="2d446-113">複雜型別只能以實體類型或其他複雜型別的屬性形式存在。</span><span class="sxs-lookup"><span data-stu-id="2d446-113">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="2d446-114">複雜類型不能參與關聯，也不能包含導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-114">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="2d446-115">複雜型別屬性不可以是 **null**。</span><span class="sxs-lookup"><span data-stu-id="2d446-115">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="2d446-116">呼叫**DbCoNtext** 時，如果遇到 null 複雜物件，就會發生**InvalidOperationException **。</span><span class="sxs-lookup"><span data-stu-id="2d446-116">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="2d446-117">複雜物件的純量屬性可以是 **null**。</span><span class="sxs-lookup"><span data-stu-id="2d446-117">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="2d446-118">複雜型別無法繼承自其他複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-118">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="2d446-119">您必須將複雜型別定義為 **類別**。</span><span class="sxs-lookup"><span data-stu-id="2d446-119">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="2d446-120">呼叫**DbCoNtext. DetectChanges**時，EF 會偵測複雜類型物件上的成員變更。</span><span class="sxs-lookup"><span data-stu-id="2d446-120">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="2d446-121">當呼叫下列成員時，Entity Framework 會自動呼叫**DetectChanges** ： **DbSet. Find**、 **DbSet**、 **DbSet. Remove**、 **DbSet. Add**、DbSet、 **Attach**、 **DbCoNtext. SaveChanges**、 **DbCoNtext**、GetValidationErrors、 **DbCoNtext.** **Entry**。</span><span class="sxs-lookup"><span data-stu-id="2d446-121">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="2d446-122">將實體的屬性重構為新的複雜型別</span><span class="sxs-lookup"><span data-stu-id="2d446-122">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="2d446-123">如果您的概念模型中已經有實體，您可能會想要將某些屬性重構成複雜型別屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-123">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="2d446-124">在設計工具介面上，選取實體的一個或多個屬性（不包括導覽屬性），然後以滑鼠右鍵按一下並選取 [ **重構-&gt; 移至新的複雜類型**]。</span><span class="sxs-lookup"><span data-stu-id="2d446-124">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![重構](~/ef6/media/refactor.png)

<span data-ttu-id="2d446-126">具有所選屬性的新複雜型別會加入至 [**模型瀏覽器**] 中。</span><span class="sxs-lookup"><span data-stu-id="2d446-126">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="2d446-127">預設名稱會指定給該複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-127">The complex type is given a default name.</span></span>

<span data-ttu-id="2d446-128">新建立型別的複雜屬性會取代已選取的屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-128">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="2d446-129">所有屬性對應均會保留。</span><span class="sxs-lookup"><span data-stu-id="2d446-129">All property mappings are preserved.</span></span>

![重構2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="2d446-131">建立新的複雜類型</span><span class="sxs-lookup"><span data-stu-id="2d446-131">Create a New Complex Type</span></span>

<span data-ttu-id="2d446-132">您也可以建立不包含現有實體之屬性的新複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-132">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="2d446-133">以滑鼠右鍵按一下 [模型瀏覽器] 中的 [**複雜類型**] 資料夾，指向 [ **AddNew 複雜類型 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="2d446-133">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="2d446-134">或者，您也可以選取 **複雜類型** 資料夾，然後按下鍵盤上的  **插入** 鍵。</span><span class="sxs-lookup"><span data-stu-id="2d446-134">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![加入新的複雜類型](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="2d446-136">含有預設名稱的新屬性隨即會加入到該複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-136">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="2d446-137">您現在可以將屬性加入至類型。</span><span class="sxs-lookup"><span data-stu-id="2d446-137">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="2d446-138">將屬性加入至複雜型別</span><span class="sxs-lookup"><span data-stu-id="2d446-138">Add Properties to a Complex Type</span></span>

<span data-ttu-id="2d446-139">複雜型別屬性可以是純量型別或現有複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-139">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="2d446-140">不過，複雜型別屬性不能有循環參考。</span><span class="sxs-lookup"><span data-stu-id="2d446-140">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="2d446-141">例如，複雜型別 **OnsiteCourseDetails** 不能有複雜型別 **OnsiteCourseDetails**的屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-141">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="2d446-142">您可以利用下列任何一種方法，將屬性加入至複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-142">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="2d446-143">以滑鼠右鍵按一下 [模型瀏覽器] 中的複雜類型，指向 [ **加入**]，然後指向 [純量 **屬性**] 或 [ **複雜屬性**]，然後選取所需的屬性類型。</span><span class="sxs-lookup"><span data-stu-id="2d446-143">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="2d446-144">或者，您也可以選取複雜型別，然後按下鍵盤上的 **插入** 鍵。</span><span class="sxs-lookup"><span data-stu-id="2d446-144">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![將屬性加入至複雜型別](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="2d446-146">含有預設名稱的新屬性隨即會加入至該複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-146">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="2d446-147">或 -</span><span class="sxs-lookup"><span data-stu-id="2d446-147">OR -</span></span>

-   <span data-ttu-id="2d446-148">以滑鼠右鍵按一下**EF 設計**工具介面上的實體屬性，然後選取 [ **複製**]，再以滑鼠右鍵按一下 [**模型瀏覽器**] 中的複雜類型，然後選取 [ **貼**上]。</span><span class="sxs-lookup"><span data-stu-id="2d446-148">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="2d446-149">重新命名複雜型別</span><span class="sxs-lookup"><span data-stu-id="2d446-149">Rename a Complex Type</span></span>

<span data-ttu-id="2d446-150">重新命名複雜型別時，所有型別的參考都會經由專案更新。</span><span class="sxs-lookup"><span data-stu-id="2d446-150">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="2d446-151">在**模型瀏覽器**中，以緩慢的方式按兩下複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-151">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="2d446-152">所選取的名稱會在編輯模式中。</span><span class="sxs-lookup"><span data-stu-id="2d446-152">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="2d446-153">或 -</span><span class="sxs-lookup"><span data-stu-id="2d446-153">OR -</span></span>

-   <span data-ttu-id="2d446-154">以滑鼠右鍵按一下 [**模型瀏覽器**] 中的複雜類型，然後選取 [ **重新命名**]。</span><span class="sxs-lookup"><span data-stu-id="2d446-154">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="2d446-155">或 -</span><span class="sxs-lookup"><span data-stu-id="2d446-155">OR -</span></span>

-   <span data-ttu-id="2d446-156">選取 [模型瀏覽器] 中的 [複雜型別]，並按下 F2 鍵。</span><span class="sxs-lookup"><span data-stu-id="2d446-156">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="2d446-157">或 -</span><span class="sxs-lookup"><span data-stu-id="2d446-157">OR -</span></span>

-   <span data-ttu-id="2d446-158">以滑鼠右鍵按一下 [**模型瀏覽器**] 中的複雜類型，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="2d446-158">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="2d446-159">在 [ **屬性** ] 視窗中編輯名稱。</span><span class="sxs-lookup"><span data-stu-id="2d446-159">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="2d446-160">將現有的複雜型別加入至實體，並將其屬性對應至資料表資料行</span><span class="sxs-lookup"><span data-stu-id="2d446-160">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="2d446-161">以滑鼠右鍵按一下實體，指向 [ **加入新**的]，然後選取 [ **複雜屬性**]。</span><span class="sxs-lookup"><span data-stu-id="2d446-161">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="2d446-162">含有預設名稱的複雜型別屬性隨即會加入到該實體。</span><span class="sxs-lookup"><span data-stu-id="2d446-162">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="2d446-163">將預設型別 (從現有複雜型別選出) 指派至該屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-163">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="2d446-164">在 [ **屬性** ] 視窗中，將所需的類型指派給屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-164">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="2d446-165">將複雜型別屬性加入到實體之後，必須將複雜型別屬性對應到資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="2d446-165">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="2d446-166">以滑鼠右鍵按一下設計介面上的實體類型，或在 **模型瀏覽器**中 並選取 [ **資料表**對應]。</span><span class="sxs-lookup"><span data-stu-id="2d446-166">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="2d446-167"> **對應詳細資料**  視窗中會顯示資料表對應。</span><span class="sxs-lookup"><span data-stu-id="2d446-167">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="2d446-168">展開  **對應 以 &lt;資料表名稱&gt;**   節點。</span><span class="sxs-lookup"><span data-stu-id="2d446-168">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="2d446-169">[資料 **行**對應 ] 節點隨即出現。</span><span class="sxs-lookup"><span data-stu-id="2d446-169">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="2d446-170">展開 [資料 **行**對應 ] 節點。</span><span class="sxs-lookup"><span data-stu-id="2d446-170">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="2d446-171">資料表中所有資料行的清單隨即出現。</span><span class="sxs-lookup"><span data-stu-id="2d446-171">A list of all the columns in the table appears.</span></span> <span data-ttu-id="2d446-172">資料行對應的預設屬性（如果有的話）會列在 [ **值/屬性**] 標題底下。</span><span class="sxs-lookup"><span data-stu-id="2d446-172">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="2d446-173">選取您要對應的資料行，然後以滑鼠右鍵按一下對應的 [ **值/屬性** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="2d446-173">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="2d446-174">所有純量屬性的下拉式清單便會顯示。</span><span class="sxs-lookup"><span data-stu-id="2d446-174">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="2d446-175">選取適當的屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-175">Select the appropriate property.</span></span>

    ![對應複雜類型](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="2d446-177">針對每一個資料表資料行重複步驟 6 和 7。</span><span class="sxs-lookup"><span data-stu-id="2d446-177">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="2d446-178">若要刪除資料行對應，請選取您要對應的資料行，然後按一下 [ **值/屬性** ] 欄位。</span><span class="sxs-lookup"><span data-stu-id="2d446-178">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="2d446-179">然後，從下拉式清單中選取 [**刪除**]。</span><span class="sxs-lookup"><span data-stu-id="2d446-179">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="2d446-180">將函式匯入對應到複雜類型</span><span class="sxs-lookup"><span data-stu-id="2d446-180">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="2d446-181">函式匯入是以預存程序為基礎。</span><span class="sxs-lookup"><span data-stu-id="2d446-181">Function imports are based on stored procedures.</span></span> <span data-ttu-id="2d446-182">若要將函式匯入對應到複雜型別，由對應預存程序傳回的資料行與此複雜型別的屬性數目必須相符，而且必須具有與屬性型別相容的儲存型別。</span><span class="sxs-lookup"><span data-stu-id="2d446-182">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="2d446-183">按兩下您想要對應至複雜類型的匯入函數。</span><span class="sxs-lookup"><span data-stu-id="2d446-183">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![函式匯入](~/ef6/media/functionimports.png)

-   <span data-ttu-id="2d446-185">填入新函式匯入的設定，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2d446-185">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="2d446-186">在 [ **預存程式名稱** ] 欄位中，指定您要在其中建立函數匯入的預存程式。</span><span class="sxs-lookup"><span data-stu-id="2d446-186">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="2d446-187">此欄位是下拉式清單，其中顯示儲存體模型中所有的預存程序。</span><span class="sxs-lookup"><span data-stu-id="2d446-187">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="2d446-188">在 [函式匯 **入名稱** ] 欄位中，指定函式匯入的名稱。</span><span class="sxs-lookup"><span data-stu-id="2d446-188">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="2d446-189">選取 [ **複雜** ] 做為傳回類型，然後從下拉式清單中選擇適當的類型，以指定特定的複雜傳回類型。</span><span class="sxs-lookup"><span data-stu-id="2d446-189">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![編輯函式匯入](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="2d446-191">按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="2d446-191">Click **OK**.</span></span>
    <span data-ttu-id="2d446-192">這個函式匯入項目便會在概念模型中建立。</span><span class="sxs-lookup"><span data-stu-id="2d446-192">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="2d446-193">自訂函式匯入的資料行對應</span><span class="sxs-lookup"><span data-stu-id="2d446-193">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="2d446-194">以滑鼠右鍵按一下 [模型瀏覽器] 中的函式匯入，然後選取 [ **函數匯入對應**]。</span><span class="sxs-lookup"><span data-stu-id="2d446-194">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="2d446-195"> **對應詳細資料**  視窗隨即出現，並顯示函式匯入的預設對應。</span><span class="sxs-lookup"><span data-stu-id="2d446-195">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="2d446-196">箭頭是指示資料行值與屬性值之間的對應。</span><span class="sxs-lookup"><span data-stu-id="2d446-196">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="2d446-197">根據預設，資料行名稱是假設與此複雜型別的屬性名稱相同。</span><span class="sxs-lookup"><span data-stu-id="2d446-197">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="2d446-198">預設資料行名稱會以灰色文字顯示。</span><span class="sxs-lookup"><span data-stu-id="2d446-198">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="2d446-199">如有必要，可將資料行名稱變更為與對應函式匯入的預存程序所傳回的資料行名稱相同。</span><span class="sxs-lookup"><span data-stu-id="2d446-199">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="2d446-200">刪除複雜型別</span><span class="sxs-lookup"><span data-stu-id="2d446-200">Delete a Complex Type</span></span>

<span data-ttu-id="2d446-201">刪除複雜型別時，是從概念模型中刪除該型別，且所有該型別所有執行個體的對應都會刪除。</span><span class="sxs-lookup"><span data-stu-id="2d446-201">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="2d446-202">但是，該型別的參考不會更新。</span><span class="sxs-lookup"><span data-stu-id="2d446-202">However, references to the type are not updated.</span></span> <span data-ttu-id="2d446-203">例如，如果實體具有類型為 ComplexType1 的複雜類型屬性，而且在**模型瀏覽器**中刪除了 ComplexType1，則不會更新對應的實體屬性。</span><span class="sxs-lookup"><span data-stu-id="2d446-203">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="2d446-204">模型將不會驗證，因為它包含參考已刪除之複雜類型的實體。</span><span class="sxs-lookup"><span data-stu-id="2d446-204">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="2d446-205">您可以使用 Entity Designer 來更新或刪除已刪除之複雜型別的參考。</span><span class="sxs-lookup"><span data-stu-id="2d446-205">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="2d446-206">以滑鼠右鍵按一下 [模型瀏覽器] 中的複雜類型，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="2d446-206">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="2d446-207">或 -</span><span class="sxs-lookup"><span data-stu-id="2d446-207">OR -</span></span>

-   <span data-ttu-id="2d446-208">在 [模型瀏覽器] 中選取 [複雜型別]，然後按下鍵盤上的 Delete 鍵。</span><span class="sxs-lookup"><span data-stu-id="2d446-208">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="2d446-209">查詢包含複雜類型屬性的實體</span><span class="sxs-lookup"><span data-stu-id="2d446-209">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="2d446-210">下列程式碼會示範如何執行查詢，以傳回包含複雜型別屬性之實體型別物件的集合。</span><span class="sxs-lookup"><span data-stu-id="2d446-210">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
