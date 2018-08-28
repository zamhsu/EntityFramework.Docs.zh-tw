---
title: 關聯性、 導覽屬性和外部索引鍵-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: c1d48f18a7dd25a6a48537f0de5379f861bf447a
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997997"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="79549-102">關聯性、 導覽屬性和外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="79549-102">Relationships, navigation properties and foreign keys</span></span>
<span data-ttu-id="79549-103">本主題提供 Entity Framework 如何管理實體之間的關聯性的概觀。</span><span class="sxs-lookup"><span data-stu-id="79549-103">This topic gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="79549-104">它也會提供有關如何對應及操作關聯性的一些指引。</span><span class="sxs-lookup"><span data-stu-id="79549-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="79549-105">在 EF 中的關聯性</span><span class="sxs-lookup"><span data-stu-id="79549-105">Relationships in EF</span></span>

<span data-ttu-id="79549-106">在關聯式資料庫 （也稱為關聯） 的資料表之間的關聯是透過外部索引鍵定義。</span><span class="sxs-lookup"><span data-stu-id="79549-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="79549-107">外部索引鍵 (FK) 是資料行或資料行的組合，用來建立與強制兩個資料表中的資料之間的連結。</span><span class="sxs-lookup"><span data-stu-id="79549-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="79549-108">通常有三種類型的關聯性： 一對一、 一個一對多和多對多。</span><span class="sxs-lookup"><span data-stu-id="79549-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="79549-109">在一對多關聯性，表示關聯性的多端資料表定義外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="79549-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="79549-110">多對多關聯性需要定義第三個資料表 （稱為聯合或聯結資料表），其主索引鍵由兩個相關資料表的外部索引鍵所組成。</span><span class="sxs-lookup"><span data-stu-id="79549-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="79549-111">在一對一的關聯性中，主索引鍵做為外部索引鍵，此外並沒有不同外部索引鍵資料行的任兩個資料表。</span><span class="sxs-lookup"><span data-stu-id="79549-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="79549-112">下圖顯示兩個參與的資料表，一個對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="79549-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="79549-113">**課程**資料表會是相依的資料表，因為它包含**DepartmentID**其連結至的資料行**部門**資料表。</span><span class="sxs-lookup"><span data-stu-id="79549-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![Database2](~/ef6/media/database2.png)

<span data-ttu-id="79549-115">在 Entity Framework 中，實體可以與其他機構可透過關聯或關係。</span><span class="sxs-lookup"><span data-stu-id="79549-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="79549-116">每個關聯性包含兩端，描述實體類型，而該關聯性中的兩個實體的類型 （一個、 零或一，或多個）。</span><span class="sxs-lookup"><span data-stu-id="79549-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="79549-117">關聯性可能會受到所參考的條件約束，當中會描述關聯性中的結尾是主要角色，也是相依角色。</span><span class="sxs-lookup"><span data-stu-id="79549-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="79549-118">導覽屬性可用來瀏覽兩個實體類型之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="79549-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="79549-119">每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="79549-120">導覽屬性可讓您巡覽及管理兩個方向，傳回參考物件的關聯性 (如果多重性是一個或零或一個) 或集合 （如果多重性是許多項目）。</span><span class="sxs-lookup"><span data-stu-id="79549-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="79549-121">您也可以選擇在此情況下您定義的導覽屬性上只有其中一個參與的關聯性的類型，而不是在同時擁有單向導覽。</span><span class="sxs-lookup"><span data-stu-id="79549-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="79549-122">建議您包含在模型中對應至資料庫中的外部索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="79549-123">包括了外部索引鍵之後，您便可以透過修改相依物件上的外部索引鍵值來建立或變更關聯性。</span><span class="sxs-lookup"><span data-stu-id="79549-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="79549-124">這個類型的關聯稱為外部索引鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="79549-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="79549-125">使用已中斷連線實體時，使用外部索引鍵是更重要的。</span><span class="sxs-lookup"><span data-stu-id="79549-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="79549-126">請注意，當使用 1-1] 或 [1-0...1 的關聯性沒有個別的外部索引鍵資料行、 主索引鍵屬性做為外部索引鍵，並一律包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="79549-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="79549-127">當外部索引鍵資料行不包含在模型中時，關聯資訊被管理做為獨立的物件。</span><span class="sxs-lookup"><span data-stu-id="79549-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="79549-128">關聯性會追蹤透過物件參考，而不是外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="79549-129">這種類型的關聯稱為*獨立關聯*。</span><span class="sxs-lookup"><span data-stu-id="79549-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="79549-130">若要修改的最常見方式*獨立關聯*是修改針對參與關聯的每個實體所產生的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="79549-131">您可以選擇在模型中使用一種或兩種關聯類型。</span><span class="sxs-lookup"><span data-stu-id="79549-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="79549-132">不過，如果您有連接只包含外部索引鍵的聯結資料表的純粹多對多關聯性時，EF 會使用獨立關聯來管理這類多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="79549-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="79549-133">下圖顯示使用 Entity Framework 設計工具建立概念模型。</span><span class="sxs-lookup"><span data-stu-id="79549-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="79549-134">模型包含兩個參與一對多關聯性的實體。</span><span class="sxs-lookup"><span data-stu-id="79549-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="79549-135">這兩個實體都有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-135">Both entities have navigation properties.</span></span> <span data-ttu-id="79549-136">**Course** depend 實體，且具有**DepartmentID**定義的外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-136">**Course** is the depend entity and has the **DepartmentID** foreign key property defined.</span></span>

