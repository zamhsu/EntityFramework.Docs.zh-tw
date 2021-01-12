---
title: 值比較子-EF Core
description: 使用值比較子來控制 EF Core 如何比較屬性值
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: 618341315de05f6efae8f43384809ed72226e18b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128507"
---
# <a name="value-comparers"></a><span data-ttu-id="69a3b-103">值比較子</span><span class="sxs-lookup"><span data-stu-id="69a3b-103">Value Comparers</span></span>

> [!NOTE]
> <span data-ttu-id="69a3b-104">這項功能是在 EF Core 3.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="69a3b-104">This feature was introduced in EF Core 3.0.</span></span>

> [!TIP]
> <span data-ttu-id="69a3b-105">您可以在 GitHub 上找到這份檔中的程式碼，做為可執行檔 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)。</span><span class="sxs-lookup"><span data-stu-id="69a3b-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="69a3b-106">背景</span><span class="sxs-lookup"><span data-stu-id="69a3b-106">Background</span></span>

<span data-ttu-id="69a3b-107">變更追蹤表示 EF Core 會自動判斷已載入的實體實例上應用程式所執行的變更，以便在呼叫時將這些變更儲存回資料庫 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="69a3b-107">Change tracking means that EF Core automatically determines what changes were performed by the application on a loaded entity instance, so that those changes can be saved back to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="69a3b-108">EF Core 通常會在從資料庫載入實例時製作實例的 *快照* 集，並將該快照集與向外傳遞至應用程式的實例進行 *比較* ，藉此執行此工作。</span><span class="sxs-lookup"><span data-stu-id="69a3b-108">EF Core usually performs this by taking a *snapshot* of the instance when it's loaded from the database, and *comparing* that snapshot to the instance handed out to the application.</span></span>

<span data-ttu-id="69a3b-109">EF Core 隨附內建邏輯來進行快照，並比較資料庫中使用的大部分標準型別，因此使用者通常不需要擔心這個主題。</span><span class="sxs-lookup"><span data-stu-id="69a3b-109">EF Core comes with built-in logic for snapshotting and comparing most standard types used in databases, so users don't usually need to worry about this topic.</span></span> <span data-ttu-id="69a3b-110">不過，當屬性透過 [值轉換器](xref:core/modeling/value-conversions)對應時，EF Core 必須對任意的使用者類型執行比較，這可能會很複雜。</span><span class="sxs-lookup"><span data-stu-id="69a3b-110">However, when a property is mapped through a [value converter](xref:core/modeling/value-conversions), EF Core needs to perform comparison on arbitrary user types, which may be complex.</span></span> <span data-ttu-id="69a3b-111">根據預設，EF Core 會使用類型所定義的預設相等比較 (例如 `Equals`) 的方法; 針對快照集，系統會複製值型別以產生快照集，而針對參考型別，則不會進行複製，而且會使用相同的實例作為快照集。</span><span class="sxs-lookup"><span data-stu-id="69a3b-111">By default, EF Core uses the default equality comparison defined by types (e.g. the `Equals` method); for snapshotting, value types are copied to produce the snapshot, while for reference types no copying occurs, and the same instance is used as the snapshot.</span></span>

<span data-ttu-id="69a3b-112">當內建的比較行為不適當時，使用者可以提供 *值比較* 子，其中包含快照的邏輯、比較和計算雜湊碼。</span><span class="sxs-lookup"><span data-stu-id="69a3b-112">In cases where the built-in comparison behavior isn't appropriate, users may provide a *value comparer*, which contains logic for snapshotting, comparing and calculating a hash code.</span></span> <span data-ttu-id="69a3b-113">例如，下列會將屬性的值轉換設定為 `List<int>` 將值轉換為資料庫中的 JSON 字串，並且也定義適當的值比較子：</span><span class="sxs-lookup"><span data-stu-id="69a3b-113">For example, the following sets up value conversion for `List<int>` property to be value converted to a JSON string in the database, and defines an appropriate value comparer as well:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="69a3b-114">如需詳細資料，請參閱下方的可變動 [類別](#mutable-classes) 。</span><span class="sxs-lookup"><span data-stu-id="69a3b-114">See [mutable classes](#mutable-classes) below for further details.</span></span>

<span data-ttu-id="69a3b-115">請注意，在解析關聯性時，判斷兩個索引鍵值是否相同，也會使用值比較子。以下將說明這一點。</span><span class="sxs-lookup"><span data-stu-id="69a3b-115">Note that value comparers are also used when determining whether two key values are the same when resolving relationships; this is explained below.</span></span>

