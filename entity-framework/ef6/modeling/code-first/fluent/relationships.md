---
title: Fluent API-關聯性-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
ms.openlocfilehash: e13a1370f46362e0f2ca3743ec5b063ce6f87cbe
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994758"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="c99f9-102">Fluent API-關聯性</span><span class="sxs-lookup"><span data-stu-id="c99f9-102">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="c99f9-103">此頁面提供有關設定 Code First 模型使用 fluent API 中的關聯性資訊。</span><span class="sxs-lookup"><span data-stu-id="c99f9-103">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="c99f9-104">如需 EF 以及如何存取和操作資料使用關聯性的關聯性的一般資訊，請參閱[關聯性和導覽屬性](~/ef6/fundamentals/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="c99f9-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>  

<span data-ttu-id="c99f9-105">當使用 Code First，您會定義您的模型，藉由定義您的網域 CLR 類別。</span><span class="sxs-lookup"><span data-stu-id="c99f9-105">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="c99f9-106">根據預設，Entity Framework 會使用 Code First 慣例，來將您的類別對應至資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="c99f9-106">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="c99f9-107">如果您使用的第一個程式碼的命名慣例，在大部分情況下您可以依賴 Code First 來設定您的外部索引鍵和您在類別上定義導覽屬性為基礎的資料表之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-107">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="c99f9-108">如果您沒有遵循慣例，定義您的類別時，或慣例如果您想要變更的方式運作，您可以使用資料註解的 fluent API 來設定您的類別，讓程式碼第一次可以對應您的資料表之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-108">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="c99f9-109">簡介</span><span class="sxs-lookup"><span data-stu-id="c99f9-109">Introduction</span></span>  

<span data-ttu-id="c99f9-110">設定時的關聯性使用 fluent API，開始與 EntityTypeConfiguration 執行個體，然後使用 HasRequired、 HasOptional 或 HasMany 方法來指定此實體所參與的關聯性的類型。</span><span class="sxs-lookup"><span data-stu-id="c99f9-110">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="c99f9-111">HasRequired 和 HasOptional 方法會採用表示參考導覽屬性的 lambda 運算式。</span><span class="sxs-lookup"><span data-stu-id="c99f9-111">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="c99f9-112">HasMany 方法會採用 lambda 運算式，表示集合導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-112">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="c99f9-113">您接著可以設定反向的導覽屬性使用 WithRequired、 WithOptional 和 WithMany 方法。</span><span class="sxs-lookup"><span data-stu-id="c99f9-113">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="c99f9-114">這些方法具有多載，不接受引數，而且可用來指定基數與單向導覽。</span><span class="sxs-lookup"><span data-stu-id="c99f9-114">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="c99f9-115">您接著可以使用 HasForeignKey 方法來設定外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-115">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="c99f9-116">這個方法會採用 lambda 運算式，表示要做為外部索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-116">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="c99f9-117">設定所需-至-選用的關聯性 （--0-或-一對一）</span><span class="sxs-lookup"><span data-stu-id="c99f9-117">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="c99f9-118">下列範例會設定--0-或-一對一關聯性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-118">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="c99f9-119">OfficeAssignment 具有屬於主索引鍵和外部索引鍵，InstructorID 屬性，因為屬性的名稱並未遵循 HasKey 方法用來設定主索引鍵的慣例。</span><span class="sxs-lookup"><span data-stu-id="c99f9-119">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="c99f9-120">設定關聯性兩端所需 （一對一）</span><span class="sxs-lookup"><span data-stu-id="c99f9-120">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="c99f9-121">在大部分情況下 Entity Framework 可以推斷的型別是依存和也就是關聯性中的主體。</span><span class="sxs-lookup"><span data-stu-id="c99f9-121">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="c99f9-122">不過，當兩個關聯性所需或兩邊都是選擇性的 Entity Framework 無法識別相依和主體。</span><span class="sxs-lookup"><span data-stu-id="c99f9-122">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="c99f9-123">當關聯性的兩端都是必要項目時，使用 WithRequiredPrincipal 或 WithRequiredDependent HasRequired 方法之後。</span><span class="sxs-lookup"><span data-stu-id="c99f9-123">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="c99f9-124">當關聯性的兩端都是選擇性項目時，使用 WithOptionalPrincipal 或 WithOptionalDependent HasOptional 方法之後。</span><span class="sxs-lookup"><span data-stu-id="c99f9-124">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="c99f9-125">設定多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="c99f9-125">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="c99f9-126">下列程式碼會設定 Course 與 Instructor 型別之間的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-126">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="c99f9-127">在下列範例中，預設 Code First 慣例來建立聯結的資料表。</span><span class="sxs-lookup"><span data-stu-id="c99f9-127">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="c99f9-128">如此一來 CourseInstructor 資料表會透過 Course_CourseID 和 Instructor_InstructorID 資料行。</span><span class="sxs-lookup"><span data-stu-id="c99f9-128">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="c99f9-129">如果您想要在資料表中指定的聯結資料表名稱和資料行的名稱必須使用 Map 方法執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="c99f9-129">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="c99f9-130">下列程式碼會產生的 CourseInstructor 資料表 CourseID 和 InstructorID 的資料行。</span><span class="sxs-lookup"><span data-stu-id="c99f9-130">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="c99f9-131">設定具有一個導覽屬性的關聯性</span><span class="sxs-lookup"><span data-stu-id="c99f9-131">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="c99f9-132">單向 （也稱為單向） 關聯性是只有其中一個關聯性端，而不是在同時定義導覽屬性時。</span><span class="sxs-lookup"><span data-stu-id="c99f9-132">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="c99f9-133">依照慣例，Code First 一律會解譯為一對多單向關聯性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-133">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="c99f9-134">例如，如果您想要的一對一關聯性介於 Instructor 與 OfficeAssignment，其中只有 Instructor 型別有一個導覽屬性，您需要使用 fluent API 來設定此關聯性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-134">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="c99f9-135">啟用串聯刪除</span><span class="sxs-lookup"><span data-stu-id="c99f9-135">Enabling Cascade Delete</span></span>  

