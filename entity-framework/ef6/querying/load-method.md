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
# <a name="the-load-method"></a><span data-ttu-id="eb3b1-103">Load 方法</span><span class="sxs-lookup"><span data-stu-id="eb3b1-103">The Load Method</span></span>
<span data-ttu-id="eb3b1-104">在幾個案例中，您可能會想要將資料庫中的實體載入到內容中，而不需要立即使用這些實體進行任何作業。</span><span class="sxs-lookup"><span data-stu-id="eb3b1-104">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="eb3b1-105">這是一個很好的例子，就是載入資料系結的實體（如 [本機資料](xref:ef6/querying/local-data)中所述）。</span><span class="sxs-lookup"><span data-stu-id="eb3b1-105">A good example of this is loading entities for data binding as described in [Local Data](xref:ef6/querying/local-data).</span></span> <span data-ttu-id="eb3b1-106">其中一個常見的方法是撰寫 LINQ 查詢，然後在其上呼叫 ToList，而只是要立即捨棄建立的清單。</span><span class="sxs-lookup"><span data-stu-id="eb3b1-106">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="eb3b1-107">載入擴充方法的運作方式就像 ToList，唯一的差別在於它可避免完全建立清單。</span><span class="sxs-lookup"><span data-stu-id="eb3b1-107">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="eb3b1-108">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="eb3b1-108">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="eb3b1-109">以下是兩個使用負載的範例。</span><span class="sxs-lookup"><span data-stu-id="eb3b1-109">Here are two examples of using Load.</span></span> <span data-ttu-id="eb3b1-110">第一個是取自 Windows Forms 資料系結應用程式，其中會在系結至本機 [資料](xref:ef6/querying/local-data)之前，使用負載來查詢實體：</span><span class="sxs-lookup"><span data-stu-id="eb3b1-110">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](xref:ef6/querying/local-data):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="eb3b1-111">第二個範例顯示如何使用 Load 載入相關實體的已篩選集合，如 [載入相關實體](xref:ef6/querying/related-data)所述：</span><span class="sxs-lookup"><span data-stu-id="eb3b1-111">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](xref:ef6/querying/related-data):</span></span>  

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
