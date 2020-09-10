---
title: Load 方法-EF6
description: Entity Framework 6 中的 Load 方法
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
uid: ef6/querying/load-method
ms.openlocfilehash: 5fbb55b899ae0ee026d42df90f80cc18fe95bfa2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620316"
---
# <a name="the-load-method"></a>Load 方法
在幾個案例中，您可能會想要將資料庫中的實體載入到內容中，而不需要立即使用這些實體進行任何作業。 這是一個很好的例子，就是載入資料系結的實體（如 [本機資料](xref:ef6/querying/local-data)中所述）。 其中一個常見的方法是撰寫 LINQ 查詢，然後在其上呼叫 ToList，而只是要立即捨棄建立的清單。 載入擴充方法的運作方式就像 ToList，唯一的差別在於它可避免完全建立清單。  

本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

以下是兩個使用負載的範例。 第一個是取自 Windows Forms 資料系結應用程式，其中會在系結至本機 [資料](xref:ef6/querying/local-data)之前，使用負載來查詢實體：  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

第二個範例顯示如何使用 Load 載入相關實體的已篩選集合，如 [載入相關實體](xref:ef6/querying/related-data)所述：  

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
