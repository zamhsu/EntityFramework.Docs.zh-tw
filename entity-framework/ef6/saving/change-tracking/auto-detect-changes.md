---
title: 自動偵測到變更-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: bca33e12674c47cc7e047e85b11746c8e39246b4
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998095"
---
# <a name="automatic-detect-changes"></a>自動偵測變更
使用大部分的 POCO 實體時如何變更實體 （以及因此需要哪些更新傳送至資料庫） 的決定會處理偵測變更演算法。 偵測變更的運作方式，藉由偵測實體的目前屬性值與原始查詢或附加實體時，會儲存在快照集的屬性值之間的差異。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

根據預設，Entity Framework 偵測的變更會自動執行時，會呼叫下列方法：  

- DbSet.Find  
- DbSet.Local  
- DbSet.Add  
- DbSet.AddRange
- DbSet.Remove  
- DbSet.RemoveRange
- DbSet.Attach  
- DbContext.SaveChanges  
- DbContext.GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker.Entries  

## <a name="disabling-automatic-detection-of-changes"></a>停用自動的偵測變更  

如果您要追蹤之實體的許多在您的內容，而且您在迴圈中呼叫其中一個方法許多次，您可能會關閉 偵測變更迴圈的期間取得大幅提升效能。 例如:   

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

不要忘記重新啟用 偵測變更的迴圈之後，我們已使用 try/finally 以確保永遠重新啟用即使迴圈中的程式碼擲回例外狀況。  

若要停用替代及重新啟用是將其保留的自動偵測所有時間和任一呼叫內容在關閉的變更。ChangeTracker.DetectChanges 明確或使用變更追蹤 proxy 的努力。 兩個選項都進階，而且可以輕鬆地導入您的應用程式的微妙的 bug 因此請小心使用它們。  

如果您要新增或移除內容中的許多物件時，請考慮使用 DbSet.AddRange 和 DbSet.RemoveRange。 這個方法會自動變更偵測一次，在新增或移除作業完成後備。 
