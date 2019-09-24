---
title: 基本儲存 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
uid: core/saving/basic
ms.openlocfilehash: 6f72458504a9dbe99038af7cfd23b6991258f6b8
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197784"
---
# <a name="basic-save"></a><span data-ttu-id="e2960-102">基本儲存</span><span class="sxs-lookup"><span data-stu-id="e2960-102">Basic Save</span></span>

<span data-ttu-id="e2960-103">了解如何使用您的內容和實體類別來新增、修改及移除資料。</span><span class="sxs-lookup"><span data-stu-id="e2960-103">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]  
> <span data-ttu-id="e2960-104">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="e2960-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="e2960-105">加入資料</span><span class="sxs-lookup"><span data-stu-id="e2960-105">Adding Data</span></span>

<span data-ttu-id="e2960-106">使用 *DbSet.Add* 方法來新增實體類別的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="e2960-106">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="e2960-107">當您呼叫 *SaveChanges*時，將會在資料庫中插入資料。</span><span class="sxs-lookup"><span data-stu-id="e2960-107">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> <span data-ttu-id="e2960-108">如[相關資料](related-data.md)一節中所述，Add、Attach 及 Update 方法對於傳遞給它們的整個實體圖表都適用。</span><span class="sxs-lookup"><span data-stu-id="e2960-108">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](related-data.md) section.</span></span> <span data-ttu-id="e2960-109">或者，您也可以使用 EntityEntry.State 屬性來僅設定單一實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="e2960-109">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="e2960-110">例如，`context.Entry(blog).State = EntityState.Modified`。</span><span class="sxs-lookup"><span data-stu-id="e2960-110">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="e2960-111">更新資料</span><span class="sxs-lookup"><span data-stu-id="e2960-111">Updating Data</span></span>

<span data-ttu-id="e2960-112">EF 會針對內容所追蹤的現有實體，自動偵測對實體所進行的變更。</span><span class="sxs-lookup"><span data-stu-id="e2960-112">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="e2960-113">這包括您從資料庫載入/查詢的實體，以及先前新增並儲存至資料庫的實體。</span><span class="sxs-lookup"><span data-stu-id="e2960-113">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="e2960-114">請直接修改指派給屬性的值，然後呼叫 *SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="e2960-114">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="e2960-115">刪除資料</span><span class="sxs-lookup"><span data-stu-id="e2960-115">Deleting Data</span></span>

<span data-ttu-id="e2960-116">使用 *DbSet.Remove* 方法來刪除實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="e2960-116">Use the *DbSet.Remove* method to delete instances of your entity classes.</span></span>

<span data-ttu-id="e2960-117">如果實體已經存在於資料庫中，在 *SaveChanges* 期間將會予以刪除。</span><span class="sxs-lookup"><span data-stu-id="e2960-117">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="e2960-118">如果尚未將實體儲存至資料庫 (亦即，其追蹤狀態為已新增)，就會從內容中將其移除，而在呼叫 *SaveChanges* 時，便不再將它插入。</span><span class="sxs-lookup"><span data-stu-id="e2960-118">If the entity has not yet been saved to the database (that is, it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="e2960-119">在單一 SaveChanges 中執行多個作業</span><span class="sxs-lookup"><span data-stu-id="e2960-119">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="e2960-120">您可以將多個新增/更新/移除作業結合成對 *SaveChanges* 的單一呼叫。</span><span class="sxs-lookup"><span data-stu-id="e2960-120">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]  
> <span data-ttu-id="e2960-121">對大多數資料庫提供者來說，*SaveChanges* 為交易式。</span><span class="sxs-lookup"><span data-stu-id="e2960-121">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="e2960-122">這意謂著所有作業不是成功就是失敗，作業一律不會只有部分套用的情況。</span><span class="sxs-lookup"><span data-stu-id="e2960-122">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
