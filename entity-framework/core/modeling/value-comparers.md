---
title: 值比較子-EF Core
description: 使用值比較子來控制 EF Core 如何比較屬性值
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-comparers
ms.openlocfilehash: 5c5e5beee72230a331a8e1c88a2020dc5ad88ecf
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983478"
---
# <a name="value-comparers"></a>值比較子

> [!NOTE]
> 這項功能是在 EF Core 3.0 中引進。

> [!TIP]
> 您可以在 GitHub 上找到這份檔中的程式碼，做為可執行檔 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)。

## <a name="background"></a>背景

[變更追蹤](xref:core/change-tracking/index) 表示 EF Core 會自動判斷已載入的實體實例上應用程式所執行的變更，以便在呼叫時將這些變更儲存回資料庫 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。 EF Core 通常會在從資料庫載入實例時製作實例的 *快照* 集，並將該快照集與向外傳遞至應用程式的實例進行 *比較* ，藉此執行此工作。

EF Core 隨附內建邏輯來進行快照，並比較資料庫中使用的大部分標準型別，因此使用者通常不需要擔心這個主題。 不過，當屬性透過 [值轉換器](xref:core/modeling/value-conversions)對應時，EF Core 必須對任意的使用者類型執行比較，這可能會很複雜。 根據預設，EF Core 會使用類型所定義的預設相等比較 (例如 `Equals`) 的方法; 針對快照集，系統會複製 [值](/dotnet/csharp/language-reference/builtin-types/value-types) 型別以產生快照集，而針對 [參考](/dotnet/csharp/language-reference/keywords/reference-types) 型別，則不會進行複製，而且會使用相同的實例作為快照集。

當內建的比較行為不適當時，使用者可以提供 *值比較* 子，其中包含快照的邏輯、比較和計算雜湊碼。 例如，下列會將屬性的值轉換設定為 `List<int>` 將值轉換為資料庫中的 JSON 字串，並且也定義適當的值比較子：

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

如需詳細資料，請參閱下方的可變動 [類別](#mutable-classes) 。

請注意，在解析關聯性時，判斷兩個索引鍵值是否相同，也會使用值比較子。以下將說明這一點。

## <a name="shallow-vs-deep-comparison"></a>淺層與深層比較

針對較小、不變的實值型別（例如 `int` ），EF Core 的預設邏輯運作正常：當快照時，值會依原樣複製，並與型別內建的相等比較比較。 在實您自己的值比較子時，請務必考慮深度或淺層比較 (，以及) 邏輯是否適當的快照。

請考慮位元組陣列，這可能會任意大。 這些可以比較：

* 依參考，因此只有在使用新的位元組陣列時，才會偵測到差異
* 依深層比較，會偵測到陣列中的位元組變化

根據預設，EF Core 會針對非索引鍵位元組陣列使用這些方法中的第一個。 也就是說，只會比較參考，而且只有當現有的位元組陣列取代為新的位元組陣列時，才會偵測到變更。 這是一項實用的決策，可避免在執行時複製整個陣列並比較它們的位元組至位元組 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。 這表示，將另一個影像取代為另一個映射的常見案例，會以高效能的方式處理。

另一方面，當使用位元組陣列來表示二進位索引鍵時，參考相等將無法運作，因為 FK 屬性不太可能會設定為與需要比較的 PK 屬性 _相同的實例_ 。 因此，EF Core 對作為索引鍵的位元組陣列使用深層比較;這不太可能會造成嚴重的效能影響，因為二進位金鑰通常很短。

請注意，所選的比較和快照式邏輯必須互相對應：深層比較需要深度快照才能正確運作。

## <a name="simple-immutable-classes"></a>簡單不可變的類別

請考慮使用值轉換器來對應簡單、不可變類別的屬性。

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

此類型的屬性不需要特殊的比較或快照集，因為：

* 覆寫相等，讓不同的實例能夠正確地比較
* 此類型為不可變，因此沒有機會變更快照集值

因此在此情況下，EF Core 的預設行為就會正常運作。

## <a name="simple-immutable-structs"></a>簡單的不可變結構

簡單結構的對應也很簡單，且不需要任何特殊的比較子或快照。

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core 有內建的支援，可產生結構屬性的已編譯、成員比較。 這表示結構不需要覆寫 EF Core 的相等，但基於 [其他原因](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)，您可能還是會選擇這麼做。 此外，不需要特殊的快照，因為結構是固定的，而且一律會成員複製。  (這種情況也適用于可變結構，但 [應該要避免變動結構](/dotnet/csharp/write-safe-efficient-code)。 ) 

## <a name="mutable-classes"></a>可變類別

建議您盡可能使用不可變的類型 (類別或結構) 具有值轉換器。 這通常更有效率，且具有比使用可變類型更簡潔的語法。 不過，這是很常見的情況，就是使用應用程式無法變更之類型的屬性。 例如，對應包含數位清單的屬性：

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<xref:System.Collections.Generic.List%601> 類別：

* 具有參考相等;包含相同值的兩個清單會視為不同。
* 是可變動的;您可以新增和移除清單中的值。

清單屬性上的一般值轉換可能會將清單轉換成 JSON 和從 JSON 轉換：

### <a name="ef-core-50"></a>[EF Core 5.0](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[較舊的版本](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

此函式 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 接受三個運算式：

* 檢查是否相等的運算式
* 用來產生雜湊碼的運算式
* 用來將值產生快照的運算式

在此情況下，藉由檢查數位序列是否相同來完成比較。

同樣地，雜湊程式碼是由相同的順序所建立。  (請注意，這是可變動值的雜湊程式碼，因此可能 [會造成問題](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)。 如果您可以，請改用不可變的。 ) 

使用複製清單來建立快照集 `ToList` 。 同樣地，只有在清單即將進行變化時，才需要這樣做。 如果可以，則改為不可變。

> [!NOTE]
> 值轉換器和比較子使用運算式而非簡單委派來建立。 這是因為 EF Core 會將這些運算式插入更複雜的運算式樹狀架構，然後再編譯到實體轉譯器委派中。 就概念而言，這與編譯器內嵌類似。 例如，簡單的轉換可能只是轉換中的編譯，而不是呼叫另一個方法來進行轉換。

## <a name="key-comparers"></a>索引鍵比較子

背景區段涵蓋了為什麼按鍵比較可能需要特殊的語法。 在主要、主體或外鍵屬性上設定索引鍵時，請務必建立適用于索引鍵的比較子。

<xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>在相同屬性上需要不同語義的罕見情況下使用。

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> 在 EF Core 5.0 中已過時。 請改用 <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>。

## <a name="overriding-the-default-comparer"></a>覆寫預設比較子

有時 EF Core 所使用的預設比較可能不適當。 例如，根據預設，在 EF Core 中偵測到位元組陣列的變化。 這可以藉由在屬性上設定不同的比較子來覆寫：

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core 現在將會比較位元組序列，因此會偵測位元組陣列突變。
