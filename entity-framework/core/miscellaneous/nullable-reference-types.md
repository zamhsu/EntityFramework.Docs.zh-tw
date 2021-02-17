---
title: 使用可為 null 的參考型別-EF Core
description: '使用 Entity Framework Core 時，使用 c # 可為 null 的參考型別'
author: roji
ms.date: 09/09/2019
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 0747b1328458fbaddd9e3cca117e378bbad5b365
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543428"
---
# <a name="working-with-nullable-reference-types"></a><span data-ttu-id="ad044-103">使用可為 Null 的參考型別</span><span class="sxs-lookup"><span data-stu-id="ad044-103">Working with Nullable Reference Types</span></span>

<span data-ttu-id="ad044-104">C # 8 引進了一項新功能，稱為 [可為 null 的參考型別 (NRT) ](/dotnet/csharp/tutorials/nullable-reference-types)，可讓您標注參考型別，以指出其是否有效，以包含 null。</span><span class="sxs-lookup"><span data-stu-id="ad044-104">C# 8 introduced a new feature called [nullable reference types (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), allowing reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="ad044-105">如果您不熟悉這項功能，建議您閱讀 c # 檔，讓自己熟悉。</span><span class="sxs-lookup"><span data-stu-id="ad044-105">If you are new to this feature, it is recommended that make yourself familiar with it by reading the C# docs.</span></span>

<span data-ttu-id="ad044-106">本頁面介紹 EF Core 對可為 null 的參考型別的支援，並說明使用這些類型的最佳作法。</span><span class="sxs-lookup"><span data-stu-id="ad044-106">This page introduces EF Core's support for nullable reference types, and describes best practices for working with them.</span></span>

## <a name="required-and-optional-properties"></a><span data-ttu-id="ad044-107">必要和選用屬性</span><span class="sxs-lookup"><span data-stu-id="ad044-107">Required and optional properties</span></span>

<span data-ttu-id="ad044-108">必要和選用屬性的主要檔，以及它們與可為 null 參考型別的互動是 [必要的和選擇性的屬性](xref:core/modeling/entity-properties#required-and-optional-properties) 頁面。</span><span class="sxs-lookup"><span data-stu-id="ad044-108">The main documentation on required and optional properties and their interaction with nullable reference types is the [Required and Optional Properties](xref:core/modeling/entity-properties#required-and-optional-properties) page.</span></span> <span data-ttu-id="ad044-109">建議您一開始先閱讀該頁面。</span><span class="sxs-lookup"><span data-stu-id="ad044-109">It is recommended you start out by reading that page first.</span></span>

> [!NOTE]
> <span data-ttu-id="ad044-110">在現有的專案上啟用可為 null 的參考型別時，請務必注意：先前設定為選用的參考型別屬性現在會設定為必要，除非它們明確標注為可為 null。</span><span class="sxs-lookup"><span data-stu-id="ad044-110">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="ad044-111">管理關係資料庫架構時，這可能會導致產生遷移，進而改變資料庫資料行的 null 屬性。</span><span class="sxs-lookup"><span data-stu-id="ad044-111">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

## <a name="non-nullable-properties-and-initialization"></a><span data-ttu-id="ad044-112">不可為 null 的屬性和初始化</span><span class="sxs-lookup"><span data-stu-id="ad044-112">Non-nullable properties and initialization</span></span>

<span data-ttu-id="ad044-113">啟用可為 null 的參考型別時，c # 編譯器會針對任何未初始化的不可為 null 的屬性發出警告，因為這些屬性會包含 null。</span><span class="sxs-lookup"><span data-stu-id="ad044-113">When nullable reference types are enabled, the C# compiler emits warnings for any uninitialized non-nullable property, as these would contain null.</span></span> <span data-ttu-id="ad044-114">因此，不能使用撰寫實體類型的一般方式：</span><span class="sxs-lookup"><span data-stu-id="ad044-114">As a result, the following, common way of writing entity types cannot be used:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithWarning.cs?name=CustomerWithWarning&highlight=5-6)]

<span data-ttu-id="ad044-115">「函式系結」是一項實用的[技巧，可](xref:core/modeling/constructors)確保您的不可為 null 的屬性會初始化：</span><span class="sxs-lookup"><span data-stu-id="ad044-115">[Constructor binding](xref:core/modeling/constructors) is a useful technique to ensure that your non-nullable properties are initialized:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithConstructorBinding.cs?name=CustomerWithConstructorBinding&highlight=6-9)]

