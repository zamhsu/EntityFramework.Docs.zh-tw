---
title: 值比較子-EF Core
description: 使用值比較子來控制 EF Core 如何比較屬性值
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: fa5352129977d858d54d4aede746b320c91b0ad3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526780"
---
# <a name="value-comparers"></a>值比較子

> [!NOTE]  
> 這項功能在 EF Core 3.0 中是新的。

> [!TIP]  
> 本檔中的程式碼可以在 GitHub 上找到可執行檔[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)。

## <a name="background"></a>背景

EF Core 需要在下列情況中比較屬性值：

* 判斷屬性是否已變更為偵測[更新變更](xref:core/saving/basic)的一部分
* 在解析關聯性時，判斷兩個索引鍵值是否相同 

這會針對一般基本類型（例如 int、bool、DateTime 等）自動處理。

如需更複雜的類型，您必須進行更多的選擇，以瞭解如何進行比較。
例如，可以比較位元組陣列：

* 依參考，只有在使用新的位元組陣列時，才會偵測到差異
* 依深度比較，會偵測到陣列中的位元組變化

根據預設，EF Core 會針對非金鑰位元組陣列使用這些方法的第一個。
也就是說，只有參考會進行比較，而且只有在以新的位元組陣列取代時，才會偵測到變更。
這是實際的決策，可避免在執行 SaveChanges 時進行許多大型位元組陣列的深層比較。
但是，取代映射的常見案例是以高效能的方式處理具有不同影像的影像。

另一方面，當使用位元組陣列來代表二進位索引鍵時，參考相等將無法作用。
FK 屬性不太可能設定為與需要比較的 PK 屬性_相同的實例_。
因此，EF Core 會針對當做索引鍵的位元組陣列使用深層比較。
這不太可能會造成顯著的效能影響，因為二進位金鑰通常很短。

### <a name="snapshots"></a>快照集

可變類型的深層比較表示 EF Core 需要能夠建立屬性值的深層「快照集」。
只要複製參考，就會導致同時改變目前的值和快照集，因為它們是_相同的物件_。
因此，在可變型別上使用深層比較時，也需要深度快照。

## <a name="properties-with-value-converters"></a>具有值轉換器的屬性

在上述情況下，EF Core 具有位元組陣列的原生對應支援，因此可以自動選擇適當的預設值。
不過，如果透過[值轉換器](xref:core/modeling/value-conversions)來對應屬性，則 EF Core 不一定會決定要使用的適當比較。
相反地，EF Core 一律會使用屬性類型所定義的預設相等比較。
這通常是正確的，但在對應更複雜的類型時可能需要覆寫。

### <a name="simple-immutable-classes"></a>簡單的不可變類別

請考慮使用值轉換器來對應簡單、不可變類別的屬性。

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

此類型的屬性不需要特殊比較或快照集，因為：
* 會覆寫相等，讓不同的實例能夠正確地進行比較
* 類型是不可變的，因此不可能改變快照集值

因此，在此情況下，EF Core 的預設行為是正常的。

### <a name="simple-immutable-structs"></a>簡單的不可變結構

簡單結構的對應也很簡單，而且不需要任何特殊的比較子或快照。

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core 已內建支援，可產生結構屬性的編譯、成員比較。
這表示結構不需要針對 EF 覆寫相等，但您仍可能因[其他原因](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)而選擇這麼做。
此外，不需要特殊的快照，因為結構不變，而且一律會成員複製。
（這也適用于可變動的結構，但[通常應該避免可變結構](/dotnet/csharp/write-safe-efficient-code)）。

### <a name="mutable-classes"></a>可變類別

建議您盡可能使用不可變的類型（類別或結構）搭配值轉換器。
這通常比使用可變類型更有效率，而且具有更簡潔的語義。

不過，這是常見的情況，就是使用應用程式無法變更之類型的屬性。
例如，對應包含數位清單的屬性： 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

[ `List<T>` 類別](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1)：
* 具有參考是否相等;包含相同值的兩個清單會視為不同。
* 是可變的;清單中的值可以加入和移除。

在清單屬性上的典型值轉換可能會將清單轉換為 JSON 的和：

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

然後，需要 `ValueComparer<T>` 在屬性上設定，以強制 EF Core 搭配此轉換來使用正確的比較：

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> 尚未實作為模型產生器（「流暢」） API 來設定值比較子。
> 相反地，上述程式碼會呼叫產生器所公開之較低層級 IMutableProperty 上的 SetValueComparer 做為 ' Metadata '。

此函式 `ValueComparer<T>` 接受三個運算式：
* 檢查是否相等的運算式
* 產生雜湊碼的運算式
* 要為值建立快照集的運算式  

在此情況下，比較是藉由檢查數位序列是否相同來完成。

同樣地，雜湊碼是以相同的順序建立。
（請注意，這是可變動值的雜湊碼，因此可能[會造成問題](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)。
如果可以的話，請改為不可變）。

建立快照集的方式是使用 ToList 複製清單。
同樣地，只有在清單即將進行變化時才需要此程式。
如果可以，請改用不可變的。 

> [!NOTE]  
> 值轉換器和比較子是使用運算式來建立，而非簡單的委派。
> 這是因為 EF 會將這些運算式插入更複雜的運算式樹狀架構中，然後再編譯成實體轉譯程式委派。
> 在概念上，這與編譯器內嵌類似。
> 例如，簡單的轉換可能只是在 cast 中編譯的，而不是呼叫另一個方法來進行轉換。    

### <a name="key-comparers"></a>索引鍵比較子

背景區段涵蓋為何按鍵比較可能需要特殊的語義。
在主要、主體或外鍵屬性上設定時，請務必建立適用于索引鍵的比較子。

當相同的屬性需要不同的語義時，請使用[SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) 。

> [!NOTE]  
> EF Core 5.0 中的 SetStructuralComparer 已過時。
> 請改用 SetKeyValueComparer。

### <a name="overriding-defaults"></a>覆寫預設值

有時候 EF Core 所使用的預設比較可能不適合。
例如，根據預設，在 EF Core 中偵測到位元組陣列的變化。
這可以藉由在屬性上設定不同的比較子來加以覆寫： 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core 現在會比較位元組序列，因此會偵測位元組陣列變化。
