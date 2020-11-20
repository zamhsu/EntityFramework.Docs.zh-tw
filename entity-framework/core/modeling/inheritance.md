---
title: 繼承-EF Core
description: 如何使用 Entity Framework Core 設定實體類型繼承
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 33429bbc4a9941ff8ea98a8f99cc652c8ea26455
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003623"
---
# <a name="inheritance"></a><span data-ttu-id="55179-103">繼承</span><span class="sxs-lookup"><span data-stu-id="55179-103">Inheritance</span></span>

<span data-ttu-id="55179-104">EF 可將 .NET 型別階層對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="55179-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="55179-105">這可讓您照常以程式碼撰寫您的 .NET 實體、使用基底和衍生型別，而且 EF 可順暢地建立適當的資料庫架構、問題查詢等。如何對應類型階層的實際詳細資料與提供者相關;此頁面描述關係資料庫內容中的繼承支援。</span><span class="sxs-lookup"><span data-stu-id="55179-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="55179-106">實體類型階層對應</span><span class="sxs-lookup"><span data-stu-id="55179-106">Entity type hierarchy mapping</span></span>

<span data-ttu-id="55179-107">依照慣例，EF 將不會自動掃描基底或衍生類型;這表示，如果您想要對應階層中的 CLR 型別，就必須在模型上明確指定該型別。</span><span class="sxs-lookup"><span data-stu-id="55179-107">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="55179-108">例如，只指定階層的基底類型，並不會造成 EF Core 隱含地包含其所有子類型。</span><span class="sxs-lookup"><span data-stu-id="55179-108">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="55179-109">下列範例會公開及其子類別的 DbSet `Blog` `RssBlog` 。</span><span class="sxs-lookup"><span data-stu-id="55179-109">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="55179-110">如果 `Blog` 有任何其他子類別，則不會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="55179-110">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="55179-111">使用 TPH 對應時，資料庫資料行會自動成為可為 null 的資料行。</span><span class="sxs-lookup"><span data-stu-id="55179-111">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="55179-112">例如，資料 `RssUrl` 行可為 null，因為一般 `Blog` 實例沒有該屬性。</span><span class="sxs-lookup"><span data-stu-id="55179-112">For example, the `RssUrl` column is nullable because regular `Blog` instances do not have that property.</span></span>

<span data-ttu-id="55179-113">如果您不想要針對階層 `DbSet` 中的一或多個實體公開，您也可以使用流暢的 API，以確保它們包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="55179-113">If you don't want to expose a `DbSet` for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="55179-114">如果您不依賴慣例，則可以使用明確地指定基底類型 `HasBaseType` 。</span><span class="sxs-lookup"><span data-stu-id="55179-114">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="55179-115">您也可以使用 `.HasBaseType((Type)null)` 從階層中移除實體類型。</span><span class="sxs-lookup"><span data-stu-id="55179-115">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="table-per-hierarchy-and-discriminator-configuration"></a><span data-ttu-id="55179-116">每個階層的資料表和鑒別子設定</span><span class="sxs-lookup"><span data-stu-id="55179-116">Table-per-hierarchy and discriminator configuration</span></span>

<span data-ttu-id="55179-117">根據預設，EF 會使用 *每個* 階層的資料表來對應繼承 (TPH) 模式。</span><span class="sxs-lookup"><span data-stu-id="55179-117">By default, EF maps the inheritance using the *table-per-hierarchy* (TPH) pattern.</span></span> <span data-ttu-id="55179-118">TPH 使用單一資料表來儲存階層中所有類型的資料，而鑒別子資料行會用來識別每個資料列所代表的類型。</span><span class="sxs-lookup"><span data-stu-id="55179-118">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="55179-119">上述模型會對應至下列資料庫架構 (請注意隱含建立的資料 `Discriminator` 行，以識別 `Blog` 每個資料列) 中所儲存的型別。</span><span class="sxs-lookup"><span data-stu-id="55179-119">The model above is mapped to the following database schema (note the implicitly created `Discriminator` column, which identifies which type of `Blog` is stored in each row).</span></span>

![使用每個階層的資料表模式查詢 Blog 實體階層之結果的螢幕擷取畫面](_static/inheritance-tph-data.png)

<span data-ttu-id="55179-121">您可以設定鑒別子資料行的名稱和類型，以及用來識別階層中每一種類型的值：</span><span class="sxs-lookup"><span data-stu-id="55179-121">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="55179-122">在上述範例中，EF 在階層的基底實體上，隱含地將鑒別子新增為 [陰影屬性](xref:core/modeling/shadow-properties) 。</span><span class="sxs-lookup"><span data-stu-id="55179-122">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="55179-123">您可以設定此屬性，就像任何其他屬性一樣：</span><span class="sxs-lookup"><span data-stu-id="55179-123">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="55179-124">最後，鑒別子也可以對應至實體中的一般 .NET 屬性：</span><span class="sxs-lookup"><span data-stu-id="55179-124">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

