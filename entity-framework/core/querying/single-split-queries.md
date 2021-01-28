---
title: 單一與分割查詢-EF Core
description: 使用 Entity Framework Core 將查詢轉譯成 SQL 中的單一和分割查詢
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 1857e05d45979c5530a349b9c7470407e7574fdf
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983426"
---
# <a name="split-queries"></a>分割查詢

## <a name="single-queries"></a>單一查詢

在關係資料庫中，所有相關的實體都會透過在單一查詢中導入聯結的方式載入。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

如果一般的 blog 有多個相關的文章，這些貼文的資料列將會複製 blog 的資訊。 這項重複會導致所謂的「笛卡兒爆炸」問題。 載入更多一對多關聯性時，重複的資料量可能會成長，而且會對應用程式的效能造成不良影響。

## <a name="split-queries"></a>分割查詢

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。 它只有在使用時才適用 `Include` 。 [此問題](https://github.com/dotnet/efcore/issues/21234) 是在沒有的情況下載入投影中的相關資料時，追蹤分割查詢的支援 `Include` 。

EF 可讓您指定應該將指定的 LINQ 查詢 *分割* 成多個 SQL 查詢。 分割查詢會為每個包含的集合導覽產生額外的 SQL 查詢，而不是聯結。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

它會產生下列 SQL：

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> 一對多相關實體一律會透過相同查詢中的聯結載入，因為它不會影響效能。

## <a name="enabling-split-queries-globally"></a>全域啟用分割查詢

您也可以將分割查詢設定為應用程式內容的預設值：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

當分割查詢設定為預設值時，仍然可以將特定查詢設定為以單一查詢的形式執行：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

EF Core 在沒有任何設定的情況下，預設會使用單一查詢模式。 因為這可能會造成效能問題，所以每當符合下列條件時，EF Core 就會產生警告：

- EF Core 會偵測到查詢會載入多個集合。
- 使用者尚未設定全域的查詢分割模式。
- `AsSingleQuery` / `AsSplitQuery` 查詢上的使用者未使用運算子。

若要關閉警告，請將查詢分割模式全域或在查詢層級設定為適當的值。

## <a name="characteristics-of-split-queries"></a>分割查詢的特性

雖然分割查詢可避免與聯結和笛卡兒爆炸相關的效能問題，但它也有一些缺點：

- 雖然大部分資料庫保證單一查詢的資料一致性，但多個查詢沒有這類保證存在。 如果在執行查詢時同時更新資料庫，則產生的資料可能不一致。 您可以藉由將查詢包裝在可序列化或快照集交易中來加以減輕，雖然這樣做可能會產生自己的效能問題。 如需詳細資訊，請參閱資料庫檔案。
- 每個查詢目前暗示對您資料庫的額外網路往返。 多個網路往返可能會降低效能，特別是在資料庫的延遲很高 (例如雲端服務) 的情況下。
- 雖然某些資料庫允許同時使用多個查詢的結果 (使用 MARS 的 SQL Server，Sqlite) 大部分只允許在任何給定的時間點啟用單一查詢。 因此，必須先在應用程式的記憶體中緩衝處理舊版查詢的所有結果，然後再執行較新的查詢，這會導致記憶體需求增加。

可惜的是，載入適用于所有案例的相關實體沒有一個策略。 請仔細考慮單一和分割查詢的優點和缺點，以選取符合您需求的查詢。
