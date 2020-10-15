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
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="1b003-103">流暢的 API-設定和對應屬性和類型</span><span class="sxs-lookup"><span data-stu-id="1b003-103">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="1b003-104">使用 Entity Framework 時 Code First 預設行為是使用一組內建至 EF 的慣例，將 POCO 類別對應至資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-104">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="1b003-105">不過，有時候您不能或不想要遵循這些慣例，也不需要將實體對應至慣例所規定的專案。</span><span class="sxs-lookup"><span data-stu-id="1b003-105">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="1b003-106">您可以使用兩種主要方式來設定 EF，以使用慣例以外的部分，也就是 [批註](xref:ef6/modeling/code-first/data-annotations) 或 EFs 流暢的 API。</span><span class="sxs-lookup"><span data-stu-id="1b003-106">There are two main ways you can configure EF to use something other than conventions, namely [annotations](xref:ef6/modeling/code-first/data-annotations) or EFs fluent API.</span></span> <span data-ttu-id="1b003-107">批註只涵蓋了一些流暢的 API 功能，因此有無法使用批註達成的對應案例。</span><span class="sxs-lookup"><span data-stu-id="1b003-107">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="1b003-108">本文旨在示範如何使用流暢的 API 來設定屬性。</span><span class="sxs-lookup"><span data-stu-id="1b003-108">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="1b003-109">Code first 流暢 API 最常透過覆寫衍生[DbCoNtext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)的[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法來存取。</span><span class="sxs-lookup"><span data-stu-id="1b003-109">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="1b003-110">下列範例旨在示範如何使用流暢的 api 執行各種工作，並可讓您將程式碼複製並自訂成符合您的模型，如果您想要查看可依原樣使用的模型，則會在本文結尾處提供。</span><span class="sxs-lookup"><span data-stu-id="1b003-110">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="1b003-111">Model-Wide 設定</span><span class="sxs-lookup"><span data-stu-id="1b003-111">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="1b003-112">預設架構 (EF6) </span><span class="sxs-lookup"><span data-stu-id="1b003-112">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="1b003-113">從 EF6 開始，您可以在 DbModelBuilder 上使用 HasDefaultSchema 方法，以指定要用於所有資料表、預存程式等的資料庫架構。針對您明確設定不同架構的任何物件，將會覆寫此預設設定。</span><span class="sxs-lookup"><span data-stu-id="1b003-113">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="1b003-114">自訂慣例 (EF6) </span><span class="sxs-lookup"><span data-stu-id="1b003-114">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="1b003-115">從 EF6 開始，您可以建立自己的慣例來補充 Code First 中包含的慣例。</span><span class="sxs-lookup"><span data-stu-id="1b003-115">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="1b003-116">如需詳細資訊，請參閱 [自訂 Code First 慣例](xref:ef6/modeling/code-first/conventions/custom)。</span><span class="sxs-lookup"><span data-stu-id="1b003-116">For more details, see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="1b003-117">屬性對應</span><span class="sxs-lookup"><span data-stu-id="1b003-117">Property Mapping</span></span>  

