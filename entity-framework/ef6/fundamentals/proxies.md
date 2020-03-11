---
title: 使用 proxy-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
ms.openlocfilehash: 8f7d2e8b41ece28efe8d1df3b0679e6e4510d64a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419336"
---
# <a name="working-with-proxies"></a>使用 proxy
建立 POCO 實體類型的實例時，Entity Framework 通常會建立動態產生之衍生類型的實例，作為實體的 proxy。 此 proxy 會覆寫實體的某些虛擬屬性，以插入在存取屬性時自動執行動作的勾點。 例如，這項機制是用來支援延遲載入關聯性。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="disabling-proxy-creation"></a>停用 proxy 建立  

有時候，防止 Entity Framework 建立 proxy 實例會很有用。 例如，序列化非 proxy 實例會比序列化 proxy 實例簡單得多。 藉由清除 ProxyCreationEnabled 旗標，可以關閉 Proxy 建立。 您可以執行這項操作的其中一個地方是在您的內容的函式中。 例如：  

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

請注意，如果沒有 proxy 要執行的類型，則 EF 不會建立 proxy。 這表示您也可以藉由擁有密封的類型和/或沒有虛擬屬性來避免 proxy。  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>明確建立 proxy 的實例  

如果您使用 new 運算子建立實體的實例，則不會建立 proxy 實例。 這可能不是問題，但如果您需要建立 proxy 實例（例如，讓消極式載入或 proxy 變更追蹤能夠正常執行），您可以使用 DbSet 的 Create 方法來這麼做。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

如果您想要建立衍生實體類型的實例，則可以使用 Create 的泛型版本。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

請注意，Create 方法不會將建立的實體加入或附加至內容。  

請注意，如果建立實體的 proxy 類型不會有任何值，Create 方法只會建立實體類型本身的實例，因為它不會執行任何動作。 例如，如果實體類型是密封的，而且（或沒有虛擬屬性），則 Create 只會建立實體類型的實例。  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>從 proxy 類型取得實際實體類型  

Proxy 類型的名稱如下所示：  

DynamicProxies. Entity. Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6  

您可以使用 ObjectCoNtext 的 GetObjectType 方法，來尋找此 proxy 類型的實體類型。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

請注意，如果傳遞至 GetObjectType 的類型是不是 proxy 類型之實體類型的實例，則仍會傳回實體的類型。 這表示您一律可以使用這個方法來取得實際實體類型，而不需要任何其他檢查，以查看該類型是否為 proxy 類型。  
