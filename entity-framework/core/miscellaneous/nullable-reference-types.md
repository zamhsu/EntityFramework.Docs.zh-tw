---
title: 使用可為 null 的參考型別-EF Core
description: '使用 Entity Framework Core 時，使用 c # 可為 null 的參考型別'
author: roji
ms.date: 09/09/2019
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 648b79576838d2ba424b5216d5ad6811912f8ccb
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429711"
---
# <a name="working-with-nullable-reference-types"></a>使用可為 Null 的參考型別

C # 8 引進了一項新功能，稱為 [可為 null 的參考型別 (NRT) ](/dotnet/csharp/tutorials/nullable-reference-types)，可讓您標注參考型別，以指出其是否有效，以包含 null。 如果您不熟悉這項功能，建議您閱讀 c # 檔，讓自己熟悉。

本頁面介紹 EF Core 對可為 null 的參考型別的支援，並說明使用這些類型的最佳作法。

## <a name="required-and-optional-properties"></a>必要和選用屬性

必要和選用屬性的主要檔，以及它們與可為 null 參考型別的互動是 [必要的和選擇性的屬性](xref:core/modeling/entity-properties#required-and-optional-properties) 頁面。 建議您一開始先閱讀該頁面。

> [!NOTE]
> 在現有的專案上啟用可為 null 的參考型別時，請務必注意：先前設定為選用的參考型別屬性現在會設定為必要，除非它們明確標注為可為 null。 管理關係資料庫架構時，這可能會導致產生遷移，進而改變資料庫資料行的 null 屬性。

## <a name="non-nullable-properties-and-initialization"></a>不可為 null 的屬性和初始化

啟用可為 null 的參考型別時，c # 編譯器會針對任何未初始化的不可為 null 的屬性發出警告，因為這些屬性會包含 null。 因此，不能使用撰寫實體類型的一般方式：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithWarning.cs?name=CustomerWithWarning&highlight=4-5)]

「函式系結」是一項實用的[技巧，可](xref:core/modeling/constructors)確保您的不可為 null 的屬性會初始化：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithConstructorBinding.cs?name=CustomerWithConstructorBinding&highlight=6-9)]

可惜的是，在某些情況下，無法選擇使用「函式系結」;例如，導覽屬性無法以這種方式初始化。

需要的導覽屬性有額外的難度：雖然特定的主體一律會有相依的，但特定的查詢可能不會載入它，視程式中該點的需求而定 ([查看載入資料) 的不同模式](xref:core/querying/related-data) 。 同時，您不需要讓這些屬性成為可為 null 的，因為這會強制所有這些屬性的存取權檢查是否為 null，即使它們是必要的。

處理這些案例的其中一種方式，是讓不可為 null 的屬性具有可為 null 的 [支援欄位](xref:core/modeling/backing-field)：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

因為導覽屬性不可為 null，所以會設定必要的導覽;只要正確載入流覽，就可以透過屬性存取相依的。 但是，如果在沒有先正確載入相關實體的情況下存取屬性，則會擲回 InvalidOperationException，因為 API 合約的使用不正確。 請注意，EF 必須設定為一律存取支援欄位，而不是屬性（property），因為它依賴能夠讀取值（即使未設定）。請參閱有關如何執行這項作業的 [支援欄位](xref:core/modeling/backing-field) 檔，並考慮指定 `PropertyAccessMode.Field` 以確定設定是否正確。

作為 terser 的替代方案，您可以直接使用容許運算子的協助將屬性初始化為 null， (！ ) ：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

由於程式設計錯誤的結果，將永遠不會觀察實際的 null 值，例如存取導覽屬性，而不事先適當地載入相關實體。

> [!NOTE]
> 集合導覽（包含多個相關實體的參考）應該一律為不可為 null。 空集合表示沒有相關的實體，但清單本身絕對不能是 null。

## <a name="dbcontext-and-dbset"></a>DbCoNtext 和 DbSet

在內容類型上具有未初始化 DbSet 屬性的常見作法也有問題，因為編譯器現在會發出警告。 您可以依照下列方式修正此問題：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

另一種策略是使用不可為 null 的 auto 屬性，但若要將它們初始化為 null，請使用 null-容許運算子 (！ ) 將編譯器警告解除回應。 DbCoNtext 基底函式可確保所有 DbSet 屬性都會初始化，而且永遠不會在其上觀察到 null。

## <a name="navigating-and-including-nullable-relationships"></a>導覽及包含可為 null 的關聯性

處理選擇性關聯性時，可能會遇到可能不可能發生實際 null 參考例外狀況的編譯器警告。 轉譯和執行 LINQ 查詢時，EF Core 保證如果選擇性的相關實體不存在，則會直接忽略它的任何導覽，而不會擲回。 不過，編譯器並不知道此 EF Core 保證，並會產生警告，就像 LINQ 查詢是在記憶體中執行，並具有 LINQ to Objects 一樣。 如此一來，就必須使用容許運算子 (！ ) 來通知編譯器，不可能有實際的 null 值：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

在選擇性導覽中包含多個層級的關聯性時，會發生類似的問題：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

如果您覺得自己這麼做很多，而且問題中的實體類型主要是 (或獨佔) 用於 EF Core 查詢中，請考慮將導覽屬性設為不可為 null，並透過流暢的 API 或資料批註將其設定為選擇性。 這會移除所有編譯器警告，同時保留關聯性。但是，如果您的實體是在 EF Core 之外進行，您可能會觀察到 null 值，但屬性會標注為不可為 null。

## <a name="limitations"></a>限制

* 反向工程目前不支援 [c # 8 可為 null 的參考型別 (NRTs) ](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 一律會產生假設功能已關閉的 c # 程式碼。 例如，可為 null 的文字資料行將 scaffold 為類型的屬性，而不是使用 `string` `string?` 流暢的 API 或資料批註來設定是否需要屬性。 您可以編輯 scaffold 程式碼，並以 c # 可 null 性注釋取代這些程式碼。 問題 [#15520](https://github.com/dotnet/efcore/issues/15520)會追蹤可為 null 之參考型別的型別支援。
* EF Core 的公用 API 介面尚未標注為可 null 性 (公用 API 為 "無警示" ) ，因此在開啟 NRT 功能時，有時很難使用。 這特別包含 EF Core 所公開的非同步 LINQ 運算子，例如 [singleordefaultasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。 我們計畫在5.0 版中解決此情況。
