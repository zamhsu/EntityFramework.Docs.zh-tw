---
title: 關聯性、導覽屬性和外鍵-EF6
description: Entity Framework 6 中的關聯性、導覽屬性和外鍵
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 63349d9a81065ea4e15a5f97ef5298cb3dc67339
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618399"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="babbe-103">關聯性、導覽屬性和外鍵</span><span class="sxs-lookup"><span data-stu-id="babbe-103">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="babbe-104">本文概述 Entity Framework 如何管理實體之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="babbe-104">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="babbe-105">它也會提供一些有關如何對應和操作關聯性的指引。</span><span class="sxs-lookup"><span data-stu-id="babbe-105">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="babbe-106">EF 中的關聯性</span><span class="sxs-lookup"><span data-stu-id="babbe-106">Relationships in EF</span></span>

<span data-ttu-id="babbe-107">在關係資料庫中，關聯性 (也稱為資料表之間的關聯) 是透過外鍵來定義。</span><span class="sxs-lookup"><span data-stu-id="babbe-107">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="babbe-108">外部索引鍵 (FK) 是可用來建立與強制兩資料表的資料之間連結的一個資料行或資料行組合。</span><span class="sxs-lookup"><span data-stu-id="babbe-108">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="babbe-109">一般來說，有三種類型的關聯性：一對一、一對多和多對多。</span><span class="sxs-lookup"><span data-stu-id="babbe-109">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="babbe-110">在一對多關聯性中，外鍵是定義在代表關聯性之多端的資料表上。</span><span class="sxs-lookup"><span data-stu-id="babbe-110">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="babbe-111">多對多關聯性牽涉到定義第三個數據表 (稱為連接或聯結資料表) ，其主鍵是由兩個相關資料表的外鍵組成。</span><span class="sxs-lookup"><span data-stu-id="babbe-111">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="babbe-112">在一對一關聯性中，主鍵會額外做為外鍵，而且兩個數據表都沒有個別的外鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="babbe-112">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="babbe-113">下圖顯示兩個參與一對多關聯性的資料表。</span><span class="sxs-lookup"><span data-stu-id="babbe-113">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="babbe-114">**課程**資料表是相依的資料表，因為它包含將它連結至**部門**資料表的**DepartmentID**資料行。</span><span class="sxs-lookup"><span data-stu-id="babbe-114">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![部門和課程資料表](~/ef6/media/database2.png)

