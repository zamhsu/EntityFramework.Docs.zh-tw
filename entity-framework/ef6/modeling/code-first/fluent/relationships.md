---
title: 流暢的 API-關聯性-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
ms.openlocfilehash: 05f282c02699f8bf3c71197ac5e01000f1855917
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419070"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="b982c-102">流暢的 API-關聯性</span><span class="sxs-lookup"><span data-stu-id="b982c-102">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="b982c-103">此頁面提供使用 Fluent API 在 Code First 模型中設定關聯性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="b982c-103">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="b982c-104">如需 EF 中關聯性和如何使用關聯性存取和運算元據的一般資訊，請參閱[& 導覽屬性的關聯](~/ef6/fundamentals/relationships.md)性。</span><span class="sxs-lookup"><span data-stu-id="b982c-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>  

<span data-ttu-id="b982c-105">使用 Code First 時，您可以藉由定義網域 CLR 類別來定義您的模型。</span><span class="sxs-lookup"><span data-stu-id="b982c-105">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="b982c-106">根據預設，Entity Framework 會使用 Code First 慣例，將您的類別對應至資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="b982c-106">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="b982c-107">如果您使用 Code First 命名慣例，在大部分的情況下，您可以依賴 Code First 根據您在類別上定義的外鍵和導覽屬性來設定資料表之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="b982c-107">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="b982c-108">如果您在定義類別時未遵循慣例，或想要變更慣例的使用方式，您可以使用 Fluent API 或資料批註來設定類別，讓 Code First 可以對應資料表之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="b982c-108">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="b982c-109">簡介</span><span class="sxs-lookup"><span data-stu-id="b982c-109">Introduction</span></span>  

<span data-ttu-id="b982c-110">當設定與 Fluent API 的關聯性時，您會從 EntityTypeConfiguration 實例開始，然後使用 HasRequired、HasOptional 或 HasMany 方法來指定這個實體所參與之關聯性的類型。</span><span class="sxs-lookup"><span data-stu-id="b982c-110">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="b982c-111">HasRequired 和 HasOptional 方法會接受代表參考導覽屬性的 lambda 運算式。</span><span class="sxs-lookup"><span data-stu-id="b982c-111">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="b982c-112">HasMany 方法會接受代表集合導覽屬性的 lambda 運算式。</span><span class="sxs-lookup"><span data-stu-id="b982c-112">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="b982c-113">接著，您可以使用 WithRequired、WithOptional 和 WithMany 方法來設定反向導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b982c-113">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="b982c-114">這些方法具有不接受引數的多載，而且可以用來指定具有單向導覽的基數。</span><span class="sxs-lookup"><span data-stu-id="b982c-114">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="b982c-115">接著，您可以使用 HasForeignKey 方法來設定外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="b982c-115">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="b982c-116">這個方法會採用 lambda 運算式，表示要當做外鍵使用的屬性。</span><span class="sxs-lookup"><span data-stu-id="b982c-116">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="b982c-117">設定必要的選擇性關聯性（一對一到零或一）</span><span class="sxs-lookup"><span data-stu-id="b982c-117">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="b982c-118">下列範例會設定一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="b982c-118">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b982c-119">OfficeAssignment 具有屬於主鍵和外鍵的 InstructorID 屬性，因為屬性的名稱不會遵循 HasKey 方法用來設定主鍵的慣例。</span><span class="sxs-lookup"><span data-stu-id="b982c-119">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="b982c-120">設定兩個端點都是必要的關聯性（一對一）</span><span class="sxs-lookup"><span data-stu-id="b982c-120">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="b982c-121">在大部分的情況下，Entity Framework 可以推斷哪個類型是相依的，而這是關聯性中的主體。</span><span class="sxs-lookup"><span data-stu-id="b982c-121">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="b982c-122">不過，當關聯性的兩端都是必要的，或兩端都是選擇性時 Entity Framework 無法識別相依和主體。</span><span class="sxs-lookup"><span data-stu-id="b982c-122">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="b982c-123">當關聯性的兩端都是必要的時，請在 HasRequired 方法後面使用 WithRequiredPrincipal 或 WithRequiredDependent。</span><span class="sxs-lookup"><span data-stu-id="b982c-123">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="b982c-124">當關聯性的兩端都是選擇性時，請在 HasOptional 方法後面使用 WithOptionalPrincipal 或 WithOptionalDependent。</span><span class="sxs-lookup"><span data-stu-id="b982c-124">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="b982c-125">設定多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="b982c-125">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="b982c-126">下列程式碼會設定課程和講師類型之間的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="b982c-126">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="b982c-127">在下列範例中，預設 Code First 慣例是用來建立聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="b982c-127">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="b982c-128">因此，CourseInstructor 資料表是使用 Course_CourseID 和 Instructor_InstructorID 資料行建立的。</span><span class="sxs-lookup"><span data-stu-id="b982c-128">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="b982c-129">如果您想要指定聯結資料表名稱和資料表中的資料行名稱，您必須使用 Map 方法來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="b982c-129">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="b982c-130">下列程式碼會產生具有 CourseID 和 InstructorID 資料行的 CourseInstructor 資料表。</span><span class="sxs-lookup"><span data-stu-id="b982c-130">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="b982c-131">使用一個導覽屬性設定關聯性</span><span class="sxs-lookup"><span data-stu-id="b982c-131">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="b982c-132">單向（也稱為單向）關聯性是只在其中一個關聯性端（而不是兩者）上定義導覽屬性時。</span><span class="sxs-lookup"><span data-stu-id="b982c-132">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="b982c-133">依照慣例，Code First 一律會將單向關聯性解讀為「一對多」。</span><span class="sxs-lookup"><span data-stu-id="b982c-133">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="b982c-134">例如，如果您想要講師和 OfficeAssignment 之間的一對一關聯性，其中只有講師類型的導覽屬性，您必須使用 Fluent API 來設定此關聯性。</span><span class="sxs-lookup"><span data-stu-id="b982c-134">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="b982c-135">啟用串聯刪除</span><span class="sxs-lookup"><span data-stu-id="b982c-135">Enabling Cascade Delete</span></span>  