## <a name="shallow-vs-deep-comparison"></a><span data-ttu-id="69a3b-116">淺層與深層比較</span><span class="sxs-lookup"><span data-stu-id="69a3b-116">Shallow vs. deep comparison</span></span>

<span data-ttu-id="69a3b-117">針對較小、不變的實值型別（例如 `int` ），EF Core 的預設邏輯運作正常：當快照時，值會依原樣複製，並與型別內建的相等比較比較。</span><span class="sxs-lookup"><span data-stu-id="69a3b-117">For small, immutable value types such as `int`, EF Core's default logic works well: the value is copied as-is when snapshotted, and compared with the type's built-in equality comparison.</span></span> <span data-ttu-id="69a3b-118">在實您自己的值比較子時，請務必考慮深度或淺層比較 (，以及) 邏輯是否適當的快照。</span><span class="sxs-lookup"><span data-stu-id="69a3b-118">When implementing your own value comparer, it's important to consider whether deep or shallow comparison (and snapshotting) logic is appropriate.</span></span>

<span data-ttu-id="69a3b-119">請考慮位元組陣列，這可能會任意大。</span><span class="sxs-lookup"><span data-stu-id="69a3b-119">Consider byte arrays, which can be arbitrarily large.</span></span> <span data-ttu-id="69a3b-120">這些可以比較：</span><span class="sxs-lookup"><span data-stu-id="69a3b-120">These could be compared:</span></span>

* <span data-ttu-id="69a3b-121">依參考，因此只有在使用新的位元組陣列時，才會偵測到差異</span><span class="sxs-lookup"><span data-stu-id="69a3b-121">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="69a3b-122">依深層比較，會偵測到陣列中的位元組變化</span><span class="sxs-lookup"><span data-stu-id="69a3b-122">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="69a3b-123">根據預設，EF Core 會針對非索引鍵位元組陣列使用這些方法中的第一個。</span><span class="sxs-lookup"><span data-stu-id="69a3b-123">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span> <span data-ttu-id="69a3b-124">也就是說，只會比較參考，而且只有當現有的位元組陣列取代為新的位元組陣列時，才會偵測到變更。</span><span class="sxs-lookup"><span data-stu-id="69a3b-124">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span> <span data-ttu-id="69a3b-125">這是一項實用的決策，可避免複製整個陣列，並在執行時比較它們的位元組對位元組， <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 而將另一個影像取代為另一個影像的常見案例是以高效能的方式處理。</span><span class="sxs-lookup"><span data-stu-id="69a3b-125">This is a pragmatic decision that avoids copying entire arrays and comparing them byte-to-byte when executing <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, and the common scenario of replacing, say, one image with another is handled in a performant way.</span></span>

<span data-ttu-id="69a3b-126">另一方面，當使用位元組陣列來表示二進位索引鍵時，參考相等將無法運作，因為 FK 屬性不太可能會設定為與需要比較的 PK 屬性 _相同的實例_ 。</span><span class="sxs-lookup"><span data-stu-id="69a3b-126">On the other hand, reference equality would not work when byte arrays are used to represent binary keys, since it's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span> <span data-ttu-id="69a3b-127">因此，EF Core 對作為索引鍵的位元組陣列使用深層比較;這不太可能會造成嚴重的效能影響，因為二進位金鑰通常很短。</span><span class="sxs-lookup"><span data-stu-id="69a3b-127">Therefore, EF Core uses deep comparisons for byte arrays acting as keys; this is unlikely to have a big performance hit since binary keys are usually short.</span></span>

<span data-ttu-id="69a3b-128">請注意，所選的比較和快照式邏輯必須互相對應：深層比較需要深度快照才能正確運作。</span><span class="sxs-lookup"><span data-stu-id="69a3b-128">Note that the chosen comparison and snapshotting logic must correspond to each other: deep comparison requires deep snapshotting to function correctly.</span></span>

## <a name="simple-immutable-classes"></a><span data-ttu-id="69a3b-129">簡單不可變的類別</span><span class="sxs-lookup"><span data-stu-id="69a3b-129">Simple immutable classes</span></span>

<span data-ttu-id="69a3b-130">請考慮使用值轉換器來對應簡單、不可變類別的屬性。</span><span class="sxs-lookup"><span data-stu-id="69a3b-130">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="69a3b-131">此類型的屬性不需要特殊的比較或快照集，因為：</span><span class="sxs-lookup"><span data-stu-id="69a3b-131">Properties of this type do not need special comparisons or snapshots because:</span></span>

