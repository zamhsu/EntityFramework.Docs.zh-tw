---
title: 值比較子-EF Core
description: 使用值比較子來控制 EF Core 如何比較屬性值
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: d59ab093c7e9d251aac80420972d3e35edabae0b
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003610"
---
# <a name="value-comparers"></a><span data-ttu-id="a80a2-103">值比較子</span><span class="sxs-lookup"><span data-stu-id="a80a2-103">Value Comparers</span></span>

> [!NOTE]  
> <span data-ttu-id="a80a2-104">這項功能是在 EF Core 3.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="a80a2-104">This feature was introduced in EF Core 3.0.</span></span>

> [!TIP]  
> <span data-ttu-id="a80a2-105">您可以在 GitHub 上找到這份檔中的程式碼，做為可執行檔 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)。</span><span class="sxs-lookup"><span data-stu-id="a80a2-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="a80a2-106">背景</span><span class="sxs-lookup"><span data-stu-id="a80a2-106">Background</span></span>

<span data-ttu-id="a80a2-107">EF Core 需要在下列情況中比較屬性值：</span><span class="sxs-lookup"><span data-stu-id="a80a2-107">EF Core needs to compare property values when:</span></span>

* <span data-ttu-id="a80a2-108">判斷屬性是否在偵測[更新變更](xref:core/saving/basic)時變更</span><span class="sxs-lookup"><span data-stu-id="a80a2-108">Determining whether a property has been changed as part of [detecting changes for updates](xref:core/saving/basic)</span></span>
* <span data-ttu-id="a80a2-109">判斷兩個索引鍵值在解析關聯性時是否相同</span><span class="sxs-lookup"><span data-stu-id="a80a2-109">Determining whether two key values are the same when resolving relationships</span></span>

<span data-ttu-id="a80a2-110">這會針對常見的基本類型（例如 int、bool、DateTime 等）自動處理。</span><span class="sxs-lookup"><span data-stu-id="a80a2-110">This is handled automatically for common primitive types such as int, bool, DateTime, etc.</span></span>

<span data-ttu-id="a80a2-111">如需更複雜的類型，您必須選擇如何進行比較。</span><span class="sxs-lookup"><span data-stu-id="a80a2-111">For more complex types, choices need to be made as to how to do the comparison.</span></span>
<span data-ttu-id="a80a2-112">例如，您可以比較位元組陣列：</span><span class="sxs-lookup"><span data-stu-id="a80a2-112">For example, a byte array could be compared:</span></span>

* <span data-ttu-id="a80a2-113">依參考，因此只有在使用新的位元組陣列時，才會偵測到差異</span><span class="sxs-lookup"><span data-stu-id="a80a2-113">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="a80a2-114">依深層比較，會偵測到陣列中的位元組變化</span><span class="sxs-lookup"><span data-stu-id="a80a2-114">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="a80a2-115">根據預設，EF Core 會針對非索引鍵位元組陣列使用這些方法中的第一個。</span><span class="sxs-lookup"><span data-stu-id="a80a2-115">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span>
<span data-ttu-id="a80a2-116">也就是說，只會比較參考，而且只有當現有的位元組陣列取代為新的位元組陣列時，才會偵測到變更。</span><span class="sxs-lookup"><span data-stu-id="a80a2-116">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span>
<span data-ttu-id="a80a2-117">這是一項實用的決策，可避免在執行 SaveChanges 時，對許多大型位元組陣列進行深層比較。</span><span class="sxs-lookup"><span data-stu-id="a80a2-117">This is a pragmatic decision that avoids deep comparison of many large byte arrays when executing SaveChanges.</span></span>
<span data-ttu-id="a80a2-118">但是，取代具有不同映射的影像的常見案例是以高效能的方式處理。</span><span class="sxs-lookup"><span data-stu-id="a80a2-118">But the common scenario of replacing, say, an image with a different image is handled in a performant way.</span></span>

<span data-ttu-id="a80a2-119">相反地，當使用位元組陣列來代表二進位索引鍵時，參考相等將無法運作。</span><span class="sxs-lookup"><span data-stu-id="a80a2-119">On the other hand, reference equality would not work when byte arrays are used to represent binary keys.</span></span>
<span data-ttu-id="a80a2-120">FK 屬性不太可能會設定為與需要比較的 PK 屬性 _相同的實例_ 。</span><span class="sxs-lookup"><span data-stu-id="a80a2-120">It's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span>
<span data-ttu-id="a80a2-121">因此，EF Core 會針對作為索引鍵的位元組陣列使用深層比較。</span><span class="sxs-lookup"><span data-stu-id="a80a2-121">Therefore, EF Core uses deep comparisons for byte arrays acting as keys.</span></span>
<span data-ttu-id="a80a2-122">這不太可能會造成嚴重的效能影響，因為二進位金鑰通常很短。</span><span class="sxs-lookup"><span data-stu-id="a80a2-122">This is unlikely to have a big performance hit since binary keys are usually short.</span></span>