<span data-ttu-id="babbe-116">在 Entity Framework 中，實體可透過關聯或關聯性與其他實體相關。</span><span class="sxs-lookup"><span data-stu-id="babbe-116">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="babbe-117">每個關聯性都包含兩個端，用來描述實體類型以及該關聯性的兩個實體 (一、零或一或多個) 之類型的多重性。</span><span class="sxs-lookup"><span data-stu-id="babbe-117">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="babbe-118">關聯性可能由參考條件約束控制，它會描述關聯性中的哪一端是主要角色，哪一端是相依角色。</span><span class="sxs-lookup"><span data-stu-id="babbe-118">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="babbe-119">導覽屬性提供在兩個實體類型之間導覽關聯的方式。</span><span class="sxs-lookup"><span data-stu-id="babbe-119">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="babbe-120">每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-120">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="babbe-121">導覽屬性可讓您流覽和管理雙向的關聯性，如果多重性是一個或零或一) ，則傳回參考物件 (; 如果多重性是許多) ，則會傳回集合 (。</span><span class="sxs-lookup"><span data-stu-id="babbe-121">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="babbe-122">您也可以選擇有單向導覽，在這種情況下，您只會在其中一個參與關聯性的類型上定義導覽屬性，而不是在兩者上定義。</span><span class="sxs-lookup"><span data-stu-id="babbe-122">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="babbe-123">建議您在模型中包含屬性，以對應至資料庫中的外鍵。</span><span class="sxs-lookup"><span data-stu-id="babbe-123">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="babbe-124">包括了外部索引鍵之後，您便可以透過修改相依物件上的外部索引鍵值來建立或變更關聯性。</span><span class="sxs-lookup"><span data-stu-id="babbe-124">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="babbe-125">這個類型的關聯稱為外部索引鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="babbe-125">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="babbe-126">使用外鍵在使用已中斷連線的實體時，更是不可或缺的。</span><span class="sxs-lookup"><span data-stu-id="babbe-126">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="babbe-127">請注意，當使用1對1或1對0時。1個關聯性，沒有個別的外鍵資料行，primary key 屬性會作為外鍵，而且一律包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="babbe-127">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="babbe-128">當模型中未包含外鍵資料行時，會將關聯資訊視為獨立物件來管理。</span><span class="sxs-lookup"><span data-stu-id="babbe-128">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="babbe-129">關聯性是透過物件參考而非外鍵屬性來追蹤。</span><span class="sxs-lookup"><span data-stu-id="babbe-129">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="babbe-130">這種類型的關聯稱為 *獨立關聯*。</span><span class="sxs-lookup"><span data-stu-id="babbe-130">This type of association is called an *independent association*.</span></span> <span data-ttu-id="babbe-131">修改 *獨立關聯* 最常見的方式，是修改為參與關聯之每個實體所產生的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-131">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="babbe-132">您可以選擇在模型中使用一種或兩種關聯類型。</span><span class="sxs-lookup"><span data-stu-id="babbe-132">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="babbe-133">但是，如果您有一個單純的多對多關聯性，且此關聯性是由只包含外鍵的聯結資料表所連接，則 EF 會使用獨立的關聯來管理這類多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="babbe-133">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="babbe-134">下圖顯示使用 Entity Framework Designer 建立的概念模型。</span><span class="sxs-lookup"><span data-stu-id="babbe-134">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="babbe-135">此模型包含兩個參與一對多關聯性的實體。</span><span class="sxs-lookup"><span data-stu-id="babbe-135">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="babbe-136">這兩個實體都有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-136">Both entities have navigation properties.</span></span> <span data-ttu-id="babbe-137">**課程** 是相依的實體，並且定義了 **DepartmentID** 外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-137">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![具有導覽屬性的部門和課程資料表](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="babbe-139">下列程式碼片段顯示使用 Code First 建立的相同模型。</span><span class="sxs-lookup"><span data-stu-id="babbe-139">The following code snippet shows the same model that was created with Code First.</span></span>

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

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="babbe-140">設定或對應關聯性</span><span class="sxs-lookup"><span data-stu-id="babbe-140">Configuring or mapping relationships</span></span>

<span data-ttu-id="babbe-141">本頁面的其餘部分將說明如何使用關聯性來存取和運算元據。</span><span class="sxs-lookup"><span data-stu-id="babbe-141">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="babbe-142">如需有關在模型中設定關聯性的詳細資訊，請參閱下列頁面。</span><span class="sxs-lookup"><span data-stu-id="babbe-142">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="babbe-143">若要在 Code First 中設定關聯性，請參閱 [資料批註](xref:ef6/modeling/code-first/data-annotations) 和 [流暢的 API-關聯](xref:ef6/modeling/code-first/fluent/relationships)性。</span><span class="sxs-lookup"><span data-stu-id="babbe-143">To configure relationships in Code First, see [Data Annotations](xref:ef6/modeling/code-first/data-annotations) and [Fluent API – Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>
-   <span data-ttu-id="babbe-144">若要使用 Entity Framework Designer 設定關聯性，請參閱 [與 EF Designer 的關聯](xref:ef6/modeling/designer/relationships)性。</span><span class="sxs-lookup"><span data-stu-id="babbe-144">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](xref:ef6/modeling/designer/relationships).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="babbe-145">建立和修改關聯性</span><span class="sxs-lookup"><span data-stu-id="babbe-145">Creating and modifying relationships</span></span>

<span data-ttu-id="babbe-146">在 *外鍵關聯*中，當您變更關聯性時，會將狀態變更為的相依物件的狀態 `EntityState.Unchanged` `EntityState.Modified` 。</span><span class="sxs-lookup"><span data-stu-id="babbe-146">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="babbe-147">在獨立的關聯性中，變更關聯性並不會更新相依物件的狀態。</span><span class="sxs-lookup"><span data-stu-id="babbe-147">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="babbe-148">下列範例示範如何使用外鍵屬性和導覽屬性來建立相關物件的關聯。</span><span class="sxs-lookup"><span data-stu-id="babbe-148">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="babbe-149">使用外鍵關聯時，您可以使用任一方法來變更、建立或修改關聯性。</span><span class="sxs-lookup"><span data-stu-id="babbe-149">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="babbe-150">使用獨立關聯，則無法使用外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-150">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="babbe-151">藉由將新值指派給外鍵屬性（property），如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="babbe-151">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="babbe-152">下列程式碼會將外鍵設定為 **null**，以移除關聯性。</span><span class="sxs-lookup"><span data-stu-id="babbe-152">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="babbe-153">請注意，外鍵屬性必須是可為 null。</span><span class="sxs-lookup"><span data-stu-id="babbe-153">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="babbe-154">如果參考處於 [已加入] 狀態 (在此範例中，課程物件) ，則在呼叫 SaveChanges 之前，參考導覽屬性不會與新物件的索引鍵值進行同步處理。</span><span class="sxs-lookup"><span data-stu-id="babbe-154">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="babbe-155">不會同步化是因為物件內容未包含新加入之物件的永久索引鍵，除非先儲存這些索引鍵。</span><span class="sxs-lookup"><span data-stu-id="babbe-155">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="babbe-156">如果您在設定關聯性之後必須立即完全同步處理新的物件，請使用下列其中一種方法。 \*</span><span class="sxs-lookup"><span data-stu-id="babbe-156">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="babbe-157">將新物件指派給導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-157">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="babbe-158">下列程式碼會建立課程與之間的關聯性 `department` 。</span><span class="sxs-lookup"><span data-stu-id="babbe-158">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="babbe-159">如果物件附加至內容，則 `course` 也會將加入至 `department.Courses` 集合，並將物件上對應的外鍵屬性 `course` 設定為部門的索引鍵屬性值。</span><span class="sxs-lookup"><span data-stu-id="babbe-159">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="babbe-160">若要刪除關聯性，請將導覽屬性設定為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="babbe-160">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="babbe-161">如果您正在使用以 .NET 4.0 為基礎的 Entity Framework，則必須先載入相關端，才能將其設定為 null。</span><span class="sxs-lookup"><span data-stu-id="babbe-161">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="babbe-162">例如：</span><span class="sxs-lookup"><span data-stu-id="babbe-162">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="babbe-163">從 Entity Framework 5.0 開始（以 .NET 4.5 為基礎），您可以將關聯性設定為 null，而不需要載入相關端。</span><span class="sxs-lookup"><span data-stu-id="babbe-163">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="babbe-164">您也可以使用下列方法，將目前的值設定為 null。</span><span class="sxs-lookup"><span data-stu-id="babbe-164">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="babbe-165">在實體集合中刪除或加入物件。</span><span class="sxs-lookup"><span data-stu-id="babbe-165">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="babbe-166">例如，您可以將類型的物件加入 `Course` 至 `department.Courses` 集合。</span><span class="sxs-lookup"><span data-stu-id="babbe-166">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="babbe-167">這項作業會建立特定課程和特定 **課程** 之間的關聯性 `department` 。</span><span class="sxs-lookup"><span data-stu-id="babbe-167">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="babbe-168">如果物件附加至內容，則會將 **課程** 物件的部門參考與外鍵屬性設定為適當的 `department` 。</span><span class="sxs-lookup"><span data-stu-id="babbe-168">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="babbe-169">藉由使用 `ChangeRelationshipState` 方法來變更兩個實體物件之間指定關聯性的狀態。</span><span class="sxs-lookup"><span data-stu-id="babbe-169">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="babbe-170">使用多層式應用程式和 *獨立關聯* (不能與外鍵關聯) 搭配使用時，最常用的方法就是使用這個方法。</span><span class="sxs-lookup"><span data-stu-id="babbe-170">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="babbe-171">此外，若要使用這個方法，您必須下拉至 `ObjectContext` ，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="babbe-171">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="babbe-172">在下列範例中，講師與課程之間有多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="babbe-172">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="babbe-173">呼叫 `ChangeRelationshipState` 方法並傳遞 `EntityState.Added` 參數，可讓您 `SchoolContext` 知道已在兩個物件之間加入關聯性：</span><span class="sxs-lookup"><span data-stu-id="babbe-173">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="babbe-174">請注意，如果您要更新 (而不只是新增) 關聯性，您必須在加入新的關聯性之後，刪除舊的關聯性：</span><span class="sxs-lookup"><span data-stu-id="babbe-174">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="babbe-175">同步處理外鍵和導覽屬性之間的變更</span><span class="sxs-lookup"><span data-stu-id="babbe-175">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="babbe-176">當您使用上述其中一個方法來變更附加至內容之物件的關聯性時，Entity Framework 必須保持外鍵、參考和集合的同步。Entity Framework 會自動管理此同步處理 (也稱為具有 proxy 的 POCO 實體的關聯性修正) 。</span><span class="sxs-lookup"><span data-stu-id="babbe-176">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="babbe-177">如需詳細資訊，請參閱 [使用](xref:ef6/fundamentals/proxies)proxy。</span><span class="sxs-lookup"><span data-stu-id="babbe-177">For more information, see [Working with Proxies](xref:ef6/fundamentals/proxies).</span></span>

<span data-ttu-id="babbe-178">如果您使用沒有 proxy 的 POCO 實體，您必須確定呼叫 **DetectChanges** 方法來同步處理內容中的相關物件。</span><span class="sxs-lookup"><span data-stu-id="babbe-178">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="babbe-179">請注意，下列 Api 會自動觸發 **DetectChanges** 呼叫。</span><span class="sxs-lookup"><span data-stu-id="babbe-179">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="babbe-180">對執行 LINQ 查詢 `DbSet`</span><span class="sxs-lookup"><span data-stu-id="babbe-180">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="babbe-181">載入相關物件</span><span class="sxs-lookup"><span data-stu-id="babbe-181">Loading related objects</span></span>

<span data-ttu-id="babbe-182">在 Entity Framework 您通常會使用導覽屬性，透過定義的關聯載入與傳回之實體相關的實體。</span><span class="sxs-lookup"><span data-stu-id="babbe-182">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="babbe-183">如需詳細資訊，請參閱 [載入相關物件](xref:ef6/querying/related-data)。</span><span class="sxs-lookup"><span data-stu-id="babbe-183">For more information, see [Loading Related Objects](xref:ef6/querying/related-data).</span></span>

> [!NOTE]
> <span data-ttu-id="babbe-184">在外部索引鍵關聯中，當您載入相依物件的相關端時，相關物件將根據目前在記憶體中之相依外部索引鍵值來載入：</span><span class="sxs-lookup"><span data-stu-id="babbe-184">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="babbe-185">在獨立關聯中，會根據目前在資料庫中的外部索引鍵值來查詢相依物件的相關端。</span><span class="sxs-lookup"><span data-stu-id="babbe-185">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="babbe-186">但是，如果已修改關聯性，而且相依物件上的參考屬性指向載入至物件內容中的不同主體物件，Entity Framework 將會嘗試建立在用戶端上定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="babbe-186">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="babbe-187">管理並行存取</span><span class="sxs-lookup"><span data-stu-id="babbe-187">Managing concurrency</span></span>

<span data-ttu-id="babbe-188">在外鍵和獨立關聯中，平行存取檢查是以模型中所定義的實體索引鍵和其他實體屬性為基礎。</span><span class="sxs-lookup"><span data-stu-id="babbe-188">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="babbe-189">使用 EF 設計工具建立模型時，請將屬性設定 `ConcurrencyMode` 為 [ **固定** ]，以指定要檢查是否有平行存取的屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-189">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="babbe-190">使用 Code First 定義模型時，請 `ConcurrencyCheck` 在您想要檢查是否有平行存取的屬性上使用批註。</span><span class="sxs-lookup"><span data-stu-id="babbe-190">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="babbe-191">使用 Code First 時，您也可以使用 `TimeStamp` 批註來指定應檢查是否有平行存取的屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-191">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="babbe-192">在指定的類別中，您只能有一個時間戳記屬性。</span><span class="sxs-lookup"><span data-stu-id="babbe-192">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="babbe-193">Code First 將此屬性對應至資料庫中的不可為 null 欄位。</span><span class="sxs-lookup"><span data-stu-id="babbe-193">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="babbe-194">當您使用參與平行存取檢查和解決的實體時，建議您一律使用外鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="babbe-194">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="babbe-195">如需詳細資訊，請參閱 [處理並行衝突](xref:ef6/saving/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="babbe-195">For more information, see [Handling Concurrency Conflicts](xref:ef6/saving/concurrency).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="babbe-196">使用重迭的索引鍵</span><span class="sxs-lookup"><span data-stu-id="babbe-196">Working with overlapping Keys</span></span>

<span data-ttu-id="babbe-197">重疊索引鍵是複合索引鍵，其索引鍵中的部分屬性也是實體中另一個索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="babbe-197">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="babbe-198">在獨立關聯中不能有重疊索引鍵。</span><span class="sxs-lookup"><span data-stu-id="babbe-198">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="babbe-199">若要變更包括重疊索引鍵的外部索引鍵關聯，我們建議您修改外部索引鍵值，而不是使用物件參考。</span><span class="sxs-lookup"><span data-stu-id="babbe-199">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