![RelationshipEFDesigner](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="79549-138">下列程式碼片段會顯示使用 Code First 所建立的相同模型。</span><span class="sxs-lookup"><span data-stu-id="79549-138">The following code snippet shows the same model that was created with Code First.</span></span>

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
     this.Course = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="79549-139">設定或對應關聯性</span><span class="sxs-lookup"><span data-stu-id="79549-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="79549-140">此頁面的其餘部分將說明如何存取及管理使用關聯性的資料。</span><span class="sxs-lookup"><span data-stu-id="79549-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="79549-141">如需設定您的模型中關聯性的資訊，請參閱下列頁面。</span><span class="sxs-lookup"><span data-stu-id="79549-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="79549-142">若要設定關聯性中第一個程式碼，請參閱[資料註解](~/ef6/modeling/code-first/data-annotations.md)並[Fluent API-關聯性](~/ef6/modeling/code-first/fluent/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="79549-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="79549-143">若要設定關聯性使用 Entity Framework 設計工具，請參閱[EF 設計工具關聯性](~/ef6/modeling/designer/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="79549-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="79549-144">建立和修改關聯性</span><span class="sxs-lookup"><span data-stu-id="79549-144">Creating and modifying relationships</span></span>

<span data-ttu-id="79549-145">在 *外部索引鍵關聯*，當您變更關聯性，而狀態會變更為 EntityState.Modified EntityState.Unchanged 的相依物件的狀態。</span><span class="sxs-lookup"><span data-stu-id="79549-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an EntityState.Unchanged state changes to EntityState.Modified.</span></span> <span data-ttu-id="79549-146">在獨立關聯中，變更關聯性不會更新相依物件的狀態。</span><span class="sxs-lookup"><span data-stu-id="79549-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="79549-147">下列範例示範如何使用的外部索引鍵屬性和導覽屬性，將相關的物件產生關聯。</span><span class="sxs-lookup"><span data-stu-id="79549-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="79549-148">與外部索引鍵的關聯，您可以使用任一種方法來變更、 建立或修改關聯性。</span><span class="sxs-lookup"><span data-stu-id="79549-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="79549-149">使用獨立關聯，則無法使用外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-149">With independent associations, you cannot use the foreign key property.</span></span>

-   <span data-ttu-id="79549-150">將新值指派到外部索引鍵屬性，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="79549-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
    ``` csharp
    course.DepartmentID = newCourse.DepartmentID;
    ```

-   <span data-ttu-id="79549-151">下列程式碼藉由設定外部索引鍵移除關聯性**null**。</span><span class="sxs-lookup"><span data-stu-id="79549-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="79549-152">請注意，外部索引鍵屬性必須是可為 null。</span><span class="sxs-lookup"><span data-stu-id="79549-152">Note, that the foreign key property must be nullable.</span></span>  
    ``` csharp
    course.DepartmentID = null;
    ```  
    >[!NOTE]
    > <span data-ttu-id="79549-153">如果參考是已加入狀態 （在此範例中，課程物件），參考導覽屬性將不會同步具有新物件的索引鍵值直到呼叫 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="79549-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="79549-154">不會同步化是因為物件內容未包含新加入之物件的永久索引鍵，除非先儲存這些索引鍵。</span><span class="sxs-lookup"><span data-stu-id="79549-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="79549-155">如果您必須有完全同步處理為您設定的關聯性的新物件，使用其中一個下列 methods.\*</span><span class="sxs-lookup"><span data-stu-id="79549-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

-   <span data-ttu-id="79549-156">將新物件指派給導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="79549-157">下列程式碼會建立 course 之間的關聯性和`department`。</span><span class="sxs-lookup"><span data-stu-id="79549-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="79549-158">這些物件會附加至內容，如果`course`也會加入至`department.Courses`集合物件和對應外部索引鍵屬性上`course`物件設定為部門的索引鍵的屬性值。</span><span class="sxs-lookup"><span data-stu-id="79549-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
    ``` csharp
    course.Department = department;
    ```

 -   <span data-ttu-id="79549-159">若要刪除關聯性，設定的導覽屬性為`null`。</span><span class="sxs-lookup"><span data-stu-id="79549-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="79549-160">如果您正在使用 Entity Framework 為基礎的.NET 4.0，相關的端必須先載入才能將它設定為 null。</span><span class="sxs-lookup"><span data-stu-id="79549-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="79549-161">例如: </span><span class="sxs-lookup"><span data-stu-id="79549-161">For example:</span></span>  
    ``` chsarp
    context.Entry(course).Reference(c => c.Department).Load();  
    course.Department = null;
    ```  
    <span data-ttu-id="79549-162">從 Entity Framework 5.0，以.NET 4.5 為基礎，您可以設定關聯性設為 null 而不需要載入相關的端。</span><span class="sxs-lookup"><span data-stu-id="79549-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="79549-163">您也可以設定為 null，使用下列方法目前的值。</span><span class="sxs-lookup"><span data-stu-id="79549-163">You can also set the current value to null using the following method.</span></span>  
    ``` csharp
    context.Entry(course).Reference(c => c.Department).CurrentValue = null;
    ```

-   <span data-ttu-id="79549-164">在實體集合中刪除或加入物件。</span><span class="sxs-lookup"><span data-stu-id="79549-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="79549-165">例如，您可以在其中新增類型的物件`Course`至`department.Courses`集合。</span><span class="sxs-lookup"><span data-stu-id="79549-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="79549-166">此作業會建立特定之間的關聯性**課程**與特定`department`。</span><span class="sxs-lookup"><span data-stu-id="79549-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="79549-167">如果這些物件會附加至內容、 部門參考和外部索引鍵屬性上**課程**物件將會設定為適當`department`。</span><span class="sxs-lookup"><span data-stu-id="79549-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
    ``` csharp
    department.Courses.Add(newCourse);
    ```

- <span data-ttu-id="79549-168">使用`ChangeRelationshipState`方法，以變更兩個實體物件之間指定關聯性的狀態。</span><span class="sxs-lookup"><span data-stu-id="79549-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="79549-169">使用多層式架構應用程式時，將最常使用這個方法和*獨立關聯*（它不能與外部索引鍵關聯）。</span><span class="sxs-lookup"><span data-stu-id="79549-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="79549-170">此外，才能使用這個方法就必須卸除至`ObjectContext`，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="79549-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="79549-171">在下列範例中，沒有講師和課程之間的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="79549-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="79549-172">呼叫`ChangeRelationshipState`方法並傳遞`EntityState.Added`參數，可讓`SchoolContext`知道兩個物件之間的關聯性，已加入：</span><span class="sxs-lookup"><span data-stu-id="79549-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>

``` csharp

       ((IObjectContextAdapter)context).ObjectContext.
                 ObjectStateManager.
                  ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
```

    Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:

``` csharp
       ((IObjectContextAdapter)context).ObjectContext.
                  ObjectStateManager.
                  ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="79549-173">同步處理之間的外部索引鍵和導覽屬性的變更</span><span class="sxs-lookup"><span data-stu-id="79549-173">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="79549-174">當您變更使用其中一種以上所述的方法附加至內容之物件的關聯性時，Entity Framework 必須保持同步的外部索引鍵、 參考和集合。Entity Framework 會自動管理此同步處理 （也就是關聯性修正程式-最多），使用 proxy 的 POCO 實體。</span><span class="sxs-lookup"><span data-stu-id="79549-174">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="79549-175">如需詳細資訊，請參閱 <<c0> [ 使用 Proxy](~/ef6/fundamentals/proxies.md)。</span><span class="sxs-lookup"><span data-stu-id="79549-175">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="79549-176">如果您使用沒有 proxy 的 POCO 實體，您必須確定**DetectChanges**呼叫方法來同步處理內容中的相關的物件。</span><span class="sxs-lookup"><span data-stu-id="79549-176">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="79549-177">請注意，下列 Api 自動觸發**DetectChanges**呼叫。</span><span class="sxs-lookup"><span data-stu-id="79549-177">Note, that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="79549-178">執行 LINQ 查詢 `DbSet`</span><span class="sxs-lookup"><span data-stu-id="79549-178">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="79549-179">載入相關物件</span><span class="sxs-lookup"><span data-stu-id="79549-179">Loading related objects</span></span>

<span data-ttu-id="79549-180">您最常使用的 Entity Framework 中使用導覽屬性載入與所定義的關聯所傳回的實體的實體。</span><span class="sxs-lookup"><span data-stu-id="79549-180">In Entity Framework you use most commonly use the navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="79549-181">如需詳細資訊，請參閱 <<c0> [ 載入相關物件](~/ef6/querying/related-data.md)。</span><span class="sxs-lookup"><span data-stu-id="79549-181">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="79549-182">在外部索引鍵關聯中，當您載入相依物件的相關端時，相關物件將根據目前在記憶體中之相依外部索引鍵值來載入：</span><span class="sxs-lookup"><span data-stu-id="79549-182">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 1.
    Course course2 = context.Courses.First(c=>c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="79549-183">在獨立關聯中，會根據目前在資料庫中的外部索引鍵值來查詢相依物件的相關端。</span><span class="sxs-lookup"><span data-stu-id="79549-183">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="79549-184">不過，如果已修改的關聯性，並指向不同的主體物件載入物件內容中，Entity Framework 的相依物件上的 [參考] 屬性將會嘗試建立關聯性，如同它被定義在用戶端上。</span><span class="sxs-lookup"><span data-stu-id="79549-184">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="79549-185">管理並行</span><span class="sxs-lookup"><span data-stu-id="79549-185">Managing concurrency</span></span>

<span data-ttu-id="79549-186">在外部索引鍵和獨立關聯中，並行存取檢查會根據實體索引鍵和其他模型中定義的實體屬性而定。</span><span class="sxs-lookup"><span data-stu-id="79549-186">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="79549-187">當使用 EF 設計工具來建立模型時，設定`ConcurrencyMode`屬性設定為**修正**指定屬性，應檢查並行存取。</span><span class="sxs-lookup"><span data-stu-id="79549-187">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="79549-188">當使用 Code First 定義的模型，使用`ConcurrencyCheck`上想要檢查並行存取的屬性註釋。</span><span class="sxs-lookup"><span data-stu-id="79549-188">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="79549-189">使用 Code First 時您也可以使用`TimeStamp`註解來指定應該檢查並行存取的屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-189">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="79549-190">您可以在某一特定類別只能有一個時間戳記屬性。</span><span class="sxs-lookup"><span data-stu-id="79549-190">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="79549-191">程式碼首先會將此屬性對應至資料庫中的非可為 null 的欄位。</span><span class="sxs-lookup"><span data-stu-id="79549-191">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="79549-192">我們建議使用參與並行存取檢查和解析實體時，一律使用外部索引鍵的關聯。</span><span class="sxs-lookup"><span data-stu-id="79549-192">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="79549-193">如需詳細資訊，請參閱 <<c0> [ 處理並行衝突](~/ef6/saving/concurrency.md)。</span><span class="sxs-lookup"><span data-stu-id="79549-193">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="79549-194">使用索引鍵重疊</span><span class="sxs-lookup"><span data-stu-id="79549-194">Working with overlapping Keys</span></span>

<span data-ttu-id="79549-195">重疊索引鍵是複合索引鍵，其索引鍵中的部分屬性也是實體中另一個索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="79549-195">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="79549-196">在獨立關聯中不能有重疊索引鍵。</span><span class="sxs-lookup"><span data-stu-id="79549-196">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="79549-197">若要變更包括重疊索引鍵的外部索引鍵關聯，我們建議您修改外部索引鍵值，而不是使用物件參考。</span><span class="sxs-lookup"><span data-stu-id="79549-197">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
