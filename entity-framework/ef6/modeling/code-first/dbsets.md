---
title: 定義 DbSets-EF6
description: 在 Entity Framework 6 中定義 DbSets
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: dc5ad7f8b4ba32454c702f354b37223007e856e3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065221"
---
# <a name="defining-dbsets"></a>定義 DbSets
使用 Code First 工作流程進行開發時，您會定義衍生的 DbCoNtext，以代表您的會話與資料庫，並針對模型中的每個類型公開 DbSet。 本主題涵蓋您可以定義 DbSet 屬性的各種方式。  

## <a name="dbcontext-with-dbset-properties"></a>具有 DbSet 屬性的 DbCoNtext  

Code First 範例中所顯示的常見案例是針對模型的實體類型，具有公用自動 DbSet 屬性的 DbCoNtext。 例如︰  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

在 Code First 模式中使用時，這會將 Blog 和 Post 設定為實體類型，以及設定可從這些類型存取的其他類型。 此外，DbCoNtext 會自動為每個屬性呼叫 setter，以設定適當 DbSet 的實例。  

## <a name="dbcontext-with-idbset-properties"></a>具有 Idbset 會屬性的 DbCoNtext  

在某些情況下，例如在建立模擬或 fakes 時，使用介面宣告設定的屬性會更有用。 在這種情況下，Idbset 會介面可用來取代 DbSet。 例如︰  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

此內容的運作方式與使用 DbSet 類別做為其 set 屬性的內容完全相同。  

## <a name="dbcontext-with-read-only-set-properties"></a>具有唯讀設定屬性的 DbCoNtext  

如果您不想要對 DbSet 或 Idbset 會屬性公開公用 setter，可以改為建立唯讀屬性，並自行建立集合實例。 例如︰  

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

請注意，DbCoNtext 會快取從 Set 方法傳回的 DbSet 實例，讓每個屬性每次呼叫時都會傳回相同的實例。  

Code First 的實體類型探索的運作方式，與使用公用 getter 和 setter 的屬性相同。  
