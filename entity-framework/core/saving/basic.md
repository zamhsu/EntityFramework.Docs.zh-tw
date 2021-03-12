---
title: 基本儲存 - EF Core
description: 使用 Entity Framework Core 新增、更新和移除資料的基本資訊
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: 5b75a6f7ac43243485dc6e27e25ee0bcdd3277e4
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023636"
---
# <a name="basic-save"></a><span data-ttu-id="7b055-103">基本儲存</span><span class="sxs-lookup"><span data-stu-id="7b055-103">Basic Save</span></span>

<span data-ttu-id="7b055-104">了解如何使用您的內容和實體類別來新增、修改及移除資料。</span><span class="sxs-lookup"><span data-stu-id="7b055-104">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]
> <span data-ttu-id="7b055-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Basics/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="7b055-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="7b055-106">加入資料</span><span class="sxs-lookup"><span data-stu-id="7b055-106">Adding Data</span></span>

<span data-ttu-id="7b055-107">使用 *DbSet.Add* 方法來新增實體類別的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="7b055-107">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="7b055-108">當您呼叫 *SaveChanges* 時，將會在資料庫中插入資料。</span><span class="sxs-lookup"><span data-stu-id="7b055-108">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]
> <span data-ttu-id="7b055-109">如[相關資料](xref:core/saving/related-data)一節中所述，Add、Attach 及 Update 方法對於傳遞給它們的整個實體圖表都適用。</span><span class="sxs-lookup"><span data-stu-id="7b055-109">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](xref:core/saving/related-data) section.</span></span> <span data-ttu-id="7b055-110">或者，您也可以使用 EntityEntry.State 屬性來僅設定單一實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="7b055-110">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="7b055-111">例如： `context.Entry(blog).State = EntityState.Modified` 。</span><span class="sxs-lookup"><span data-stu-id="7b055-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="7b055-112">更新資料</span><span class="sxs-lookup"><span data-stu-id="7b055-112">Updating Data</span></span>

<span data-ttu-id="7b055-113">EF 會針對內容所追蹤的現有實體，自動偵測對實體所進行的變更。</span><span class="sxs-lookup"><span data-stu-id="7b055-113">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="7b055-114">這包括您從資料庫載入/查詢的實體，以及先前新增並儲存至資料庫的實體。</span><span class="sxs-lookup"><span data-stu-id="7b055-114">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="7b055-115">請直接修改指派給屬性的值，然後呼叫 *SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="7b055-115">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="7b055-116">刪除資料</span><span class="sxs-lookup"><span data-stu-id="7b055-116">Deleting Data</span></span>

<span data-ttu-id="7b055-117">使用 *DbSet.Remove* 方法來刪除實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="7b055-117">Use the *DbSet.Remove* method to delete instances of your entity classes.</span></span>

<span data-ttu-id="7b055-118">如果實體已經存在於資料庫中，在 *SaveChanges* 期間將會予以刪除。</span><span class="sxs-lookup"><span data-stu-id="7b055-118">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="7b055-119">如果尚未將實體儲存至資料庫 (亦即，其追蹤狀態為已新增)，就會從內容中將其移除，而在呼叫 *SaveChanges* 時，便不再將它插入。</span><span class="sxs-lookup"><span data-stu-id="7b055-119">If the entity has not yet been saved to the database (that is, it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="7b055-120">在單一 SaveChanges 中執行多個作業</span><span class="sxs-lookup"><span data-stu-id="7b055-120">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="7b055-121">您可以將多個新增/更新/移除作業結合成對 *SaveChanges* 的單一呼叫。</span><span class="sxs-lookup"><span data-stu-id="7b055-121">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]
> <span data-ttu-id="7b055-122">對大多數資料庫提供者來說，*SaveChanges* 為交易式。</span><span class="sxs-lookup"><span data-stu-id="7b055-122">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="7b055-123">這意謂著所有作業不是成功就是失敗，作業一律不會只有部分套用的情況。</span><span class="sxs-lookup"><span data-stu-id="7b055-123">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
