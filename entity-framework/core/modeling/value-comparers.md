---
title: 值比較子-EF Core
description: 使用值比較子來控制 EF Core 如何比較屬性值
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: d07aee866a542f55c4e1074c5782e67cb4035a89
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616677"
---
# <a name="value-comparers"></a>值比較子

> [!NOTE]  
> 這項功能是 EF Core 3.0 中的新功能。

> [!TIP]  
> 您可以在 GitHub 上找到這份檔中的程式碼，做為可執行檔 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)。

## <a name="background"></a>背景

EF Core 需要在下列情況中比較屬性值：

* 判斷屬性是否在偵測[更新變更](xref:core/saving/basic)時變更
* 判斷兩個索引鍵值在解析關聯性時是否相同

這會針對常見的基本類型（例如 int、bool、DateTime 等）自動處理。

如需更複雜的類型，您必須選擇如何進行比較。
例如，您可以比較位元組陣列：

* 依參考，因此只有在使用新的位元組陣列時，才會偵測到差異
* 依深層比較，會偵測到陣列中的位元組變化

根據預設，EF Core 會針對非索引鍵位元組陣列使用這些方法中的第一個。
也就是說，只會比較參考，而且只有當現有的位元組陣列取代為新的位元組陣列時，才會偵測到變更。
這是一項實用的決策，可避免在執行 SaveChanges 時，對許多大型位元組陣列進行深層比較。
但是，取代具有不同映射的影像的常見案例是以高效能的方式處理。

相反地，當使用位元組陣列來代表二進位索引鍵時，參考相等將無法運作。
FK 屬性不太可能會設定為與需要比較的 PK 屬性 _相同的實例_ 。
因此，EF Core 會針對作為索引鍵的位元組陣列使用深層比較。
這不太可能會造成嚴重的效能影響，因為二進位金鑰通常很短。

### <a name="snapshots"></a>快照集

可變型別的深層比較表示 EF Core 需要能夠建立屬性值的深度「快照」。
只要複製參考，就會導致變更目前的值和快照集，因為它們是 _相同的物件_。
因此，在可變類型上使用深層比較時，也需要深度快照。

## <a name="properties-with-value-converters"></a>具有值轉換器的屬性

在上述案例中，EF Core 具有位元組陣列的原生對應支援，因此可以自動選擇適當的預設值。
但是，如果屬性是透過 [值轉換器](xref:core/modeling/value-conversions)對應，則 EF Core 不一定會判斷要使用的適當比較。
相反地，EF Core 一律會使用屬性類型所定義的預設相等比較。
這通常是正確的，但在對應更複雜的類型時可能需要覆寫。

### <a name="simple-immutable-classes"></a>簡單不可變的類別

請考慮使用值轉換器來對應簡單、不可變類別的屬性。

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

此類型的屬性不需要特殊的比較或快照集，因為：

* 覆寫相等，讓不同的實例能夠正確地比較
* 此類型為不可變，因此沒有機會變更快照集值

因此在此情況下，EF Core 的預設行為就會正常運作。

### <a name="simple-immutable-structs"></a>簡單的不可變結構

簡單結構的對應也很簡單，且不需要任何特殊的比較子或快照。

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core 有內建的支援，可產生結構屬性的已編譯、成員比較。
這表示結構不需要針對 EF 覆寫相等，但基於 [其他原因](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)，您可能還是會選擇這麼做。
此外，不需要特殊的快照，因為結構不變，且一律會成員複製。
 (這種情況也適用于可變結構，但 [應該要避免變動結構](/dotnet/csharp/write-safe-efficient-code)。 ) 

### <a name="mutable-classes"></a>可變類別

建議您盡可能使用不可變的類型 (類別或結構) 具有值轉換器。
這通常更有效率，且具有比使用可變類型更簡潔的語法。

不過，這是很常見的情況，就是使用應用程式無法變更之類型的屬性。
例如，對應包含數位清單的屬性：

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

[ `List<T>` 類別](/dotnet/api/system.collections.generic.list-1)：

* 具有參考相等;包含相同值的兩個清單會視為不同。
* 是可變動的;您可以新增和移除清單中的值。

清單屬性上的一般值轉換可能會將清單轉換成 JSON 和從 JSON 轉換：

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

然後，這需要 `ValueComparer<T>` 在屬性上設定，以強制 EF Core 搭配此轉換使用正確的比較：

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> 模型產生器 ( 「流暢」 ) API 來設定值比較子尚未實作為值比較子。
> 相反地，上述程式碼會在 builder 公開的較低層級 IMutableProperty 上呼叫 SetValueComparer，作為 ' Metadata '。

此函式 `ValueComparer<T>` 接受三個運算式：

* 檢查是否相等的運算式
* 用來產生雜湊碼的運算式
* 用來將值產生快照的運算式  

在此情況下，藉由檢查數位序列是否相同來完成比較。

同樣地，雜湊程式碼是由相同的順序所建立。
 (請注意，這是可變動值的雜湊程式碼，因此可能 [會造成問題](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)。
如果您可以，請改用不可變的。 ) 

您可以使用 ToList 複製清單來建立快照集。
同樣地，只有在清單即將進行變化時，才需要這樣做。
如果可以，則改為不可變。

> [!NOTE]  
> 值轉換器和比較子使用運算式而非簡單委派來建立。
> 這是因為 EF 會將這些運算式插入更複雜的運算式樹狀架構，然後再編譯到實體轉譯器委派中。
> 就概念而言，這與編譯器內嵌類似。
> 例如，簡單的轉換可能只是轉換中的編譯，而不是呼叫另一個方法來進行轉換。

### <a name="key-comparers"></a>索引鍵比較子

背景區段涵蓋了為什麼按鍵比較可能需要特殊的語法。
在主要、主體或外鍵屬性上設定索引鍵時，請務必建立適用于索引鍵的比較子。

在不需要相同屬性之不同語義的罕見情況下，請使用 [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer) 。

> [!NOTE]  
> SetStructuralComparer 在 EF Core 5.0 中已過時。
> 請改用 SetKeyValueComparer。

### <a name="overriding-defaults"></a>覆寫預設值

有時 EF Core 所使用的預設比較可能不適當。
例如，根據預設，在 EF Core 中偵測到位元組陣列的變化。
這可以藉由在屬性上設定不同的比較子來覆寫：

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core 現在將會比較位元組序列，因此會偵測位元組陣列突變。
