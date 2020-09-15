---
title: 流暢的 API-關聯性-EF6
description: Entity Framework 6 中的流暢 API 關聯性
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 73e910279540fc93845aeebdfd79af9b95c60fae
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073921"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="1de6b-103">流暢的 API-關聯性</span><span class="sxs-lookup"><span data-stu-id="1de6b-103">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="1de6b-104">此頁面提供有關使用流暢 API 在 Code First 模型中設定關聯性的資訊。</span><span class="sxs-lookup"><span data-stu-id="1de6b-104">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="1de6b-105">如需 EF 中關聯性的一般資訊，以及如何使用關聯性存取和運算元據，請參閱 [關聯性 & 導覽屬性](xref:ef6/fundamentals/relationships)。</span><span class="sxs-lookup"><span data-stu-id="1de6b-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>  

<span data-ttu-id="1de6b-106">使用 Code First 時，您可以藉由定義網域 CLR 類別來定義您的模型。</span><span class="sxs-lookup"><span data-stu-id="1de6b-106">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="1de6b-107">根據預設，Entity Framework 會使用 Code First 慣例，將類別對應至資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="1de6b-107">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="1de6b-108">如果您使用 Code First 命名慣例，在大部分情況下，您可以依賴 Code First，根據您在類別上定義的外鍵和導覽屬性，設定資料表之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-108">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="1de6b-109">如果您在定義類別時未遵循慣例，或想要變更慣例的運作方式，您可以使用流暢的 API 或資料注釋來設定您的類別，讓 Code First 可以對應資料表之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-109">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="1de6b-110">簡介</span><span class="sxs-lookup"><span data-stu-id="1de6b-110">Introduction</span></span>  

<span data-ttu-id="1de6b-111">使用流暢的 API 來設定關聯性時，您會從 EntityTypeConfiguration 實例開始，然後使用 HasRequired、HasOptional 或 HasMany 方法來指定此實體所參與之關聯性的類型。</span><span class="sxs-lookup"><span data-stu-id="1de6b-111">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="1de6b-112">HasRequired 和 HasOptional 方法會採用表示參考導覽屬性的 lambda 運算式。</span><span class="sxs-lookup"><span data-stu-id="1de6b-112">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="1de6b-113">HasMany 方法會採用表示集合導覽屬性的 lambda 運算式。</span><span class="sxs-lookup"><span data-stu-id="1de6b-113">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="1de6b-114">然後，您可以使用 WithRequired、WithOptional 和 WithMany 方法來設定反向導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-114">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="1de6b-115">這些方法具有未採用引數的多載，而且可以用來指定具有單向導覽的基數。</span><span class="sxs-lookup"><span data-stu-id="1de6b-115">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="1de6b-116">然後，您可以使用 HasForeignKey 方法來設定外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-116">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="1de6b-117">這個方法會採用 lambda 運算式，表示要當做外鍵使用的屬性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-117">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="1de6b-118">設定必要的選擇性關聯性 (一對一-零或一) </span><span class="sxs-lookup"><span data-stu-id="1de6b-118">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="1de6b-119">下列範例會設定一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-119">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1de6b-120">OfficeAssignment 具有 InstructorID 屬性，此屬性是主鍵和外鍵，因為屬性的名稱未遵循慣例，所以會使用 HasKey 方法來設定主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="1de6b-120">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="1de6b-121">設定兩個端點之間的關聯性 (一對一) </span><span class="sxs-lookup"><span data-stu-id="1de6b-121">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="1de6b-122">在大多數情況下，Entity Framework 可以推斷哪一個是相依的型別，而這是關聯性中的主體。</span><span class="sxs-lookup"><span data-stu-id="1de6b-122">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="1de6b-123">不過，當關聯性的兩端都是必要的，或兩端都是選擇性的時 Entity Framework 無法識別相依和主體。</span><span class="sxs-lookup"><span data-stu-id="1de6b-123">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="1de6b-124">當關聯性的兩端都需要時，請在 HasRequired 方法之後使用 WithRequiredPrincipal 或 WithRequiredDependent。</span><span class="sxs-lookup"><span data-stu-id="1de6b-124">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="1de6b-125">當關聯性的兩端都是選擇性時，請在 HasOptional 方法之後使用 WithOptionalPrincipal 或 WithOptionalDependent。</span><span class="sxs-lookup"><span data-stu-id="1de6b-125">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="1de6b-126">設定多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="1de6b-126">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="1de6b-127">下列程式碼會設定課程和講師類型之間的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-127">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="1de6b-128">在下列範例中，預設的 Code First 慣例是用來建立聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="1de6b-128">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="1de6b-129">因此，會使用 Course_CourseID 和 Instructor_InstructorID 資料行來建立 CourseInstructor 資料表。</span><span class="sxs-lookup"><span data-stu-id="1de6b-129">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="1de6b-130">如果您想要在資料表中指定聯結資料表名稱和資料行的名稱，您需要使用 Map 方法進行其他設定。</span><span class="sxs-lookup"><span data-stu-id="1de6b-130">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="1de6b-131">下列程式碼會產生具有 CourseID 和 InstructorID 資料行的 CourseInstructor 資料表。</span><span class="sxs-lookup"><span data-stu-id="1de6b-131">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
    .Map(m =>
    {
        m.ToTable("CourseInstructor");
        m.MapLeftKey("CourseID");
        m.MapRightKey("InstructorID");
    });
