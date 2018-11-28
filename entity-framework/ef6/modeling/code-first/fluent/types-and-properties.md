---
title: Fluent API-設定和對應屬性及型別-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
ms.openlocfilehash: 7371cc99142ccf8fc6bea237d7d58d1e67fcecec
ms.sourcegitcommit: 75f8a179ac9a70ad390fc7ab2a6c5e714e701b8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2018
ms.locfileid: "52339799"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="24f73-102">Fluent API-設定和對應的屬性和類型</span><span class="sxs-lookup"><span data-stu-id="24f73-102">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="24f73-103">使用 Entity Framework Code First 時的預設行為是將您的 POCO 類別對應到資料表使用一組內建到 EF 的慣例。</span><span class="sxs-lookup"><span data-stu-id="24f73-103">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="24f73-104">有時候，不過，您無法或不想遵循這些慣例，而且必須將實體對應至規範的要求以外的項目。</span><span class="sxs-lookup"><span data-stu-id="24f73-104">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="24f73-105">有兩種主要方式，您可以設定使用以外的慣例，也就是 EF[註解](~/ef6/modeling/code-first/data-annotations.md)或 EFs fluent API。</span><span class="sxs-lookup"><span data-stu-id="24f73-105">There are two main ways you can configure EF to use something other than conventions, namely [annotations](~/ef6/modeling/code-first/data-annotations.md) or EFs fluent API.</span></span> <span data-ttu-id="24f73-106">註解只涵蓋 fluent API 功能的子集，因此會有無法使用註解來達成的對應案例。</span><span class="sxs-lookup"><span data-stu-id="24f73-106">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="24f73-107">這篇文章是設計用來示範如何使用 fluent API 來設定屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-107">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="24f73-108">Code first fluent API 最常存取藉由覆寫[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法，在您的衍生[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)。</span><span class="sxs-lookup"><span data-stu-id="24f73-108">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="24f73-109">下列範例設計用來示範如何執行各種工作，使用 fluent api，並可讓您複製程式碼，並自訂以配合您的模型，如果您想要查看它們可用來與當做模型-則提供這篇文章的結尾。</span><span class="sxs-lookup"><span data-stu-id="24f73-109">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="24f73-110">模型範圍內的設定</span><span class="sxs-lookup"><span data-stu-id="24f73-110">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="24f73-111">預設結構描述 (EF6 之後版本)</span><span class="sxs-lookup"><span data-stu-id="24f73-111">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="24f73-112">從 EF6 與您可以使用 HasDefaultSchema 方法上 DbModelBuilder 來指定要用於所有資料表、 預存程序等資料庫結構描述。您明確設定不同的結構描述的任何物件，將會覆寫此預設設定。</span><span class="sxs-lookup"><span data-stu-id="24f73-112">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="24f73-113">自訂慣例 (EF6 之後版本)</span><span class="sxs-lookup"><span data-stu-id="24f73-113">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="24f73-114">從的 EF6，您可以建立自己的慣例，來補充 Code First 中包含的項目。</span><span class="sxs-lookup"><span data-stu-id="24f73-114">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="24f73-115">如需詳細資訊，請參閱 <<c0> [ 自訂程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/custom.md)。</span><span class="sxs-lookup"><span data-stu-id="24f73-115">For more details, see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="24f73-116">屬性對應</span><span class="sxs-lookup"><span data-stu-id="24f73-116">Property Mapping</span></span>  

