---
title: 儲存相關資料 - EF Core
description: 在 Entity Framework Core 中儲存相關實體和管理關聯性圖形的相關資訊
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/related-data
ms.openlocfilehash: f1cc752587e6a3eb58d070f5f573450b51986f70
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129092"
---
# <a name="saving-related-data"></a><span data-ttu-id="0d273-103">儲存相關資料</span><span class="sxs-lookup"><span data-stu-id="0d273-103">Saving Related Data</span></span>

<span data-ttu-id="0d273-104">儲存隔離的實體之外，您也可以利用模型中所定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0d273-104">In addition to isolated entities, you can also make use of the relationships defined in your model.</span></span>

> [!TIP]
> <span data-ttu-id="0d273-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="0d273-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) on GitHub.</span></span>

## <a name="adding-a-graph-of-new-entities"></a><span data-ttu-id="0d273-106">新增新實體的圖表</span><span class="sxs-lookup"><span data-stu-id="0d273-106">Adding a graph of new entities</span></span>

<span data-ttu-id="0d273-107">如果您建立數個新的相關實體，則將其中一個實體新增至內容中時，也會一併新增其他實體。</span><span class="sxs-lookup"><span data-stu-id="0d273-107">If you create several new related entities, adding one of them to the context will cause the others to be added too.</span></span>

<span data-ttu-id="0d273-108">在下列範例中，會將部落格及三篇相關文章都插入到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="0d273-108">In the following example, the blog and three related posts are all inserted into the database.</span></span> <span data-ttu-id="0d273-109">系統會找出並新增文章，因為可以透過 `Blog.Posts` 導覽屬性觸達這些文章。</span><span class="sxs-lookup"><span data-stu-id="0d273-109">The posts are found and added, because they are reachable via the `Blog.Posts` navigation property.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]
> <span data-ttu-id="0d273-110">請使用 EntityEntry.State 屬性來僅設定單一實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="0d273-110">Use the EntityEntry.State property to set the state of just a single entity.</span></span> <span data-ttu-id="0d273-111">例如： `context.Entry(blog).State = EntityState.Modified` 。</span><span class="sxs-lookup"><span data-stu-id="0d273-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="adding-a-related-entity"></a><span data-ttu-id="0d273-112">新增相關實體</span><span class="sxs-lookup"><span data-stu-id="0d273-112">Adding a related entity</span></span>

<span data-ttu-id="0d273-113">如果您從內容所追蹤實體的導覽屬性參考新的實體，系統將會探索到該實體並插入到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="0d273-113">If you reference a new entity from the navigation property of an entity that is already tracked by the context, the entity will be discovered and inserted into the database.</span></span>

<span data-ttu-id="0d273-114">在下列範例中，會插入 `post` 實體，因為該實體已新增至擷取自資料庫 `blog` 實體的 `Posts` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0d273-114">In the following example, the `post` entity is inserted because it is added to the `Posts` property of the `blog` entity which was fetched from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a><span data-ttu-id="0d273-115">變更關聯性</span><span class="sxs-lookup"><span data-stu-id="0d273-115">Changing relationships</span></span>

<span data-ttu-id="0d273-116">如果您變更某個實體的導覽屬性，系統將會對資料庫中的外部索引鍵資料行進行對應的變更。</span><span class="sxs-lookup"><span data-stu-id="0d273-116">If you change the navigation property of an entity, the corresponding changes will be made to the foreign key column in the database.</span></span>

<span data-ttu-id="0d273-117">在下列範例中，會將 `post` 實體更新成屬於新的 `blog` 實體，因為其 `Blog` 導覽屬性是設定為指向 `blog`。</span><span class="sxs-lookup"><span data-stu-id="0d273-117">In the following example, the `post` entity is updated to belong to the new `blog` entity because its `Blog` navigation property is set to point to `blog`.</span></span> <span data-ttu-id="0d273-118">請注意，系統也會將 `blog` 插入到資料庫中，因為它是內容所追蹤實體 (`post`) 的導覽屬性所參考的新實體。</span><span class="sxs-lookup"><span data-stu-id="0d273-118">Note that `blog` will also be inserted into the database because it is a new entity that is referenced by the navigation property of an entity that is already tracked by the context (`post`).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a><span data-ttu-id="0d273-119">移除關聯性</span><span class="sxs-lookup"><span data-stu-id="0d273-119">Removing relationships</span></span>

<span data-ttu-id="0d273-120">您可以藉由將參考導覽設定為 `null`，或從集合導覽中移除相關實體，來移除關聯性。</span><span class="sxs-lookup"><span data-stu-id="0d273-120">You can remove a relationship by setting a reference navigation to `null`, or removing the related entity from a collection navigation.</span></span>

<span data-ttu-id="0d273-121">根據關聯性中所設定的串聯刪除行為，移除關聯性可能會對相依實體產生副作用。</span><span class="sxs-lookup"><span data-stu-id="0d273-121">Removing a relationship can have side effects on the dependent entity, according to the cascade delete behavior configured in the relationship.</span></span>

<span data-ttu-id="0d273-122">針對必要關聯性，預設會設定串聯刪除行為，系統將會從資料庫中刪除子系/相依實體。</span><span class="sxs-lookup"><span data-stu-id="0d273-122">By default, for required relationships, a cascade delete behavior is configured and the child/dependent entity will be deleted from the database.</span></span> <span data-ttu-id="0d273-123">針對選擇性關聯性，預設並不會設定串聯刪除，但外部索引鍵屬性將會設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="0d273-123">For optional relationships, cascade delete is not configured by default, but the foreign key property will be set to null.</span></span>

<span data-ttu-id="0d273-124">若要了解如何設定關聯性的必要性，請參閱[必要和選擇性關聯性](xref:core/modeling/relationships#required-and-optional-relationships)。</span><span class="sxs-lookup"><span data-stu-id="0d273-124">See [Required and Optional Relationships](xref:core/modeling/relationships#required-and-optional-relationships) to learn about how the requiredness of relationships can be configured.</span></span>

<span data-ttu-id="0d273-125">如需有關串聯刪除行為如何運作、如何明確設定這些行為及如何依慣例選取這些行為的更多詳細資料，請參閱[串聯刪除](xref:core/saving/cascade-delete)。</span><span class="sxs-lookup"><span data-stu-id="0d273-125">See [Cascade Delete](xref:core/saving/cascade-delete) for more details on how cascade delete behaviors work, how they can be configured explicitly and  how they are selected by convention.</span></span>

<span data-ttu-id="0d273-126">在下列範例中，在 `Blog` 與 `Post` 之間的關聯性上已設定串聯刪除，因此會從資料庫中刪除 `post`實體。</span><span class="sxs-lookup"><span data-stu-id="0d273-126">In the following example, a cascade delete is configured on the relationship between `Blog` and `Post`, so the `post` entity is deleted from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
