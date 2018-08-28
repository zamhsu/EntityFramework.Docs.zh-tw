---
title: 定義 DbSets-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
ms.openlocfilehash: cc45ed1ceb20bc90090adb3e93c10651c69c9a6a
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993853"
---
# <a name="defining-dbsets"></a>定義 DbSets
使用 Code First 的工作流程開發時您會定義衍生的 DbContext，其代表您的工作階段與資料庫，並會公開您的模型中每個類型的 DbSet。 本主題涵蓋您可以定義 DbSet 屬性的各種方式。  

## <a name="dbcontext-with-dbset-properties"></a>DbContext DbSet 屬性  

第一個程式碼範例所示的常見案例是模型的將 DbContext 具有公用自動針對您實體類型的 DbSet 屬性。 例如:   

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

使用 Code First 模式中，這會設定為實體類型，以及設定可從這些其他類型的部落格和文章。 此外 DbContext 會自動為每個這些屬性，以執行個體設定為適當的 DbSet 呼叫 setter。  

## <a name="dbcontext-with-idbset-properties"></a>DbContext IDbSet 屬性  

有些情況，例如當建立模擬或 fakes，很多有用來宣告您的設定屬性使用的介面。 在此情況下 IDbSet 介面可用以取代 DbSet。 例如:   

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

此內容適用於使用 DbSet 類別，針對其設定屬性的內容完全相同的方式。  

## <a name="dbcontext-with-read-only-set-properties"></a>使用唯讀的 DbContext 設定屬性  

如果您不想公開公用 setter DbSet 或 IDbSet 屬性可以改為建立唯讀屬性，並自行建立集執行個體。 例如:   

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

請注意，DbContext 會快取的 DbSet 傳回從 Set 方法，以便每次呼叫時，每個屬性會傳回相同的執行個體的執行個體。  

探索相同的方式中的第一個程式碼運作的實體類型一樣對具有公用 getter 和 setter 的屬性。  
