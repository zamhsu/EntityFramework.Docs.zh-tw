---
title: 連接字串 」 和 「 模型-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
ms.openlocfilehash: 2c9f084107e4de7f5439bf0082b46a3b538496e0
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490738"
---
# <a name="connection-strings-and-models"></a>連接字串和模型
本主題涵蓋了 Entity Framework 會使用，哪一個資料庫連接的探索，並可以變更它的方式。 同時涵蓋在本主題中使用 Code First 和 EF 設計工具所建立的模型。  

通常 Entity Framework 應用程式會使用一個衍生自 DbContext 類別。 這個衍生的類別會呼叫建構函式的其中一個基底 DbContext 類別來控制：  

- 內容連接到資料庫的方式 — 也就是如何連接字串是找到/已使用  
- 是否將使用的內容計算使用 Code First 模型，或將使用 EF 設計工具建立模型  
- 其他進階的選項  

下列片段會示範幾種 DbContext 建構函式使用。  

## <a name="use-code-first-with-connection-by-convention"></a>使用 Code First 與連線依慣例  

如果您從未在您的應用程式中的任何其他設定，然後在 DbContext 上呼叫的無參數建構函式會造成 DbContext 執行 Code First 模式中依照慣例建立的資料庫連線。 例如:   

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

在此範例中 DbContext 會使用您的衍生的內容 class—Demo.EF.BloggingContext—as 資料庫名稱的命名空間限定的名稱，並建立使用 SQL Express 或 LocalDB 此資料庫的連接字串。 如果兩者均安裝，將會使用 SQL Express。  

Visual Studio 2010 包含 SQL Express 預設和 Visual Studio 2012 和更新版本不包含 LocalDB。 在安裝期間，EntityFramework NuGet 套件會檢查之資料庫伺服器可用。 NuGet 套件會更新組態檔設定第一個程式碼會使用慣例來建立連接時的預設資料庫伺服器。 如果 SQL Express 執行時，它會使用它。 如果未提供 SQL Express 然後 LocalDB 會註冊為預設值改為。 不會變更至組態檔若它已包含設定預設連接 factory。  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>使用 Code First 慣例和指定的資料庫名稱的連線  

如果您從未在您的應用程式中的任何其他設定，然後在 DbContext 上呼叫字串建構函式，以您想要使用的資料庫名稱將會導致 DbContext 執行 Code First 模式中使用的資料庫的慣例所建立的資料庫連接該名稱。 例如:   

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

在此範例中 DbContext"BloggingDatabase 」 做為資料庫名稱，並建立使用 SQL Express （與 Visual Studio 2010 一起安裝）] 或 [LocalDB （與 Visual Studio 2012 一起安裝） 此資料庫的連接字串。 如果兩者均安裝，將會使用 SQL Express。  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>使用 Code First 與 app.config/web.config 檔案中的連接字串  

您可以選擇將連接字串放在您的 app.config 或 web.config 檔案。 例如:   

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

這是簡單的方式，向使用 SQL Express 或 LocalDB 以外的資料庫伺服器的 DbContext，上述範例中指定的 SQL Server Compact Edition 資料庫。  

如果連接字串的名稱符合您的內容 （不論有命名空間限定性條件) 的名稱然後它會找到 DbContext 的無參數建構函式使用時。 如果連接字串名稱不同於您內容的名稱，您可以告訴 DbContext Code First 模式中使用這個連線，將連接字串名稱傳遞至 DbContext 建構函式。 例如:   

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

或者，您可以使用格式 「 名稱 =\<連接字串名稱\>"傳遞至 DbContext 建構函式的字串。 例如:   

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

此表單可讓您明確您預期要在組態檔中找到的連接字串。 如果找不到具有指定名稱的連接字串，將會擲回例外狀況。  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>資料庫/Model First app.config/web.config 檔案中的連接字串  

在於您的模型已經存在，而且不從產生的程式碼應用程式執行時，使用 EF 設計工具建立的模型是不同的程式碼第一次。 此模型通常會有為 EDMX 檔案在您的專案。  

設計工具會將您的 app.config 或 web.config 檔案的 EF 連接字串。 其中包含如何在 EDMX 檔案中尋找資訊的相關資訊，此連接字串是特殊的。 例如:   

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

EF 設計工具也會產生程式碼，告訴她能夠使用此連接將連接字串名稱傳遞至 DbContext 建構函式的 DbContext。 例如:   

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbContext 知道要載入現有的模型 （而非計算從程式碼使用 Code First） 因為連接字串是包含要使用之模型的詳細資料的 EF 連接字串。  

## <a name="other-dbcontext-constructor-options"></a>DbContext 建構函式的其他選項  

DbContext 類別包含其他建構函式和使用模式可讓某些更進階的案例。 這些因素包括：  

- 您可以使用 DbModelBuilder 類別建置，Code First 模型而不具現化 DbContext 執行個體。 這樣的結果是 DbModel 物件。 然後您可以為其中一個的 DbContext 建構函式傳遞此 DbModel 物件，當您準備好建立您的 DbContext 執行個體。  
- 您可以傳遞至 DbContext 的完整連接字串，而非只是資料庫或連接字串名稱。 根據預設使用 System.Data.SqlClient 的提供者，使用此連接字串可以變更此設定 IConnectionFactory 至內容的不同實作。Database.DefaultConnectionFactory。  
- 您可以使用現有的 DbConnection 物件傳遞至 DbContext 建構函式。 如果連接物件是 EntityConnection，執行個體，則在連接中指定的模型會使用，而不是計算模型，使用程式碼第一次。 如果該物件是某個其他型別的執行個體 — 比方說，SqlConnection — 內容 Code First 模式會使用它。  
- 您可以將現有 ObjectContext 傳遞至 DbContext 建構函式建立 DbContext 包裝現有的內容。 這可以用於現有的應用程式，使用 ObjectContext，但其想要利用應用程式的某些部分中的 DbContext。  
