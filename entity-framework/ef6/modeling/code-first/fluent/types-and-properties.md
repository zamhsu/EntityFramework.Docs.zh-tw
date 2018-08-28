---
title: Fluent API-設定和對應屬性及型別-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
ms.openlocfilehash: e65a3f4721e5c28de63d143e1143f3584e145477
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996983"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>Fluent API-設定和對應的屬性和類型
使用 Entity Framework Code First 時的預設行為是將您的 POCO 類別對應到資料表使用一組內建到 EF 的慣例。 有時候，不過，您無法或不想遵循這些慣例，而且必須將實體對應至規範的要求以外的項目。  

有兩種主要方式，您可以設定使用以外的慣例，也就是 EF[註解](~/ef6/modeling/code-first/data-annotations.md)或 EFs fluent API。 註解只涵蓋 fluent API 功能的子集，因此會有無法使用註解來達成的對應案例。 這篇文章是設計用來示範如何使用 fluent API 來設定屬性。  

Code first fluent API 最常存取藉由覆寫[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法，在您的衍生[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)。 下列範例設計用來示範如何執行各種工作，使用 fluent api，並可讓您複製程式碼，並自訂以配合您的模型，如果您想要查看它們可用來與當做模型-則提供這篇文章的結尾。  

## <a name="model-wide-settings"></a>模型範圍內的設定  

### <a name="default-schema-ef6-onwards"></a>預設結構描述 (EF6 之後版本)  

從 EF6 與您可以使用 HasDefaultSchema 方法上 DbModelBuilder 來指定要用於所有資料表、 預存程序等資料庫結構描述。您明確設定不同的結構描述的任何物件，將會覆寫此預設設定。  

``` csharp
modelBuilder.HasDefaultSchema(“sales”);
```  

### <a name="custom-conventions-ef6-onwards"></a>自訂慣例 (EF6 之後版本)  

從的 EF6，您可以建立自己的慣例，來補充 Code First 中包含的項目。 如需詳細資訊，請參閱 <<c0> [ 自訂程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/custom.md)。  

## <a name="property-mapping"></a>屬性對應  

[屬性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法用來設定每個屬性屬於實體或複雜類型的屬性。 屬性的方法用來取得指定屬性的組態物件。 組態物件上的選項專屬於設定; 的型別IsUnicode 是例如只有字串屬性才有提供。  

### <a name="configuring-a-primary-key"></a>設定主索引鍵  

主索引鍵的 Entity Framework 慣例是：  

1. 您的類別定義的屬性，其名稱為 「 識別碼 」 或 「 識別碼 」  
2. 或類別名稱後面加上 「 識別碼 」 或 「 識別碼 」  

若要明確設定主索引鍵屬性，您可以使用 HasKey 方法。 在下列範例中，HasKey 方法用來設定 OfficeAssignment 型別上的 InstructorID 主索引鍵。  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>設定複合主索引鍵  

下列範例會設定 DepartmentID 及名稱屬性是部門類型的複合主索引鍵。  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>關閉 數字的主索引鍵的 身分識別  

下列範例會將 [DepartmentID] 屬性設 System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None 表示不會由資料庫所產生的值。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>在屬性上指定的最大長度  

在下列範例中，[名稱] 屬性應該不超過 50 個字元。 如果您超過 50 個字元的值，您會收到[DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx)例外狀況。 如果 Code First 會建立一個資料庫在這個模型中它會同時設定 [名稱] 欄的最大長度，為 50 個字元。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>設定為 Required 屬性  

在下列範例中，[名稱] 屬性是必要的。 如果您未指定名稱，您會收到 DbEntityValidationException 例外狀況。 如果第一個程式碼從這個模型建立資料庫再用來儲存這個屬性的資料行通常是不可為 null。  

> [!NOTE]
> 在某些情況下它可能無法在資料庫中是不可為 null，即使需要此屬性，資料行。 比方說，當使用 TPH 繼承策略資料的多個類型會儲存在單一資料表。 如果衍生型別包含必要的屬性資料行無法進行不可為 null 因為並非所有的型別階層架構中會有這個屬性。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>設定一或多個屬性的索引  

> [!NOTE]
> **EF6.1 及更新版本僅**-Entity Framework 6.1 中導入的索引屬性。 如果您使用較早版本不適用這一節的資訊。  

建立索引不原生支援的 Fluent API，但是您可以使用的支援**IndexAttribute**透過 Fluent API。 索引屬性，會處理包括模型上的註釋的模型，然後變成稍後在管線中的資料庫中的索引。 您可以手動新增這些相同使用 Fluent API 的註解。  

若要這樣做最簡單的方式是建立的執行個體**IndexAttribute**包含新索引的所有設定。 然後，您就可以建立的執行個體**IndexAnnotation**即會將轉換的 EF 特定型別**IndexAttribute**設定成可以儲存在 EF 模型的模型註釋。 這些接著傳遞給**HasColumnAnnotation** Fluent api，並指定名稱的方法**Index**的註解。  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

如需完整的清單中可用的設定**IndexAttribute**，請參閱*Index*一節[Code First 資料註解](~/ef6/modeling/code-first/data-annotations.md)。 這包括自訂的索引名稱、 建立唯一索引，以及建立多重資料行索引。  

您也可以傳遞陣列的單一屬性上指定多個索引的註釋**IndexAttribute**的建構函式**IndexAnnotation**。  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>指定不在資料庫中的資料行中對應的 CLR 屬性  

下列範例示範如何指定 CLR 類型上的屬性不對應至資料庫中的資料行。  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>在資料庫中的特定資料行中對應的 CLR 屬性  

下列範例會對應至 DepartmentName 資料庫資料行的名稱 CLR 屬性。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>重新命名模型中未定義的外部索引鍵  

如果您選擇不在 CLR 型別上定義的外部索引鍵，但想要在資料庫中指定它應該有何種名稱，執行下列作業：  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>設定是否為字串屬性支援 Unicode 內容  

預設字串為 Unicode (SQL Server 中的 nvarchar)。 您可以使用 IsUnicode 方法，以指定的 varchar 類型應為字串。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>設定資料庫資料行的資料類型  

[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法可讓相同的基本類型的不同表示法的對應。 使用此方法無法讓您執行任何轉換的資料在執行階段。 請注意，IsUnicode 設定資料行的慣用的方法為 varchar，因為它與資料庫無關。  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>複雜類型上設定屬性  

有兩種方式可設定純量屬性上的複雜型別。  

您可以呼叫 ComplexTypeConfiguration 屬性。  

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

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>設定要當做開放式並行存取 Token 的屬性  

若要指定實體中的屬性代表並行語彙基元，您可以使用 ConcurrencyCheck 屬性或 IsConcurrencyToken; 方法。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

您也可以使用 IsRowVersion 方法來設定要在資料庫中的資料列版本的屬性。 設定此屬性的資料列版本會自動設定它的開放式並行存取 token。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>類型對應  

### <a name="specifying-that-a-class-is-a-complex-type"></a>指定類別複雜型別  

依照慣例，指定沒有主索引鍵的型別會被視為複雜型別。 有一些案例，其中第一個程式碼不會偵測到複雜型別 （例如，如果您沒有名為 ID 的屬性，但您並不表示讓它成為主索引鍵）。 在這種情況下，您會使用 fluent API，明確指定的類型是複雜類型。  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>指定不將 CLR 實體型別對應至資料庫中的資料表  

下列範例示範如何對應到資料庫中的資料表時，排除 CLR 型別。  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>將實體類型對應至資料庫中的特定資料表  

部門的所有屬性會都對應到名為 t_ 部門的資料表中的資料行。  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

您也可以指定結構描述名稱，像這樣：  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>對應表-table-per Hierarchy (TPH) 繼承  

TPH 對應案例中，在繼承階層架構中的所有型別會對應至單一資料表。 鑑別子資料行用來識別每個資料列型別。 當使用 Code First 建立模型，TPH 是參與繼承階層架構中的類型的預設策略。 根據預設，鑑別子資料行加入至具有名稱"鑑別子 」 的資料表，並在階層中每個類型的 CLR 型別名稱將用於鑑別子值。 您可以使用 fluent API，以修改預設行為。  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>對應每一類一表 (TPT) 繼承  

在 TPT 對應案例中，所有的類型會對應至個別資料表。 單獨屬於基底型別 (Base Type) 或衍生型別 (Derived Type) 的屬性會儲存在對應至該型別的資料表中。 對應至衍生類型的資料表也會儲存在衍生的資料表，與基底資料表的外部索引鍵。  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>對應資料表每個實體 (Tpc) 繼承  

TPC 對應案例中，在階層中的所有非抽象型別會對應至個別資料表。 對應至衍生類別的資料表會有沒有對應到基底類別，在資料庫中資料表的關聯性。 所有類別的屬性，包括繼承的屬性會對應到相對應資料表的資料行。  

呼叫 MapInheritedProperties 方法來設定每個衍生的類型。 MapInheritedProperties 重新對應至新的資料行，資料表中的衍生類別繼承自基底類別的所有屬性。  

> [!NOTE]
> 請注意，因為不會共用參與 TPC 繼承階層架構中的資料表有主索引鍵會重複的實體索引鍵會對應到子類別中，如果您有使用相同的識別值種子的資料庫產生值的資料表中插入時。 若要解決此問題，您可以指定不同的初始種子值，每個資料表，或關閉主索引鍵屬性上的身分識別。 使用 Code First 時，身分識別會是整數索引鍵屬性的預設值。  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>將實體類型的屬性對應至多個資料表中的資料庫 （實體分割）  

實體分割可讓分散到多個資料表的實體類型的屬性。 在下列範例中，部門實體會分割成兩個資料表： 部門和 DepartmentDetails。 實體分割，可用於對應方法的多個呼叫對應至特定資料表的屬性子集。  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>將多個實體類型對應至資料庫 （分割的資料表） 中的一個資料表  

下列範例會共用一個資料表的主索引鍵的兩個實體類型。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>將實體類型對應到 Insert/Update/Delete 預存程序 (EF6 之後版本)  

開始使用 EF6 可以對應預存程序用於插入更新和刪除實體。 如需詳細資訊，請參閱[程式碼第一個 Insert/Update/Delete 預存程序](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)。  

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
