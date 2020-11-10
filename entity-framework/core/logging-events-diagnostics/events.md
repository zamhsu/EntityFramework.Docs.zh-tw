---
title: .NET 事件-EF Core
description: EF Core 所定義的 .NET 事件
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 21ee65b7a2c5155c4d5b45350f3f47bdcee22921
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431417"
---
# <a name="net-events-in-ef-core"></a>EF Core 中的 .NET 事件

> [!TIP]  
> 您可以從 GitHub [下載事件範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) 。

Entity Framework Core (EF Core) 會公開 [.net 事件](/dotnet/standard/events/) ，以在 EF Core 程式碼中發生特定情況時做為回呼。 事件比 [攔截](xref:core/logging-events-diagnostics/interceptors) 器更簡單，並允許更具彈性的註冊。 不過，它們只會進行同步處理，因此無法執行非封鎖的非同步 i/o。

每個實例都會註冊事件 `DbContext` 。 您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。

## <a name="events-raised-by-ef-core"></a>EF Core 所引發的事件

EF Core 會引發下列事件：

| 事件 | 引進的版本 | 引發時
|:------|--------------------|-------
| `DbContext.SavingChanges` <!-- Issue #2748 -->| 5.0 | 在 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或開頭 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| `DbContext.SavedChanges`  <!-- Issue #2748 -->| 5.0 | 在成功 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或之後 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| `DbContext.SaveChangesFailed`  <!-- Issue #2748 -->| 5.0 | 在失敗的結尾 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | 2.1 | 當實體由內容追蹤時
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | 2.1 | 當追蹤的實體變更其狀態時

### <a name="example-timestamp-state-changes"></a>範例：時間戳記狀態變更

DbCoNtext 追蹤的每個實體都有 <xref:Microsoft.EntityFrameworkCore.EntityState> 。 例如， `Added` 狀態表示實體將會插入至資料庫。

這個範例會使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> 和 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> 事件來偵測實體何時變更狀態。 接著，它會使用目前的時間為實體加上戳記，指出此變更發生的時間。 這會產生時間戳記，以指出實體插入、刪除及/或上次更新的時間。

此範例中的實體類型會執行定義時間戳記屬性的介面：

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

然後，應用程式 DbCoNtext 上的方法可以設定任何實作為此介面之實體的時間戳記：

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

這個方法具有適當的簽章，可用來做為和事件的事件處理常式 `Tracked` `StateChanged` 。 在 DbCoNtext 的函式中，會為這兩個事件註冊處理常式。 請注意，您可以在任何時間將事件附加至 DbCoNtext;在內容的函式中，這種情況並不是必要的。

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

這兩個事件都是必要的，因為新 `Tracked` 的實體會在第一次追蹤時引發事件。 `StateChanged` 只有當實體 _已_ 在追蹤時變更狀態時，才會引發事件。

此範例的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) 包含一個簡單的主控台應用程式，可對 [日誌] 資料庫進行變更：

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

這段程式碼的輸出會顯示發生的狀態變更和套用的時間戳記：

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
