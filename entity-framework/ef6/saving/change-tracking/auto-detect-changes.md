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
# <a name="automatic-detect-changes"></a>自動偵測變更
使用大部分 POCO 實體時，判斷實體變更的方式（以及需要傳送至資料庫的更新）會由「偵測變更」演算法處理。 偵測變更的運作方式是偵測實體的目前屬性值與查詢或附加實體時，儲存在快照中的原始屬性值之間的差異。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

根據預設，當呼叫下列方法時，Entity Framework 會自動執行偵測變更：  

- DbSet.Find  
- DbSet. Local  
- DbSet。新增  
- DbSet. AddRange
- DbSet. Remove  
- DbSet. RemoveRange
- DbSet。 Attach  
- DbContext.SaveChanges  
- DbCoNtext. GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker 專案  

## <a name="disabling-automatic-detection-of-changes"></a>停用變更的自動偵測  

如果您要追蹤內容中的許多實體，而您在迴圈中多次呼叫其中一種方法，則在迴圈期間關閉變更的偵測，可能會大幅改善效能。 例如：  

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

別忘了在迴圈之後重新啟用變更偵測，我們使用了 try/finally 確保一律重新啟用，即使迴圈中的程式碼擲回例外狀況也一樣。  

停用和重新啟用的替代方法是隨時保持關閉變更的自動偵測，以及呼叫內容。ChangeTracker DetectChanges 明確或使用變更追蹤 proxy。 這兩個選項都是先進的，而且可以輕鬆地在您的應用程式中引進微妙的錯誤，因此請小心使用它們。  

如果您需要從內容新增或移除許多物件，請考慮使用 DbSet. AddRange 和 DbSet. RemoveRange。 這個方法只會在完成新增或移除作業之後，自動偵測變更一次。 
