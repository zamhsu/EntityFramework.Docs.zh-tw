---
title: 產生的值-EF Core
description: 如何在使用 Entity Framework Core 時設定屬性的值產生
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: e1eebd6c8b385cde71bc5ca556d8e348e2221494
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166039"
---
# <a name="generated-values"></a><span data-ttu-id="d8963-103">產生的值</span><span class="sxs-lookup"><span data-stu-id="d8963-103">Generated Values</span></span>

<span data-ttu-id="d8963-104">資料庫資料行的值會以各種不同的方式產生：主鍵資料行通常是自動遞增的整數、其他資料行有預設值或計算值等等。此頁面詳細說明使用 EF Core 產生設定值的各種模式。</span><span class="sxs-lookup"><span data-stu-id="d8963-104">Database columns can have their values generated in various ways: primary key columns are frequently auto-incrementing integers, other columns have default or computed values, etc. This page details various patterns for configuration value generation with EF Core.</span></span>

## <a name="default-values"></a><span data-ttu-id="d8963-105">預設值</span><span class="sxs-lookup"><span data-stu-id="d8963-105">Default values</span></span>

<span data-ttu-id="d8963-106">在關係資料庫中，可以使用預設值來設定資料行。如果插入的資料列沒有該資料行的值，則會使用預設值。</span><span class="sxs-lookup"><span data-stu-id="d8963-106">On relational databases, a column can be configured with a default value; if a row is inserted without a value for that column, the default value will be used.</span></span>

<span data-ttu-id="d8963-107">您可以設定屬性的預設值：</span><span class="sxs-lookup"><span data-stu-id="d8963-107">You can configure a default value on a property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

<span data-ttu-id="d8963-108">您也可以指定用來計算預設值的 SQL 片段：</span><span class="sxs-lookup"><span data-stu-id="d8963-108">You can also specify a SQL fragment that is used to calculate the default value:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a><span data-ttu-id="d8963-109">計算資料行</span><span class="sxs-lookup"><span data-stu-id="d8963-109">Computed columns</span></span>

<span data-ttu-id="d8963-110">在大部分的關係資料庫上，您可以將資料行設定成在資料庫中計算其值，通常會有運算式參考其他資料行：</span><span class="sxs-lookup"><span data-stu-id="d8963-110">On most relational databases, a column can be configured to have its value computed in the database, typically with an expression referring to other columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

<span data-ttu-id="d8963-111">上述程式會建立 *虛擬* 計算資料行，其值會在每次從資料庫提取時計算。</span><span class="sxs-lookup"><span data-stu-id="d8963-111">The above creates a *virtual* computed column, whose value is computed every time it is fetched from the database.</span></span> <span data-ttu-id="d8963-112">您也可以指定將計算資料行 *儲存* (有時稱為 *保存*) ，這表示它會在每次更新資料列時計算，並與一般資料行一起儲存在磁片上：</span><span class="sxs-lookup"><span data-stu-id="d8963-112">You may also specify that a computed column be *stored* (sometimes called *persisted*), meaning that it is computed on every update of the row, and is stored on disk alongside regular columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> <span data-ttu-id="d8963-113">EF Core 5.0 已新增建立預存計算資料行的支援。</span><span class="sxs-lookup"><span data-stu-id="d8963-113">Support for creating stored computed columns was added in EF Core 5.0.</span></span>

## <a name="primary-keys"></a><span data-ttu-id="d8963-114">主索引鍵</span><span class="sxs-lookup"><span data-stu-id="d8963-114">Primary keys</span></span>

<span data-ttu-id="d8963-115">依照慣例，如果應用程式未提供值，就會設定 short、int、long 或 Guid 類型的非複合主鍵，以產生插入的實體值。</span><span class="sxs-lookup"><span data-stu-id="d8963-115">By convention, non-composite primary keys of type short, int, long, or Guid are set up to have values generated for inserted entities if a value isn't provided by the application.</span></span> <span data-ttu-id="d8963-116">您的資料庫提供者通常會處理必要的設定;例如，SQL Server 中的數值主要索引鍵會自動設定為識別資料行。</span><span class="sxs-lookup"><span data-stu-id="d8963-116">Your database provider typically takes care of the necessary configuration; for example, a numeric primary key in SQL Server is automatically set up to be an IDENTITY column.</span></span>

