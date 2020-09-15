---
title: 自動偵測變更-EF6
description: 自動偵測 Entity Framework 6 中的變更
author: divega
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: aa8b94d843d99a8f040bdb065297d2e19b4770d7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073778"
---
# <a name="automatic-detect-changes"></a>自動偵測變更
使用大部分的 POCO 實體時，會決定實體如何變更 (，因此必須將更新傳送至資料庫) 由「偵測變更」演算法處理。 偵測變更的運作方式是偵測實體的目前屬性值與在查詢或附加實體時儲存在快照中的原始屬性值之間的差異。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

根據預設，當呼叫下列方法時，Entity Framework 會自動執行偵測變更：  

- DbSet.Find  
- DbSet  
- DbSet 新增  
- DbSet. >addrange
- DbSet。移除  
- DbSet. RemoveRange
- DbSet 附加  
- DbContext.SaveChanges  
- DbCoNtext. GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker 專案  

## <a name="disabling-automatic-detection-of-changes"></a>停用自動偵測變更  

如果您要在您的內容中追蹤許多實體，而且在迴圈中多次呼叫其中一個方法，則您可以關閉迴圈期間的變更偵測，以獲得大幅的效能改進。 例如：  

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

別忘了重新啟用迴圈之後的變更偵測，我們使用了 try/finally 來確保即使迴圈中的程式碼擲回例外狀況，也一律會重新啟用。  

停用和重新啟用的替代方法是隨時保留自動偵測變更的功能，並呼叫內容。ChangeTracker，明確地 DetectChanges 或使用變更追蹤 proxy。 這兩個選項都是先進的，很容易就會在您的應用程式中引進微妙的 bug，因此請小心使用。  

如果您需要從內容中新增或移除許多物件，請考慮使用 DbSet. >addrange 和 DbSet. RemoveRange。 這種方法只會在新增或移除作業完成後自動偵測變更一次。 
