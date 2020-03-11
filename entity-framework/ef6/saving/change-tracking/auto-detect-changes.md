---
title: 自動偵測變更-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: 9af85fd7ca48a14432a1f33c59079fc438ef8810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416974"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="c2319-102">自動偵測變更</span><span class="sxs-lookup"><span data-stu-id="c2319-102">Automatic detect changes</span></span>
<span data-ttu-id="c2319-103">使用大部分 POCO 實體時，判斷實體變更的方式（以及需要傳送至資料庫的更新）會由「偵測變更」演算法處理。</span><span class="sxs-lookup"><span data-stu-id="c2319-103">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="c2319-104">偵測變更的運作方式是偵測實體的目前屬性值與查詢或附加實體時，儲存在快照中的原始屬性值之間的差異。</span><span class="sxs-lookup"><span data-stu-id="c2319-104">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="c2319-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="c2319-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="c2319-106">根據預設，當呼叫下列方法時，Entity Framework 會自動執行偵測變更：</span><span class="sxs-lookup"><span data-stu-id="c2319-106">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="c2319-107">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="c2319-107">DbSet.Find</span></span>  
- <span data-ttu-id="c2319-108">DbSet. Local</span><span class="sxs-lookup"><span data-stu-id="c2319-108">DbSet.Local</span></span>  
- <span data-ttu-id="c2319-109">DbSet。新增</span><span class="sxs-lookup"><span data-stu-id="c2319-109">DbSet.Add</span></span>  
- <span data-ttu-id="c2319-110">DbSet. AddRange</span><span class="sxs-lookup"><span data-stu-id="c2319-110">DbSet.AddRange</span></span>
- <span data-ttu-id="c2319-111">DbSet. Remove</span><span class="sxs-lookup"><span data-stu-id="c2319-111">DbSet.Remove</span></span>  
- <span data-ttu-id="c2319-112">DbSet. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="c2319-112">DbSet.RemoveRange</span></span>
- <span data-ttu-id="c2319-113">DbSet。 Attach</span><span class="sxs-lookup"><span data-stu-id="c2319-113">DbSet.Attach</span></span>  
- <span data-ttu-id="c2319-114">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="c2319-114">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="c2319-115">DbCoNtext. GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="c2319-115">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="c2319-116">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="c2319-116">DbContext.Entry</span></span>  
- <span data-ttu-id="c2319-117">DbChangeTracker 專案</span><span class="sxs-lookup"><span data-stu-id="c2319-117">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="c2319-118">停用變更的自動偵測</span><span class="sxs-lookup"><span data-stu-id="c2319-118">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="c2319-119">如果您要追蹤內容中的許多實體，而您在迴圈中多次呼叫其中一種方法，則在迴圈期間關閉變更的偵測，可能會大幅改善效能。</span><span class="sxs-lookup"><span data-stu-id="c2319-119">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="c2319-120">例如：</span><span class="sxs-lookup"><span data-stu-id="c2319-120">For example:</span></span>  

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

<span data-ttu-id="c2319-121">別忘了在迴圈之後重新啟用變更偵測，我們使用了 try/finally 確保一律重新啟用，即使迴圈中的程式碼擲回例外狀況也一樣。</span><span class="sxs-lookup"><span data-stu-id="c2319-121">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="c2319-122">停用和重新啟用的替代方法是隨時保持關閉變更的自動偵測，以及呼叫內容。ChangeTracker DetectChanges 明確或使用變更追蹤 proxy。</span><span class="sxs-lookup"><span data-stu-id="c2319-122">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="c2319-123">這兩個選項都是先進的，而且可以輕鬆地在您的應用程式中引進微妙的錯誤，因此請小心使用它們。</span><span class="sxs-lookup"><span data-stu-id="c2319-123">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="c2319-124">如果您需要從內容新增或移除許多物件，請考慮使用 DbSet. AddRange 和 DbSet. RemoveRange。</span><span class="sxs-lookup"><span data-stu-id="c2319-124">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="c2319-125">這個方法只會在完成新增或移除作業之後，自動偵測變更一次。</span><span class="sxs-lookup"><span data-stu-id="c2319-125">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
