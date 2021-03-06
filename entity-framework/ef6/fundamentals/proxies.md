---
title: 使用 proxy-EF6
description: 使用 Entity Framework 6 中的 proxy
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: de44e891c074a8811e466d040f6451bea0f950f4
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635844"
---
# <a name="working-with-proxies"></a>使用 proxy
建立 POCO 實體類型的實例時，Entity Framework 通常會建立動態產生之衍生型別的實例，作為實體的 proxy。 此 proxy 會覆寫實體的一些虛擬屬性，以便在存取屬性時自動插入用來執行動作的勾點。 例如，這項機制可用來支援關聯性的延遲載入。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="disabling-proxy-creation"></a>停用 proxy 建立  

有時候，防止 Entity Framework 建立 proxy 實例會很有用。 例如，序列化非 proxy 實例比序列化 proxy 實例簡單得多。 您可以藉由清除旗標來關閉 Proxy 建立 `ProxyCreationEnabled` 。 您可以在內容的函式中，進行這項作業。 例如：  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

請注意，EF 不會針對沒有 proxy 要執行的型別建立 proxy。 這表示您也可以將密封的類型和/或沒有虛擬屬性，以避免 proxy。  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>明確建立 proxy 的實例  

如果您使用 new 運算子建立實體的實例，將不會建立 proxy 實例。 這可能不是問題，但如果您需要建立 proxy 實例 (例如，讓消極式載入或 proxy 變更追蹤正常運作) 那麼您可以使用的方法來執行這項作業 `Create` `DbSet` 。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

`Create`如果您想要建立衍生實體類型的實例，則可以使用的泛型版本。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

請注意，此 `Create` 方法不會將建立的實體新增或附加至內容。  

請注意， `Create` 如果為實體建立 proxy 型別不會有任何值，此方法只會建立實體型別本身的實例，因為它不會進行任何動作。 例如，如果實體類型是密封的，且（或）沒有虛擬屬性，則 `Create` 只會建立實體類型的實例。  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>從 proxy 類型取得實際的實體類型  

Proxy 類型的名稱看起來像這樣：  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

您可以使用中的方法，尋找此 proxy 類型的實體類型 `GetObjectType` `ObjectContext` 。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

請注意，如果傳遞給的型別 `GetObjectType` 是不是 proxy 型別的實體型別實例，則仍然會傳回實體的型別。 這表示您一律可以使用這個方法來取得實際的實體型別，而不需要任何其他檢查，就能查看型別是否為 proxy 型別。  
