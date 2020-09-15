---
title: 複雜類型-EF Designer-EF6
description: 複雜類型-Entity Framework 6 中的 EF 設計工具
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: 6ebba9ea36ddad1a25b21bf98a6ac3090c327144
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073509"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="76be6-103">複雜類型-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="76be6-103">Complex Types - EF Designer</span></span>
<span data-ttu-id="76be6-104">本主題說明如何使用 Entity Framework Designer (EF) 設計工具來對應複雜型別，以及如何查詢包含複雜型別屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="76be6-104">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="76be6-105">下圖顯示使用 EF 設計工具時使用的主視窗。</span><span class="sxs-lookup"><span data-stu-id="76be6-105">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="76be6-107">建置概念模型時，與未對應之實體和關聯有關的警告可能會在 [錯誤清單] 中出現。</span><span class="sxs-lookup"><span data-stu-id="76be6-107">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="76be6-108">您可以忽略這些警告，因為在您選擇從模型產生資料庫之後，錯誤就會消失。</span><span class="sxs-lookup"><span data-stu-id="76be6-108">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="76be6-109">什麼是複雜類型</span><span class="sxs-lookup"><span data-stu-id="76be6-109">What is a Complex Type</span></span>

<span data-ttu-id="76be6-110">複雜型別是實體型別的非純量屬性，可讓純量屬性得以在實體內組織。</span><span class="sxs-lookup"><span data-stu-id="76be6-110">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="76be6-111">如同實體，複雜類型包含純量屬性或其他複雜類型屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-111">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="76be6-112">當您使用代表複雜類型的物件時，請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="76be6-112">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="76be6-113">複雜型別沒有索引鍵，因此無法獨立存在。</span><span class="sxs-lookup"><span data-stu-id="76be6-113">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="76be6-114">複雜型別只能以實體類型或其他複雜型別的屬性形式存在。</span><span class="sxs-lookup"><span data-stu-id="76be6-114">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="76be6-115">複雜類型不能參與關聯，也不能包含導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-115">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="76be6-116">複雜型別屬性不可以是 **null**。</span><span class="sxs-lookup"><span data-stu-id="76be6-116">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="76be6-117">當**InvalidOperationException **呼叫**DbCoNtext**   並遇到 null 複雜物件時，就會發生 InvalidOperationException。</span><span class="sxs-lookup"><span data-stu-id="76be6-117">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="76be6-118">複雜物件的純量屬性可以是 **null**。</span><span class="sxs-lookup"><span data-stu-id="76be6-118">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="76be6-119">複雜型別無法繼承自其他複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-119">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="76be6-120">您必須將複雜類型定義為 **類別**。</span><span class="sxs-lookup"><span data-stu-id="76be6-120">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="76be6-121">當呼叫 **DbCoNtext** 時，EF 會偵測對複雜類型物件上之成員的變更。</span><span class="sxs-lookup"><span data-stu-id="76be6-121">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="76be6-122">當呼叫下列成員時，Entity Framework 會自動呼叫**DetectChanges** ： **DbSet. Find**、 **DbSet**、 **DbSet. Remove**、 **DbSet. Add**、DbSet. **Attach**、DbCoNtext **. SaveChanges**、 **DbCoNtext. GetValidationErrors**、 **DbCoNtext. Entry**、 **DbChangeTracker。**</span><span class="sxs-lookup"><span data-stu-id="76be6-122">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="76be6-123">將實體的屬性重構至新的複雜型別</span><span class="sxs-lookup"><span data-stu-id="76be6-123">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="76be6-124">如果您的概念模型中已經有實體，您可能會想要將某些屬性重構為複雜類型屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-124">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="76be6-125">在設計工具介面上，選取一或多個屬性 (不包括實體的導覽屬性) ，然後以滑鼠右鍵按一下並選取 [ **重構- &gt; 移至新的複雜型**別]。</span><span class="sxs-lookup"><span data-stu-id="76be6-125">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![重構至新的複雜型別](~/ef6/media/refactor.png)

<span data-ttu-id="76be6-127">具有所選屬性的新複雜型別會加入至 **模型瀏覽器**。</span><span class="sxs-lookup"><span data-stu-id="76be6-127">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="76be6-128">預設名稱會指定給該複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-128">The complex type is given a default name.</span></span>