### <a name="snapshots"></a><span data-ttu-id="a80a2-123">快照集</span><span class="sxs-lookup"><span data-stu-id="a80a2-123">Snapshots</span></span>

<span data-ttu-id="a80a2-124">可變型別的深層比較表示 EF Core 需要能夠建立屬性值的深度「快照」。</span><span class="sxs-lookup"><span data-stu-id="a80a2-124">Deep comparisons on mutable types means that EF Core needs the ability to create a deep "snapshot" of the property value.</span></span>
<span data-ttu-id="a80a2-125">只要複製參考，就會導致變更目前的值和快照集，因為它們是 _相同的物件_。</span><span class="sxs-lookup"><span data-stu-id="a80a2-125">Just copying the reference instead would result in mutating both the current value and the snapshot, since they are _the same object_.</span></span>
<span data-ttu-id="a80a2-126">因此，在可變類型上使用深層比較時，也需要深度快照。</span><span class="sxs-lookup"><span data-stu-id="a80a2-126">Therefore, when deep comparisons are used on mutable types, deep snapshotting is also required.</span></span>

## <a name="properties-with-value-converters"></a><span data-ttu-id="a80a2-127">具有值轉換器的屬性</span><span class="sxs-lookup"><span data-stu-id="a80a2-127">Properties with value converters</span></span>

<span data-ttu-id="a80a2-128">在上述案例中，EF Core 具有位元組陣列的原生對應支援，因此可以自動選擇適當的預設值。</span><span class="sxs-lookup"><span data-stu-id="a80a2-128">In the case above, EF Core has native mapping support for byte arrays and so can automatically choose appropriate defaults.</span></span>
<span data-ttu-id="a80a2-129">但是，如果屬性是透過 [值轉換器](xref:core/modeling/value-conversions)對應，則 EF Core 不一定會判斷要使用的適當比較。</span><span class="sxs-lookup"><span data-stu-id="a80a2-129">However, if the property is mapped through a [value converter](xref:core/modeling/value-conversions), then EF Core can't always determine the appropriate comparison to use.</span></span>
<span data-ttu-id="a80a2-130">相反地，EF Core 一律會使用屬性類型所定義的預設相等比較。</span><span class="sxs-lookup"><span data-stu-id="a80a2-130">Instead, EF Core always uses the default equality comparison defined by the type of the property.</span></span>
<span data-ttu-id="a80a2-131">這通常是正確的，但在對應更複雜的類型時可能需要覆寫。</span><span class="sxs-lookup"><span data-stu-id="a80a2-131">This is often correct, but may need to be overridden when mapping more complex types.</span></span>

### <a name="simple-immutable-classes"></a><span data-ttu-id="a80a2-132">簡單不可變的類別</span><span class="sxs-lookup"><span data-stu-id="a80a2-132">Simple immutable classes</span></span>

<span data-ttu-id="a80a2-133">請考慮使用值轉換器來對應簡單、不可變類別的屬性。</span><span class="sxs-lookup"><span data-stu-id="a80a2-133">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="a80a2-134">此類型的屬性不需要特殊的比較或快照集，因為：</span><span class="sxs-lookup"><span data-stu-id="a80a2-134">Properties of this type do not need special comparisons or snapshots because:</span></span>

* <span data-ttu-id="a80a2-135">覆寫相等，讓不同的實例能夠正確地比較</span><span class="sxs-lookup"><span data-stu-id="a80a2-135">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="a80a2-136">此類型為不可變，因此沒有機會變更快照集值</span><span class="sxs-lookup"><span data-stu-id="a80a2-136">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="a80a2-137">因此在此情況下，EF Core 的預設行為就會正常運作。</span><span class="sxs-lookup"><span data-stu-id="a80a2-137">So in this case the default behavior of EF Core is fine as it is.</span></span>

### <a name="simple-immutable-structs"></a><span data-ttu-id="a80a2-138">簡單的不可變結構</span><span class="sxs-lookup"><span data-stu-id="a80a2-138">Simple immutable Structs</span></span>

