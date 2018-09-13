---
title: 自動偵測到變更-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: 9af85fd7ca48a14432a1f33c59079fc438ef8810
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490983"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="c72a2-102">自動偵測變更</span><span class="sxs-lookup"><span data-stu-id="c72a2-102">Automatic detect changes</span></span>
<span data-ttu-id="c72a2-103">使用大部分的 POCO 實體時如何變更實體 （以及因此需要哪些更新傳送至資料庫） 的決定會處理偵測變更演算法。</span><span class="sxs-lookup"><span data-stu-id="c72a2-103">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="c72a2-104">偵測變更的運作方式，藉由偵測實體的目前屬性值與原始查詢或附加實體時，會儲存在快照集的屬性值之間的差異。</span><span class="sxs-lookup"><span data-stu-id="c72a2-104">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="c72a2-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="c72a2-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="c72a2-106">根據預設，Entity Framework 偵測的變更會自動執行時，會呼叫下列方法：</span><span class="sxs-lookup"><span data-stu-id="c72a2-106">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="c72a2-107">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="c72a2-107">DbSet.Find</span></span>  
- <span data-ttu-id="c72a2-108">DbSet.Local</span><span class="sxs-lookup"><span data-stu-id="c72a2-108">DbSet.Local</span></span>  
- <span data-ttu-id="c72a2-109">DbSet.Add</span><span class="sxs-lookup"><span data-stu-id="c72a2-109">DbSet.Add</span></span>  
- <span data-ttu-id="c72a2-110">DbSet.AddRange</span><span class="sxs-lookup"><span data-stu-id="c72a2-110">DbSet.AddRange</span></span>
- <span data-ttu-id="c72a2-111">DbSet.Remove</span><span class="sxs-lookup"><span data-stu-id="c72a2-111">DbSet.Remove</span></span>  
- <span data-ttu-id="c72a2-112">DbSet.RemoveRange</span><span class="sxs-lookup"><span data-stu-id="c72a2-112">DbSet.RemoveRange</span></span>
- <span data-ttu-id="c72a2-113">DbSet.Attach</span><span class="sxs-lookup"><span data-stu-id="c72a2-113">DbSet.Attach</span></span>  
- <span data-ttu-id="c72a2-114">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="c72a2-114">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="c72a2-115">DbContext.GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="c72a2-115">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="c72a2-116">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="c72a2-116">DbContext.Entry</span></span>  
- <span data-ttu-id="c72a2-117">DbChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="c72a2-117">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="c72a2-118">停用自動的偵測變更</span><span class="sxs-lookup"><span data-stu-id="c72a2-118">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="c72a2-119">如果您要追蹤之實體的許多在您的內容，而且您在迴圈中呼叫其中一個方法許多次，您可能會關閉 偵測變更迴圈的期間取得大幅提升效能。</span><span class="sxs-lookup"><span data-stu-id="c72a2-119">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="c72a2-120">例如: </span><span class="sxs-lookup"><span data-stu-id="c72a2-120">For example:</span></span>  

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

<span data-ttu-id="c72a2-121">不要忘記重新啟用 偵測變更的迴圈之後，我們已使用 try/finally 以確保永遠重新啟用即使迴圈中的程式碼擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="c72a2-121">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="c72a2-122">若要停用替代及重新啟用是將其保留的自動偵測所有時間和任一呼叫內容在關閉的變更。ChangeTracker.DetectChanges 明確或使用變更追蹤 proxy 的努力。</span><span class="sxs-lookup"><span data-stu-id="c72a2-122">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="c72a2-123">兩個選項都進階，而且可以輕鬆地導入您的應用程式的微妙的 bug 因此請小心使用它們。</span><span class="sxs-lookup"><span data-stu-id="c72a2-123">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="c72a2-124">如果您要新增或移除內容中的許多物件時，請考慮使用 DbSet.AddRange 和 DbSet.RemoveRange。</span><span class="sxs-lookup"><span data-stu-id="c72a2-124">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="c72a2-125">這個方法會自動變更偵測一次，在新增或移除作業完成後備。</span><span class="sxs-lookup"><span data-stu-id="c72a2-125">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