* <span data-ttu-id="69a3b-132">覆寫相等，讓不同的實例能夠正確地比較</span><span class="sxs-lookup"><span data-stu-id="69a3b-132">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="69a3b-133">此類型為不可變，因此沒有機會變更快照集值</span><span class="sxs-lookup"><span data-stu-id="69a3b-133">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="69a3b-134">因此在此情況下，EF Core 的預設行為就會正常運作。</span><span class="sxs-lookup"><span data-stu-id="69a3b-134">So in this case the default behavior of EF Core is fine as it is.</span></span>

## <a name="simple-immutable-structs"></a><span data-ttu-id="69a3b-135">簡單的不可變結構</span><span class="sxs-lookup"><span data-stu-id="69a3b-135">Simple immutable structs</span></span>

<span data-ttu-id="69a3b-136">簡單結構的對應也很簡單，且不需要任何特殊的比較子或快照。</span><span class="sxs-lookup"><span data-stu-id="69a3b-136">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="69a3b-137">EF Core 有內建的支援，可產生結構屬性的已編譯、成員比較。</span><span class="sxs-lookup"><span data-stu-id="69a3b-137">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="69a3b-138">這表示結構不需要覆寫 EF Core 的相等，但基於 [其他原因](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)，您可能還是會選擇這麼做。</span><span class="sxs-lookup"><span data-stu-id="69a3b-138">This means structs don't need to have equality overridden for EF Core, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="69a3b-139">此外，不需要特殊的快照，因為結構是固定的，而且一律會成員複製。</span><span class="sxs-lookup"><span data-stu-id="69a3b-139">Also, special snapshotting is not needed since structs are immutable and are always copied memberwise anyway.</span></span>
<span data-ttu-id="69a3b-140"> (這種情況也適用于可變結構，但 [應該要避免變動結構](/dotnet/csharp/write-safe-efficient-code)。 ) </span><span class="sxs-lookup"><span data-stu-id="69a3b-140">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

## <a name="mutable-classes"></a><span data-ttu-id="69a3b-141">可變類別</span><span class="sxs-lookup"><span data-stu-id="69a3b-141">Mutable classes</span></span>

<span data-ttu-id="69a3b-142">建議您盡可能使用不可變的類型 (類別或結構) 具有值轉換器。</span><span class="sxs-lookup"><span data-stu-id="69a3b-142">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="69a3b-143">這通常更有效率，且具有比使用可變類型更簡潔的語法。</span><span class="sxs-lookup"><span data-stu-id="69a3b-143">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="69a3b-144">不過，這是很常見的情況，就是使用應用程式無法變更之類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="69a3b-144">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="69a3b-145">例如，對應包含數位清單的屬性：</span><span class="sxs-lookup"><span data-stu-id="69a3b-145">For example, mapping a property containing a list of numbers:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="69a3b-146"><xref:System.Collections.Generic.List%601> 類別：</span><span class="sxs-lookup"><span data-stu-id="69a3b-146">The <xref:System.Collections.Generic.List%601> class:</span></span>

* <span data-ttu-id="69a3b-147">具有參考相等;包含相同值的兩個清單會視為不同。</span><span class="sxs-lookup"><span data-stu-id="69a3b-147">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="69a3b-148">是可變動的;您可以新增和移除清單中的值。</span><span class="sxs-lookup"><span data-stu-id="69a3b-148">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="69a3b-149">清單屬性上的一般值轉換可能會將清單轉換成 JSON 和從 JSON 轉換：</span><span class="sxs-lookup"><span data-stu-id="69a3b-149">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

### <a name="ef-core-50"></a>[<span data-ttu-id="69a3b-150">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="69a3b-150">EF Core 5.0</span></span>](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[<span data-ttu-id="69a3b-151">較舊的版本</span><span class="sxs-lookup"><span data-stu-id="69a3b-151">Older versions</span></span>](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

<span data-ttu-id="69a3b-152">此函式 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 接受三個運算式：</span><span class="sxs-lookup"><span data-stu-id="69a3b-152">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructor accepts three expressions:</span></span>

* <span data-ttu-id="69a3b-153">檢查是否相等的運算式</span><span class="sxs-lookup"><span data-stu-id="69a3b-153">An expression for checking equality</span></span>
* <span data-ttu-id="69a3b-154">用來產生雜湊碼的運算式</span><span class="sxs-lookup"><span data-stu-id="69a3b-154">An expression for generating a hash code</span></span>
* <span data-ttu-id="69a3b-155">用來將值產生快照的運算式</span><span class="sxs-lookup"><span data-stu-id="69a3b-155">An expression to snapshot a value</span></span>