<span data-ttu-id="76be6-129">新建立型別的複雜屬性會取代已選取的屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-129">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="76be6-130">所有屬性對應均會保留。</span><span class="sxs-lookup"><span data-stu-id="76be6-130">All property mappings are preserved.</span></span>

![重構至新的複雜型別2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="76be6-132">建立新的複雜型別</span><span class="sxs-lookup"><span data-stu-id="76be6-132">Create a New Complex Type</span></span>

<span data-ttu-id="76be6-133">您也可以建立不包含現有實體屬性的新複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-133">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="76be6-134">以滑鼠右鍵按一下 [模型瀏覽器] 中的 [ **複雜類型** ] 資料夾，指向 [ **AddNew 複雜類型 ...**]。</span><span class="sxs-lookup"><span data-stu-id="76be6-134">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="76be6-135">或者，您也可以選取 [**複雜類型**] 資料夾，然後按鍵盤上的 [ **插入**]   鍵。</span><span class="sxs-lookup"><span data-stu-id="76be6-135">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![加入新的複雜類型](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="76be6-137">含有預設名稱的新屬性隨即會加入到該複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-137">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="76be6-138">您現在可以將屬性加入至類型。</span><span class="sxs-lookup"><span data-stu-id="76be6-138">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="76be6-139">將屬性加入至複雜型別</span><span class="sxs-lookup"><span data-stu-id="76be6-139">Add Properties to a Complex Type</span></span>

<span data-ttu-id="76be6-140">複雜型別屬性可以是純量型別或現有複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-140">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="76be6-141">不過，複雜型別屬性不能有循環參考。</span><span class="sxs-lookup"><span data-stu-id="76be6-141">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="76be6-142">例如，複雜型別 **OnsiteCourseDetails**   不能有複雜型別 **OnsiteCourseDetails**的屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-142">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="76be6-143">您可以利用下列任何一種方法，將屬性加入至複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-143">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="76be6-144">以滑鼠右鍵按一下 [模型瀏覽器] 中的複雜類型，指向 [ **加入**]，然後指向 [純量 **屬性**]   或 [ **複雜屬性**]，然後選取所需的屬性類型。</span><span class="sxs-lookup"><span data-stu-id="76be6-144">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="76be6-145">或者，您也可以選取複雜類型，然後按鍵盤上的 **插入**   鍵。</span><span class="sxs-lookup"><span data-stu-id="76be6-145">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![將屬性加入至複雜型別](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="76be6-147">含有預設名稱的新屬性隨即會加入至該複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-147">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="76be6-148">或 -</span><span class="sxs-lookup"><span data-stu-id="76be6-148">OR -</span></span>

-   <span data-ttu-id="76be6-149">以滑鼠右鍵按一下 **EF 設計** 工具介面上的實體屬性，然後選取 [ **複製**]，再以滑鼠右鍵按一下 [ **模型瀏覽器** ] 中的複雜類型，然後選取 [ **貼**上]。</span><span class="sxs-lookup"><span data-stu-id="76be6-149">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="76be6-150">重新命名複雜型別</span><span class="sxs-lookup"><span data-stu-id="76be6-150">Rename a Complex Type</span></span>

<span data-ttu-id="76be6-151">重新命名複雜型別時，所有型別的參考都會經由專案更新。</span><span class="sxs-lookup"><span data-stu-id="76be6-151">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="76be6-152">在 **模型瀏覽器**中，以緩慢的方式按兩下複雜型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-152">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="76be6-153">所選取的名稱會在編輯模式中。</span><span class="sxs-lookup"><span data-stu-id="76be6-153">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="76be6-154">或 -</span><span class="sxs-lookup"><span data-stu-id="76be6-154">OR -</span></span>

-   <span data-ttu-id="76be6-155">以滑鼠右鍵按一下 [ **模型瀏覽器** ] 中的複雜類型，然後選取 [ **重新命名**]。</span><span class="sxs-lookup"><span data-stu-id="76be6-155">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="76be6-156">或 -</span><span class="sxs-lookup"><span data-stu-id="76be6-156">OR -</span></span>

-   <span data-ttu-id="76be6-157">選取 [模型瀏覽器] 中的 [複雜型別]，並按下 F2 鍵。</span><span class="sxs-lookup"><span data-stu-id="76be6-157">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="76be6-158">或 -</span><span class="sxs-lookup"><span data-stu-id="76be6-158">OR -</span></span>

-   <span data-ttu-id="76be6-159">以滑鼠右鍵按一下 [ **模型瀏覽器** ] 中的複雜類型，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="76be6-159">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="76be6-160">在 [ **屬性**   ] 視窗中編輯名稱。</span><span class="sxs-lookup"><span data-stu-id="76be6-160">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="76be6-161">將現有的複雜類型加入至實體，並將其屬性對應至資料表資料行</span><span class="sxs-lookup"><span data-stu-id="76be6-161">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="76be6-162">以滑鼠右鍵按一下實體，指向 [ **加入新**的]，然後選取 [ **複雜屬性**]。</span><span class="sxs-lookup"><span data-stu-id="76be6-162">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="76be6-163">含有預設名稱的複雜型別屬性隨即會加入到該實體。</span><span class="sxs-lookup"><span data-stu-id="76be6-163">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="76be6-164">將預設型別 (從現有複雜型別選出) 指派至該屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-164">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="76be6-165">在 [ **屬性**] 視窗中，將所需的型別指派給屬性   。</span><span class="sxs-lookup"><span data-stu-id="76be6-165">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="76be6-166">將複雜型別屬性加入到實體之後，必須將複雜型別屬性對應到資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="76be6-166">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="76be6-167">在設計介面上或在 **模型瀏覽器**中，以滑鼠右鍵按一下實體類型   ，然後選取 [ **資料表**對應]。</span><span class="sxs-lookup"><span data-stu-id="76be6-167">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="76be6-168">資料表對應會顯示在 [ **對應詳細資料**   ] 視窗中。</span><span class="sxs-lookup"><span data-stu-id="76be6-168">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="76be6-169">展開 [ \*\*對應至 &lt; 資料表名稱 &gt; \*\*]   節點。</span><span class="sxs-lookup"><span data-stu-id="76be6-169">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="76be6-170">[資料 **行**對應]   節點隨即出現。</span><span class="sxs-lookup"><span data-stu-id="76be6-170">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="76be6-171">展開 [資料 **行**對應]   節點。</span><span class="sxs-lookup"><span data-stu-id="76be6-171">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="76be6-172">資料表中所有資料行的清單隨即出現。</span><span class="sxs-lookup"><span data-stu-id="76be6-172">A list of all the columns in the table appears.</span></span> <span data-ttu-id="76be6-173">如果資料行對應的任何) 列在 [ **值/屬性**] 標題下，則預設屬性 (  。</span><span class="sxs-lookup"><span data-stu-id="76be6-173">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="76be6-174">選取您要對應的資料行，然後以滑鼠右鍵按一下對應的 [ **值/屬性**]   欄位。</span><span class="sxs-lookup"><span data-stu-id="76be6-174">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="76be6-175">所有純量屬性的下拉式清單便會顯示。</span><span class="sxs-lookup"><span data-stu-id="76be6-175">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="76be6-176">選取適當的屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-176">Select the appropriate property.</span></span>

    ![地圖複雜型別](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="76be6-178">針對每一個資料表資料行重複步驟 6 和 7。</span><span class="sxs-lookup"><span data-stu-id="76be6-178">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="76be6-179">若要刪除資料行對應，請選取您要對應的資料行，然後按一下 [ **值/屬性**]   欄位。</span><span class="sxs-lookup"><span data-stu-id="76be6-179">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="76be6-180">然後，從下拉式清單中選取 [ **刪除** ]。</span><span class="sxs-lookup"><span data-stu-id="76be6-180">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="76be6-181">將函數匯入對應到複雜型別</span><span class="sxs-lookup"><span data-stu-id="76be6-181">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="76be6-182">函式匯入是以預存程序為基礎。</span><span class="sxs-lookup"><span data-stu-id="76be6-182">Function imports are based on stored procedures.</span></span> <span data-ttu-id="76be6-183">若要將函式匯入對應到複雜型別，由對應預存程序傳回的資料行與此複雜型別的屬性數目必須相符，而且必須具有與屬性型別相容的儲存型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-183">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="76be6-184">按兩下您要對應至複雜類型的已匯入函數。</span><span class="sxs-lookup"><span data-stu-id="76be6-184">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![函數匯入](~/ef6/media/functionimports.png)

-   <span data-ttu-id="76be6-186">填入新函式匯入的設定，如下所示：</span><span class="sxs-lookup"><span data-stu-id="76be6-186">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="76be6-187">在 [ **預存程式名稱**] 欄位中，指定您要在其中建立函數匯入的預存程式   。</span><span class="sxs-lookup"><span data-stu-id="76be6-187">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="76be6-188">此欄位是下拉式清單，其中顯示儲存體模型中所有的預存程序。</span><span class="sxs-lookup"><span data-stu-id="76be6-188">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="76be6-189">在 [ **函數匯入名稱**] 欄位中指定函式匯入的名稱   。</span><span class="sxs-lookup"><span data-stu-id="76be6-189">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="76be6-190">選取 [ **複雜**]   做為傳回型別，然後從下拉式清單中選擇適當的類型，以指定特定的複雜傳回型別。</span><span class="sxs-lookup"><span data-stu-id="76be6-190">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![編輯函式匯入](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="76be6-192">按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="76be6-192">Click **OK**.</span></span>
    <span data-ttu-id="76be6-193">這個函式匯入項目便會在概念模型中建立。</span><span class="sxs-lookup"><span data-stu-id="76be6-193">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="76be6-194">自訂函數匯入的資料行對應</span><span class="sxs-lookup"><span data-stu-id="76be6-194">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="76be6-195">以滑鼠右鍵按一下 [模型瀏覽器] 中的函式匯入，然後選取 [ **函數匯入對應**]。</span><span class="sxs-lookup"><span data-stu-id="76be6-195">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="76be6-196">[ **對應詳細資料**   ] 視窗隨即出現，並顯示函數匯入的預設對應。</span><span class="sxs-lookup"><span data-stu-id="76be6-196">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="76be6-197">箭頭是指示資料行值與屬性值之間的對應。</span><span class="sxs-lookup"><span data-stu-id="76be6-197">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="76be6-198">根據預設，資料行名稱是假設與此複雜型別的屬性名稱相同。</span><span class="sxs-lookup"><span data-stu-id="76be6-198">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="76be6-199">預設資料行名稱會以灰色文字顯示。</span><span class="sxs-lookup"><span data-stu-id="76be6-199">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="76be6-200">如有必要，可將資料行名稱變更為與對應函式匯入的預存程序所傳回的資料行名稱相同。</span><span class="sxs-lookup"><span data-stu-id="76be6-200">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="76be6-201">刪除複雜型別</span><span class="sxs-lookup"><span data-stu-id="76be6-201">Delete a Complex Type</span></span>

<span data-ttu-id="76be6-202">刪除複雜型別時，是從概念模型中刪除該型別，且所有該型別所有執行個體的對應都會刪除。</span><span class="sxs-lookup"><span data-stu-id="76be6-202">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="76be6-203">但是，該型別的參考不會更新。</span><span class="sxs-lookup"><span data-stu-id="76be6-203">However, references to the type are not updated.</span></span> <span data-ttu-id="76be6-204">例如，如果實體具有類型為 ComplexType1 的複雜類型屬性，而且在 **模型瀏覽器**中刪除 ComplexType1，則不會更新對應的實體屬性。</span><span class="sxs-lookup"><span data-stu-id="76be6-204">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="76be6-205">模型不會進行驗證，因為它包含參考已刪除之複雜類型的實體。</span><span class="sxs-lookup"><span data-stu-id="76be6-205">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="76be6-206">您可以使用 Entity Designer 來更新或刪除已刪除之複雜型別的參考。</span><span class="sxs-lookup"><span data-stu-id="76be6-206">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="76be6-207">以滑鼠右鍵按一下 [模型瀏覽器] 中的複雜類型，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="76be6-207">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="76be6-208">或 -</span><span class="sxs-lookup"><span data-stu-id="76be6-208">OR -</span></span>

-   <span data-ttu-id="76be6-209">在 [模型瀏覽器] 中選取 [複雜型別]，然後按下鍵盤上的 Delete 鍵。</span><span class="sxs-lookup"><span data-stu-id="76be6-209">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="76be6-210">查詢包含複雜型別屬性的實體</span><span class="sxs-lookup"><span data-stu-id="76be6-210">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="76be6-211">下列程式碼示範如何執行查詢，該查詢會傳回包含複雜型別屬性之實體類型物件的集合。</span><span class="sxs-lookup"><span data-stu-id="76be6-211">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

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
