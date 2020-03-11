---
title: 流暢的 API-設定和對應屬性和類型-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
ms.openlocfilehash: 7371cc99142ccf8fc6bea237d7d58d1e67fcecec
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419063"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="2e034-102">流暢的 API-設定和對應屬性和類型</span><span class="sxs-lookup"><span data-stu-id="2e034-102">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="2e034-103">使用 Entity Framework 時 Code First 預設行為是使用內建至 EF 的一組慣例，將 POCO 類別對應至資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-103">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="2e034-104">不過，有時候您不能或不想要遵循這些慣例，也不需要將實體對應至慣例所規定的其他專案。</span><span class="sxs-lookup"><span data-stu-id="2e034-104">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="2e034-105">有兩種主要方式可將 EF 設定為使用慣例以外的專案，即[附注](~/ef6/modeling/code-first/data-annotations.md)或 EFs Fluent API。</span><span class="sxs-lookup"><span data-stu-id="2e034-105">There are two main ways you can configure EF to use something other than conventions, namely [annotations](~/ef6/modeling/code-first/data-annotations.md) or EFs fluent API.</span></span> <span data-ttu-id="2e034-106">批註只涵蓋 Fluent API 功能的子集，因此有一些無法使用注釋達成的對應案例。</span><span class="sxs-lookup"><span data-stu-id="2e034-106">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="2e034-107">本文的設計目的是要示範如何使用 Fluent API 來設定屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-107">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="2e034-108">Code first Fluent API 最常透過覆寫衍生[DbCoNtext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)上的[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法來存取。</span><span class="sxs-lookup"><span data-stu-id="2e034-108">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="2e034-109">下列範例的設計目的是要示範如何使用流暢的 api 來執行各種工作，並可讓您將程式碼複製並加以自訂，以符合您的模型，如果您想要查看可以用原樣使用的模型，則會在本文結尾處提供。</span><span class="sxs-lookup"><span data-stu-id="2e034-109">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="2e034-110">全模型設定</span><span class="sxs-lookup"><span data-stu-id="2e034-110">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="2e034-111">預設架構（EF6 開始）</span><span class="sxs-lookup"><span data-stu-id="2e034-111">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="2e034-112">從 EF6 開始，您可以在 DbModelBuilder 上使用 HasDefaultSchema 方法，以指定要用於所有資料表、預存程式等的資料庫架構。針對您明確設定不同架構的任何物件，將會覆寫此預設設定。</span><span class="sxs-lookup"><span data-stu-id="2e034-112">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="2e034-113">自訂慣例（EF6 和更新版本）</span><span class="sxs-lookup"><span data-stu-id="2e034-113">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="2e034-114">從 EF6 開始，您可以建立自己的慣例來補充 Code First 中包含的慣例。</span><span class="sxs-lookup"><span data-stu-id="2e034-114">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="2e034-115">如需詳細資訊，請參閱[自訂 Code First 慣例](~/ef6/modeling/code-first/conventions/custom.md)。</span><span class="sxs-lookup"><span data-stu-id="2e034-115">For more details, see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="2e034-116">屬性對應</span><span class="sxs-lookup"><span data-stu-id="2e034-116">Property Mapping</span></span>  

