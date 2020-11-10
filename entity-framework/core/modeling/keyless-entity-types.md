---
title: 無索引鍵實體類型-EF Core
description: 如何使用 Entity Framework Core 設定無索引鍵實體類型
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: d1a60e0504b22623b97c1a4963d2e3f70faa365c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429503"
---
# <a name="keyless-entity-types"></a>無索引鍵實體類型

> [!NOTE]
> 這項功能是在查詢類型的名稱下加入。 在 EF Core 3.0 中，概念已重新命名為無索引鍵的實體類型。 `[Keyless]`資料批註在 EFCore 5.0 中變成可供使用。

除了一般實體類型之外，EF Core 模型也可以包含 _無索引鍵的實體類型_ ，可用來針對不包含索引鍵值的資料執行資料庫查詢。

## <a name="defining-keyless-entity-types"></a>定義無索引鍵實體類型

您可以使用資料批註或流暢的 API 來定義無索引鍵實體類型：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>無索引鍵實體類型特性

無索引鍵實體類型支援許多與一般實體類型相同的對應功能，例如繼承對應和導覽屬性。 在關聯式存放區上，他們可以透過流暢的 API 方法或資料批註來設定目標資料庫物件和資料行。

不過，它們與一般實體類型不同，因為它們：

- 無法定義索引鍵。
- 永遠不會追蹤 _DbCoNtext_ 中的變更，因此永遠不會在資料庫中插入、更新或刪除。
- 慣例不會探索到。
- 只支援一組流覽對應功能，特別是：
  - 它們絕對不能做為關聯性的主要端點。
  - 他們可能沒有擁有實體的導覽
  - 它們只能包含指向一般實體的參考導覽屬性。
  - 實體不能包含無索引鍵實體類型的導覽屬性。
- 需要使用 `[Keyless]` 資料批註或 `.HasNoKey()` 方法呼叫來設定。
- 可能對應至 _定義查詢_ 。 定義查詢是在模型中宣告的查詢，可作為無索引鍵實體類型的資料來源。

## <a name="usage-scenarios"></a>使用方式情節

無索引鍵實體類型的部分主要使用案例如下：

- 做為 [原始 SQL 查詢](xref:core/querying/raw-sql)的傳回型別。
- 對應至不含主鍵的資料庫 views。
- 對應至未定義主鍵的資料表。
- 對應至模型中定義的查詢。

## <a name="mapping-to-database-objects"></a>對應至資料庫物件

使用或流暢的 API 即可將無索引鍵實體類型對應至資料庫 `ToTable` 物件 `ToView` 。 從 EF Core 的觀點來看，這個方法中所指定的資料庫物件是一個 _視圖_ ，這表示它會被視為唯讀查詢來源，而不能是 update、insert 或 delete 作業的目標。 但是，這並不表示資料庫物件實際上必須是資料庫的視圖。 它也可以是將被視為唯讀的資料庫資料表。 相反地，對於一般的實體類型，EF Core 會假設在方法中指定的資料庫物件 `ToTable` 可以視為 _資料表_ ，這表示它可以當做查詢來源使用，也可作為 update、delete 和 insert 作業的目標。 事實上，您可以在中指定資料庫的名稱 `ToTable` ，而且只要視圖設定為可在資料庫上更新，一切都應可正常運作。

> [!NOTE]
> `ToView` 假設物件已經存在於資料庫中，而不是由遷移所建立。

## <a name="example"></a>範例

下列範例示範如何使用無索引鍵的實體類型來查詢資料庫檢視。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) \(英文\)。

首先，我們會定義簡單的 Blog 和 Post 模型：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

接下來，我們會定義一個簡單的資料庫檢視，讓我們查詢與每個 blog 相關聯的貼文數目：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

接下來，我們會定義一個類別來保存資料庫檢視的結果：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

接下來，我們會使用 API 在 _OnModelCreating_ 中設定無索引鍵實體類型 `HasNoKey` 。
我們會使用流暢的設定 API 來設定無索引鍵實體類型的對應：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

接下來，我們會將設定 `DbContext` 為包含 `DbSet<T>` ：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最後，我們可以用標準方式來查詢資料庫檢視：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> 請注意，我們也定義了內容層級查詢屬性 (DbSet) 做為針對此型別進行查詢的根。

> [!TIP]
> 若要使用記憶體內部提供者來測試對應至 views 的無索引鍵實體型別，請透過將它們對應至查詢 `ToInMemoryQuery` 。 如需詳細資訊，請參閱使用此技術的可執行 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) 。
