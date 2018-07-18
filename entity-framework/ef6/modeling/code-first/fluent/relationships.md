---
title: Fluent API-關聯性-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
caps.latest.revision: 3
ms.openlocfilehash: 7437b395fbed07dcb8c93cd8418317611e14a60b
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120327"
---
# <a name="fluent-api---relationships"></a>Fluent API-關聯性
> [!NOTE]
> 此頁面提供有關設定 Code First 模型使用 fluent API 中的關聯性資訊。 如需 EF 以及如何存取和操作資料使用關聯性的關聯性的一般資訊，請參閱[關聯性和導覽屬性](~/ef6/fundamentals/relationships.md)。  

當使用 Code First，您會定義您的模型，藉由定義您的網域 CLR 類別。 根據預設，Entity Framework 會使用 Code First 慣例，來將您的類別對應至資料庫結構描述。 如果您使用的第一個程式碼的命名慣例，在大部分情況下您可以依賴 Code First 來設定您的外部索引鍵和您在類別上定義導覽屬性為基礎的資料表之間的關聯性。 如果您沒有遵循慣例，定義您的類別時，或慣例如果您想要變更的方式運作，您可以使用資料註解的 fluent API 來設定您的類別，讓程式碼第一次可以對應您的資料表之間的關聯性。  

## <a name="introduction"></a>簡介  

設定時的關聯性使用 fluent API，開始與 EntityTypeConfiguration 執行個體，然後使用 HasRequired、 HasOptional 或 HasMany 方法來指定此實體所參與的關聯性的類型。 HasRequired 和 HasOptional 方法會採用表示參考導覽屬性的 lambda 運算式。 HasMany 方法會採用 lambda 運算式，表示集合導覽屬性。 您接著可以設定反向的導覽屬性使用 WithRequired、 WithOptional 和 WithMany 方法。 這些方法具有多載，不接受引數，而且可用來指定基數與單向導覽。  

您接著可以使用 HasForeignKey 方法來設定外部索引鍵屬性。 這個方法會採用 lambda 運算式，表示要做為外部索引鍵的屬性。  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>設定所需-至-選用的關聯性 （--0-或-一對一）  

下列範例會設定--0-或-一對一關聯性。 OfficeAssignment 具有屬於主索引鍵和外部索引鍵，InstructorID 屬性，因為屬性的名稱並未遵循 HasKey 方法用來設定主索引鍵的慣例。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>設定關聯性兩端所需 （一對一）  

在大部分情況下 Entity Framework 可以推斷的型別是依存和也就是關聯性中的主體。 不過，當兩個關聯性所需或兩邊都是選擇性的 Entity Framework 無法識別相依和主體。 當關聯性的兩端都是必要項目時，使用 WithRequiredPrincipal 或 WithRequiredDependent HasRequired 方法之後。 當關聯性的兩端都是選擇性項目時，使用 WithOptionalPrincipal 或 WithOptionalDependent HasOptional 方法之後。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>設定多對多關聯性  

下列程式碼會設定 Course 與 Instructor 型別之間的多對多關聯性。 在下列範例中，預設 Code First 慣例來建立聯結的資料表。 如此一來 CourseInstructor 資料表會透過 Course_CourseID 和 Instructor_InstructorID 資料行。  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

如果您想要在資料表中指定的聯結資料表名稱和資料行的名稱必須使用 Map 方法執行其他設定。 下列程式碼會產生的 CourseInstructor 資料表 CourseID 和 InstructorID 的資料行。  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>設定具有一個導覽屬性的關聯性  

單向 （也稱為單向） 關聯性是只有其中一個關聯性端，而不是在同時定義導覽屬性時。 依照慣例，Code First 一律會解譯為一對多單向關聯性。 例如，如果您想要的一對一關聯性介於 Instructor 與 OfficeAssignment，其中只有 Instructor 型別有一個導覽屬性，您需要使用 fluent API 來設定此關聯性。  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>啟用串聯刪除  

您可以使用 WillCascadeOnDelete 方法，將關聯性設定串聯刪除。 如果相依的實體上的外部索引鍵不是可為 null，然後第一個程式碼設定串聯刪除關聯性。 如果相依的實體上的外部索引鍵可為 null，第一個程式碼不會設定串聯刪除關聯性，而且當主體已刪除的外部索引鍵將會設定為 null。  

您可以使用，以移除這些 cascade delete 慣例：  

modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>（)  
modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>（)  

下列程式碼會設定為 required 關聯性，並再停用串聯刪除。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>設定複合外部索引鍵  

如果部門型別上的主索引鍵是由 DepartmentID 和 Name 屬性所組成，您會設定主索引鍵部門 」 和 「 外部索引鍵的課程類型，如下所示：  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>重新命名模型中未定義的外部索引鍵  

如果您選擇不在 CLR 型別上定義的外部索引鍵，但想要在資料庫中指定它應該有何種名稱，執行下列作業：  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>設定外部索引鍵名稱未遵循的程式碼的第一個慣例  

如果在課程類別上的外部索引鍵屬性呼叫而不是 DepartmentID SomeDepartmentID，您需要執行下列工作來指定您想要將外部索引鍵的 SomeDepartmentID:  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>範例中所使用的模型  

下列的 Code First 模型用於此頁面上的範例。  

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