```  

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="1de6b-132">使用一個導覽屬性設定關聯性</span><span class="sxs-lookup"><span data-stu-id="1de6b-132">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="1de6b-133">單向 (也稱為單向) 關聯性，這是指只在其中一個關聯性端上定義導覽屬性，而不是同時在兩者上定義。</span><span class="sxs-lookup"><span data-stu-id="1de6b-133">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="1de6b-134">依照慣例，Code First 一律會將單向關聯性解讀為一對多。</span><span class="sxs-lookup"><span data-stu-id="1de6b-134">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="1de6b-135">例如，如果您想要講師與 OfficeAssignment 之間的一對一關聯性，其中只有講師型別的導覽屬性，您必須使用流暢的 API 來設定此關聯性。</span><span class="sxs-lookup"><span data-stu-id="1de6b-135">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="1de6b-136">啟用 Cascade Delete</span><span class="sxs-lookup"><span data-stu-id="1de6b-136">Enabling Cascade Delete</span></span>  

<span data-ttu-id="1de6b-137">您可以使用 WillCascadeOnDelete 方法來設定關聯性的串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="1de6b-137">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="1de6b-138">如果相依實體上的外鍵不可為 null，則 Code First 會在關聯性上設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="1de6b-138">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="1de6b-139">如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當刪除主體時，外鍵將會設定為 null。</span><span class="sxs-lookup"><span data-stu-id="1de6b-139">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="1de6b-140">您可以使用下列方法移除這些串聯刪除慣例：</span><span class="sxs-lookup"><span data-stu-id="1de6b-140">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="1de6b-141">modelBuilder 移除 \<OneToManyCascadeDeleteConvention\> ( # A1</span><span class="sxs-lookup"><span data-stu-id="1de6b-141">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="1de6b-142">modelBuilder 移除 \<ManyToManyCascadeDeleteConvention\> ( # A1</span><span class="sxs-lookup"><span data-stu-id="1de6b-142">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="1de6b-143">下列程式碼會設定必要的關聯性，然後停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="1de6b-143">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="1de6b-144">設定複合外鍵</span><span class="sxs-lookup"><span data-stu-id="1de6b-144">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="1de6b-145">如果部門類型上的主鍵是由 DepartmentID 和 Name 屬性組成，您可以設定部門的主鍵和課程類型上的外鍵，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1de6b-145">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

``` csharp
// Composite primary key
modelBuilder.Entity<Department>()
.HasKey(d => new { d.DepartmentID, d.Name });

// Composite foreign key
modelBuilder.Entity<Course>()  
    .HasRequired(c => c.Department)  
    .WithMany(d => d.Courses)
    .HasForeignKey(d => new { d.DepartmentID, d.DepartmentName });
```  

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="1de6b-146">重新命名模型中未定義的外鍵</span><span class="sxs-lookup"><span data-stu-id="1de6b-146">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="1de6b-147">如果您選擇不要在 CLR 類型上定義外鍵，但想要指定它在資料庫中應有的名稱，請執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="1de6b-147">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="1de6b-148">設定未遵循 Code First 慣例的外鍵名稱</span><span class="sxs-lookup"><span data-stu-id="1de6b-148">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="1de6b-149">如果課程類別的外鍵屬性稱為 SomeDepartmentID 而非 DepartmentID，您需要執行下列動作，以指定您想要 SomeDepartmentID 成為外鍵：</span><span class="sxs-lookup"><span data-stu-id="1de6b-149">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="1de6b-150">範例中使用的模型</span><span class="sxs-lookup"><span data-stu-id="1de6b-150">Model Used in Samples</span></span>  

<span data-ttu-id="1de6b-151">下列 Code First 模型用於此頁面上的範例。</span><span class="sxs-lookup"><span data-stu-id="1de6b-151">The following Code First model is used for the samples on this page.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
