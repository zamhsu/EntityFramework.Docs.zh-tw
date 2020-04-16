---
title: 關聯、導覽屬性與外鍵 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434322"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="b589a-102">關聯、導航屬性和外鍵</span><span class="sxs-lookup"><span data-stu-id="b589a-102">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="b589a-103">本文概述了實體框架如何管理實體之間的關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-103">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="b589a-104">它還就如何映射和操作關係提供了一些指導。</span><span class="sxs-lookup"><span data-stu-id="b589a-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="b589a-105">EF 中的關係</span><span class="sxs-lookup"><span data-stu-id="b589a-105">Relationships in EF</span></span>

<span data-ttu-id="b589a-106">在關係資料庫中,表之間的關係(也稱為關聯)通過外鍵定義。</span><span class="sxs-lookup"><span data-stu-id="b589a-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="b589a-107">外部索引鍵 (FK) 是可用來建立與強制兩資料表的資料之間連結的一個資料行或資料行組合。</span><span class="sxs-lookup"><span data-stu-id="b589a-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="b589a-108">通常有三種類型的關係:一對一、一對多和多對多。</span><span class="sxs-lookup"><span data-stu-id="b589a-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="b589a-109">在一對多關係中,外鍵在表上定義,該表表示關係的多端。</span><span class="sxs-lookup"><span data-stu-id="b589a-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="b589a-110">多對多關係涉及定義第三個表(稱為連接表或聯接表),其主鍵由兩個相關表中的外鍵組成。</span><span class="sxs-lookup"><span data-stu-id="b589a-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="b589a-111">在一對一關係中,主鍵另外充當外鍵,並且兩個表沒有單獨的外鍵列。</span><span class="sxs-lookup"><span data-stu-id="b589a-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="b589a-112">下圖顯示了兩個參與一對多關係的表。</span><span class="sxs-lookup"><span data-stu-id="b589a-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="b589a-113">**課程**表是從屬表,因為它包含將其連結到 **「部門」** 表的 **「部門 ID」** 列。</span><span class="sxs-lookup"><span data-stu-id="b589a-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![部門和課程表](~/ef6/media/database2.png)

