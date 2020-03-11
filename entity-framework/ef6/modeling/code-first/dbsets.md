---
title: 定義 DbSets-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
ms.openlocfilehash: 045b22d2b9d26804948689dd7c9dd694baadda7e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419091"
---
# <a name="defining-dbsets"></a>定義 DbSets
使用 Code First 工作流程進行開發時，您會定義衍生的 DbCoNtext，以代表您的資料庫會話，並針對模型中的每個型別公開一個 DbSet。 本主題涵蓋您可以定義 DbSet 屬性的各種方式。  

## <a name="dbcontext-with-dbset-properties"></a>具有 DbSet 屬性的 DbCoNtext  

Code First 範例中所顯示的常見案例，就是讓模型的實體類型具有公用自動 DbSet 屬性的 DbCoNtext。 例如：  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

在 Code First 模式中使用時，這會將 Blog 和 Post 設定為實體類型，以及設定可從這些專案存取的其他類型。 此外，DbCoNtext 會自動為每個屬性呼叫 setter，以設定適當 DbSet 的實例。  

## <a name="dbcontext-with-idbset-properties"></a>具有 Idbset 會屬性的 DbCoNtext  

在某些情況下，例如建立模擬或 fakes 時，使用介面宣告您的集合屬性會更有用。 在這種情況下，Idbset 會介面可以用來取代 DbSet。 例如：  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

此內容的運作方式與使用其 set 屬性之 DbSet 類別的內容完全相同。  

## <a name="dbcontext-with-read-only-set-properties"></a>具有唯讀集屬性的 DbCoNtext  

如果您不想要公開 DbSet 或 Idbset 會屬性的公用 setter，您可以改為建立唯讀屬性，並自行建立集合實例。 例如：  

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

請注意，DbCoNtext 會快取從 Set 方法傳回的 DbSet 實例，如此一來，每個屬性都會在每次呼叫時傳回相同的實例。  

Code First 的實體類型探索在這裡的運作方式與使用公用 getter 和 setter 的屬性相同。  