<span data-ttu-id="a80a2-139">簡單結構的對應也很簡單，且不需要任何特殊的比較子或快照。</span><span class="sxs-lookup"><span data-stu-id="a80a2-139">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="a80a2-140">EF Core 有內建的支援，可產生結構屬性的已編譯、成員比較。</span><span class="sxs-lookup"><span data-stu-id="a80a2-140">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="a80a2-141">這表示結構不需要針對 EF 覆寫相等，但基於 [其他原因](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)，您可能還是會選擇這麼做。</span><span class="sxs-lookup"><span data-stu-id="a80a2-141">This means structs don't need to have equality overridden for EF, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="a80a2-142">此外，不需要特殊的快照，因為結構不變，且一律會成員複製。</span><span class="sxs-lookup"><span data-stu-id="a80a2-142">Also, special snapshotting is not needed since structs immutable and are always memberwise copied anyway.</span></span>
<span data-ttu-id="a80a2-143"> (這種情況也適用于可變結構，但 [應該要避免變動結構](/dotnet/csharp/write-safe-efficient-code)。 ) </span><span class="sxs-lookup"><span data-stu-id="a80a2-143">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

### <a name="mutable-classes"></a><span data-ttu-id="a80a2-144">可變類別</span><span class="sxs-lookup"><span data-stu-id="a80a2-144">Mutable classes</span></span>

<span data-ttu-id="a80a2-145">建議您盡可能使用不可變的類型 (類別或結構) 具有值轉換器。</span><span class="sxs-lookup"><span data-stu-id="a80a2-145">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="a80a2-146">這通常更有效率，且具有比使用可變類型更簡潔的語法。</span><span class="sxs-lookup"><span data-stu-id="a80a2-146">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="a80a2-147">不過，這是很常見的情況，就是使用應用程式無法變更之類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="a80a2-147">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="a80a2-148">例如，對應包含數位清單的屬性：</span><span class="sxs-lookup"><span data-stu-id="a80a2-148">For example, mapping a property containing a list of numbers:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="a80a2-149">[ `List<T>` 類別](/dotnet/api/system.collections.generic.list-1)：</span><span class="sxs-lookup"><span data-stu-id="a80a2-149">The [`List<T>` class](/dotnet/api/system.collections.generic.list-1):</span></span>

* <span data-ttu-id="a80a2-150">具有參考相等;包含相同值的兩個清單會視為不同。</span><span class="sxs-lookup"><span data-stu-id="a80a2-150">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="a80a2-151">是可變動的;您可以新增和移除清單中的值。</span><span class="sxs-lookup"><span data-stu-id="a80a2-151">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="a80a2-152">清單屬性上的一般值轉換可能會將清單轉換成 JSON 和從 JSON 轉換：</span><span class="sxs-lookup"><span data-stu-id="a80a2-152">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="a80a2-153">然後，這需要 `ValueComparer<T>` 在屬性上設定，以強制 EF Core 搭配此轉換使用正確的比較：</span><span class="sxs-lookup"><span data-stu-id="a80a2-153">This then requires setting a `ValueComparer<T>` on the property to force EF Core use correct comparisons with this conversion:</span></span>

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> <span data-ttu-id="a80a2-154">模型產生器 ( 「流暢」 ) API 來設定值比較子尚未實作為值比較子。</span><span class="sxs-lookup"><span data-stu-id="a80a2-154">The model builder ("fluent") API to set a value comparer has not yet been implemented.</span></span>
> <span data-ttu-id="a80a2-155">相反地，上述程式碼會在 builder 公開的較低層級 IMutableProperty 上呼叫 SetValueComparer，作為 ' Metadata '。</span><span class="sxs-lookup"><span data-stu-id="a80a2-155">Instead, the code above calls SetValueComparer on the lower-level IMutableProperty exposed by the builder as 'Metadata'.</span></span>

<span data-ttu-id="a80a2-156">此函式 `ValueComparer<T>` 接受三個運算式：</span><span class="sxs-lookup"><span data-stu-id="a80a2-156">The `ValueComparer<T>` constructor accepts three expressions:</span></span>

* <span data-ttu-id="a80a2-157">檢查是否相等的運算式</span><span class="sxs-lookup"><span data-stu-id="a80a2-157">An expression for checking equality</span></span>
* <span data-ttu-id="a80a2-158">用來產生雜湊碼的運算式</span><span class="sxs-lookup"><span data-stu-id="a80a2-158">An expression for generating a hash code</span></span>
* <span data-ttu-id="a80a2-159">用來將值產生快照的運算式</span><span class="sxs-lookup"><span data-stu-id="a80a2-159">An expression to snapshot a value</span></span>  