<span data-ttu-id="c99f9-136">您可以使用 WillCascadeOnDelete 方法，將關聯性設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="c99f9-136">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="c99f9-137">如果相依的實體上的外部索引鍵不是可為 null，然後第一個程式碼設定串聯刪除關聯性。</span><span class="sxs-lookup"><span data-stu-id="c99f9-137">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="c99f9-138">如果相依的實體上的外部索引鍵可為 null，第一個程式碼不會設定串聯刪除關聯性，而且當主體已刪除的外部索引鍵將會設定為 null。</span><span class="sxs-lookup"><span data-stu-id="c99f9-138">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="c99f9-139">您可以使用，以移除這些 cascade delete 慣例：</span><span class="sxs-lookup"><span data-stu-id="c99f9-139">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="c99f9-140">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>（)</span><span class="sxs-lookup"><span data-stu-id="c99f9-140">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="c99f9-141">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>（)</span><span class="sxs-lookup"><span data-stu-id="c99f9-141">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="c99f9-142">下列程式碼會設定為 required 關聯性，並再停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="c99f9-142">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="c99f9-143">設定複合外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="c99f9-143">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="c99f9-144">如果部門型別上的主索引鍵是由 DepartmentID 和 Name 屬性所組成，您會設定主索引鍵部門 」 和 「 外部索引鍵的課程類型，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c99f9-144">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="c99f9-145">重新命名模型中未定義的外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="c99f9-145">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="c99f9-146">如果您選擇不在 CLR 型別上定義的外部索引鍵，但想要在資料庫中指定它應該有何種名稱，執行下列作業：</span><span class="sxs-lookup"><span data-stu-id="c99f9-146">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="c99f9-147">設定外部索引鍵名稱未遵循的程式碼的第一個慣例</span><span class="sxs-lookup"><span data-stu-id="c99f9-147">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="c99f9-148">如果在課程類別上的外部索引鍵屬性呼叫而不是 DepartmentID SomeDepartmentID，您需要執行下列工作來指定您想要將外部索引鍵的 SomeDepartmentID:</span><span class="sxs-lookup"><span data-stu-id="c99f9-148">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="c99f9-149">範例中所使用的模型</span><span class="sxs-lookup"><span data-stu-id="c99f9-149">Model Used in Samples</span></span>  

<span data-ttu-id="c99f9-150">下列的 Code First 模型用於此頁面上的範例。</span><span class="sxs-lookup"><span data-stu-id="c99f9-150">The following Code First model is used for the samples on this page.</span></span>  

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
