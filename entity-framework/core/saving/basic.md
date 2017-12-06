---
title: "基本的儲存層 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
ms.technology: entity-framework-core
uid: core/saving/basic
ms.openlocfilehash: e99d755b8f7c42b15a73cfdb6a2f8999a405a739
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="basic-save"></a><span data-ttu-id="1d6af-102">基本的儲存</span><span class="sxs-lookup"><span data-stu-id="1d6af-102">Basic Save</span></span>

<span data-ttu-id="1d6af-103">了解如何新增、 修改和移除使用您的內容和實體類別的資料。</span><span class="sxs-lookup"><span data-stu-id="1d6af-103">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]  
> <span data-ttu-id="1d6af-104">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="1d6af-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="1d6af-105">加入資料</span><span class="sxs-lookup"><span data-stu-id="1d6af-105">Adding Data</span></span>

<span data-ttu-id="1d6af-106">使用*DbSet.Add*方法，將實體類別的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="1d6af-106">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="1d6af-107">資料會插入資料庫中，當您呼叫*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="1d6af-107">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

## <a name="updating-data"></a><span data-ttu-id="1d6af-108">更新資料</span><span class="sxs-lookup"><span data-stu-id="1d6af-108">Updating Data</span></span>

<span data-ttu-id="1d6af-109">EF 會自動偵測現有的實體會受到內容追蹤時所做的變更。</span><span class="sxs-lookup"><span data-stu-id="1d6af-109">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="1d6af-110">這包括您載入/查詢從資料庫中的實體和實體，先前加入及儲存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="1d6af-110">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="1d6af-111">只需修改指派給屬性的值，然後呼叫*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="1d6af-111">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="1d6af-112">刪除資料</span><span class="sxs-lookup"><span data-stu-id="1d6af-112">Deleting Data</span></span>

<span data-ttu-id="1d6af-113">使用*DbSet.Remove*方法，以刪除您的實體類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="1d6af-113">Use the *DbSet.Remove* method to delete instances of you entity classes.</span></span>

<span data-ttu-id="1d6af-114">若實體已經存在資料庫中，則會刪除期間*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="1d6af-114">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="1d6af-115">如果實體尚未儲存到資料庫 （也就是它會追蹤中加入） 然後從內容中移除，並將無法再插入時*SaveChanges*呼叫。</span><span class="sxs-lookup"><span data-stu-id="1d6af-115">If the entity has not yet been saved to the database (i.e. it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="1d6af-116">多個在單一的 SaveChanges 作業</span><span class="sxs-lookup"><span data-stu-id="1d6af-116">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="1d6af-117">您可以結合多項新增/更新/移除作業的單一呼叫*SaveChanges*。</span><span class="sxs-lookup"><span data-stu-id="1d6af-117">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]  
> <span data-ttu-id="1d6af-118">對於大部分的資料庫提供者， *SaveChanges*為交易式。</span><span class="sxs-lookup"><span data-stu-id="1d6af-118">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="1d6af-119">這表示所有作業將會成功或失敗，作業將會永遠不會向左部分套用。</span><span class="sxs-lookup"><span data-stu-id="1d6af-119">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]
