---
title: Load 方法-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
caps.latest.revision: 3
ms.openlocfilehash: 83af79220b52de6e3063868fd9bdac56867d49cb
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120341"
---
# <a name="the-load-method"></a><span data-ttu-id="01c2b-102">Load 方法</span><span class="sxs-lookup"><span data-stu-id="01c2b-102">The Load Method</span></span>
<span data-ttu-id="01c2b-103">有幾個案例，您可能要從資料庫載入實體到內容而不立即進行與這些實體的任何項目。</span><span class="sxs-lookup"><span data-stu-id="01c2b-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="01c2b-104">一個很好的範例中所述，正在載入資料繫結的實體[本機資料](~/ef6/querying/local-data.md)。</span><span class="sxs-lookup"><span data-stu-id="01c2b-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="01c2b-105">若要這樣做的一個常見方式是撰寫 LINQ 查詢，然後再呼叫它，才可立即捨棄 建立的清單的 ToList。</span><span class="sxs-lookup"><span data-stu-id="01c2b-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="01c2b-106">Load 擴充方法的運作方式類似 ToList 不同之處在於它完全可避免建立清單。</span><span class="sxs-lookup"><span data-stu-id="01c2b-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="01c2b-107">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="01c2b-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="01c2b-108">以下是使用載入兩個範例。</span><span class="sxs-lookup"><span data-stu-id="01c2b-108">Here are two examples of using Load.</span></span> <span data-ttu-id="01c2b-109">第一個取自 Windows Form 資料繫結應用程式可載入查詢繫結至本機集合中之前, 的實體中所述[本機資料](~/ef6/querying/local-data.md):</span><span class="sxs-lookup"><span data-stu-id="01c2b-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="01c2b-110">第二個範例示範如何使用負載載入相關實體的已篩選的集合中所述[載入相關實體](~/ef6/querying/related-data.md):</span><span class="sxs-lookup"><span data-stu-id="01c2b-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

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
