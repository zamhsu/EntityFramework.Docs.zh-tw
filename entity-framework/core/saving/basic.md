---
title: 基本的儲存層 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
ms.technology: entity-framework-core
uid: core/saving/basic
ms.openlocfilehash: deead323301dc4a0ee0748b4536ddff4596b99e6
ms.sourcegitcommit: 4997314356118d0d97b04ad82e433e49bb9420a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="basic-save"></a><span data-ttu-id="ae1da-102">基本的儲存</span><span class="sxs-lookup"><span data-stu-id="ae1da-102">Basic Save</span></span>

<span data-ttu-id="ae1da-103">了解如何新增、 修改和移除使用您的內容和實體類別的資料。</span><span class="sxs-lookup"><span data-stu-id="ae1da-103">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]  
> <span data-ttu-id="ae1da-104">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="ae1da-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="ae1da-105">加入資料</span><span class="sxs-lookup"><span data-stu-id="ae1da-105">Adding Data</span></span>

<span data-ttu-id="ae1da-106">使用*DbSet.Add*方法，將實體類別的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="ae1da-106">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="ae1da-107">資料會插入資料庫中，當您呼叫*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="ae1da-107">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> <span data-ttu-id="ae1da-108">中所述，所有工作之實體的完整的圖形的 Add、 附加、 和更新方法傳遞給它們，[相關資料](related-data.md)> 一節。</span><span class="sxs-lookup"><span data-stu-id="ae1da-108">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](related-data.md) section.</span></span> <span data-ttu-id="ae1da-109">或者，EntityEntry.State 屬性可以用來設定只在單一實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="ae1da-109">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="ae1da-110">例如，`context.Entry(blog).State = EntityState.Modified`。</span><span class="sxs-lookup"><span data-stu-id="ae1da-110">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="ae1da-111">更新資料</span><span class="sxs-lookup"><span data-stu-id="ae1da-111">Updating Data</span></span>

<span data-ttu-id="ae1da-112">EF 會自動偵測現有的實體會受到內容追蹤時所做的變更。</span><span class="sxs-lookup"><span data-stu-id="ae1da-112">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="ae1da-113">這包括您載入/查詢從資料庫中的實體和實體，先前加入及儲存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae1da-113">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="ae1da-114">只需修改指派給屬性的值，然後呼叫*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="ae1da-114">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="ae1da-115">刪除資料</span><span class="sxs-lookup"><span data-stu-id="ae1da-115">Deleting Data</span></span>

<span data-ttu-id="ae1da-116">使用*DbSet.Remove*方法，以刪除您的實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="ae1da-116">Use the *DbSet.Remove* method to delete instances of you entity classes.</span></span>

<span data-ttu-id="ae1da-117">若實體已經存在資料庫中，則會刪除期間*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="ae1da-117">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="ae1da-118">如果實體尚未儲存到資料庫 （也就是它會追蹤中加入） 然後從內容中移除，並將無法再插入時*SaveChanges*呼叫。</span><span class="sxs-lookup"><span data-stu-id="ae1da-118">If the entity has not yet been saved to the database (i.e. it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="ae1da-119">多個在單一的 SaveChanges 作業</span><span class="sxs-lookup"><span data-stu-id="ae1da-119">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="ae1da-120">您可以結合多項新增/更新/移除作業的單一呼叫*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="ae1da-120">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]  
> <span data-ttu-id="ae1da-121">對於大部分的資料庫提供者， *SaveChanges*為交易式。</span><span class="sxs-lookup"><span data-stu-id="ae1da-121">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="ae1da-122">這表示所有作業將會成功或失敗，作業將會永遠不會向左部分套用。</span><span class="sxs-lookup"><span data-stu-id="ae1da-122">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]
