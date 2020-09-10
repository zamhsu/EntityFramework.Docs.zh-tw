---
title: 自動偵測變更-EF6
description: 自動偵測 Entity Framework 6 中的變更
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: 938af809ac89d3490da9885497fc5601453e1c34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619992"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="7de8b-103">自動偵測變更</span><span class="sxs-lookup"><span data-stu-id="7de8b-103">Automatic detect changes</span></span>
<span data-ttu-id="7de8b-104">使用大部分的 POCO 實體時，會決定實體如何變更 (，因此必須將更新傳送至資料庫) 由「偵測變更」演算法處理。</span><span class="sxs-lookup"><span data-stu-id="7de8b-104">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="7de8b-105">偵測變更的運作方式是偵測實體的目前屬性值與在查詢或附加實體時儲存在快照中的原始屬性值之間的差異。</span><span class="sxs-lookup"><span data-stu-id="7de8b-105">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="7de8b-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="7de8b-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="7de8b-107">根據預設，當呼叫下列方法時，Entity Framework 會自動執行偵測變更：</span><span class="sxs-lookup"><span data-stu-id="7de8b-107">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="7de8b-108">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="7de8b-108">DbSet.Find</span></span>  
- <span data-ttu-id="7de8b-109">DbSet</span><span class="sxs-lookup"><span data-stu-id="7de8b-109">DbSet.Local</span></span>  
- <span data-ttu-id="7de8b-110">DbSet 新增</span><span class="sxs-lookup"><span data-stu-id="7de8b-110">DbSet.Add</span></span>  
- <span data-ttu-id="7de8b-111">DbSet. >addrange</span><span class="sxs-lookup"><span data-stu-id="7de8b-111">DbSet.AddRange</span></span>
- <span data-ttu-id="7de8b-112">DbSet。移除</span><span class="sxs-lookup"><span data-stu-id="7de8b-112">DbSet.Remove</span></span>  
- <span data-ttu-id="7de8b-113">DbSet. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="7de8b-113">DbSet.RemoveRange</span></span>
- <span data-ttu-id="7de8b-114">DbSet 附加</span><span class="sxs-lookup"><span data-stu-id="7de8b-114">DbSet.Attach</span></span>  
- <span data-ttu-id="7de8b-115">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="7de8b-115">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="7de8b-116">DbCoNtext. GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="7de8b-116">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="7de8b-117">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="7de8b-117">DbContext.Entry</span></span>  
- <span data-ttu-id="7de8b-118">DbChangeTracker 專案</span><span class="sxs-lookup"><span data-stu-id="7de8b-118">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="7de8b-119">停用自動偵測變更</span><span class="sxs-lookup"><span data-stu-id="7de8b-119">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="7de8b-120">如果您要在您的內容中追蹤許多實體，而且在迴圈中多次呼叫其中一個方法，則您可以關閉迴圈期間的變更偵測，以獲得大幅的效能改進。</span><span class="sxs-lookup"><span data-stu-id="7de8b-120">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="7de8b-121">例如：</span><span class="sxs-lookup"><span data-stu-id="7de8b-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

<span data-ttu-id="7de8b-122">別忘了重新啟用迴圈之後的變更偵測，我們使用了 try/finally 來確保即使迴圈中的程式碼擲回例外狀況，也一律會重新啟用。</span><span class="sxs-lookup"><span data-stu-id="7de8b-122">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="7de8b-123">停用和重新啟用的替代方法是隨時保留自動偵測變更的功能，並呼叫內容。ChangeTracker，明確地 DetectChanges 或使用變更追蹤 proxy。</span><span class="sxs-lookup"><span data-stu-id="7de8b-123">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="7de8b-124">這兩個選項都是先進的，很容易就會在您的應用程式中引進微妙的 bug，因此請小心使用。</span><span class="sxs-lookup"><span data-stu-id="7de8b-124">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="7de8b-125">如果您需要從內容中新增或移除許多物件，請考慮使用 DbSet. >addrange 和 DbSet. RemoveRange。</span><span class="sxs-lookup"><span data-stu-id="7de8b-125">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="7de8b-126">這種方法只會在新增或移除作業完成後自動偵測變更一次。</span><span class="sxs-lookup"><span data-stu-id="7de8b-126">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