<span data-ttu-id="24f73-117">[屬性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法用來設定每個屬性屬於實體或複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-117">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="24f73-118">屬性的方法用來取得指定屬性的組態物件。</span><span class="sxs-lookup"><span data-stu-id="24f73-118">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="24f73-119">組態物件上的選項專屬於設定; 的型別IsUnicode 是例如只有字串屬性才有提供。</span><span class="sxs-lookup"><span data-stu-id="24f73-119">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="24f73-120">設定主索引鍵</span><span class="sxs-lookup"><span data-stu-id="24f73-120">Configuring a Primary Key</span></span>  

<span data-ttu-id="24f73-121">主索引鍵的 Entity Framework 慣例是：</span><span class="sxs-lookup"><span data-stu-id="24f73-121">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="24f73-122">您的類別定義的屬性，其名稱為 「 識別碼 」 或 「 識別碼 」</span><span class="sxs-lookup"><span data-stu-id="24f73-122">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="24f73-123">或類別名稱後面加上 「 識別碼 」 或 「 識別碼 」</span><span class="sxs-lookup"><span data-stu-id="24f73-123">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="24f73-124">若要明確設定主索引鍵屬性，您可以使用 HasKey 方法。</span><span class="sxs-lookup"><span data-stu-id="24f73-124">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="24f73-125">在下列範例中，HasKey 方法用來設定 OfficeAssignment 型別上的 InstructorID 主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="24f73-125">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="24f73-126">設定複合主索引鍵</span><span class="sxs-lookup"><span data-stu-id="24f73-126">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="24f73-127">下列範例會設定 DepartmentID 及名稱屬性是部門類型的複合主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="24f73-127">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="24f73-128">關閉 數字的主索引鍵的 身分識別</span><span class="sxs-lookup"><span data-stu-id="24f73-128">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="24f73-129">下列範例會將 [DepartmentID] 屬性設 System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None 表示不會由資料庫所產生的值。</span><span class="sxs-lookup"><span data-stu-id="24f73-129">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="24f73-130">在屬性上指定的最大長度</span><span class="sxs-lookup"><span data-stu-id="24f73-130">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="24f73-131">在下列範例中，[名稱] 屬性應該不超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="24f73-131">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="24f73-132">如果您超過 50 個字元的值，您會收到[DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx)例外狀況。</span><span class="sxs-lookup"><span data-stu-id="24f73-132">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="24f73-133">如果 Code First 會建立一個資料庫在這個模型中它會同時設定 [名稱] 欄的最大長度，為 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="24f73-133">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="24f73-134">設定為 Required 屬性</span><span class="sxs-lookup"><span data-stu-id="24f73-134">Configuring the Property to be Required</span></span>  

<span data-ttu-id="24f73-135">在下列範例中，[名稱] 屬性是必要的。</span><span class="sxs-lookup"><span data-stu-id="24f73-135">In the following example, the Name property is required.</span></span> <span data-ttu-id="24f73-136">如果您未指定名稱，您會收到 DbEntityValidationException 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="24f73-136">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="24f73-137">如果第一個程式碼從這個模型建立資料庫再用來儲存這個屬性的資料行通常是不可為 null。</span><span class="sxs-lookup"><span data-stu-id="24f73-137">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="24f73-138">在某些情況下它可能無法在資料庫中是不可為 null，即使需要此屬性，資料行。</span><span class="sxs-lookup"><span data-stu-id="24f73-138">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="24f73-139">比方說，當使用 TPH 繼承策略資料的多個類型會儲存在單一資料表。</span><span class="sxs-lookup"><span data-stu-id="24f73-139">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="24f73-140">如果衍生型別包含必要的屬性資料行無法進行不可為 null 因為並非所有的型別階層架構中會有這個屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-140">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="24f73-141">設定一或多個屬性的索引</span><span class="sxs-lookup"><span data-stu-id="24f73-141">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="24f73-142">**EF6.1 及更新版本僅**-Entity Framework 6.1 中導入的索引屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-142">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="24f73-143">如果您使用較早版本不適用這一節的資訊。</span><span class="sxs-lookup"><span data-stu-id="24f73-143">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="24f73-144">建立索引不原生支援的 Fluent API，但是您可以使用的支援**IndexAttribute**透過 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="24f73-144">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="24f73-145">索引屬性，會處理包括模型上的註釋的模型，然後變成稍後在管線中的資料庫中的索引。</span><span class="sxs-lookup"><span data-stu-id="24f73-145">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="24f73-146">您可以手動新增這些相同使用 Fluent API 的註解。</span><span class="sxs-lookup"><span data-stu-id="24f73-146">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="24f73-147">若要這樣做最簡單的方式是建立的執行個體**IndexAttribute**包含新索引的所有設定。</span><span class="sxs-lookup"><span data-stu-id="24f73-147">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="24f73-148">然後，您就可以建立的執行個體**IndexAnnotation**即會將轉換的 EF 特定型別**IndexAttribute**設定成可以儲存在 EF 模型的模型註釋。</span><span class="sxs-lookup"><span data-stu-id="24f73-148">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="24f73-149">這些接著傳遞給**HasColumnAnnotation** Fluent api，並指定名稱的方法**Index**的註解。</span><span class="sxs-lookup"><span data-stu-id="24f73-149">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="24f73-150">如需完整的清單中可用的設定**IndexAttribute**，請參閱*Index*一節[Code First 資料註解](~/ef6/modeling/code-first/data-annotations.md)。</span><span class="sxs-lookup"><span data-stu-id="24f73-150">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](~/ef6/modeling/code-first/data-annotations.md).</span></span> <span data-ttu-id="24f73-151">這包括自訂的索引名稱、 建立唯一索引，以及建立多重資料行索引。</span><span class="sxs-lookup"><span data-stu-id="24f73-151">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="24f73-152">您也可以傳遞陣列的單一屬性上指定多個索引的註釋**IndexAttribute**的建構函式**IndexAnnotation**。</span><span class="sxs-lookup"><span data-stu-id="24f73-152">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="24f73-153">指定不在資料庫中的資料行中對應的 CLR 屬性</span><span class="sxs-lookup"><span data-stu-id="24f73-153">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="24f73-154">下列範例示範如何指定 CLR 類型上的屬性不對應至資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="24f73-154">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="24f73-155">在資料庫中的特定資料行中對應的 CLR 屬性</span><span class="sxs-lookup"><span data-stu-id="24f73-155">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="24f73-156">下列範例會對應至 DepartmentName 資料庫資料行的名稱 CLR 屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-156">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="24f73-157">重新命名模型中未定義的外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="24f73-157">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="24f73-158">如果您選擇不在 CLR 型別上定義的外部索引鍵，但想要在資料庫中指定它應該有何種名稱，執行下列作業：</span><span class="sxs-lookup"><span data-stu-id="24f73-158">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="24f73-159">設定是否為字串屬性支援 Unicode 內容</span><span class="sxs-lookup"><span data-stu-id="24f73-159">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="24f73-160">預設字串為 Unicode (SQL Server 中的 nvarchar)。</span><span class="sxs-lookup"><span data-stu-id="24f73-160">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="24f73-161">您可以使用 IsUnicode 方法，以指定的 varchar 類型應為字串。</span><span class="sxs-lookup"><span data-stu-id="24f73-161">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="24f73-162">設定資料庫資料行的資料類型</span><span class="sxs-lookup"><span data-stu-id="24f73-162">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="24f73-163">[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法可讓相同的基本類型的不同表示法的對應。</span><span class="sxs-lookup"><span data-stu-id="24f73-163">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="24f73-164">使用此方法無法讓您執行任何轉換的資料在執行階段。</span><span class="sxs-lookup"><span data-stu-id="24f73-164">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="24f73-165">請注意，IsUnicode 設定資料行的慣用的方法為 varchar，因為它與資料庫無關。</span><span class="sxs-lookup"><span data-stu-id="24f73-165">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="24f73-166">複雜類型上設定屬性</span><span class="sxs-lookup"><span data-stu-id="24f73-166">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="24f73-167">有兩種方式可設定純量屬性上的複雜型別。</span><span class="sxs-lookup"><span data-stu-id="24f73-167">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="24f73-168">您可以呼叫 ComplexTypeConfiguration 屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-168">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="24f73-169">您也可以使用點標記法來存取複雜型別的屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-169">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="24f73-170">設定要當做開放式並行存取 Token 的屬性</span><span class="sxs-lookup"><span data-stu-id="24f73-170">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="24f73-171">若要指定實體中的屬性代表並行語彙基元，您可以使用 ConcurrencyCheck 屬性或 IsConcurrencyToken; 方法。</span><span class="sxs-lookup"><span data-stu-id="24f73-171">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="24f73-172">您也可以使用 IsRowVersion 方法來設定要在資料庫中的資料列版本的屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-172">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="24f73-173">設定此屬性的資料列版本會自動設定它的開放式並行存取 token。</span><span class="sxs-lookup"><span data-stu-id="24f73-173">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="24f73-174">類型對應</span><span class="sxs-lookup"><span data-stu-id="24f73-174">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="24f73-175">指定類別複雜型別</span><span class="sxs-lookup"><span data-stu-id="24f73-175">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="24f73-176">依照慣例，指定沒有主索引鍵的型別會被視為複雜型別。</span><span class="sxs-lookup"><span data-stu-id="24f73-176">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="24f73-177">有一些案例，其中第一個程式碼不會偵測到複雜型別 （例如，如果您沒有名為 ID 的屬性，但您並不表示讓它成為主索引鍵）。</span><span class="sxs-lookup"><span data-stu-id="24f73-177">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="24f73-178">在這種情況下，您會使用 fluent API，明確指定的類型是複雜類型。</span><span class="sxs-lookup"><span data-stu-id="24f73-178">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="24f73-179">指定不將 CLR 實體型別對應至資料庫中的資料表</span><span class="sxs-lookup"><span data-stu-id="24f73-179">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="24f73-180">下列範例示範如何對應到資料庫中的資料表時，排除 CLR 型別。</span><span class="sxs-lookup"><span data-stu-id="24f73-180">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="24f73-181">將實體類型對應至資料庫中的特定資料表</span><span class="sxs-lookup"><span data-stu-id="24f73-181">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="24f73-182">部門的所有屬性會都對應到名為 t_ 部門的資料表中的資料行。</span><span class="sxs-lookup"><span data-stu-id="24f73-182">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="24f73-183">您也可以指定結構描述名稱，像這樣：</span><span class="sxs-lookup"><span data-stu-id="24f73-183">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="24f73-184">對應表-table-per Hierarchy (TPH) 繼承</span><span class="sxs-lookup"><span data-stu-id="24f73-184">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="24f73-185">TPH 對應案例中，在繼承階層架構中的所有型別會對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="24f73-185">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="24f73-186">鑑別子資料行用來識別每個資料列型別。</span><span class="sxs-lookup"><span data-stu-id="24f73-186">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="24f73-187">當使用 Code First 建立模型，TPH 是參與繼承階層架構中的類型的預設策略。</span><span class="sxs-lookup"><span data-stu-id="24f73-187">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="24f73-188">根據預設，鑑別子資料行加入至具有名稱"鑑別子 」 的資料表，並在階層中每個類型的 CLR 型別名稱將用於鑑別子值。</span><span class="sxs-lookup"><span data-stu-id="24f73-188">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="24f73-189">您可以使用 fluent API，以修改預設行為。</span><span class="sxs-lookup"><span data-stu-id="24f73-189">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="24f73-190">對應每一類一表 (TPT) 繼承</span><span class="sxs-lookup"><span data-stu-id="24f73-190">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="24f73-191">在 TPT 對應案例中，所有的類型會對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="24f73-191">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="24f73-192">單獨屬於基底型別 (Base Type) 或衍生型別 (Derived Type) 的屬性會儲存在對應至該型別的資料表中。</span><span class="sxs-lookup"><span data-stu-id="24f73-192">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="24f73-193">對應至衍生類型的資料表也會儲存在衍生的資料表，與基底資料表的外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="24f73-193">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="24f73-194">對應資料表每個實體 (Tpc) 繼承</span><span class="sxs-lookup"><span data-stu-id="24f73-194">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="24f73-195">TPC 對應案例中，在階層中的所有非抽象型別會對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="24f73-195">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="24f73-196">對應至衍生類別的資料表會有沒有對應到基底類別，在資料庫中資料表的關聯性。</span><span class="sxs-lookup"><span data-stu-id="24f73-196">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="24f73-197">所有類別的屬性，包括繼承的屬性會對應到相對應資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="24f73-197">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="24f73-198">呼叫 MapInheritedProperties 方法來設定每個衍生的類型。</span><span class="sxs-lookup"><span data-stu-id="24f73-198">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="24f73-199">MapInheritedProperties 重新對應至新的資料行，資料表中的衍生類別繼承自基底類別的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-199">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="24f73-200">請注意，因為不會共用參與 TPC 繼承階層架構中的資料表有主索引鍵會重複的實體索引鍵會對應到子類別中，如果您有使用相同的識別值種子的資料庫產生值的資料表中插入時。</span><span class="sxs-lookup"><span data-stu-id="24f73-200">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="24f73-201">若要解決此問題，您可以指定不同的初始種子值，每個資料表，或關閉主索引鍵屬性上的身分識別。</span><span class="sxs-lookup"><span data-stu-id="24f73-201">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="24f73-202">使用 Code First 時，身分識別會是整數索引鍵屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="24f73-202">Identity is the default value for integer key properties when working with Code First.</span></span>  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="24f73-203">將實體類型的屬性對應至多個資料表中的資料庫 （實體分割）</span><span class="sxs-lookup"><span data-stu-id="24f73-203">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="24f73-204">實體分割可讓分散到多個資料表的實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="24f73-204">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="24f73-205">在下列範例中，部門實體會分割成兩個資料表： 部門和 DepartmentDetails。</span><span class="sxs-lookup"><span data-stu-id="24f73-205">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="24f73-206">實體分割，可用於對應方法的多個呼叫對應至特定資料表的屬性子集。</span><span class="sxs-lookup"><span data-stu-id="24f73-206">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="24f73-207">將多個實體類型對應至資料庫 （分割的資料表） 中的一個資料表</span><span class="sxs-lookup"><span data-stu-id="24f73-207">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="24f73-208">下列範例會共用一個資料表的主索引鍵的兩個實體類型。</span><span class="sxs-lookup"><span data-stu-id="24f73-208">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="24f73-209">將實體類型對應到 Insert/Update/Delete 預存程序 (EF6 之後版本)</span><span class="sxs-lookup"><span data-stu-id="24f73-209">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="24f73-210">開始使用 EF6 可以對應預存程序用於插入更新和刪除實體。</span><span class="sxs-lookup"><span data-stu-id="24f73-210">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="24f73-211">如需詳細資訊，請參閱[程式碼第一個 Insert/Update/Delete 預存程序](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)。</span><span class="sxs-lookup"><span data-stu-id="24f73-211">For more details see, [Code First Insert/Update/Delete Stored Procedures](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="24f73-212">範例中所使用的模型</span><span class="sxs-lookup"><span data-stu-id="24f73-212">Model Used in Samples</span></span>  

<span data-ttu-id="24f73-213">下列的 Code First 模型用於此頁面上的範例。</span><span class="sxs-lookup"><span data-stu-id="24f73-213">The following Code First model is used for the samples on this page.</span></span>  

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
