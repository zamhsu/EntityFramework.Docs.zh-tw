---
title: 連接字串和模型-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
ms.openlocfilehash: 2c9f084107e4de7f5439bf0082b46a3b538496e0
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419294"
---
# <a name="connection-strings-and-models"></a>連接字串和模型
本主題涵蓋 Entity Framework 如何探索要使用的資料庫連接，以及您可以如何變更它。 使用 Code First 和 EF 設計工具建立的模型都涵蓋在本主題中。  

Entity Framework 的應用程式通常會使用衍生自 DbCoNtext 的類別。 此衍生類別會呼叫基底 DbCoNtext 類別上的其中一個函式來控制：  

- 內容將如何連接至資料庫，也就是如何找到/使用連接字串  
- 內容是否會使用 Code First 來計算模型，或載入使用 EF 設計工具建立的模型  
- 其他 advanced 選項  

下列片段顯示一些可使用 DbCoNtext 的函式的方式。  

## <a name="use-code-first-with-connection-by-convention"></a>依慣例使用 Code First 搭配連接  

如果您尚未在應用程式中進行任何其他設定，則在 DbCoNtext 上呼叫無參數的函式會導致 DbCoNtext 在 Code First 模式中執行，並以慣例建立資料庫連接。 例如：  

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

在此範例中，DbCoNtext 會使用衍生內容類別的命名空間限定名稱（BloggingCoNtext）作為資料庫名稱，並使用 SQL Express 或 LocalDB 建立此資料庫的連接字串。 如果兩者都已安裝，則會使用 SQL Express。  

Visual Studio 2010 預設包含 SQL Express，而 Visual Studio 2012 和更新版本則包含 LocalDB。 在安裝期間，EntityFramework NuGet 套件會檢查是否有可用的資料庫伺服器。 然後，NuGet 封裝會藉由設定預設的資料庫伺服器（依慣例建立連接時 Code First 使用）來更新設定檔。 如果 SQL Express 正在執行，將會使用它。 如果 SQL Express 無法使用，則 LocalDB 會改為註冊為預設值。 如果設定檔案已包含預設連線處理站的設定，則不會進行任何變更。  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>使用 Code First 搭配依照慣例和指定的資料庫名稱來連接  

如果您尚未在應用程式中進行任何其他設定，則使用您想要使用的資料庫名稱在 DbCoNtext 上呼叫字串的函式，將會導致 DbCoNtext 在 Code First 模式中執行，並以慣例建立資料庫連接到該名稱。 例如：  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

在此範例中，DbCoNtext 會使用 "BloggingDatabase" 作為資料庫名稱，並使用 SQL Express （隨 Visual Studio 2010）或 LocalDB （隨 Visual Studio 2012 安裝）來建立此資料庫的連接字串。 如果兩者都已安裝，則會使用 SQL Express。  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>搭配 app.config/web.config 檔案中的連接字串使用 Code First  

您可以選擇將連接字串放在 app.config 或 web.config 檔案中。 例如：  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

這是一個簡單的方法，告訴 DbCoNtext 使用 SQL Express 或 LocalDB 以外的資料庫伺服器，上述範例會指定 SQL Server Compact 版本資料庫。  

如果連接字串的名稱符合您的內容名稱（不論有無命名空間限定性），則在使用無參數的函式時，DbCoNtext 就會找到它。 如果連接字串名稱與您的內容名稱不同，則您可以將連接字串名稱傳遞至 DbCoNtext 的函式，告訴 DbCoNtext 在 Code First 模式中使用此連接。 例如：  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

或者，您可以針對傳遞給 DbCoNtext 函式的字串使用 "name =\<連接字串名稱\>" 格式。 例如：  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

此表單會明確指出您預期在設定檔中找到連接字串。 如果找不到具有指定名稱的連接字串，則會擲回例外狀況。  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>在 app.config/web.config 檔案中使用連接字串的資料庫/Model First  

使用 EF 設計工具建立的模型與 Code First 不同之處在于，您的模型已經存在，而且不會在應用程式執行時從程式碼產生。 模型通常會以 .EDMX 檔案的形式存在於您的專案中。  

設計工具會將 EF 連接字串新增至您的 app.config 或 web.config 檔案。 此連接字串的特殊之處在于，其中包含如何在您的 EDMX 檔案中尋找資訊的相關資訊。 例如：  

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

EF 設計工具也會產生程式碼，告訴 DbCoNtext 使用這個連接的方法，是將連接字串名稱傳遞至 DbCoNtext 的函式。 例如：  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbCoNtext 知道要載入現有的模型（而不是使用 Code First 從程式碼計算），因為連接字串是 EF 連接字串，其中包含要使用之模型的詳細資料。  

## <a name="other-dbcontext-constructor-options"></a>其他 DbCoNtext 的函數選項  

DbCoNtext 類別包含其他的函式和使用模式，可啟用一些更先進的案例。 其中的一些改進和變更如下：  

- 您可以使用 DbModelBuilder 類別來建立 Code First 模型，而不具現化 DbCoNtext 實例。 這個的結果是 DbModel 物件。 當您準備好建立 DbCoNtext 實例時，可以將這個 DbModel 物件傳遞至其中一個 DbCoNtext 的函式。  
- 您可以將完整連接字串傳遞至 DbCoNtext，而不只是資料庫或連接字串名稱。 根據預設，此連接字串會搭配 SqlClient 提供者使用。這可以藉由在內容上設定不同的 IConnectionFactory 執行來加以變更。DefaultConnectionFactory。  
- 您可以藉由將現有的 DbConnection 物件傳遞至 DbCoNtext 的函式來使用它。 如果連線物件是 EntityConnection 的實例，則會使用連接中指定的模型，而不是使用 Code First 來計算模型。 如果物件是其他類型的實例（例如 SqlConnection），則內容會將它用於 Code First 模式。  
- 您可以將現有的 ObjectCoNtext 傳遞至 DbCoNtext 的函式，以建立包裝現有內容的 DbCoNtext。 這可用於使用 ObjectCoNtext 的現有應用程式，但想要在應用程式的某些部分中利用 DbCoNtext。  