<span data-ttu-id="ad044-116">可惜的是，在某些情況下，無法選擇使用「函式系結」;例如，導覽屬性無法以這種方式初始化。</span><span class="sxs-lookup"><span data-stu-id="ad044-116">Unfortunately, in some scenarios constructor binding isn't an option; navigation properties, for example, cannot be initialized in this way.</span></span>

<span data-ttu-id="ad044-117">需要的導覽屬性有額外的難度：雖然特定的主體一律會有相依的，但特定的查詢可能不會載入它，視程式中該點的需求而定 ([查看載入資料) 的不同模式](xref:core/querying/related-data) 。</span><span class="sxs-lookup"><span data-stu-id="ad044-117">Required navigation properties present an additional difficulty: although a dependent will always exist for a given principal, it may or may not be loaded by a particular query, depending on the needs at that point in the program ([see the different patterns for loading data](xref:core/querying/related-data)).</span></span> <span data-ttu-id="ad044-118">同時，您不需要讓這些屬性成為可為 null 的，因為這會強制所有這些屬性的存取權檢查是否為 null，即使它們是必要的。</span><span class="sxs-lookup"><span data-stu-id="ad044-118">At the same time, it is undesirable to make these properties nullable, since that would force all access to them to check for null, even if they are required.</span></span>

<span data-ttu-id="ad044-119">處理這些案例的其中一種方式，是讓不可為 null 的屬性具有可為 null 的 [支援欄位](xref:core/modeling/backing-field)：</span><span class="sxs-lookup"><span data-stu-id="ad044-119">One way to deal with these scenarios, is to have a non-nullable property with a nullable [backing field](xref:core/modeling/backing-field):</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

<span data-ttu-id="ad044-120">因為導覽屬性不可為 null，所以會設定必要的導覽;只要正確載入流覽，就可以透過屬性存取相依的。</span><span class="sxs-lookup"><span data-stu-id="ad044-120">Since the navigation property is non-nullable, a required navigation is configured; and as long as the navigation is properly loaded, the dependent will be accessible via the property.</span></span> <span data-ttu-id="ad044-121">但是，如果在沒有先正確載入相關實體的情況下存取屬性，則會擲回 InvalidOperationException，因為 API 合約的使用不正確。</span><span class="sxs-lookup"><span data-stu-id="ad044-121">If, however, the property is accessed without first properly loading the related entity, an InvalidOperationException is thrown, since the API contract has been used incorrectly.</span></span> <span data-ttu-id="ad044-122">請注意，EF 必須設定為一律存取支援欄位，而不是屬性（property），因為它依賴能夠讀取值（即使未設定）。請參閱有關如何執行這項作業的 [支援欄位](xref:core/modeling/backing-field) 檔，並考慮指定 `PropertyAccessMode.Field` 以確定設定是否正確。</span><span class="sxs-lookup"><span data-stu-id="ad044-122">Note that EF must be configured to always access the backing field and not the property, as it relies on being able to read the value even when unset; consult the documentation on [backing fields](xref:core/modeling/backing-field) on how to do this, and consider specifying `PropertyAccessMode.Field` to make sure the configuration is correct.</span></span>

<span data-ttu-id="ad044-123">作為 terser 的替代方案，您可以直接使用容許運算子的協助將屬性初始化為 null， (！ ) ：</span><span class="sxs-lookup"><span data-stu-id="ad044-123">As a terser alternative, it is possible to simply initialize the property to null with the help of the null-forgiving operator (!):</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