<span data-ttu-id="b589a-115">在實體框架中,實體可以通過關聯或關係與其他實體關聯。</span><span class="sxs-lookup"><span data-stu-id="b589a-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="b589a-116">每個關係包含兩個端,用於描述該關係中兩個實體的實體類型和類型的多重性(一個、零或一個或多個)。</span><span class="sxs-lookup"><span data-stu-id="b589a-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="b589a-117">關聯性可能由參考條件約束控制，它會描述關聯性中的哪一端是主要角色，哪一端是相依角色。</span><span class="sxs-lookup"><span data-stu-id="b589a-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="b589a-118">導航屬性提供了一種導航兩種實體類型之間的關聯的方法。</span><span class="sxs-lookup"><span data-stu-id="b589a-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="b589a-119">每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="b589a-120">導航屬性允許您在兩個方向上導航和管理關係,傳回引用物件(如果多重性為1或為零或1)或集合(如果多重性多)。</span><span class="sxs-lookup"><span data-stu-id="b589a-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="b589a-121">您還可以選擇單向導航,在這種情況下,您只對參與關係的一種類型定義導航屬性,而不是同時在兩種類型上定義導航屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="b589a-122">建議在模型中包括映射到資料庫中的外鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="b589a-123">包括了外部索引鍵之後，您便可以透過修改相依物件上的外部索引鍵值來建立或變更關聯性。</span><span class="sxs-lookup"><span data-stu-id="b589a-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="b589a-124">這個類型的關聯稱為外部索引鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="b589a-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="b589a-125">使用斷開連接的實體時,使用外鍵更為重要。</span><span class="sxs-lookup"><span data-stu-id="b589a-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="b589a-126">請注意,使用 1 比 1 或 1 比 0 時。1 個關係,沒有單獨的外鍵列,主鍵屬性充當外鍵,始終包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="b589a-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="b589a-127">當模型中不包括外鍵列時,關聯資訊將作為獨立物件進行管理。</span><span class="sxs-lookup"><span data-stu-id="b589a-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="b589a-128">關係通過物件引用而不是外鍵屬性進行跟蹤。</span><span class="sxs-lookup"><span data-stu-id="b589a-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="b589a-129">這種類型的關聯稱為*獨立關聯*。</span><span class="sxs-lookup"><span data-stu-id="b589a-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="b589a-130">修改*獨立關聯的*最常見方法是修改為參與關聯的每個實體生成的導航屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="b589a-131">您可以選擇在模型中使用一種或兩種關聯類型。</span><span class="sxs-lookup"><span data-stu-id="b589a-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="b589a-132">但是,如果您有一個純多對多關係,該關係由僅包含外鍵的聯接表連接,則 EF 將使用獨立關聯來管理此類多對多關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="b589a-133">下圖顯示了使用實體框架設計器創建的概念模型。</span><span class="sxs-lookup"><span data-stu-id="b589a-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="b589a-134">該模型包含兩個參與一對多關係的實體。</span><span class="sxs-lookup"><span data-stu-id="b589a-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="b589a-135">兩個實體都具有導航屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-135">Both entities have navigation properties.</span></span> <span data-ttu-id="b589a-136">**課程**是從屬實體,並且定義了**DepartmentID**外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-136">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![具有導覽屬性的部門和課程表](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="b589a-138">以下代碼段顯示使用代碼優先創建的模型。</span><span class="sxs-lookup"><span data-stu-id="b589a-138">The following code snippet shows the same model that was created with Code First.</span></span>

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="b589a-139">設定或對應關係</span><span class="sxs-lookup"><span data-stu-id="b589a-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="b589a-140">本頁的其餘部分介紹如何使用關係訪問和操作數據。</span><span class="sxs-lookup"><span data-stu-id="b589a-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="b589a-141">有關在模型中設置關係的資訊,請參閱以下頁面。</span><span class="sxs-lookup"><span data-stu-id="b589a-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="b589a-142">在程式碼優先中設定關聯,請參考[資料註解](~/ef6/modeling/code-first/data-annotations.md)與[Fluent API = 關係](~/ef6/modeling/code-first/fluent/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="b589a-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="b589a-143">要使用實體框架設計器設定關係,請參閱[與 EF 設計器的關係](~/ef6/modeling/designer/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="b589a-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="b589a-144">建立與修改關係</span><span class="sxs-lookup"><span data-stu-id="b589a-144">Creating and modifying relationships</span></span>

<span data-ttu-id="b589a-145">在*外鍵關聯*中,當您變更關係時`EntityState.Unchanged`,具有狀態的從屬物件的狀態將更改`EntityState.Modified`為 。</span><span class="sxs-lookup"><span data-stu-id="b589a-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="b589a-146">在獨立關係中,更改關係不會更新從屬物件的狀態。</span><span class="sxs-lookup"><span data-stu-id="b589a-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="b589a-147">以下範例展示如何使用外鍵屬性和導航屬性來關聯相關物件。</span><span class="sxs-lookup"><span data-stu-id="b589a-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="b589a-148">使用外鍵關聯,可以使用任一方法更改、創建或修改關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="b589a-149">使用獨立關聯，則無法使用外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="b589a-150">通過將新值分配給外鍵屬性,如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="b589a-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="b589a-151">以下代碼通過將外鍵設置為**null**來刪除關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="b589a-152">請注意,外鍵屬性必須為空。</span><span class="sxs-lookup"><span data-stu-id="b589a-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="b589a-153">如果引用處於添加狀態(在此示例中為課程物件),則在調用 SaveChanges 之前,引用導航屬性不會與新物件的鍵值同步。</span><span class="sxs-lookup"><span data-stu-id="b589a-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="b589a-154">不會同步化是因為物件內容未包含新加入之物件的永久索引鍵，除非先儲存這些索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b589a-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="b589a-155">如果在設置關係后立即必須完全同步新物件,請使用以下方法之一。</span><span class="sxs-lookup"><span data-stu-id="b589a-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="b589a-156">將新物件指派給導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="b589a-157">以下代碼在課程和之間`department`創建關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="b589a-158">如果物件附加到上下文,`course`則 還會將`department.Courses`添加到集合`course`中,並且 物件上的相應外鍵屬性將設置為部門的關鍵屬性值。</span><span class="sxs-lookup"><span data-stu-id="b589a-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="b589a-159">要刪除關聯,請將導覽屬性設定`null`為 。</span><span class="sxs-lookup"><span data-stu-id="b589a-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="b589a-160">如果使用基於 .NET 4.0 的實體框架,則需要在將其設置為 null 之前載入相關端。</span><span class="sxs-lookup"><span data-stu-id="b589a-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="b589a-161">例如：</span><span class="sxs-lookup"><span data-stu-id="b589a-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="b589a-162">從基於 .NET 4.5 的實體框架 5.0 開始,可以將關係設置為 null,而無需載入相關端。</span><span class="sxs-lookup"><span data-stu-id="b589a-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="b589a-163">您還可以使用以下方法將當前值設定為 null。</span><span class="sxs-lookup"><span data-stu-id="b589a-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="b589a-164">在實體集合中刪除或加入物件。</span><span class="sxs-lookup"><span data-stu-id="b589a-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="b589a-165">例如,可以將類型`Course`物件添加到集合中`department.Courses`。</span><span class="sxs-lookup"><span data-stu-id="b589a-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="b589a-166">此操作在特定**課程**和特定`department`之間創建關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="b589a-167">如果物件附加到上下文,**則課程**物件的部門參考和外鍵屬性設定為`department`相應的 。</span><span class="sxs-lookup"><span data-stu-id="b589a-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="b589a-168">通過使用方法`ChangeRelationshipState`更改兩個實體對象之間指定關係的狀態。</span><span class="sxs-lookup"><span data-stu-id="b589a-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="b589a-169">此方法最常用於使用 N-Tier 應用程式和獨立*關聯*(不能與外鍵關聯一起使用)。</span><span class="sxs-lookup"><span data-stu-id="b589a-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="b589a-170">此外,要使用此方法,必須下拉至`ObjectContext`,如下例所示。</span><span class="sxs-lookup"><span data-stu-id="b589a-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="b589a-171">在下面的示例中,教師和課程之間存在多對多關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="b589a-172">呼叫`ChangeRelationshipState`方法並傳遞`EntityState.Added`參數`SchoolContext`, 可以知道兩個物件之間已新增了關係:</span><span class="sxs-lookup"><span data-stu-id="b589a-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="b589a-173">請注意,如果要更新(而不僅僅是添加)關係,則必須在添加新關係后刪除舊關係:</span><span class="sxs-lookup"><span data-stu-id="b589a-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="b589a-174">同步外鍵與導覽屬性之間的變更</span><span class="sxs-lookup"><span data-stu-id="b589a-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="b589a-175">使用上述方法之一更改附加到上下文的對象之間的關係時,實體框架需要保持外鍵、引用和集合同步。實體框架自動管理具有代理的 POCO 實體的此同步(也稱為關係修復)。</span><span class="sxs-lookup"><span data-stu-id="b589a-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="b589a-176">有關詳細資訊,請參閱[使用代理](~/ef6/fundamentals/proxies.md)。</span><span class="sxs-lookup"><span data-stu-id="b589a-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="b589a-177">如果使用沒有代理的 POCO 實體,則必須確保調用**檢測更改**方法以同步上下文中的相關物件。</span><span class="sxs-lookup"><span data-stu-id="b589a-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="b589a-178">請注意,以下 API 會自動觸發**檢測更改**調用。</span><span class="sxs-lookup"><span data-stu-id="b589a-178">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   <span data-ttu-id="b589a-179">執行 LINQ 查詢`DbSet`</span><span class="sxs-lookup"><span data-stu-id="b589a-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="b589a-180">載入相關物件</span><span class="sxs-lookup"><span data-stu-id="b589a-180">Loading related objects</span></span>

<span data-ttu-id="b589a-181">在實體框架中,通常使用導航屬性載入由定義的關聯與返回的實體相關的實體。</span><span class="sxs-lookup"><span data-stu-id="b589a-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="b589a-182">有關詳細資訊,請參閱[載入相關物件](~/ef6/querying/related-data.md)。</span><span class="sxs-lookup"><span data-stu-id="b589a-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="b589a-183">在外部索引鍵關聯中，當您載入相依物件的相關端時，相關物件將根據目前在記憶體中之相依外部索引鍵值來載入：</span><span class="sxs-lookup"><span data-stu-id="b589a-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="b589a-184">在獨立關聯中，會根據目前在資料庫中的外部索引鍵值來查詢相依物件的相關端。</span><span class="sxs-lookup"><span data-stu-id="b589a-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="b589a-185">但是,如果修改了關係,並且從屬物件的引用屬性指向在物件上下文中載入的不同主體物件,實體框架將嘗試創建在用戶端上定義的關係。</span><span class="sxs-lookup"><span data-stu-id="b589a-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="b589a-186">管理並行存取</span><span class="sxs-lookup"><span data-stu-id="b589a-186">Managing concurrency</span></span>

<span data-ttu-id="b589a-187">在外鍵和獨立關聯中,併發檢查基於模型中定義的實體密鑰和其他實體屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="b589a-188">使用 EF 設計器建立模型時`ConcurrencyMode`,將 屬性設置為**固定**以指定應檢查屬性為併發性。</span><span class="sxs-lookup"><span data-stu-id="b589a-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="b589a-189">使用 Code First 定義模型`ConcurrencyCheck`時,對 要檢查為併發的屬性使用註釋。</span><span class="sxs-lookup"><span data-stu-id="b589a-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="b589a-190">使用 Code First`TimeStamp`時, 還可以使用註釋指定應檢查屬性為併發性。</span><span class="sxs-lookup"><span data-stu-id="b589a-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="b589a-191">給定類中只能有一個時間戳屬性。</span><span class="sxs-lookup"><span data-stu-id="b589a-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="b589a-192">代碼 First 將此屬性映射到資料庫中的非空欄位。</span><span class="sxs-lookup"><span data-stu-id="b589a-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="b589a-193">我們建議您在使用參與併發檢查和解析的實體時始終使用外鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="b589a-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="b589a-194">有關詳細資訊,請參閱[處理併發衝突](~/ef6/saving/concurrency.md)。</span><span class="sxs-lookup"><span data-stu-id="b589a-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="b589a-195">使用重疊鍵</span><span class="sxs-lookup"><span data-stu-id="b589a-195">Working with overlapping Keys</span></span>

<span data-ttu-id="b589a-196">重疊索引鍵是複合索引鍵，其索引鍵中的部分屬性也是實體中另一個索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="b589a-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="b589a-197">在獨立關聯中不能有重疊索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b589a-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="b589a-198">若要變更包括重疊索引鍵的外部索引鍵關聯，我們建議您修改外部索引鍵值，而不是使用物件參考。</span><span class="sxs-lookup"><span data-stu-id="b589a-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
