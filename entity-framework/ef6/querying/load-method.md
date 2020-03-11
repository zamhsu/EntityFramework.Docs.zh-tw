---
title: Load 方法-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
ms.openlocfilehash: bcea8ab2477f44281cd5de824457a72a84ccc766
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417120"
---
# <a name="the-load-method"></a>Load 方法
在幾種情況下，您可能會想要將實體從資料庫載入至內容，而不需要立即對那些實體執行任何動作。 如[本機資料](~/ef6/querying/local-data.md)所述，載入資料系結的實體就是一個很好的例子。 其中一個常見的方法是撰寫 LINQ 查詢，然後在它上呼叫 ToList，只是為了立即捨棄已建立的清單。 Load 擴充方法的運作方式就像 ToList，不同之處在于它可避免完全建立清單。  

本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

以下是使用 Load 的兩個範例。 第一個是從 Windows Forms 資料系結應用程式取得，其中會使用負載來查詢實體，然後再系結至本機集合，如[本機資料](~/ef6/querying/local-data.md)中所述：  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

第二個範例顯示如何使用負載來載入已篩選的相關實體集合，如[載入相關實體](~/ef6/querying/related-data.md)中所述：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