<span data-ttu-id="ad044-124">由於程式設計錯誤的結果，將永遠不會觀察實際的 null 值，例如存取導覽屬性，而不事先適當地載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="ad044-124">An actual null value will never be observed except as a result of a programming bug, e.g. accessing the navigation property without properly loading the related entity beforehand.</span></span>

> [!NOTE]
> <span data-ttu-id="ad044-125">集合導覽（包含多個相關實體的參考）應該一律為不可為 null。</span><span class="sxs-lookup"><span data-stu-id="ad044-125">Collection navigations, which contain references to multiple related entities, should always be non-nullable.</span></span> <span data-ttu-id="ad044-126">空集合表示沒有相關的實體，但清單本身絕對不能是 null。</span><span class="sxs-lookup"><span data-stu-id="ad044-126">An empty collection means that no related entities exist, but the list itself should never be null.</span></span>

## <a name="dbcontext-and-dbset"></a><span data-ttu-id="ad044-127">DbCoNtext 和 DbSet</span><span class="sxs-lookup"><span data-stu-id="ad044-127">DbContext and DbSet</span></span>

<span data-ttu-id="ad044-128">在內容類型上具有未初始化 DbSet 屬性的常見作法也有問題，因為編譯器現在會發出警告。</span><span class="sxs-lookup"><span data-stu-id="ad044-128">The common practice of having uninitialized DbSet properties on context types is also problematic, as the compiler will now emit warnings for them.</span></span> <span data-ttu-id="ad044-129">您可以依照下列方式修正此問題：</span><span class="sxs-lookup"><span data-stu-id="ad044-129">This can be fixed as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

<span data-ttu-id="ad044-130">另一種策略是使用不可為 null 的 auto 屬性，但若要將它們初始化為 null，請使用 null-容許運算子 (！ ) 將編譯器警告解除回應。</span><span class="sxs-lookup"><span data-stu-id="ad044-130">Another strategy is to use non-nullable auto-properties, but to initialize them to null, using the null-forgiving operator (!) to silence the compiler warning.</span></span> <span data-ttu-id="ad044-131">DbCoNtext 基底函式可確保所有 DbSet 屬性都會初始化，而且永遠不會在其上觀察到 null。</span><span class="sxs-lookup"><span data-stu-id="ad044-131">The DbContext base constructor ensures that all DbSet properties will get initialized, and null will never be observed on them.</span></span>

## <a name="navigating-and-including-nullable-relationships"></a><span data-ttu-id="ad044-132">導覽及包含可為 null 的關聯性</span><span class="sxs-lookup"><span data-stu-id="ad044-132">Navigating and including nullable relationships</span></span>

<span data-ttu-id="ad044-133">處理選擇性關聯性時，可能會遇到可能不可能發生實際 null 參考例外狀況的編譯器警告。</span><span class="sxs-lookup"><span data-stu-id="ad044-133">When dealing with optional relationships, it's possible to encounter compiler warnings where an actual null reference exception would be impossible.</span></span> <span data-ttu-id="ad044-134">轉譯和執行 LINQ 查詢時，EF Core 保證如果選擇性的相關實體不存在，則會直接忽略它的任何導覽，而不會擲回。</span><span class="sxs-lookup"><span data-stu-id="ad044-134">When translating and executing your LINQ queries, EF Core guarantees that if an optional related entity does not exist, any navigation to it will simply be ignored, rather than throwing.</span></span> <span data-ttu-id="ad044-135">不過，編譯器並不知道此 EF Core 保證，並會產生警告，就像 LINQ 查詢是在記憶體中執行，並具有 LINQ to Objects 一樣。</span><span class="sxs-lookup"><span data-stu-id="ad044-135">However, the compiler is unaware of this EF Core guarantee, and produces warnings as if the LINQ query were executed in memory, with LINQ to Objects.</span></span> <span data-ttu-id="ad044-136">如此一來，就必須使用容許運算子 (！ ) 來通知編譯器，不可能有實際的 null 值：</span><span class="sxs-lookup"><span data-stu-id="ad044-136">As a result, it is necessary to use the null-forgiving operator (!) to inform the compiler that an actual null value isn't possible:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?name=Navigating)]

