---
title: 無索引鍵實體類型-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 3dbc2700fc9bb277eb90885dfc2506c250ae21f1
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445941"
---
# <a name="keyless-entity-types"></a>無索引鍵實體類型

> [!NOTE]
> 這項功能已在 EF Core 2.1 中的查詢類型名稱下新增。 在 EF Core 3.0 中，概念已重新命名為無索引鍵的實體類型。

除了一般實體類型之外，EF Core 模型也可以包含_無索引鍵的實體類型_，可用來對不包含索引鍵值的資料執行資料庫查詢。

## <a name="keyless-entity-types-characteristics"></a>無索引鍵實體類型特性

無索引鍵實體類型支援許多與一般實體類型相同的對應功能，例如繼承對應和導覽屬性。 在關聯式存放區上，他們可以透過 Fluent API 方法或資料批註來設定目標資料庫物件和資料行。

不過，它們與一般實體類型不同之處如下：

- 不能定義索引鍵。
- 永遠不會針對_DbCoNtext_中的變更進行追蹤，因此永遠不會在資料庫上插入、更新或刪除。
- 一律不會依照慣例來探索。
- 僅支援導覽對應功能的子集，特別是：
  - 它們可能永遠不會做為關聯性的主要端點。
  - 他們可能不會有所擁有實體的導覽
  - 它們只能包含指向一般實體的參考導覽屬性。
  - 實體不能包含無索引鍵實體類型的導覽屬性。
- 需要使用 `.HasNoKey()` 方法呼叫來設定。
- 可能會對應到_定義查詢_。 定義查詢是在模型中宣告的查詢，做為無索引鍵實體類型的資料來源。

## <a name="usage-scenarios"></a>使用案例

無索引鍵實體類型的一些主要使用案例如下：

- 做為[原始 SQL 查詢](xref:core/querying/raw-sql)的傳回型別。
- 對應至不含主鍵的資料庫 views。
- 對應至未定義主鍵的資料表。
- 對應至模型中定義的查詢。

## <a name="mapping-to-database-objects"></a>對應至資料庫物件

將無索引鍵實體類型對應至資料庫物件是使用 `ToTable` 或 `ToView` Fluent API 來達成。 從 EF Core 的觀點來看，這個方法中指定的資料庫物件是一個_視圖_，這表示它會被視為唯讀查詢來源，而且不能是 update、insert 或 delete 作業的目標。 不過，這並不表示資料庫物件實際上必須是資料庫的視圖。 它也可以是將視為唯讀的資料庫資料表。 相反地，針對一般實體類型，EF Core 會假設在 `ToTable` 方法中指定的資料庫物件可以視為_資料表_，這表示它可以做為查詢來源，但也可做為更新、刪除和插入作業的目標。 事實上，您可以在 `ToTable` 中指定資料庫檢視的名稱，只要將此視圖設定為可在資料庫上更新，一切都應該能正常執行。

> [!NOTE]
> `ToView` 會假設物件已經存在於資料庫中，而且不會由遷移所建立。

## <a name="example"></a>範例

下列範例顯示如何使用無索引鍵實體類型來查詢資料庫檢視。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) \(英文\)。

首先，我們會定義簡單的 Blog 和 Post 模型：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

接下來，我們會定義一個簡單的資料庫檢視，讓我們查詢與每個 blog 相關聯的貼文數目：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

接下來，我們會定義一個類別，以保存資料庫檢視的結果：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

接下來，我們會使用 `HasNoKey` API 來設定_OnModelCreating_中的無索引鍵實體類型。
我們會使用流暢的設定 API 來設定無索引鍵實體類型的對應：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

接下來，我們會將 `DbContext` 設定為包含 `DbSet<T>`：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最後，我們可以透過標準方式來查詢資料庫檢視：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> 請注意，我們也定義了內容層級查詢屬性（DbSet），以做為此類型之查詢的根。
