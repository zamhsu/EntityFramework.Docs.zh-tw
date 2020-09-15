---
title: 查詢和尋找實體 - EF6
description: 在 Entity Framework 6 中查詢及尋找實體
author: divega
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: d55c88280bcf164457da89ec58c180e9aa1ad12d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072989"
---
# <a name="querying-and-finding-entities"></a>查詢和尋找實體
本主題涵蓋您可以使用 Entity Framework 查詢資料的各種方法，包括 LINQ 和 Find 方法。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="finding-entities-using-a-query"></a>使用查詢尋找實體  

DbSet 和 IDbSet 會實作 IQueryable，因此可作為針對資料庫撰寫 LINQ 查詢的起點。 這並不是深入討論 LINQ　的適當位置，但以下是一些簡單範例：  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

請注意，DbSet 和 IDbSet 一律會針對資料庫建立查詢，即使傳回的實體已存在於內容中，還是一律會對資料庫來回查詢。 針對資料庫執行查詢的時機：  

- 它是由 **foreach** (C#) 或 **For Each** (Visual Basic) 陳述式所列舉。  
- 它是由 [ToArray](https://msdn.microsoft.com/library/bb298736)、[ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) 或 [ToList](https://msdn.microsoft.com/library/bb342261) 等集合作業所列舉。  
- LINQ 運算子 (例如 [First](https://msdn.microsoft.com/library/bb291976) 或 [Any](https://msdn.microsoft.com/library/bb337697)) 是在查詢的最外面部分中指定。  
- 會呼叫下列方法：DbSet 上的 [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) 擴充方法、[DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) 和 Database.ExecuteSqlCommand。  

從資料庫傳回結果時，內容中不存在的物件會附加到內容。 如果某個物件已經存在於內容中，就會傳回現有的物件 (**不會**使用資料庫值來覆寫項目中物件屬性的目前和原始值)。  

當您執行查詢時，已新增至內容但尚未儲存到資料庫的實體，並不會作為結果集的一部分傳回。 若要取得內容中的資料，請參閱[本機資料](xref:ef6/querying/local-data)。  

如果查詢未從資料庫傳回任何資料列，結果將會是空集合，而非 **null**。  

## <a name="finding-entities-using-primary-keys"></a>使用主索引鍵尋找實體  

DbSet 上的 Find 方法會使用主索引鍵值，嘗試尋找內容所追蹤的實體。 如果內容中找不到此實體，則會將查詢傳送到資料庫，以便在該處尋找實體。 如果內容或資料庫中找不到此實體，就會傳回 null。  

Find 不同於以兩種顯著方式使用查詢：  

- 只有在內容中找不到具有指定索引鍵的實體時，才會進行資料庫的反覆存取。  
- Find 將會傳回處於 [已新增] 狀態的實體。 也就是說，Find 將傳回已新增至內容，但尚未儲存到資料庫的實體。  
### <a name="finding-an-entity-by-primary-key"></a>透過主索引鍵來尋找實體  

下列程式碼示範 Find 的一些用法：  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a>透過複合主索引鍵來尋找實體  

Entity Framework 允許您的實體具有複合索引鍵 - 這是由多個屬性組成的索引鍵。 例如，您可能擁有 BlogSettings 實體，其代表特定部落格的使用者設定。 由於使用者的每個部落格永遠只有一個 BlogSettings，因此您可以選擇將 BlogSettings 的主索引鍵設為 BlogId 和使用者名稱的組合。 下列程式碼會嘗試尋找 BlogId 為 3 且 Username 為 "johndoe1987" 的 BlogSettings：  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

請注意，當您擁有複合索引鍵時，需要使用 ColumnAttribute 或 Fluent API 來指定複合索引鍵屬性的順序。 在指定構成索引鍵的值時，對 Find 的呼叫必須使用此順序。  