<span data-ttu-id="1b003-118">[屬性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法是用來設定屬於實體或複雜類型之每個屬性的屬性。</span><span class="sxs-lookup"><span data-stu-id="1b003-118">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="1b003-119">屬性方法是用來取得指定之屬性的設定物件。</span><span class="sxs-lookup"><span data-stu-id="1b003-119">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="1b003-120">設定物件上的選項僅適用于所設定的類型;例如，IsUnicode 只能在字串屬性上使用。</span><span class="sxs-lookup"><span data-stu-id="1b003-120">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="1b003-121">設定主要金鑰</span><span class="sxs-lookup"><span data-stu-id="1b003-121">Configuring a Primary Key</span></span>  

<span data-ttu-id="1b003-122">主要索引鍵的 Entity Framework 慣例為：</span><span class="sxs-lookup"><span data-stu-id="1b003-122">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="1b003-123">您的類別會定義其名稱為 "ID" 或 "Id" 的屬性</span><span class="sxs-lookup"><span data-stu-id="1b003-123">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="1b003-124">或類別名稱，後面接著 "ID" 或 "Id"</span><span class="sxs-lookup"><span data-stu-id="1b003-124">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="1b003-125">若要將屬性明確設定為主要索引鍵，您可以使用 HasKey 方法。</span><span class="sxs-lookup"><span data-stu-id="1b003-125">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="1b003-126">在下列範例中，HasKey 方法是用來設定 OfficeAssignment 型別上的 InstructorID primary key。</span><span class="sxs-lookup"><span data-stu-id="1b003-126">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="1b003-127">設定複合主鍵</span><span class="sxs-lookup"><span data-stu-id="1b003-127">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="1b003-128">下列範例會將 DepartmentID 和 Name 屬性設定為部門類型的複合主鍵。</span><span class="sxs-lookup"><span data-stu-id="1b003-128">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="1b003-129">關閉數值主鍵的身分識別</span><span class="sxs-lookup"><span data-stu-id="1b003-129">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="1b003-130">下列範例會將 DepartmentID 屬性設定為 ComponentModel，以表示資料庫不會產生此值的值。</span><span class="sxs-lookup"><span data-stu-id="1b003-130">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="1b003-131">指定屬性的最大長度</span><span class="sxs-lookup"><span data-stu-id="1b003-131">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="1b003-132">在下列範例中，名稱屬性不應超過50個字元。</span><span class="sxs-lookup"><span data-stu-id="1b003-132">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="1b003-133">如果您將值的長度超過50個字元，就會收到 [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1b003-133">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="1b003-134">如果 Code First 從此模型建立資料庫，也會將 [名稱] 資料行的最大長度設定為50個字元。</span><span class="sxs-lookup"><span data-stu-id="1b003-134">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="1b003-135">將屬性設定為必要</span><span class="sxs-lookup"><span data-stu-id="1b003-135">Configuring the Property to be Required</span></span>  

<span data-ttu-id="1b003-136">在下列範例中，必須要有 Name 屬性。</span><span class="sxs-lookup"><span data-stu-id="1b003-136">In the following example, the Name property is required.</span></span> <span data-ttu-id="1b003-137">如果您未指定名稱，就會收到 DbEntityValidationException 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1b003-137">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="1b003-138">如果 Code First 從此模型建立資料庫，則用來儲存這個屬性的資料行通常會是不可為 null。</span><span class="sxs-lookup"><span data-stu-id="1b003-138">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="1b003-139">在某些情況下，即使屬性是必要的，資料庫中的資料行還是可能無法成為可為 null。</span><span class="sxs-lookup"><span data-stu-id="1b003-139">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="1b003-140">例如，使用多個類型的 TPH 繼承策略資料時，會儲存在單一資料表中。</span><span class="sxs-lookup"><span data-stu-id="1b003-140">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="1b003-141">如果衍生型別包含必要的屬性，則資料行不能成為不可為 null，因為階層中的所有類型都不會有這個屬性。</span><span class="sxs-lookup"><span data-stu-id="1b003-141">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="1b003-142">設定一或多個屬性的索引</span><span class="sxs-lookup"><span data-stu-id="1b003-142">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="1b003-143">**僅限 ef 6.1** 和更新版本-Index 屬性是在 Entity Framework 6.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="1b003-143">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="1b003-144">如果您使用的是較舊的版本，本節中的資訊並不適用。</span><span class="sxs-lookup"><span data-stu-id="1b003-144">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="1b003-145">流暢的 API 原本就不支援建立索引，但您可以透過流暢的 API 來利用 **IndexAttribute** 的支援。</span><span class="sxs-lookup"><span data-stu-id="1b003-145">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="1b003-146">索引屬性的處理方式是在模型上包含模型批註，然後在管線中稍後再轉換為資料庫中的索引。</span><span class="sxs-lookup"><span data-stu-id="1b003-146">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="1b003-147">您可以使用流暢的 API 手動新增這些相同的附注。</span><span class="sxs-lookup"><span data-stu-id="1b003-147">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="1b003-148">最簡單的方法是建立 **IndexAttribute** 的實例，其中包含新索引的所有設定。</span><span class="sxs-lookup"><span data-stu-id="1b003-148">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="1b003-149">然後，您可以建立 **IndexAnnotation** 的實例，這是 ef 特定型別，會將 **IndexAttribute** 設定轉換成可儲存在 EF 模型上的模型批註。</span><span class="sxs-lookup"><span data-stu-id="1b003-149">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="1b003-150">然後，您可以在流暢的 API 上將這些方法傳遞給 **HasColumnAnnotation** 方法，並指定注釋的名稱 **索引** 。</span><span class="sxs-lookup"><span data-stu-id="1b003-150">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="1b003-151">如需**IndexAttribute**中可用設定的完整清單，請參閱[Code First 資料批註](xref:ef6/modeling/code-first/data-annotations)的*索引*區段。</span><span class="sxs-lookup"><span data-stu-id="1b003-151">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](xref:ef6/modeling/code-first/data-annotations).</span></span> <span data-ttu-id="1b003-152">這包括自訂索引名稱、建立唯一索引，以及建立多資料行索引。</span><span class="sxs-lookup"><span data-stu-id="1b003-152">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="1b003-153">您可以在單一屬性上指定多個索引注釋，方法是將 **IndexAttribute** 的陣列傳遞至 **IndexAnnotation**的函式。</span><span class="sxs-lookup"><span data-stu-id="1b003-153">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="1b003-154">指定不將 CLR 屬性對應到資料庫中的資料行</span><span class="sxs-lookup"><span data-stu-id="1b003-154">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="1b003-155">下列範例顯示如何指定 CLR 型別上的屬性未對應到資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="1b003-155">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="1b003-156">將 CLR 屬性對應到資料庫中的特定資料行</span><span class="sxs-lookup"><span data-stu-id="1b003-156">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="1b003-157">下列範例會將名稱 CLR 屬性對應至 DepartmentName 資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="1b003-157">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="1b003-158">重新命名模型中未定義的外鍵</span><span class="sxs-lookup"><span data-stu-id="1b003-158">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="1b003-159">如果您選擇不要在 CLR 類型上定義外鍵，但想要指定它在資料庫中應有的名稱，請執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="1b003-159">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="1b003-160">設定字串屬性是否支援 Unicode 內容</span><span class="sxs-lookup"><span data-stu-id="1b003-160">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="1b003-161">依預設，字串是 SQL Server) 中的 Unicode (Nvarchar。</span><span class="sxs-lookup"><span data-stu-id="1b003-161">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="1b003-162">您可以使用 IsUnicode 方法來指定字串必須是 Varchar 類型。</span><span class="sxs-lookup"><span data-stu-id="1b003-162">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="1b003-163">設定資料庫資料行的資料類型</span><span class="sxs-lookup"><span data-stu-id="1b003-163">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="1b003-164">[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法可讓您對應到相同基本類型的不同標記法。</span><span class="sxs-lookup"><span data-stu-id="1b003-164">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="1b003-165">使用這個方法並不會讓您在執行時間執行任何資料轉換。</span><span class="sxs-lookup"><span data-stu-id="1b003-165">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="1b003-166">請注意，IsUnicode 是將資料行設定為 Varchar 的慣用方法，因為它是資料庫中立的。</span><span class="sxs-lookup"><span data-stu-id="1b003-166">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="1b003-167">設定複雜類型的屬性</span><span class="sxs-lookup"><span data-stu-id="1b003-167">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="1b003-168">有兩種方式可以設定複雜類型的純量屬性。</span><span class="sxs-lookup"><span data-stu-id="1b003-168">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="1b003-169">您可以呼叫 ComplexTypeConfiguration 上的屬性。</span><span class="sxs-lookup"><span data-stu-id="1b003-169">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="1b003-170">您也可以使用點標記法來存取複雜型別的屬性。</span><span class="sxs-lookup"><span data-stu-id="1b003-170">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="1b003-171">設定要當做開放式並行存取權杖使用的屬性</span><span class="sxs-lookup"><span data-stu-id="1b003-171">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="1b003-172">若要指定實體中的屬性工作表示並行存取權杖，您可以使用 ConcurrencyCheck 屬性或 IsConcurrencyToken 方法。</span><span class="sxs-lookup"><span data-stu-id="1b003-172">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="1b003-173">您也可以使用 IsRowVersion 方法，將屬性設定為資料庫中的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="1b003-173">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="1b003-174">將屬性設定為數據列版本時，會自動將此屬性設定為開放式並行存取權杖。</span><span class="sxs-lookup"><span data-stu-id="1b003-174">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="1b003-175">型別對應</span><span class="sxs-lookup"><span data-stu-id="1b003-175">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="1b003-176">指定類別為複雜類型</span><span class="sxs-lookup"><span data-stu-id="1b003-176">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="1b003-177">依照慣例，沒有指定主鍵的型別會被視為複雜型別。</span><span class="sxs-lookup"><span data-stu-id="1b003-177">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="1b003-178">在某些情況下，Code First 不會偵測到複雜型別 (例如，如果您有一個名為 ID 的屬性，但您不表示它是主鍵) 。</span><span class="sxs-lookup"><span data-stu-id="1b003-178">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="1b003-179">在這種情況下，您會使用流暢的 API 來明確指定類型為複雜類型。</span><span class="sxs-lookup"><span data-stu-id="1b003-179">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="1b003-180">指定不將 CLR 實體類型對應至資料庫中的資料表</span><span class="sxs-lookup"><span data-stu-id="1b003-180">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="1b003-181">下列範例示範如何將 CLR 型別對應至資料庫中的資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-181">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="1b003-182">將實體類型對應至資料庫中的特定資料表</span><span class="sxs-lookup"><span data-stu-id="1b003-182">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="1b003-183">部門的所有屬性都會對應到稱為 t_ 部門的資料表中的資料行。</span><span class="sxs-lookup"><span data-stu-id="1b003-183">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="1b003-184">您也可以指定架構名稱，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1b003-184">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="1b003-185">將每個階層的資料表對應 (TPH) 繼承</span><span class="sxs-lookup"><span data-stu-id="1b003-185">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="1b003-186">在 TPH 對應案例中，繼承階層架構中的所有類型都會對應至單一資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-186">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="1b003-187">鑒別子資料行是用來識別每個資料列的類型。</span><span class="sxs-lookup"><span data-stu-id="1b003-187">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="1b003-188">使用 Code First 建立模型時，TPH 是參與繼承階層之類型的預設策略。</span><span class="sxs-lookup"><span data-stu-id="1b003-188">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="1b003-189">根據預設，鑒別子資料行會加入至名稱為 "鑒別子" 的資料表，而階層中每個型別的 CLR 型別名稱會用在鑒別子值。</span><span class="sxs-lookup"><span data-stu-id="1b003-189">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="1b003-190">您可以使用流暢的 API 來修改預設行為。</span><span class="sxs-lookup"><span data-stu-id="1b003-190">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="1b003-191">將每個類型的資料表對應 (TPT) 繼承</span><span class="sxs-lookup"><span data-stu-id="1b003-191">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="1b003-192">在 TPT 對應案例中，所有類型都會對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-192">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="1b003-193">單獨屬於基底型別 (Base Type) 或衍生型別 (Derived Type) 的屬性會儲存在對應至該型別的資料表中。</span><span class="sxs-lookup"><span data-stu-id="1b003-193">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="1b003-194">對應至衍生類型的資料表也會儲存外鍵，以聯結衍生資料表與基表。</span><span class="sxs-lookup"><span data-stu-id="1b003-194">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="1b003-195">將每個具體的資料表對應 (TPC) 繼承</span><span class="sxs-lookup"><span data-stu-id="1b003-195">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="1b003-196">在 TPC 對應案例中，階層中的所有非抽象類別型都會對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-196">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="1b003-197">對應至衍生類別的資料表與對應至資料庫中基類的資料表沒有任何關聯性。</span><span class="sxs-lookup"><span data-stu-id="1b003-197">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="1b003-198">類別的所有屬性（包括繼承的屬性）都會對應到對應資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="1b003-198">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="1b003-199">呼叫 MapInheritedProperties 方法來設定每個衍生型別。</span><span class="sxs-lookup"><span data-stu-id="1b003-199">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="1b003-200">MapInheritedProperties 會將繼承自基類的所有屬性重新對應至衍生類別之資料表中的新資料行。</span><span class="sxs-lookup"><span data-stu-id="1b003-200">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="1b003-201">請注意，由於參與 TPC 繼承階層的資料表不會共用主鍵，因此，如果您的資料庫產生值具有相同的識別種子，則在對應至子類別的資料表中插入時，將會有重複的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="1b003-201">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="1b003-202">若要解決這個問題，您可以為每個資料表指定不同的初始種子值，或在主要索引鍵屬性上關閉身分識別。</span><span class="sxs-lookup"><span data-stu-id="1b003-202">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="1b003-203">使用 Code First 時，身分識別是整數索引鍵屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="1b003-203">Identity is the default value for integer key properties when working with Code First.</span></span>  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="1b003-204">將實體類型的屬性對應至資料庫中的多個資料表 (實體分割) </span><span class="sxs-lookup"><span data-stu-id="1b003-204">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="1b003-205">實體分割可讓實體類型的屬性分散到多個資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-205">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="1b003-206">在下列範例中，部門實體會分割成兩個數據表：部門和 DepartmentDetails。</span><span class="sxs-lookup"><span data-stu-id="1b003-206">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="1b003-207">實體分割會使用多個對應方法呼叫，將屬性子集對應至特定的資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-207">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="1b003-208">將多個實體類型對應至資料庫中的一個資料表， (資料表分割) </span><span class="sxs-lookup"><span data-stu-id="1b003-208">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="1b003-209">下列範例會將共用主要索引鍵的兩個實體類型對應到一個資料表。</span><span class="sxs-lookup"><span data-stu-id="1b003-209">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="1b003-210">將實體類型對應至插入/更新/刪除預存程式 (EF6 之後) </span><span class="sxs-lookup"><span data-stu-id="1b003-210">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="1b003-211">從 EF6 開始，您可以對應實體以使用插入更新和刪除的預存程式。</span><span class="sxs-lookup"><span data-stu-id="1b003-211">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="1b003-212">如需詳細資訊，請參閱 [Code First Insert/Update/Delete 預存程式](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)。</span><span class="sxs-lookup"><span data-stu-id="1b003-212">For more details see, [Code First Insert/Update/Delete Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="1b003-213">範例中使用的模型</span><span class="sxs-lookup"><span data-stu-id="1b003-213">Model Used in Samples</span></span>  

<span data-ttu-id="1b003-214">下列 Code First 模型用於此頁面上的範例。</span><span class="sxs-lookup"><span data-stu-id="1b003-214">The following Code First model is used for the samples on this page.</span></span>  

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
