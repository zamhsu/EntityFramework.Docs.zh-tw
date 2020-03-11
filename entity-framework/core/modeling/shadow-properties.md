---
title: 陰影屬性-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 229cfd83f75b01dff9ac9ad30ee55c7cc727c19e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417409"
---
# <a name="shadow-properties"></a>陰影屬性

陰影屬性是未在您的 .NET 實體類別中定義，但已針對 EF Core 模型中的實體類型定義的屬性。 這些屬性的值和狀態純粹是在變更追蹤程式中進行維護。 當資料庫中有資料不應在對應的實體類型上公開時，陰影屬性會很有用。

## <a name="foreign-key-shadow-properties"></a>外鍵陰影屬性

陰影屬性最常用於外鍵屬性，其中兩個實體之間的關聯性是由資料庫中的外鍵值所表示，但是在實體類型上使用實體之間的導覽屬性來管理關聯性各種. 依照慣例，EF 會在發現關聯性時引進陰影屬性，但在相依實體類別中找不到外鍵屬性。

屬性會命名為 `<navigation property name><principal key property name>` （指向主體實體的相依實體上的導覽會用於命名）。 如果主體索引鍵屬性名稱包含導覽屬性的名稱，則名稱只會 `<principal key property name>`。 如果相依實體上沒有導覽屬性，則會在其位置使用主體類型名稱。

例如，下列程式代碼清單會導致 `BlogId` 影子屬性引進 `Post` 實體：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>設定陰影屬性

您可以使用流暢的 API 來設定陰影屬性。 一旦呼叫 `Property`的字串多載之後，您就可以將任何其他屬性的設定呼叫鏈在一起。 在下列範例中，因為 `Blog` 沒有名為 `LastUpdated`的 CLR 屬性，所以會建立陰影屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

如果提供給 `Property` 方法的名稱符合現有屬性的名稱（陰影屬性或在實體類別上定義的內容），則程式碼會設定該現有的屬性，而不會引進新的陰影屬性。

## <a name="accessing-shadow-properties"></a>存取陰影屬性

您可以透過 `ChangeTracker` API 取得和變更陰影屬性值：

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

您可以透過 `EF.Property` 的靜態方法，在 LINQ 查詢中參考陰影屬性：

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```
