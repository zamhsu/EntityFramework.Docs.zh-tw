---
title: "儲存相關資料的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
ms.technology: entity-framework-core
uid: core/saving/related-data
ms.openlocfilehash: 078879163002cb66e0f0f439415789963181ec15
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="saving-related-data"></a>儲存相關的資料

除了隔離的實體，您也可以將使用模型中定義的關聯性。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/RelatedData/)GitHub 上。

## <a name="adding-a-graph-of-new-entities"></a>加入新的實體圖形

如果您建立新的數個相關的實體，加入其中的內容將會造成太加入其他。

在下列範例中，部落格和三個相關的文章所有插入資料庫。 找到及加入，因為它們是可透過連線公佈`Blog.Posts`導覽屬性。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

## <a name="adding-a-related-entity"></a>新增相關的實體

如果您從導覽屬性，已經受到內容追蹤時的實體參考新的實體，將探索到的實體，並插入至資料庫。

在下列範例中，`post`會插入實體，因為它會加入至`Posts`屬性`blog`從資料庫中提取的實體。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>變更關聯性

如果您變更實體的導覽屬性，對應的變更將會對資料庫中的外部索引鍵資料行。

在下列範例中，`post`實體會更新為隸屬於新`blog`實體因為其`Blog`瀏覽屬性設定為指向`blog`。 請注意，`blog`就也會插入到資料庫，所以它已經受到內容追蹤實體的導覽屬性所參考的新實體 (`post`)。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>移除關聯性

您可以移除關聯性設為參考導覽`null`，或移除相關的實體集合的巡覽。

移除關聯性可以有相依的實體上的副作用，根據串聯刪除關聯性中設定的行為。

根據預設，針對必要的關聯性，串聯刪除行為設定，將會從資料庫刪除子/相依實體。 選擇性的關聯性的串聯刪除未設定依預設，但將設定外部索引鍵屬性為 null。

請參閱[必要和選擇性的關聯性](../modeling/relationships.md#required-and-optional-relationships)若要了解如何設定 requiredness 的關聯性。

請參閱[Cascade Delete](cascade-delete.md)的 cascade delete 行為方式的詳細工作，他們可以設定的方式明確及如何選取依慣例。

在下列範例中，串聯刪除設定之間的關聯性`Blog`和`Post`，因此`post`從資料庫刪除實體。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#RemovingRelationships)]
