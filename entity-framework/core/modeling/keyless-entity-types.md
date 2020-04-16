---
title: 無鑰匙實體型態 - EF 核心
description: 如何使用實體框架核心設定無鑰匙實體類型
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434210"
---
# <a name="keyless-entity-types"></a>無索引鍵實體類型

> [!NOTE]
> 此功能在 EF Core 2.1 中以查詢類型的名義添加。 在 EF Core 3.0 中,概念重新命名為無鑰匙實體類型。

除了常規實體類型外,EF Core 模型還可以包含_無鍵實體類型_,可用於對不包含鍵值的數據執行資料庫查詢。

## <a name="defining-keyless-entity-types"></a>定義無鍵實體類型

可以使用資料註解或 Fluent API 定義無鍵實體類型:

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>沒有鍵實體類型特徵

無鍵實體類型支援許多與常規實體類型相同的映射功能,如繼承映射和導航屬性。 在關係存儲上,他們可以通過流暢的 API 方法或數據註釋配置目標資料庫物件和列。

但是,它們不同於常規實體類型,因為它們:

- 無法定義金鑰。
- 永遠不會追蹤_DbContext_中的更改,因此永遠不會在資料庫中插入、更新或刪除。
- 從來都沒有被慣例發現。
- 僅支持導航映射功能的子集,特別是:
  - 他們可能永遠不會作為關係的主要目的。
  - 它們可能沒有指向擁有的實體的導航
  - 它們只能包含指向常規實體的引用導航屬性。
  - 實體不能包含無鍵實體類型的導航屬性。
- 需要使用`[Keyless]`數據註釋或方法調用`.HasNoKey()`進行 配置。
- 可以映射到_定義查詢_。 定義查詢是在模型中聲明的查詢,它充當無鍵實體類型的數據源。

## <a name="usage-scenarios"></a>使用方式情節

沒有鍵實體類型的一些主要使用機制是:

- 用作[原始 SQL 查詢的](xref:core/querying/raw-sql)傳回類型。
- 映射到不包含主鍵的資料庫檢視。
- 映射到未定義主鍵的表。
- 映射到模型中定義的查詢。

## <a name="mapping-to-database-objects"></a>映射到資料庫物件

使用`ToTable`或`ToView`fluent API 將無鍵實體類型映射到資料庫物件。 從 EF Core 的角度來看,此方法中指定的資料庫物件是一個_檢視_,這意味著它被視為唯讀查詢來源,不能成為更新、插入或刪除操作的目標。 但是,這並不意味著資料庫對象實際上需要是資料庫視圖。 它也可以是資料庫表,將被視為唯讀。 相反,對於常規實體類型,EF Core`ToTable`假定 方法中指定的資料庫物件可以視為_表_,這意味著它可以用作查詢源,但也可以通過更新、刪除和插入操作作為目標。 事實上,您可以指定中的`ToTable`資料庫檢視的名稱,只要檢視配置為在資料庫上可升算,所有內容都應該正常工作。

> [!NOTE]
> `ToView`假定該物件已存在於資料庫中,並且不會由遷移創建。

## <a name="example"></a>範例

下面的範例展示如何使用無鍵實體類型來查詢資料庫檢視。

> [!TIP]
> 您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)。

首先,我們定義了一個簡單的博客和帖子模型:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

接下來,我們定義一個簡單的資料庫視圖,該視圖將使我們能夠查詢與每個博客關聯的帖子數:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

接下來,我們定義一個類來保存資料庫視圖的結果:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

接下來,我們使用`HasNoKey`API 在_OnModel 創建_中配置無鑰匙實體類型。
我們使用流暢的設定 API 設定無鑰匙實體類型的對應:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

接下來,我們將 設定`DbContext`為`DbSet<T>`包括 :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最後,我們可以以標準方式查詢資料庫檢視:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> 請注意,我們還定義了上下文級別查詢屬性 (DbSet), 以充噹針對此類型的查詢的根。
