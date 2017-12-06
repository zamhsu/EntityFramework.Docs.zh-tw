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
# <a name="saving-related-data"></a><span data-ttu-id="e09ea-102">儲存相關的資料</span><span class="sxs-lookup"><span data-stu-id="e09ea-102">Saving Related Data</span></span>

<span data-ttu-id="e09ea-103">除了隔離的實體，您也可以將使用模型中定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e09ea-103">In addition to isolated entities, you can also make use of the relationships defined in your model.</span></span>

> [!TIP]  
> <span data-ttu-id="e09ea-104">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/RelatedData/)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="e09ea-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/RelatedData/) on GitHub.</span></span>

## <a name="adding-a-graph-of-new-entities"></a><span data-ttu-id="e09ea-105">加入新的實體圖形</span><span class="sxs-lookup"><span data-stu-id="e09ea-105">Adding a graph of new entities</span></span>

<span data-ttu-id="e09ea-106">如果您建立新的數個相關的實體，加入其中的內容將會造成太加入其他。</span><span class="sxs-lookup"><span data-stu-id="e09ea-106">If you create several new related entities, adding one of them to the context will cause the others to be added too.</span></span>

<span data-ttu-id="e09ea-107">在下列範例中，部落格和三個相關的文章所有插入資料庫。</span><span class="sxs-lookup"><span data-stu-id="e09ea-107">In the following example, the blog and three related posts are all inserted into the database.</span></span> <span data-ttu-id="e09ea-108">找到及加入，因為它們是可透過連線公佈`Blog.Posts`導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e09ea-108">The posts are found and added, because they are reachable via the `Blog.Posts` navigation property.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

## <a name="adding-a-related-entity"></a><span data-ttu-id="e09ea-109">新增相關的實體</span><span class="sxs-lookup"><span data-stu-id="e09ea-109">Adding a related entity</span></span>

<span data-ttu-id="e09ea-110">如果您從導覽屬性，已經受到內容追蹤時的實體參考新的實體，將探索到的實體，並插入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e09ea-110">If you reference a new entity from the navigation property of an entity that is already tracked by the context, the entity will be discovered and inserted into the database.</span></span>

<span data-ttu-id="e09ea-111">在下列範例中，`post`會插入實體，因為它會加入至`Posts`屬性`blog`從資料庫中提取的實體。</span><span class="sxs-lookup"><span data-stu-id="e09ea-111">In the following example, the `post` entity is inserted because it is added to the `Posts` property of the `blog` entity which was fetched from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a><span data-ttu-id="e09ea-112">變更關聯性</span><span class="sxs-lookup"><span data-stu-id="e09ea-112">Changing relationships</span></span>

<span data-ttu-id="e09ea-113">如果您變更實體的導覽屬性，對應的變更將會對資料庫中的外部索引鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="e09ea-113">If you change the navigation property of an entity, the corresponding changes will be made to the foreign key column in the database.</span></span>

<span data-ttu-id="e09ea-114">在下列範例中，`post`實體會更新為隸屬於新`blog`實體因為其`Blog`瀏覽屬性設定為指向`blog`。</span><span class="sxs-lookup"><span data-stu-id="e09ea-114">In the following example, the `post` entity is updated to belong to the new `blog` entity because its `Blog` navigation property is set to point to `blog`.</span></span> <span data-ttu-id="e09ea-115">請注意，`blog`就也會插入到資料庫，所以它已經受到內容追蹤實體的導覽屬性所參考的新實體 (`post`)。</span><span class="sxs-lookup"><span data-stu-id="e09ea-115">Note that `blog` will also be inserted into the database because it is a new entity that is referenced by the navigation property of an entity that is already tracked by the context (`post`).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a><span data-ttu-id="e09ea-116">移除關聯性</span><span class="sxs-lookup"><span data-stu-id="e09ea-116">Removing relationships</span></span>

<span data-ttu-id="e09ea-117">您可以移除關聯性設為參考導覽`null`，或移除相關的實體集合的巡覽。</span><span class="sxs-lookup"><span data-stu-id="e09ea-117">You can remove a relationship by setting a reference navigation to `null`, or removing the related entity from a collection navigation.</span></span>

<span data-ttu-id="e09ea-118">移除關聯性可以有相依的實體上的副作用，根據串聯刪除關聯性中設定的行為。</span><span class="sxs-lookup"><span data-stu-id="e09ea-118">Removing a relationship can have side effects on the dependent entity, according to the cascade delete behavior configured in the relationship.</span></span>

<span data-ttu-id="e09ea-119">根據預設，針對必要的關聯性，串聯刪除行為設定，將會從資料庫刪除子/相依實體。</span><span class="sxs-lookup"><span data-stu-id="e09ea-119">By default, for required relationships, a cascade delete behavior is configured and the child/dependent entity will be deleted from the database.</span></span> <span data-ttu-id="e09ea-120">選擇性的關聯性的串聯刪除未設定依預設，但將設定外部索引鍵屬性為 null。</span><span class="sxs-lookup"><span data-stu-id="e09ea-120">For optional relationships, cascade delete is not configured by default, but the foreign key property will be set to null.</span></span>

<span data-ttu-id="e09ea-121">請參閱[必要和選擇性的關聯性](../modeling/relationships.md#required-and-optional-relationships)若要了解如何設定 requiredness 的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e09ea-121">See [Required and Optional Relationships](../modeling/relationships.md#required-and-optional-relationships) to learn about how the requiredness of relationships can be configured.</span></span>

<span data-ttu-id="e09ea-122">請參閱[Cascade Delete](cascade-delete.md)的 cascade delete 行為方式的詳細工作，他們可以設定的方式明確及如何選取依慣例。</span><span class="sxs-lookup"><span data-stu-id="e09ea-122">See [Cascade Delete](cascade-delete.md) for more details on how cascade delete behaviors work, how they can be configured explicitly and  how they are selected by convention.</span></span>

<span data-ttu-id="e09ea-123">在下列範例中，串聯刪除設定之間的關聯性`Blog`和`Post`，因此`post`從資料庫刪除實體。</span><span class="sxs-lookup"><span data-stu-id="e09ea-123">In the following example, a cascade delete is configured on the relationship between `Blog` and `Post`, so the `post` entity is deleted from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#RemovingRelationships)]
