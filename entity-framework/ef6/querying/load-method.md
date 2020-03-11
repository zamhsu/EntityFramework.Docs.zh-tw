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
# <a name="the-load-method"></a><span data-ttu-id="666ba-102">Load 方法</span><span class="sxs-lookup"><span data-stu-id="666ba-102">The Load Method</span></span>
<span data-ttu-id="666ba-103">在幾種情況下，您可能會想要將實體從資料庫載入至內容，而不需要立即對那些實體執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="666ba-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="666ba-104">如[本機資料](~/ef6/querying/local-data.md)所述，載入資料系結的實體就是一個很好的例子。</span><span class="sxs-lookup"><span data-stu-id="666ba-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="666ba-105">其中一個常見的方法是撰寫 LINQ 查詢，然後在它上呼叫 ToList，只是為了立即捨棄已建立的清單。</span><span class="sxs-lookup"><span data-stu-id="666ba-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="666ba-106">Load 擴充方法的運作方式就像 ToList，不同之處在于它可避免完全建立清單。</span><span class="sxs-lookup"><span data-stu-id="666ba-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="666ba-107">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="666ba-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="666ba-108">以下是使用 Load 的兩個範例。</span><span class="sxs-lookup"><span data-stu-id="666ba-108">Here are two examples of using Load.</span></span> <span data-ttu-id="666ba-109">第一個是從 Windows Forms 資料系結應用程式取得，其中會使用負載來查詢實體，然後再系結至本機集合，如[本機資料](~/ef6/querying/local-data.md)中所述：</span><span class="sxs-lookup"><span data-stu-id="666ba-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="666ba-110">第二個範例顯示如何使用負載來載入已篩選的相關實體集合，如[載入相關實體](~/ef6/querying/related-data.md)中所述：</span><span class="sxs-lookup"><span data-stu-id="666ba-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

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
