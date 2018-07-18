---
title: 使用 proxy-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
caps.latest.revision: 3
ms.openlocfilehash: 4632e246d28a3cd53dabe5ac76e44f4538739abc
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120232"
---
# <a name="working-with-proxies"></a>使用 proxy
在建立 POCO 實體類型執行個體時，Entity Framework 通常會建立動態產生的衍生型別，做為實體的 proxy 的執行個體。 此 proxy 會覆寫要插入存取屬性時，就會自動執行動作的攔截程序之實體的某些虛擬屬性。 比方說，這項機制用來支援關聯性的消極式載入。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="disabling-proxy-creation"></a>停用 proxy 建立  

有時候它適合防止 Entity Framework 建立 proxy 執行個體。 比方說，序列化非 proxy 執行個體可達事半功倍比序列化 proxy 執行個體。 Proxy 建立可以藉由清除 ProxyCreationEnabled 旗標關閉。 可以這麼做的一個位置是在您內容的建構函式。 例如:   

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

請注意，EF 會建立型別的 proxy 其中沒有任何要執行的 proxy。 這表示您也可以避免 proxy 擁有密封格式，和/或沒有任何虛擬屬性的型別。  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>明確建立 proxy 的執行個體  

如果您建立使用新的運算子實體的執行個體，將不會建立 proxy 執行個體。 這可能不是問題，但如果您要建立的 proxy 執行個體 （例如，讓消極式載入或 proxy 變更追蹤會運作） 然後您可以這樣使用 DbSet 的 Create 方法。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

如果您想要建立衍生的實體型別的執行個體，則可以使用建立的泛型版本。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

請注意，Create 方法不會加入或建立的實體附加至內容。  

建立方法會只是建立實體型別本身的執行個體如果建立 proxy 型別之實體的附註會有任何值，因為它不會執行任何動作。 例如，如果實體類型密封格式，及/或有任何虛擬屬性，則只會建立建立實體類型的執行個體。  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>從 proxy 類型中取得實際的實體類型  

Proxy 型別有看起來像這樣的名稱：  

System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6  

您可以在此 proxy 使用針對類型從 ObjectContext GetObjectType 方法找到的實體類型。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

請注意，如果型別傳遞至 GetObjectType 仍然會傳回不是 proxy 類型的實體類型的實體類型的執行個體。 這表示您永遠可以使用這個方法，而不需要任何其他檢查類型是否 proxy 型別取得實際的實體類型。  
