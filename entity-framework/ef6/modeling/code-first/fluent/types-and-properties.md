---
title: 流暢的 API-設定和對應屬性和類型-EF6
description: 流暢的 API-設定和對應 Entity Framework 6 中的屬性和類型
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: 821672bcb797314c96189443ace7f875a79c8582
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065143"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>流暢的 API-設定和對應屬性和類型
使用 Entity Framework 時 Code First 預設行為是使用一組內建至 EF 的慣例，將 POCO 類別對應至資料表。 不過，有時候您不能或不想要遵循這些慣例，也不需要將實體對應至慣例所規定的專案。  

您可以使用兩種主要方式來設定 EF，以使用慣例以外的部分，也就是 [批註](xref:ef6/modeling/code-first/data-annotations) 或 EFs 流暢的 API。 批註只涵蓋了一些流暢的 API 功能，因此有無法使用批註達成的對應案例。 本文旨在示範如何使用流暢的 API 來設定屬性。  

Code first 流暢 API 最常透過覆寫衍生[DbCoNtext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)的[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法來存取。 下列範例旨在示範如何使用流暢的 api 執行各種工作，並可讓您將程式碼複製並自訂成符合您的模型，如果您想要查看可依原樣使用的模型，則會在本文結尾處提供。  

## <a name="model-wide-settings"></a>Model-Wide 設定  

### <a name="default-schema-ef6-onwards"></a>預設架構 (EF6)   

從 EF6 開始，您可以在 DbModelBuilder 上使用 HasDefaultSchema 方法，以指定要用於所有資料表、預存程式等的資料庫架構。針對您明確設定不同架構的任何物件，將會覆寫此預設設定。  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>自訂慣例 (EF6)   

從 EF6 開始，您可以建立自己的慣例來補充 Code First 中包含的慣例。 如需詳細資訊，請參閱 [自訂 Code First 慣例](xref:ef6/modeling/code-first/conventions/custom)。  

## <a name="property-mapping"></a>屬性對應  

[屬性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法是用來設定屬於實體或複雜類型之每個屬性的屬性。 屬性方法是用來取得指定之屬性的設定物件。 設定物件上的選項僅適用于所設定的類型;例如，IsUnicode 只能在字串屬性上使用。  

### <a name="configuring-a-primary-key"></a>設定主要金鑰  

主要索引鍵的 Entity Framework 慣例為：  

1. 您的類別會定義其名稱為 "ID" 或 "Id" 的屬性  
2. 或類別名稱，後面接著 "ID" 或 "Id"  

若要將屬性明確設定為主要索引鍵，您可以使用 HasKey 方法。 在下列範例中，HasKey 方法是用來設定 OfficeAssignment 型別上的 InstructorID primary key。  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>設定複合主鍵  

下列範例會將 DepartmentID 和 Name 屬性設定為部門類型的複合主鍵。  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>關閉數值主鍵的身分識別  

下列範例會將 DepartmentID 屬性設定為 ComponentModel，以表示資料庫不會產生此值的值。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>指定屬性的最大長度  

在下列範例中，名稱屬性不應超過50個字元。 如果您將值的長度超過50個字元，就會收到 [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) 例外狀況。 如果 Code First 從此模型建立資料庫，也會將 [名稱] 資料行的最大長度設定為50個字元。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>將屬性設定為必要  

在下列範例中，必須要有 Name 屬性。 如果您未指定名稱，就會收到 DbEntityValidationException 例外狀況。 如果 Code First 從此模型建立資料庫，則用來儲存這個屬性的資料行通常會是不可為 null。  

> [!NOTE]
> 在某些情況下，即使屬性是必要的，資料庫中的資料行還是可能無法成為可為 null。 例如，使用多個類型的 TPH 繼承策略資料時，會儲存在單一資料表中。 如果衍生型別包含必要的屬性，則資料行不能成為不可為 null，因為階層中的所有類型都不會有這個屬性。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>設定一或多個屬性的索引  

> [!NOTE]
> **僅限 ef 6.1** 和更新版本-Index 屬性是在 Entity Framework 6.1 中引進。 如果您使用的是較舊的版本，本節中的資訊並不適用。  

流暢的 API 原本就不支援建立索引，但您可以透過流暢的 API 來利用 **IndexAttribute** 的支援。 索引屬性的處理方式是在模型上包含模型批註，然後在管線中稍後再轉換為資料庫中的索引。 您可以使用流暢的 API 手動新增這些相同的附注。  

最簡單的方法是建立 **IndexAttribute** 的實例，其中包含新索引的所有設定。 然後，您可以建立 **IndexAnnotation** 的實例，這是 ef 特定型別，會將 **IndexAttribute** 設定轉換成可儲存在 EF 模型上的模型批註。 然後，您可以在流暢的 API 上將這些方法傳遞給 **HasColumnAnnotation** 方法，並指定注釋的名稱 **索引** 。  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

如需**IndexAttribute**中可用設定的完整清單，請參閱[Code First 資料批註](xref:ef6/modeling/code-first/data-annotations)的*索引*區段。 這包括自訂索引名稱、建立唯一索引，以及建立多資料行索引。  

您可以在單一屬性上指定多個索引注釋，方法是將 **IndexAttribute** 的陣列傳遞至 **IndexAnnotation**的函式。  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>指定不將 CLR 屬性對應到資料庫中的資料行  

下列範例顯示如何指定 CLR 型別上的屬性未對應到資料庫中的資料行。  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>將 CLR 屬性對應到資料庫中的特定資料行  

下列範例會將名稱 CLR 屬性對應至 DepartmentName 資料庫資料行。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>重新命名模型中未定義的外鍵  

如果您選擇不要在 CLR 類型上定義外鍵，但想要指定它在資料庫中應有的名稱，請執行下列動作：  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>設定字串屬性是否支援 Unicode 內容  

依預設，字串是 SQL Server) 中的 Unicode (Nvarchar。 您可以使用 IsUnicode 方法來指定字串必須是 Varchar 類型。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>設定資料庫資料行的資料類型  

[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法可讓您對應到相同基本類型的不同標記法。 使用這個方法並不會讓您在執行時間執行任何資料轉換。 請注意，IsUnicode 是將資料行設定為 Varchar 的慣用方法，因為它是資料庫中立的。  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>設定複雜類型的屬性  

有兩種方式可以設定複雜類型的純量屬性。  

您可以呼叫 ComplexTypeConfiguration 上的屬性。  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

您也可以使用點標記法來存取複雜型別的屬性。  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>設定要當做開放式並行存取權杖使用的屬性  

若要指定實體中的屬性工作表示並行存取權杖，您可以使用 ConcurrencyCheck 屬性或 IsConcurrencyToken 方法。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

您也可以使用 IsRowVersion 方法，將屬性設定為資料庫中的資料列版本。 將屬性設定為數據列版本時，會自動將此屬性設定為開放式並行存取權杖。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>型別對應  

### <a name="specifying-that-a-class-is-a-complex-type"></a>指定類別為複雜類型  

依照慣例，沒有指定主鍵的型別會被視為複雜型別。 在某些情況下，Code First 不會偵測到複雜型別 (例如，如果您有一個名為 ID 的屬性，但您不表示它是主鍵) 。 在這種情況下，您會使用流暢的 API 來明確指定類型為複雜類型。  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>指定不將 CLR 實體類型對應至資料庫中的資料表  

下列範例示範如何將 CLR 型別對應至資料庫中的資料表。  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>將實體類型對應至資料庫中的特定資料表  

部門的所有屬性都會對應到稱為 t_ 部門的資料表中的資料行。  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

您也可以指定架構名稱，如下所示：  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>將每個階層的資料表對應 (TPH) 繼承  

在 TPH 對應案例中，繼承階層架構中的所有類型都會對應至單一資料表。 鑒別子資料行是用來識別每個資料列的類型。 使用 Code First 建立模型時，TPH 是參與繼承階層之類型的預設策略。 根據預設，鑒別子資料行會加入至名稱為 "鑒別子" 的資料表，而階層中每個型別的 CLR 型別名稱會用在鑒別子值。 您可以使用流暢的 API 來修改預設行為。  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>將每個類型的資料表對應 (TPT) 繼承  

在 TPT 對應案例中，所有類型都會對應至個別資料表。 單獨屬於基底型別 (Base Type) 或衍生型別 (Derived Type) 的屬性會儲存在對應至該型別的資料表中。 對應至衍生類型的資料表也會儲存外鍵，以聯結衍生資料表與基表。  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>將每個具體的資料表對應 (TPC) 繼承  

在 TPC 對應案例中，階層中的所有非抽象類別型都會對應至個別資料表。 對應至衍生類別的資料表與對應至資料庫中基類的資料表沒有任何關聯性。 類別的所有屬性（包括繼承的屬性）都會對應到對應資料表的資料行。  

呼叫 MapInheritedProperties 方法來設定每個衍生型別。 MapInheritedProperties 會將繼承自基類的所有屬性重新對應至衍生類別之資料表中的新資料行。  

> [!NOTE]
> 請注意，由於參與 TPC 繼承階層的資料表不會共用主鍵，因此，如果您的資料庫產生值具有相同的識別種子，則在對應至子類別的資料表中插入時，將會有重複的實體索引鍵。 若要解決這個問題，您可以為每個資料表指定不同的初始種子值，或在主要索引鍵屬性上關閉身分識別。 使用 Code First 時，身分識別是整數索引鍵屬性的預設值。  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>將實體類型的屬性對應至資料庫中的多個資料表 (實體分割)   

實體分割可讓實體類型的屬性分散到多個資料表。 在下列範例中，部門實體會分割成兩個數據表：部門和 DepartmentDetails。 實體分割會使用多個對應方法呼叫，將屬性子集對應至特定的資料表。  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>將多個實體類型對應至資料庫中的一個資料表， (資料表分割)   

下列範例會將共用主要索引鍵的兩個實體類型對應到一個資料表。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>將實體類型對應至插入/更新/刪除預存程式 (EF6 之後)   

從 EF6 開始，您可以對應實體以使用插入更新和刪除的預存程式。 如需詳細資訊，請參閱 [Code First Insert/Update/Delete 預存程式](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)。  

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
