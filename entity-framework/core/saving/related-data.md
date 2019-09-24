---
title: 儲存相關資料 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
uid: core/saving/related-data
ms.openlocfilehash: 45c7b8e4bfa4ce7967ad76ef4a7d4818b0d3aebf
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197886"
---
# <a name="saving-related-data"></a>儲存相關資料

儲存隔離的實體之外，您也可以利用模型中所定義的關聯性。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) \(英文\)。

## <a name="adding-a-graph-of-new-entities"></a>新增新實體的圖表

如果您建立數個新的相關實體，則將其中一個實體新增至內容中時，也會一併新增其他實體。

在下列範例中，會將部落格及三篇相關文章都插入到資料庫中。 系統會找出並新增文章，因為可以透過 `Blog.Posts` 導覽屬性觸達這些文章。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> 請使用 EntityEntry.State 屬性來僅設定單一實體的狀態。 例如，`context.Entry(blog).State = EntityState.Modified`。

## <a name="adding-a-related-entity"></a>新增相關實體

如果您從內容所追蹤實體的導覽屬性參考新的實體，系統將會探索到該實體並插入到資料庫中。

在下列範例中，會插入 `post` 實體，因為該實體已新增至擷取自資料庫 `blog` 實體的 `Posts` 屬性。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>變更關聯性

如果您變更某個實體的導覽屬性，系統將會對資料庫中的外部索引鍵資料行進行對應的變更。

在下列範例中，會將 `post` 實體更新成屬於新的 `blog` 實體，因為其 `Blog` 導覽屬性是設定為指向 `blog`。 請注意，系統也會將 `blog` 插入到資料庫中，因為它是內容所追蹤實體 (`post`) 的導覽屬性所參考的新實體。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>移除關聯性

您可以藉由將參考導覽設定為 `null`，或從集合導覽中移除相關實體，來移除關聯性。

根據關聯性中所設定的串聯刪除行為，移除關聯性可能會對相依實體產生副作用。

針對必要關聯性，預設會設定串聯刪除行為，系統將會從資料庫中刪除子系/相依實體。 針對選擇性關聯性，預設並不會設定串聯刪除，但外部索引鍵屬性將會設定為 Null。

若要了解如何設定關聯性的必要性，請參閱[必要和選擇性關聯性](../modeling/relationships.md#required-and-optional-relationships)。

如需有關串聯刪除行為如何運作、如何明確設定這些行為及如何依慣例選取這些行為的更多詳細資料，請參閱[串聯刪除](cascade-delete.md)。

在下列範例中，在 `Blog` 與 `Post` 之間的關聯性上已設定串聯刪除，因此會從資料庫中刪除 `post`實體。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