<span data-ttu-id="ad044-137">在選擇性導覽中包含多個層級的關聯性時，會發生類似的問題：</span><span class="sxs-lookup"><span data-stu-id="ad044-137">A similar issue occurs when including multiple levels of relationships across optional navigations:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?name=Including&highlight=2)]

<span data-ttu-id="ad044-138">如果您覺得自己這麼做很多，而且問題中的實體類型主要是 (或獨佔) 用於 EF Core 查詢中，請考慮將導覽屬性設為不可為 null，並透過流暢的 API 或資料批註將其設定為選擇性。</span><span class="sxs-lookup"><span data-stu-id="ad044-138">If you find yourself doing this a lot, and the entity types in question are predominantly (or exclusively) used in EF Core queries, consider making the navigation properties non-nullable, and to configure them as optional via the Fluent API or Data Annotations.</span></span> <span data-ttu-id="ad044-139">這會移除所有編譯器警告，同時保留關聯性。但是，如果您的實體是在 EF Core 之外進行，您可能會觀察到 null 值，但屬性會標注為不可為 null。</span><span class="sxs-lookup"><span data-stu-id="ad044-139">This will remove all compiler warnings while keeping the relationship optional; however, if your entities are traversed outside of EF Core, you may observe null values although the properties are annotated as non-nullable.</span></span>

## <a name="limitations"></a><span data-ttu-id="ad044-140">限制</span><span class="sxs-lookup"><span data-stu-id="ad044-140">Limitations</span></span>

* <span data-ttu-id="ad044-141">反向工程目前不支援 [c # 8 可為 null 的參考型別 (NRTs) ](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 一律會產生假設功能已關閉的 c # 程式碼。</span><span class="sxs-lookup"><span data-stu-id="ad044-141">Reverse engineering does not currently support [C# 8 nullable reference types (NRTs)](/dotnet/csharp/tutorials/nullable-reference-types): EF Core always generates C# code that assumes the feature is off.</span></span> <span data-ttu-id="ad044-142">例如，可為 null 的文字資料行將 scaffold 為類型的屬性，而不是使用 `string` `string?` 流暢的 API 或資料批註來設定是否需要屬性。</span><span class="sxs-lookup"><span data-stu-id="ad044-142">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="ad044-143">您可以編輯 scaffold 程式碼，並以 c # 可 null 性注釋取代這些程式碼。</span><span class="sxs-lookup"><span data-stu-id="ad044-143">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="ad044-144">問題 [#15520](https://github.com/dotnet/efcore/issues/15520)會追蹤可為 null 之參考型別的型別支援。</span><span class="sxs-lookup"><span data-stu-id="ad044-144">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/dotnet/efcore/issues/15520).</span></span>
* <span data-ttu-id="ad044-145">EF Core 的公用 API 介面尚未標注為可 null 性 (公用 API 為 "無警示" ) ，因此在開啟 NRT 功能時，有時很難使用。</span><span class="sxs-lookup"><span data-stu-id="ad044-145">EF Core's public API surface has not yet been annotated for nullability (the public API is "null-oblivious"), making it sometimes awkward to use when the NRT feature is turned on.</span></span> <span data-ttu-id="ad044-146">這特別包含 EF Core 所公開的非同步 LINQ 運算子，例如 [singleordefaultasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。</span><span class="sxs-lookup"><span data-stu-id="ad044-146">This notably includes the async LINQ operators exposed by EF Core, such as [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span> <span data-ttu-id="ad044-147">我們計畫在6.0 版中解決此情況。</span><span class="sxs-lookup"><span data-stu-id="ad044-147">We plan to address this for the 6.0 release.</span></span>
