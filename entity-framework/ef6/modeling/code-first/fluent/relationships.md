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
# <a name="fluent-api---relationships"></a>流暢的 API-關聯性
> [!NOTE]
> 此頁面提供使用 Fluent API 在 Code First 模型中設定關聯性的相關資訊。 如需 EF 中關聯性和如何使用關聯性存取和運算元據的一般資訊，請參閱[& 導覽屬性的關聯](~/ef6/fundamentals/relationships.md)性。  

使用 Code First 時，您可以藉由定義網域 CLR 類別來定義您的模型。 根據預設，Entity Framework 會使用 Code First 慣例，將您的類別對應至資料庫架構。 如果您使用 Code First 命名慣例，在大部分的情況下，您可以依賴 Code First 根據您在類別上定義的外鍵和導覽屬性來設定資料表之間的關聯性。 如果您在定義類別時未遵循慣例，或想要變更慣例的使用方式，您可以使用 Fluent API 或資料批註來設定類別，讓 Code First 可以對應資料表之間的關聯性。  

## <a name="introduction"></a>簡介  

當設定與 Fluent API 的關聯性時，您會從 EntityTypeConfiguration 實例開始，然後使用 HasRequired、HasOptional 或 HasMany 方法來指定這個實體所參與之關聯性的類型。 HasRequired 和 HasOptional 方法會接受代表參考導覽屬性的 lambda 運算式。 HasMany 方法會接受代表集合導覽屬性的 lambda 運算式。 接著，您可以使用 WithRequired、WithOptional 和 WithMany 方法來設定反向導覽屬性。 這些方法具有不接受引數的多載，而且可以用來指定具有單向導覽的基數。  

接著，您可以使用 HasForeignKey 方法來設定外鍵屬性。 這個方法會採用 lambda 運算式，表示要當做外鍵使用的屬性。  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>設定必要的選擇性關聯性（一對一到零或一）  

下列範例會設定一對零或一關聯性。 OfficeAssignment 具有屬於主鍵和外鍵的 InstructorID 屬性，因為屬性的名稱不會遵循 HasKey 方法用來設定主鍵的慣例。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>設定兩個端點都是必要的關聯性（一對一）  

在大部分的情況下，Entity Framework 可以推斷哪個類型是相依的，而這是關聯性中的主體。 不過，當關聯性的兩端都是必要的，或兩端都是選擇性時 Entity Framework 無法識別相依和主體。 當關聯性的兩端都是必要的時，請在 HasRequired 方法後面使用 WithRequiredPrincipal 或 WithRequiredDependent。 當關聯性的兩端都是選擇性時，請在 HasOptional 方法後面使用 WithOptionalPrincipal 或 WithOptionalDependent。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>設定多對多關聯性  

下列程式碼會設定課程和講師類型之間的多對多關聯性。 在下列範例中，預設 Code First 慣例是用來建立聯結資料表。 因此，CourseInstructor 資料表是使用 Course_CourseID 和 Instructor_InstructorID 資料行建立的。  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

如果您想要指定聯結資料表名稱和資料表中的資料行名稱，您必須使用 Map 方法來執行其他設定。 下列程式碼會產生具有 CourseID 和 InstructorID 資料行的 CourseInstructor 資料表。  

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

單向（也稱為單向）關聯性是只在其中一個關聯性端（而不是兩者）上定義導覽屬性時。 依照慣例，Code First 一律會將單向關聯性解讀為「一對多」。 例如，如果您想要講師和 OfficeAssignment 之間的一對一關聯性，其中只有講師類型的導覽屬性，您必須使用 Fluent API 來設定此關聯性。  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>啟用串聯刪除  

您可以使用 WillCascadeOnDelete 方法，針對關聯性設定串聯刪除。 如果相依實體的外鍵不可為 null，則 Code First 在關聯性上設定 cascade delete。 如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當主體被刪除時，外鍵將會設定為 null。  

您可以使用下列方法移除這些串聯刪除慣例：  

modelBuilder\>（）移除\<  
modelBuilder\>（）移除\<  

下列程式碼會將關聯性設定為必要，然後停用串聯刪除。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>設定複合外鍵  

如果部門類型上的主鍵是由 DepartmentID 和 Name 屬性所組成，您可以設定部門的主要金鑰和課程類型上的外鍵，如下所示：  

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

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>正在設定未遵循 Code First 慣例的外鍵名稱  

如果課程類別上的外鍵屬性呼叫 SomeDepartmentID 而不是 DepartmentID，您就必須執行下列動作，指定要讓 SomeDepartmentID 成為外鍵：  

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
