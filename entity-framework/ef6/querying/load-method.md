---
title: Load 方法-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
ms.openlocfilehash: f7e8410b8fb8b5c3e86c51cd61868604a7566d0c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996647"
---
# <a name="the-load-method"></a>Load 方法
有幾個案例，您可能要從資料庫載入實體到內容而不立即進行與這些實體的任何項目。 一個很好的範例中所述，正在載入資料繫結的實體[本機資料](~/ef6/querying/local-data.md)。 若要這樣做的一個常見方式是撰寫 LINQ 查詢，然後再呼叫它，才可立即捨棄 建立的清單的 ToList。 Load 擴充方法的運作方式類似 ToList 不同之處在於它完全可避免建立清單。  

本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

以下是使用載入兩個範例。 第一個取自 Windows Form 資料繫結應用程式可載入查詢繫結至本機集合中之前, 的實體中所述[本機資料](~/ef6/querying/local-data.md):  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

第二個範例示範如何使用負載載入相關實體的已篩選的集合中所述[載入相關實體](~/ef6/querying/related-data.md):  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();
}
```  