<span data-ttu-id="2e034-117">[屬性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法是用來設定屬於實體或複雜類型之每個屬性的屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-117">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="2e034-118">屬性方法是用來取得指定屬性的設定物件。</span><span class="sxs-lookup"><span data-stu-id="2e034-118">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="2e034-119">Configuration 物件上的選項是所設定類型特有的。例如，IsUnicode 僅適用于字串屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-119">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="2e034-120">設定主要金鑰</span><span class="sxs-lookup"><span data-stu-id="2e034-120">Configuring a Primary Key</span></span>  

<span data-ttu-id="2e034-121">主要金鑰的 Entity Framework 慣例是：</span><span class="sxs-lookup"><span data-stu-id="2e034-121">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="2e034-122">您的類別定義的屬性名稱為 "ID" 或 "Id"</span><span class="sxs-lookup"><span data-stu-id="2e034-122">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="2e034-123">或類別名稱，後面接著 "ID" 或 "Id"</span><span class="sxs-lookup"><span data-stu-id="2e034-123">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="2e034-124">若要將屬性明確設定為主要索引鍵，您可以使用 HasKey 方法。</span><span class="sxs-lookup"><span data-stu-id="2e034-124">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="2e034-125">在下列範例中，HasKey 方法是用來在 OfficeAssignment 型別上設定 InstructorID 的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="2e034-125">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="2e034-126">設定複合主要金鑰</span><span class="sxs-lookup"><span data-stu-id="2e034-126">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="2e034-127">下列範例會將 DepartmentID 和 Name 屬性設定為部門類型的複合主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="2e034-127">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="2e034-128">關閉數值主要金鑰的識別</span><span class="sxs-lookup"><span data-stu-id="2e034-128">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="2e034-129">下列範例會將 DepartmentID 屬性設定為 System.workflow.componentmodel.activity，以指出此值將不會由資料庫產生。</span><span class="sxs-lookup"><span data-stu-id="2e034-129">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="2e034-130">指定屬性的最大長度</span><span class="sxs-lookup"><span data-stu-id="2e034-130">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="2e034-131">在下列範例中，Name 屬性的長度不能超過50個字元。</span><span class="sxs-lookup"><span data-stu-id="2e034-131">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="2e034-132">如果您將此值的長度超過50個字元，就會收到[DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx)例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2e034-132">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="2e034-133">如果 Code First 從這個模型建立資料庫，它也會將 [名稱] 資料行的最大長度設定為50個字元。</span><span class="sxs-lookup"><span data-stu-id="2e034-133">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="2e034-134">將屬性設定為必要</span><span class="sxs-lookup"><span data-stu-id="2e034-134">Configuring the Property to be Required</span></span>  

<span data-ttu-id="2e034-135">在下列範例中，必須要有 Name 屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-135">In the following example, the Name property is required.</span></span> <span data-ttu-id="2e034-136">如果您未指定名稱，則會收到 DbEntityValidationException 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2e034-136">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="2e034-137">如果 Code First 從這個模型建立資料庫，則用來儲存此屬性的資料行通常會是不可為 null 的。</span><span class="sxs-lookup"><span data-stu-id="2e034-137">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="2e034-138">在某些情況下，即使屬性是必要的，資料庫中的資料行還是無法是不可為 null 的。</span><span class="sxs-lookup"><span data-stu-id="2e034-138">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="2e034-139">例如，使用多個類型的 TPH 繼承策略資料時，會儲存在單一資料表中。</span><span class="sxs-lookup"><span data-stu-id="2e034-139">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="2e034-140">如果衍生類型包含必要的屬性，則資料行不能設為不可為 null，因為不是階層中的所有類型都有這個屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-140">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="2e034-141">設定一或多個屬性的索引</span><span class="sxs-lookup"><span data-stu-id="2e034-141">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="2e034-142">**Ef 6.1 僅適用**于 Entity Framework 6.1 中引進的索引屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-142">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="2e034-143">如果您使用較舊的版本，本節中的資訊將不適用。</span><span class="sxs-lookup"><span data-stu-id="2e034-143">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="2e034-144">流暢的 API 原本不支援建立索引，但您可以透過流暢的 API 使用**IndexAttribute**的支援。</span><span class="sxs-lookup"><span data-stu-id="2e034-144">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="2e034-145">索引屬性的處理方式是在模型上包含模型注釋，然後在管線中稍後再轉換成資料庫中的索引。</span><span class="sxs-lookup"><span data-stu-id="2e034-145">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="2e034-146">您可以使用流暢的 API 手動新增這些相同的注釋。</span><span class="sxs-lookup"><span data-stu-id="2e034-146">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="2e034-147">若要這麼做，最簡單的方法是建立**IndexAttribute**的實例，其中包含新索引的所有設定。</span><span class="sxs-lookup"><span data-stu-id="2e034-147">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="2e034-148">接著，您可以建立**IndexAnnotation**的實例，這是 EF 特定型別，會將**IndexAttribute**設定轉換成可儲存在 EF 模型上的模型注釋。</span><span class="sxs-lookup"><span data-stu-id="2e034-148">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="2e034-149">然後，這些可以在流暢的 API 上傳遞至**HasColumnAnnotation**方法，並指定批註的名稱**索引**。</span><span class="sxs-lookup"><span data-stu-id="2e034-149">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="2e034-150">如需**IndexAttribute**中可用設定的完整清單，請參閱[Code First 資料批註](~/ef6/modeling/code-first/data-annotations.md)的*索引*一節。</span><span class="sxs-lookup"><span data-stu-id="2e034-150">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](~/ef6/modeling/code-first/data-annotations.md).</span></span> <span data-ttu-id="2e034-151">這包括自訂索引名稱、建立唯一索引，以及建立多重資料行索引。</span><span class="sxs-lookup"><span data-stu-id="2e034-151">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="2e034-152">您可以在單一屬性上指定多個索引注釋，方法是將**IndexAttribute**的陣列傳遞給**IndexAnnotation**的函式。</span><span class="sxs-lookup"><span data-stu-id="2e034-152">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="2e034-153">指定不要將 CLR 屬性對應至資料庫中的資料行</span><span class="sxs-lookup"><span data-stu-id="2e034-153">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="2e034-154">下列範例顯示如何指定 CLR 類型上的屬性未對應至資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="2e034-154">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="2e034-155">將 CLR 屬性對應至資料庫中的特定資料行</span><span class="sxs-lookup"><span data-stu-id="2e034-155">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="2e034-156">下列範例會將 Name CLR 屬性對應至 DepartmentName 資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="2e034-156">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="2e034-157">重新命名模型中未定義的外鍵</span><span class="sxs-lookup"><span data-stu-id="2e034-157">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="2e034-158">如果您選擇不要在 CLR 類型上定義外鍵，但想要指定它在資料庫中應有的名稱，請執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="2e034-158">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="2e034-159">設定字串屬性是否支援 Unicode 內容</span><span class="sxs-lookup"><span data-stu-id="2e034-159">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="2e034-160">根據預設，字串為 Unicode （在 SQL Server 中為 Nvarchar）。</span><span class="sxs-lookup"><span data-stu-id="2e034-160">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="2e034-161">您可以使用 IsUnicode 方法來指定字串應為 Varchar 類型。</span><span class="sxs-lookup"><span data-stu-id="2e034-161">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="2e034-162">設定資料庫資料行的資料類型</span><span class="sxs-lookup"><span data-stu-id="2e034-162">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="2e034-163">[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法可讓您對應至相同基本類型的不同標記法。</span><span class="sxs-lookup"><span data-stu-id="2e034-163">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="2e034-164">使用這個方法並不會讓您在執行時間執行資料的任何轉換。</span><span class="sxs-lookup"><span data-stu-id="2e034-164">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="2e034-165">請注意，IsUnicode 是將資料行設定為 Varchar 的慣用方式，因為它與資料庫無關。</span><span class="sxs-lookup"><span data-stu-id="2e034-165">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="2e034-166">設定複雜類型的屬性</span><span class="sxs-lookup"><span data-stu-id="2e034-166">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="2e034-167">有兩種方式可以在複雜型別上設定純量屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-167">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="2e034-168">您可以在 ComplexTypeConfiguration 上呼叫屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-168">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="2e034-169">您也可以使用點標記法來存取複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="2e034-169">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="2e034-170">設定要當做開放式並行存取權杖使用的屬性</span><span class="sxs-lookup"><span data-stu-id="2e034-170">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="2e034-171">若要指定實體中的屬性代表並行標記，您可以使用 ConcurrencyCheck 屬性或 IsConcurrencyToken 方法。</span><span class="sxs-lookup"><span data-stu-id="2e034-171">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="2e034-172">您也可以使用 IsRowVersion 方法，將屬性設定為資料庫中的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="2e034-172">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="2e034-173">將屬性設為數據列版本會自動將它設定為開放式並行存取權杖。</span><span class="sxs-lookup"><span data-stu-id="2e034-173">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="2e034-174">類型對應</span><span class="sxs-lookup"><span data-stu-id="2e034-174">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="2e034-175">指定類別為複雜類型</span><span class="sxs-lookup"><span data-stu-id="2e034-175">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="2e034-176">依照慣例，未指定主鍵的型別會被視為複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2e034-176">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="2e034-177">在某些情況下，Code First 不會偵測到複雜型別（例如，如果您有一個名為 ID 的屬性，但不表示它是主要索引鍵）。</span><span class="sxs-lookup"><span data-stu-id="2e034-177">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="2e034-178">在這種情況下，您可以使用 Fluent API 明確指定型別為複雜型別。</span><span class="sxs-lookup"><span data-stu-id="2e034-178">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="2e034-179">指定不要將 CLR 實體類型對應至資料庫中的資料表</span><span class="sxs-lookup"><span data-stu-id="2e034-179">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="2e034-180">下列範例示範如何排除 CLR 類型，使其無法對應至資料庫中的資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-180">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="2e034-181">將實體類型對應至資料庫中的特定資料表</span><span class="sxs-lookup"><span data-stu-id="2e034-181">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="2e034-182">部門的所有屬性都會對應到名為 t_ 部門的資料表中的資料行。</span><span class="sxs-lookup"><span data-stu-id="2e034-182">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="2e034-183">您也可以指定架構名稱，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e034-183">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="2e034-184">對應每個階層的資料表（TPH）繼承</span><span class="sxs-lookup"><span data-stu-id="2e034-184">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="2e034-185">在 TPH 對應案例中，繼承階層架構中的所有類型都會對應到單一資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-185">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="2e034-186">鑒別子資料行是用來識別每個資料列的類型。</span><span class="sxs-lookup"><span data-stu-id="2e034-186">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="2e034-187">使用 Code First 建立模型時，TPH 是參與繼承階層架構之類型的預設策略。</span><span class="sxs-lookup"><span data-stu-id="2e034-187">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="2e034-188">根據預設，會將鑒別子資料行加入至名稱為 "鑒別子" 的資料表，而階層中每個類型的 CLR 類型名稱會用於鑒別子值。</span><span class="sxs-lookup"><span data-stu-id="2e034-188">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="2e034-189">您可以使用 Fluent API 來修改預設行為。</span><span class="sxs-lookup"><span data-stu-id="2e034-189">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="2e034-190">對應每一類型的資料表（TPT）繼承</span><span class="sxs-lookup"><span data-stu-id="2e034-190">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="2e034-191">在 TPT 對應案例中，所有類型都會對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-191">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="2e034-192">單獨屬於基底型別 (Base Type) 或衍生型別 (Derived Type) 的屬性會儲存在對應至該型別的資料表中。</span><span class="sxs-lookup"><span data-stu-id="2e034-192">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="2e034-193">對應至衍生類型的資料表也會儲存聯結衍生資料表與基表的外鍵。</span><span class="sxs-lookup"><span data-stu-id="2e034-193">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="2e034-194">對應每個具體的資料表類別（TPC）繼承</span><span class="sxs-lookup"><span data-stu-id="2e034-194">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="2e034-195">在 TPC 對應案例中，階層中的所有非抽象類別型都會對應到個別資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-195">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="2e034-196">對應至衍生類別的資料表與對應至資料庫中基類的資料表沒有關聯性。</span><span class="sxs-lookup"><span data-stu-id="2e034-196">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="2e034-197">類別的所有屬性（包括繼承的屬性）都會對應至對應資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="2e034-197">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="2e034-198">呼叫 MapInheritedProperties 方法，以設定每個衍生類型。</span><span class="sxs-lookup"><span data-stu-id="2e034-198">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="2e034-199">MapInheritedProperties 會將繼承自基類的所有屬性重新對應到衍生類別之資料表中的新資料行。</span><span class="sxs-lookup"><span data-stu-id="2e034-199">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="2e034-200">請注意，由於參與 TPC 繼承階層架構的資料表不會共用主鍵，因此如果您的資料庫產生的值具有相同的身分識別種子，則在對應至子類別的資料表中插入時，將會有重複的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="2e034-200">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="2e034-201">若要解決這個問題，您可以為每個資料表指定不同的初始種子值，或在 primary key 屬性上關閉識別。</span><span class="sxs-lookup"><span data-stu-id="2e034-201">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="2e034-202">識別是使用 Code First 時，整數索引鍵屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="2e034-202">Identity is the default value for integer key properties when working with Code First.</span></span>  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="2e034-203">將實體類型的屬性對應至資料庫中的多個資料表（實體分割）</span><span class="sxs-lookup"><span data-stu-id="2e034-203">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="2e034-204">實體分割可讓實體類型的屬性散佈到多個資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-204">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="2e034-205">在下列範例中，部門實體會分割成兩個數據表：部門和 DepartmentDetails。</span><span class="sxs-lookup"><span data-stu-id="2e034-205">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="2e034-206">實體分割會使用多個對應方法呼叫，將屬性的子集對應至特定的資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-206">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="2e034-207">將多個實體類型對應至資料庫中的一個資料表（資料表分割）</span><span class="sxs-lookup"><span data-stu-id="2e034-207">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="2e034-208">下列範例會將共用主要索引鍵的兩個實體類型對應到一個資料表。</span><span class="sxs-lookup"><span data-stu-id="2e034-208">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="2e034-209">將實體類型對應到插入/更新/刪除預存程式（EF6）</span><span class="sxs-lookup"><span data-stu-id="2e034-209">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="2e034-210">從 EF6 開始，您可以對應實體來使用插入更新和刪除的預存程式。</span><span class="sxs-lookup"><span data-stu-id="2e034-210">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="2e034-211">如需詳細資訊，請參閱[Code First 插入/更新/刪除預存程式](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)。</span><span class="sxs-lookup"><span data-stu-id="2e034-211">For more details see, [Code First Insert/Update/Delete Stored Procedures](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="2e034-212">範例中使用的模型</span><span class="sxs-lookup"><span data-stu-id="2e034-212">Model Used in Samples</span></span>  

<span data-ttu-id="2e034-213">下列 Code First 模型用於此頁面上的範例。</span><span class="sxs-lookup"><span data-stu-id="2e034-213">The following Code First model is used for the samples on this page.</span></span>  

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