<span data-ttu-id="55179-125">當查詢使用 TPH 模式的衍生實體時，EF Core 會在查詢中的鑒別子資料行上新增述詞。</span><span class="sxs-lookup"><span data-stu-id="55179-125">When querying for derived entities, which use the TPH pattern, EF Core adds a predicate over discriminator column in the query.</span></span> <span data-ttu-id="55179-126">此篩選準則可確保不會針對基底類型或不在結果中的同級類型取得任何額外的資料列。</span><span class="sxs-lookup"><span data-stu-id="55179-126">This filter makes sure that we don't get any additional rows for base types or sibling types not in the result.</span></span> <span data-ttu-id="55179-127">因為查詢基底實體將會取得階層中所有實體的結果，所以會略過基底實體類型的篩選述詞。</span><span class="sxs-lookup"><span data-stu-id="55179-127">This filter predicate is skipped for the base entity type since querying for the base entity will get results for all the entities in the hierarchy.</span></span> <span data-ttu-id="55179-128">從查詢具體化結果時，如果我們遇到不會對應到模型中任何實體類型的鑒別子值，我們會擲回例外狀況，因為我們不知道如何具體化結果。</span><span class="sxs-lookup"><span data-stu-id="55179-128">When materializing results from a query, if we come across a discriminator value, which isn't mapped to any entity type in the model, we throw an exception since we don't know how to materialize the results.</span></span> <span data-ttu-id="55179-129">只有當您的資料庫包含具有鑒別子值的資料列時，不會在 EF 模型中對應，才會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="55179-129">This error only occurs if your database contains rows with discriminator values, which aren't mapped in the EF model.</span></span> <span data-ttu-id="55179-130">如果您有這類資料，您可以將 EF Core 模型中的鑒別子對應標示為 [不完整]，表示我們應該一律加入篩選述詞，以便查詢階層中的任何類型。</span><span class="sxs-lookup"><span data-stu-id="55179-130">If you have such data, then you can mark the discriminator mapping in EF Core model as incomplete to indicate that we should always add filter predicate for querying any type in the hierarchy.</span></span> <span data-ttu-id="55179-131">`IsComplete(false)` 在鑒別子設定上呼叫會將對應標示為不完整。</span><span class="sxs-lookup"><span data-stu-id="55179-131">`IsComplete(false)` call on the discriminator configuration marks the mapping to be incomplete.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a><span data-ttu-id="55179-132">共用資料行</span><span class="sxs-lookup"><span data-stu-id="55179-132">Shared columns</span></span>

<span data-ttu-id="55179-133">依預設，當階層中有兩個同級實體類型的屬性具有相同名稱時，它們會對應至兩個不同的資料行。</span><span class="sxs-lookup"><span data-stu-id="55179-133">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="55179-134">但是，如果其型別相同，則可以對應到相同的資料庫資料行：</span><span class="sxs-lookup"><span data-stu-id="55179-134">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a><span data-ttu-id="55179-135">每一類型的資料表設定</span><span class="sxs-lookup"><span data-stu-id="55179-135">Table-per-type configuration</span></span>

> [!NOTE]
> <span data-ttu-id="55179-136">EF Core 5.0 中引進了每一類型的 (TPT) 功能的資料表。</span><span class="sxs-lookup"><span data-stu-id="55179-136">The table-per-type (TPT) feature was introduced in EF Core 5.0.</span></span> <span data-ttu-id="55179-137">EF6 支援每個具體的資料表類型 (TPC) ，但 EF Core 尚未支援。</span><span class="sxs-lookup"><span data-stu-id="55179-137">Table-per-concrete-type (TPC) is supported by EF6, but is not yet supported by EF Core.</span></span>

<span data-ttu-id="55179-138">在 TPT 對應模式中，所有類型都會對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="55179-138">In the TPT mapping pattern, all the types are mapped to individual tables.</span></span> <span data-ttu-id="55179-139">單獨屬於基底型別 (Base Type) 或衍生型別 (Derived Type) 的屬性會儲存在對應至該型別的資料表中。</span><span class="sxs-lookup"><span data-stu-id="55179-139">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="55179-140">對應至衍生類型的資料表也會儲存外鍵，以聯結衍生資料表與基表。</span><span class="sxs-lookup"><span data-stu-id="55179-140">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

<span data-ttu-id="55179-141">EF 會為上述模型建立下列資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="55179-141">EF will create the following database schema for the model above.</span></span>

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> <span data-ttu-id="55179-142">如果重新命名 primary key 條件約束，則新名稱將會套用至對應至階層的所有資料表，未來 EF 版本將允許在 [問題 19970](https://github.com/dotnet/efcore/issues/19970) 修正時，只針對特定資料表重新命名條件約束。</span><span class="sxs-lookup"><span data-stu-id="55179-142">If the primary key constraint is renamed the new name will be applied to all the tables mapped to the hierarchy, future EF versions will allow renaming the constraint only for a particular table when [issue 19970](https://github.com/dotnet/efcore/issues/19970) is fixed.</span></span>

<span data-ttu-id="55179-143">如果您使用大量設定，則可以藉由呼叫來抓取特定資料表的資料行名稱 <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> 。</span><span class="sxs-lookup"><span data-stu-id="55179-143">If you are employing bulk configuration you can retrieve the column name for a specific table by calling <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