<span data-ttu-id="69a3b-156">在此情況下，藉由檢查數位序列是否相同來完成比較。</span><span class="sxs-lookup"><span data-stu-id="69a3b-156">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="69a3b-157">同樣地，雜湊程式碼是由相同的順序所建立。</span><span class="sxs-lookup"><span data-stu-id="69a3b-157">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="69a3b-158"> (請注意，這是可變動值的雜湊程式碼，因此可能 [會造成問題](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)。</span><span class="sxs-lookup"><span data-stu-id="69a3b-158">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="69a3b-159">如果您可以，請改用不可變的。 ) </span><span class="sxs-lookup"><span data-stu-id="69a3b-159">Be immutable instead if you can.)</span></span>

<span data-ttu-id="69a3b-160">使用複製清單來建立快照集 `ToList` 。</span><span class="sxs-lookup"><span data-stu-id="69a3b-160">The snapshot is created by cloning the list with `ToList`.</span></span>
<span data-ttu-id="69a3b-161">同樣地，只有在清單即將進行變化時，才需要這樣做。</span><span class="sxs-lookup"><span data-stu-id="69a3b-161">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="69a3b-162">如果可以，則改為不可變。</span><span class="sxs-lookup"><span data-stu-id="69a3b-162">Be immutable instead if you can.</span></span>

> [!NOTE]
> <span data-ttu-id="69a3b-163">值轉換器和比較子使用運算式而非簡單委派來建立。</span><span class="sxs-lookup"><span data-stu-id="69a3b-163">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="69a3b-164">這是因為 EF Core 會將這些運算式插入更複雜的運算式樹狀架構，然後再編譯到實體轉譯器委派中。</span><span class="sxs-lookup"><span data-stu-id="69a3b-164">This is because EF Core inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="69a3b-165">就概念而言，這與編譯器內嵌類似。</span><span class="sxs-lookup"><span data-stu-id="69a3b-165">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="69a3b-166">例如，簡單的轉換可能只是轉換中的編譯，而不是呼叫另一個方法來進行轉換。</span><span class="sxs-lookup"><span data-stu-id="69a3b-166">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>

## <a name="key-comparers"></a><span data-ttu-id="69a3b-167">索引鍵比較子</span><span class="sxs-lookup"><span data-stu-id="69a3b-167">Key comparers</span></span>

<span data-ttu-id="69a3b-168">背景區段涵蓋了為什麼按鍵比較可能需要特殊的語法。</span><span class="sxs-lookup"><span data-stu-id="69a3b-168">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="69a3b-169">在主要、主體或外鍵屬性上設定索引鍵時，請務必建立適用于索引鍵的比較子。</span><span class="sxs-lookup"><span data-stu-id="69a3b-169">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="69a3b-170"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>在相同屬性上需要不同語義的罕見情況下使用。</span><span class="sxs-lookup"><span data-stu-id="69a3b-170">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]
> <span data-ttu-id="69a3b-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> 在 EF Core 5.0 中已過時。</span><span class="sxs-lookup"><span data-stu-id="69a3b-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> has been obsoleted in EF Core 5.0.</span></span> <span data-ttu-id="69a3b-172">請改用 <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>。</span><span class="sxs-lookup"><span data-stu-id="69a3b-172">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> instead.</span></span>

## <a name="overriding-the-default-comparer"></a><span data-ttu-id="69a3b-173">覆寫預設比較子</span><span class="sxs-lookup"><span data-stu-id="69a3b-173">Overriding the default comparer</span></span>

<span data-ttu-id="69a3b-174">有時 EF Core 所使用的預設比較可能不適當。</span><span class="sxs-lookup"><span data-stu-id="69a3b-174">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="69a3b-175">例如，根據預設，在 EF Core 中偵測到位元組陣列的變化。</span><span class="sxs-lookup"><span data-stu-id="69a3b-175">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="69a3b-176">這可以藉由在屬性上設定不同的比較子來覆寫：</span><span class="sxs-lookup"><span data-stu-id="69a3b-176">This can be overridden by setting a different comparer on the property:</span></span>

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="69a3b-177">EF Core 現在將會比較位元組序列，因此會偵測位元組陣列突變。</span><span class="sxs-lookup"><span data-stu-id="69a3b-177">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