<span data-ttu-id="b982c-136">您可以使用 WillCascadeOnDelete 方法，針對關聯性設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b982c-136">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="b982c-137">如果相依實體的外鍵不可為 null，則 Code First 在關聯性上設定 cascade delete。</span><span class="sxs-lookup"><span data-stu-id="b982c-137">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="b982c-138">如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當主體被刪除時，外鍵將會設定為 null。</span><span class="sxs-lookup"><span data-stu-id="b982c-138">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="b982c-139">您可以使用下列方法移除這些串聯刪除慣例：</span><span class="sxs-lookup"><span data-stu-id="b982c-139">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="b982c-140">modelBuilder\>（）移除\<</span><span class="sxs-lookup"><span data-stu-id="b982c-140">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="b982c-141">modelBuilder\>（）移除\<</span><span class="sxs-lookup"><span data-stu-id="b982c-141">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="b982c-142">下列程式碼會將關聯性設定為必要，然後停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b982c-142">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="b982c-143">設定複合外鍵</span><span class="sxs-lookup"><span data-stu-id="b982c-143">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="b982c-144">如果部門類型上的主鍵是由 DepartmentID 和 Name 屬性所組成，您可以設定部門的主要金鑰和課程類型上的外鍵，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b982c-144">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="b982c-145">重新命名模型中未定義的外鍵</span><span class="sxs-lookup"><span data-stu-id="b982c-145">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="b982c-146">如果您選擇不要在 CLR 類型上定義外鍵，但想要指定它在資料庫中應有的名稱，請執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="b982c-146">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="b982c-147">正在設定未遵循 Code First 慣例的外鍵名稱</span><span class="sxs-lookup"><span data-stu-id="b982c-147">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="b982c-148">如果課程類別上的外鍵屬性呼叫 SomeDepartmentID 而不是 DepartmentID，您就必須執行下列動作，指定要讓 SomeDepartmentID 成為外鍵：</span><span class="sxs-lookup"><span data-stu-id="b982c-148">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="b982c-149">範例中使用的模型</span><span class="sxs-lookup"><span data-stu-id="b982c-149">Model Used in Samples</span></span>  

<span data-ttu-id="b982c-150">下列 Code First 模型用於此頁面上的範例。</span><span class="sxs-lookup"><span data-stu-id="b982c-150">The following Code First model is used for the samples on this page.</span></span>  

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