<span data-ttu-id="d8963-117">如需詳細資訊，請 [參閱關於金鑰的檔](xref:core/modeling/keys)。</span><span class="sxs-lookup"><span data-stu-id="d8963-117">For more information, [see the documentation about keys](xref:core/modeling/keys).</span></span>

## <a name="explicitly-configuring-value-generation"></a><span data-ttu-id="d8963-118">明確設定產生值</span><span class="sxs-lookup"><span data-stu-id="d8963-118">Explicitly configuring value generation</span></span>

<span data-ttu-id="d8963-119">我們在上面看到 EF Core 會自動設定主鍵的值產生-但我們可能會想要對非索引鍵屬性進行相同的設定。</span><span class="sxs-lookup"><span data-stu-id="d8963-119">We saw above that EF Core automatically sets up value generation for primary keys - but we may want to do the same for non-key properties.</span></span> <span data-ttu-id="d8963-120">您可以設定任何屬性，將其值產生給插入的實體，如下所示：</span><span class="sxs-lookup"><span data-stu-id="d8963-120">You can configure any property to have its value generated for inserted entities as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d8963-121">資料批註</span><span class="sxs-lookup"><span data-stu-id="d8963-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="d8963-122">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d8963-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

<span data-ttu-id="d8963-123">同樣地，您可以將屬性設定為在新增或更新時產生其值：</span><span class="sxs-lookup"><span data-stu-id="d8963-123">Similarly, a property can be configured to have its value generated on add or update:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d8963-124">資料批註</span><span class="sxs-lookup"><span data-stu-id="d8963-124">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="d8963-125">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d8963-125">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> <span data-ttu-id="d8963-126">與預設值或計算資料行不同的是，我們不會指定產生值的 *方式* ;這取決於所使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="d8963-126">Unlike with default values or computed columns, we are not specifying *how* the values are to be generated; that depends on the database provider being used.</span></span> <span data-ttu-id="d8963-127">資料庫提供者可能會自動為某些屬性類型設定值產生，但其他專案可能會要求您手動設定值的產生方式。</span><span class="sxs-lookup"><span data-stu-id="d8963-127">Database providers may automatically set up value generation for some property types, but others may require you to manually set up how the value is generated.</span></span>
>
> <span data-ttu-id="d8963-128">例如，在 SQL Server 上，當 GUID 屬性設定為 add 時產生的值時，提供者會使用演算法來產生最佳的連續 GUID 值，以自動執行值產生用戶端。</span><span class="sxs-lookup"><span data-stu-id="d8963-128">For example, on SQL Server, when a GUID property is configured as value generated on add, the provider automatically performs value generation client-side, using an algorithm to generate optimal sequential GUID values.</span></span> <span data-ttu-id="d8963-129">不過， <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAdd%2A> 在 datetime 屬性上指定將沒有任何作用 ([請參閱下一節中的日期時間值產生](#datetime-value-generation)) 。</span><span class="sxs-lookup"><span data-stu-id="d8963-129">However, specifying <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAdd%2A> on a DateTime property will have no effect ([see the section below for DateTime value generation](#datetime-value-generation)).</span></span>
>
> <span data-ttu-id="d8963-130">同樣地，在新增或更新時所設定的 byte [] 屬性，以及標示為並行 token 的，都會設定 rowversion 資料類型，以便在資料庫中自動產生值。</span><span class="sxs-lookup"><span data-stu-id="d8963-130">Similarly, byte[] properties that are configured as generated on add or update and marked as concurrency tokens are set up with the rowversion data type, so that values are automatically generated in the database.</span></span> <span data-ttu-id="d8963-131">不過，指定 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAdd%2A> 不會有任何作用。</span><span class="sxs-lookup"><span data-stu-id="d8963-131">However, specifying <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAdd%2A> has no effect.</span></span>
>
> [!NOTE]
> <span data-ttu-id="d8963-132">根據所使用的資料庫提供者而定，值可能會由 EF 或資料庫中的用戶端產生。</span><span class="sxs-lookup"><span data-stu-id="d8963-132">Depending on the database provider being used, values may be generated client side by EF or in the database.</span></span> <span data-ttu-id="d8963-133">如果值是由資料庫所產生，則當您將實體新增至內容時，EF 可能會指派暫時值;這個暫存值接著會由資料庫產生的值取代 `SaveChanges()` 。</span><span class="sxs-lookup"><span data-stu-id="d8963-133">If the value is generated by the database, then EF may assign a temporary value when you add the entity to the context; this temporary value will then be replaced by the database generated value during `SaveChanges()`.</span></span> <span data-ttu-id="d8963-134">如需詳細資訊，請 [參閱臨時值上的](xref:core/change-tracking/explicit-tracking#temporary-values)檔。</span><span class="sxs-lookup"><span data-stu-id="d8963-134">For more information, [see the docs on temporary values](xref:core/change-tracking/explicit-tracking#temporary-values).</span></span>

## <a name="datetime-value-generation"></a><span data-ttu-id="d8963-135">日期/時間值產生</span><span class="sxs-lookup"><span data-stu-id="d8963-135">Date/time value generation</span></span>

<span data-ttu-id="d8963-136">常見的要求是具有資料庫資料行，其中包含第一次將資料行插入的日期/時間， (新增) 上所產生的值，或上次更新的日期/時間 (在新增或更新) 時所產生的值。</span><span class="sxs-lookup"><span data-stu-id="d8963-136">A common request is to have a database column which contains the date/time for when the column was first inserted (value generated on add), or for when it was last updated (value generated on add or update).</span></span> <span data-ttu-id="d8963-137">由於有各種策略可以達成此目的，EF Core 提供者通常不會自動為日期/時間資料行設定值產生，您必須自行設定。</span><span class="sxs-lookup"><span data-stu-id="d8963-137">As there are various strategies to do this, EF Core providers usually don't set up value generation automatically for date/time columns - you have to configure this yourself.</span></span>

### <a name="creation-timestamp"></a><span data-ttu-id="d8963-138">建立時間戳記</span><span class="sxs-lookup"><span data-stu-id="d8963-138">Creation timestamp</span></span>

<span data-ttu-id="d8963-139">將日期/時間資料行設定為具有資料列的建立時間戳記，通常只需使用適當的 SQL 函數來設定預設值。</span><span class="sxs-lookup"><span data-stu-id="d8963-139">Configuring a date/time column to have the creation timestamp of the row is usually a matter of configuring a default value with the appropriate SQL function.</span></span> <span data-ttu-id="d8963-140">例如，在 SQL Server，您可以使用下列內容：</span><span class="sxs-lookup"><span data-stu-id="d8963-140">For example, on SQL Server you may use the following:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

<span data-ttu-id="d8963-141">請務必選取適當的函式，因為有數個可能存在 (例如 `GETDATE()` `GETUTCDATE()`) 。</span><span class="sxs-lookup"><span data-stu-id="d8963-141">Be sure to select the appropriate function, as several may exist (e.g. `GETDATE()` vs. `GETUTCDATE()`).</span></span>

### <a name="update-timestamp"></a><span data-ttu-id="d8963-142">更新時間戳記</span><span class="sxs-lookup"><span data-stu-id="d8963-142">Update timestamp</span></span>

<span data-ttu-id="d8963-143">雖然儲存的計算資料行看起來像是管理上次更新時間戳記的良好解決方案，但資料庫通常不允許 `GETDATE()` 在計算資料行中指定函數。</span><span class="sxs-lookup"><span data-stu-id="d8963-143">Although stored computed columns seem like a good solution for managing last-updated timestamps, databases usually don't allow specifying functions such as `GETDATE()` in a computed column.</span></span> <span data-ttu-id="d8963-144">或者，您可以設定資料庫觸發程式來達成相同的效果：</span><span class="sxs-lookup"><span data-stu-id="d8963-144">As an alternative, you can set up a database trigger to achieve the same effect:</span></span>

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

<span data-ttu-id="d8963-145">如需建立觸發程式的 [相關資訊，請參閱在遷移中使用原始 SQL 的相關檔](xref:core/managing-schemas/migrations/managing#adding-raw-sql)。</span><span class="sxs-lookup"><span data-stu-id="d8963-145">For information on creating triggers, [see the documentation on using raw SQL in migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span></span>

## <a name="overriding-value-generation"></a><span data-ttu-id="d8963-146">覆寫產生值</span><span class="sxs-lookup"><span data-stu-id="d8963-146">Overriding value generation</span></span>

<span data-ttu-id="d8963-147">雖然已針對值產生設定屬性，但在許多情況下，您仍然可以明確地指定值。</span><span class="sxs-lookup"><span data-stu-id="d8963-147">Although a property is configured for value generation, in many cases you may still explicitly specify a value for it.</span></span> <span data-ttu-id="d8963-148">這是否會實際運作，取決於已設定的特定值產生機制;雖然您可以指定明確的值，而不是使用資料行的預設值，但是無法使用計算資料行來完成相同的工作。</span><span class="sxs-lookup"><span data-stu-id="d8963-148">Whether this will actually work depends on the specific value generation mechanism that has been configured; while you may specify an explicit value instead of using a column's default value, the same cannot be done with computed columns.</span></span>

<span data-ttu-id="d8963-149">若要以明確值覆寫值產生，只要將屬性設為任何不是該屬性之類型的 CLR 預設值的值， (for、for `null` `string` `0` `int` 、 `Guid.Empty` for、 `Guid` 等 ) 。</span><span class="sxs-lookup"><span data-stu-id="d8963-149">To override value generation with an explicit value, simply set the property to any value that is not the CLR default value for that property's type (`null` for `string`, `0` for `int`, `Guid.Empty` for `Guid`, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="d8963-150">依預設，嘗試將明確的值插入 SQL Server 身分識別會失敗; [如需因應措施，請參閱這些](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)檔。</span><span class="sxs-lookup"><span data-stu-id="d8963-150">Trying to insert explicit values into SQL Server IDENTITY fails by default; [see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span></span>

<span data-ttu-id="d8963-151">若要針對已設定為在新增或更新時產生之值的屬性提供明確的值，您也必須設定屬性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="d8963-151">To provide an explicit value for properties that have been configured as value generated on add or update, you must also configure the property as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a><span data-ttu-id="d8963-152">不產生任何值</span><span class="sxs-lookup"><span data-stu-id="d8963-152">No value generation</span></span>

<span data-ttu-id="d8963-153">除了上面所述的特定案例之外，屬性通常不會設定值產生;這表示應用程式一定要提供要儲存至資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="d8963-153">Apart from specific scenarios such as those described above, properties typically have no value generation configured; this means that it's up to the application to always supply a value to be saved to the database.</span></span> <span data-ttu-id="d8963-154">必須先將此值指派給新的實體，才能將其新增至內容。</span><span class="sxs-lookup"><span data-stu-id="d8963-154">This value must be assigned to new entities before they are added to the context.</span></span>

<span data-ttu-id="d8963-155">不過，在某些情況下，您可能會想要停用依慣例設定的值產生。</span><span class="sxs-lookup"><span data-stu-id="d8963-155">However, in some cases you may want to disable value generation that has been set up by convention.</span></span> <span data-ttu-id="d8963-156">例如，int 類型的主鍵通常會隱含地設定為值產生的 (例如 SQL Server) 上的識別欄位。</span><span class="sxs-lookup"><span data-stu-id="d8963-156">For example, a primary key of type int is usually implicitly configured as value-generated-on-add (e.g. identity column on SQL Server).</span></span> <span data-ttu-id="d8963-157">您可以透過下列方式停用此功能：</span><span class="sxs-lookup"><span data-stu-id="d8963-157">You can disable this via the following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d8963-158">資料批註</span><span class="sxs-lookup"><span data-stu-id="d8963-158">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="d8963-159">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d8963-159">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
