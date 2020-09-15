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
# <a name="fluent-api---relationships"></a>流暢的 API-關聯性
> [!NOTE]
> 此頁面提供有關使用流暢 API 在 Code First 模型中設定關聯性的資訊。 如需 EF 中關聯性的一般資訊，以及如何使用關聯性存取和運算元據，請參閱 [關聯性 & 導覽屬性](xref:ef6/fundamentals/relationships)。  

使用 Code First 時，您可以藉由定義網域 CLR 類別來定義您的模型。 根據預設，Entity Framework 會使用 Code First 慣例，將類別對應至資料庫架構。 如果您使用 Code First 命名慣例，在大部分情況下，您可以依賴 Code First，根據您在類別上定義的外鍵和導覽屬性，設定資料表之間的關聯性。 如果您在定義類別時未遵循慣例，或想要變更慣例的運作方式，您可以使用流暢的 API 或資料注釋來設定您的類別，讓 Code First 可以對應資料表之間的關聯性。  

## <a name="introduction"></a>簡介  

使用流暢的 API 來設定關聯性時，您會從 EntityTypeConfiguration 實例開始，然後使用 HasRequired、HasOptional 或 HasMany 方法來指定此實體所參與之關聯性的類型。 HasRequired 和 HasOptional 方法會採用表示參考導覽屬性的 lambda 運算式。 HasMany 方法會採用表示集合導覽屬性的 lambda 運算式。 然後，您可以使用 WithRequired、WithOptional 和 WithMany 方法來設定反向導覽屬性。 這些方法具有未採用引數的多載，而且可以用來指定具有單向導覽的基數。  

然後，您可以使用 HasForeignKey 方法來設定外鍵屬性。 這個方法會採用 lambda 運算式，表示要當做外鍵使用的屬性。  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>設定必要的選擇性關聯性 (一對一-零或一)   

下列範例會設定一對零或一關聯性。 OfficeAssignment 具有 InstructorID 屬性，此屬性是主鍵和外鍵，因為屬性的名稱未遵循慣例，所以會使用 HasKey 方法來設定主要索引鍵。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>設定兩個端點之間的關聯性 (一對一)   

在大多數情況下，Entity Framework 可以推斷哪一個是相依的型別，而這是關聯性中的主體。 不過，當關聯性的兩端都是必要的，或兩端都是選擇性的時 Entity Framework 無法識別相依和主體。 當關聯性的兩端都需要時，請在 HasRequired 方法之後使用 WithRequiredPrincipal 或 WithRequiredDependent。 當關聯性的兩端都是選擇性時，請在 HasOptional 方法之後使用 WithOptionalPrincipal 或 WithOptionalDependent。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>設定多對多關聯性  

下列程式碼會設定課程和講師類型之間的多對多關聯性。 在下列範例中，預設的 Code First 慣例是用來建立聯結資料表。 因此，會使用 Course_CourseID 和 Instructor_InstructorID 資料行來建立 CourseInstructor 資料表。  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

如果您想要在資料表中指定聯結資料表名稱和資料行的名稱，您需要使用 Map 方法進行其他設定。 下列程式碼會產生具有 CourseID 和 InstructorID 資料行的 CourseInstructor 資料表。  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>使用一個導覽屬性設定關聯性  

單向 (也稱為單向) 關聯性，這是指只在其中一個關聯性端上定義導覽屬性，而不是同時在兩者上定義。 依照慣例，Code First 一律會將單向關聯性解讀為一對多。 例如，如果您想要講師與 OfficeAssignment 之間的一對一關聯性，其中只有講師型別的導覽屬性，您必須使用流暢的 API 來設定此關聯性。  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>啟用 Cascade Delete  

您可以使用 WillCascadeOnDelete 方法來設定關聯性的串聯刪除。 如果相依實體上的外鍵不可為 null，則 Code First 會在關聯性上設定串聯刪除。 如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當刪除主體時，外鍵將會設定為 null。  

您可以使用下列方法移除這些串聯刪除慣例：  

modelBuilder 移除 \<OneToManyCascadeDeleteConvention\> ( # A1  
modelBuilder 移除 \<ManyToManyCascadeDeleteConvention\> ( # A1  

下列程式碼會設定必要的關聯性，然後停用串聯刪除。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>設定複合外鍵  

如果部門類型上的主鍵是由 DepartmentID 和 Name 屬性組成，您可以設定部門的主鍵和課程類型上的外鍵，如下所示：  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>重新命名模型中未定義的外鍵  

如果您選擇不要在 CLR 類型上定義外鍵，但想要指定它在資料庫中應有的名稱，請執行下列動作：  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>設定未遵循 Code First 慣例的外鍵名稱  

如果課程類別的外鍵屬性稱為 SomeDepartmentID 而非 DepartmentID，您需要執行下列動作，以指定您想要 SomeDepartmentID 成為外鍵：  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>範例中使用的模型  

下列 Code First 模型用於此頁面上的範例。  

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
