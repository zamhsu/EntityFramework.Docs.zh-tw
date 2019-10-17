---
title: 使用可為 null 的參考型別-EF Core
author: roji
ms.date: 09/09/2019
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 055f492214596506ce2c28485ade359d175c4ac2
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445894"
---
# <a name="working-with-nullable-reference-types"></a>使用可為 Null 的參考型別

C#8引進了一個稱為[nullable 參考型別](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，允許標注參考型別，指出其是否有效，以包含 null。 如果您是這項功能的新手，建議您閱讀C#檔，讓自己熟悉它。

本頁面介紹可為 null 參考型別的 EF Core 支援，並說明使用它們的最佳作法。

## <a name="required-and-optional-properties"></a>必要和選擇性屬性

必要和選擇性屬性的主要檔以及其與 nullable 參考型別的互動，都是[必要和選擇性的屬性](xref:core/modeling/required-optional)頁面。 建議您一開始先閱讀該頁面。

> [!NOTE]
> 在現有專案上啟用可為 null 的參考型別時，請特別注意：先前設定為選擇性的參考類型屬性現在會設定為必要，除非它們已明確標注成可為 null。 管理關係資料庫架構時，這可能會產生可改變資料庫資料行之 null 屬性的遷移。

## <a name="dbcontext-and-dbset"></a>DbCoNtext 和 DbSet

當可為 null 的參考型別啟用C#時，編譯器會針對任何未初始化的不可為 null 屬性發出警告，因為這會包含 null。 因此，在內容上定義不可為 null 的 `DbSet` 的常見作法，現在會產生警告。 不過，EF Core 一律會初始化 DbCoNtext 衍生類型上的所有 `DbSet` 屬性，因此即使編譯器不知道這一點，也一定會是 null。 因此，建議您不要將您的 `DbSet` 屬性保持為可為 null-可讓您在沒有 null 檢查的情況下存取它們，並藉由使用 null 容許運算子（！）的協助明確地將其設定為 null，以回應編譯器警告：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

## <a name="non-nullable-properties-and-initialization"></a>不可為 null 的屬性和初始化

未初始化之不可為 null 的參考型別的編譯器警告也是實體類型上一般屬性的問題。 在上述範例中，我們使用「函式系結」來避免這些[警告，這](xref:core/modeling/constructors)項功能可與不可為 null 的屬性完美搭配運作，確保它們一律會初始化。 不過，在某些情況下，不會有一個選項：例如，無法以這種方式初始化導覽屬性。

所需的導覽屬性有額外的困難：雖然特定主體的相依一定會存在，但根據程式中該時間點的需求而定，特定查詢可能會載入它（[請參閱不同的模式載入資料](xref:core/querying/related-data)）。 同時，您不需要讓這些屬性成為可為 null，因為這會強制所有的存取權檢查是否為 null，即使是必要的也一樣。

處理這些案例的其中一種方式，是將不可為 null 的屬性與可為 null 的[支援欄位](xref:core/modeling/backing-field)搭配使用：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=12-17)]

由於導覽屬性不可為 null，因此會設定必要的導覽;而且只要正確載入導覽，就可以透過屬性存取相依性。 不過，如果在沒有第一次正確載入相關實體的情況下存取屬性，則會擲回 InvalidOperationException，因為 API 合約的使用不正確。

做為 terser 的替代方案，您可以使用 null 容許運算子（！）的協助，將屬性初始化為 null：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

除非發生程式設計錯誤（例如存取導覽屬性而不事先適當載入相關實體），否則永遠不會觀察到實際的 null 值。

> [!NOTE]
> 包含多個相關實體參考的集合導覽，應一律不可為 null。 空集合表示沒有相關的實體存在，但清單本身絕對不應為 null。

## <a name="navigating-and-including-nullable-relationships"></a>導覽和包含可為 null 的關聯性

處理選擇性關聯性時，可能會遇到不可能發生實際 null 參考例外狀況的編譯器警告。 轉譯和執行 LINQ 查詢時，EF Core 保證如果有選擇性的相關實體不存在，則會直接忽略它的任何導覽，而不是擲回。 不過，編譯器不會察覺這項 EF Core 保證，而且會產生警告，如同 LINQ 查詢已在記憶體中執行，並具有 LINQ to Objects。 因此，必須使用 null 容許運算子（！）來通知編譯器，實際的 null 值不可能：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

在選擇性的導覽中包含多個關聯性層級時，就會發生類似的問題：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

如果您發現自己這麼做很多，而且有問題的實體類型主要（或專門）用於 EF Core 查詢，請考慮將導覽屬性設為不可為 null，並透過流暢的 API 或資料批註，將其設定為選擇性。 這會移除所有編譯器警告，同時保持關聯性的選擇性;不過，如果您的實體在 EF Core 之外進行遍歷，您可能會觀察到 null 值，雖然屬性會標注為不可為 null。

## <a name="scaffolding"></a>Scaffolding

[反向C#工程目前不支援8可為 null 的參考型別功能](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 一律C#會產生假設功能已關閉的程式碼。 例如，可為 null 的文字資料行將會 scaffold 為具有類型 `string` 的屬性，而不是 `string?`，而是用來設定是否需要屬性的流暢 API 或資料批註。 您可以編輯 scaffold 程式碼，並將其C#取代為 null 屬性注釋。 由問題[#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)追蹤可為 null 之參考型別的樣板支援。