<span data-ttu-id="a80a2-160">在此情況下，藉由檢查數位序列是否相同來完成比較。</span><span class="sxs-lookup"><span data-stu-id="a80a2-160">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="a80a2-161">同樣地，雜湊程式碼是由相同的順序所建立。</span><span class="sxs-lookup"><span data-stu-id="a80a2-161">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="a80a2-162"> (請注意，這是可變動值的雜湊程式碼，因此可能 [會造成問題](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)。</span><span class="sxs-lookup"><span data-stu-id="a80a2-162">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="a80a2-163">如果您可以，請改用不可變的。 ) </span><span class="sxs-lookup"><span data-stu-id="a80a2-163">Be immutable instead if you can.)</span></span>

<span data-ttu-id="a80a2-164">您可以使用 ToList 複製清單來建立快照集。</span><span class="sxs-lookup"><span data-stu-id="a80a2-164">The snapshot is created by cloning the list with ToList.</span></span>
<span data-ttu-id="a80a2-165">同樣地，只有在清單即將進行變化時，才需要這樣做。</span><span class="sxs-lookup"><span data-stu-id="a80a2-165">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="a80a2-166">如果可以，則改為不可變。</span><span class="sxs-lookup"><span data-stu-id="a80a2-166">Be immutable instead if you can.</span></span>

> [!NOTE]  
> <span data-ttu-id="a80a2-167">值轉換器和比較子使用運算式而非簡單委派來建立。</span><span class="sxs-lookup"><span data-stu-id="a80a2-167">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="a80a2-168">這是因為 EF 會將這些運算式插入更複雜的運算式樹狀架構，然後再編譯到實體轉譯器委派中。</span><span class="sxs-lookup"><span data-stu-id="a80a2-168">This is because EF inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="a80a2-169">就概念而言，這與編譯器內嵌類似。</span><span class="sxs-lookup"><span data-stu-id="a80a2-169">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="a80a2-170">例如，簡單的轉換可能只是轉換中的編譯，而不是呼叫另一個方法來進行轉換。</span><span class="sxs-lookup"><span data-stu-id="a80a2-170">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>

### <a name="key-comparers"></a><span data-ttu-id="a80a2-171">索引鍵比較子</span><span class="sxs-lookup"><span data-stu-id="a80a2-171">Key comparers</span></span>

<span data-ttu-id="a80a2-172">背景區段涵蓋了為什麼按鍵比較可能需要特殊的語法。</span><span class="sxs-lookup"><span data-stu-id="a80a2-172">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="a80a2-173">在主要、主體或外鍵屬性上設定索引鍵時，請務必建立適用于索引鍵的比較子。</span><span class="sxs-lookup"><span data-stu-id="a80a2-173">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="a80a2-174">在不需要相同屬性之不同語義的罕見情況下，請使用 [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer) 。</span><span class="sxs-lookup"><span data-stu-id="a80a2-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer) in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]  
> <span data-ttu-id="a80a2-175">SetStructuralComparer 在 EF Core 5.0 中已過時。</span><span class="sxs-lookup"><span data-stu-id="a80a2-175">SetStructuralComparer has been obsoleted in EF Core 5.0.</span></span>
> <span data-ttu-id="a80a2-176">請改用 SetKeyValueComparer。</span><span class="sxs-lookup"><span data-stu-id="a80a2-176">Use SetKeyValueComparer instead.</span></span>

### <a name="overriding-defaults"></a><span data-ttu-id="a80a2-177">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="a80a2-177">Overriding defaults</span></span>

<span data-ttu-id="a80a2-178">有時 EF Core 所使用的預設比較可能不適當。</span><span class="sxs-lookup"><span data-stu-id="a80a2-178">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="a80a2-179">例如，根據預設，在 EF Core 中偵測到位元組陣列的變化。</span><span class="sxs-lookup"><span data-stu-id="a80a2-179">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="a80a2-180">這可以藉由在屬性上設定不同的比較子來覆寫：</span><span class="sxs-lookup"><span data-stu-id="a80a2-180">This can be overridden by setting a different comparer on the property:</span></span>

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="a80a2-181">EF Core 現在將會比較位元組序列，因此會偵測位元組陣列突變。</span><span class="sxs-lookup"><span data-stu-id="a80a2-181">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
