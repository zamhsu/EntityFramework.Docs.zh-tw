---
title: 陰影和索引子屬性-EF Core
description: 在 Entity Framework Core 模型中設定陰影和索引子屬性
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: f03dc000bb111253ae74c05a668703f2e6237a57
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430413"
---
# <a name="shadow-and-indexer-properties"></a>陰影和索引子屬性

陰影屬性是未在 .NET 實體類別中定義的屬性，但是是在 EF Core 模型中為該實體類型定義的。 這些屬性的值和狀態只會在變更追蹤程式中維護。 當資料庫中的資料不應該在對應的實體類型上公開時，陰影屬性會很有用。

## <a name="foreign-key-shadow-properties"></a>外鍵陰影屬性

陰影屬性最常用於外鍵屬性，也就是兩個實體之間的關聯性是由資料庫中的外鍵值表示，但在實體類型上使用實體類型之間的導覽屬性來管理關聯性。 依照慣例，當探索到關聯性，但在相依實體類別中找不到任何外鍵屬性時，EF 將會引入陰影屬性。

屬性將會命名為 `<navigation property name><principal key property name>` (相依實體上的導覽（指向主體實體）用於命名) 。 如果主要索引鍵屬性名稱包含導覽屬性的名稱，則名稱就是 `<principal key property name>` 。 如果相依實體上沒有導覽屬性，則會在其位置使用主體類型名稱。

例如，下列程式代碼清單會導致將 `BlogId` 陰影屬性引進 `Post` 實體：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>設定陰影屬性

您可以使用流暢的 API 來設定陰影屬性。 一旦您呼叫的字串多載之後 `Property` ，您就可以將任何設定呼叫連結至其他屬性。 在下列範例中，因為沒有 `Blog` 名為的 CLR 屬性 `LastUpdated` ，所以會建立陰影屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

如果提供給方法的名稱 `Property` 符合現有屬性的名稱 (陰影屬性，或是實體類別上定義的) ，則程式碼將會設定該現有的屬性，而不是引入新的陰影屬性。

## <a name="accessing-shadow-properties"></a>存取陰影屬性

您可以透過 API 取得及變更陰影屬性值 `ChangeTracker` ：

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

您可以透過靜態方法，在 LINQ 查詢中參考陰影屬性 `EF.Property` ：

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

因為變更追蹤器不會追蹤傳回的實體，所以無法在無追蹤查詢之後存取陰影屬性。

## <a name="property-bag-entity-types"></a>屬性包實體類型

> [!NOTE]
> 已在 EF Core 5.0 中新增屬性包實體類型的支援。

僅包含索引子屬性的實體類型稱為「屬性包」實體類型。 這些實體類型沒有陰影屬性。 目前僅 `Dictionary<string, object>` 支援做為屬性包實體類型。 這表示必須將它設定為具有唯一名稱的共用實體型別，而且 `DbSet` 必須使用呼叫來執行對應的屬性 `Set` 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
