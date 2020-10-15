---
title: 連接字串和模型-EF6
description: Entity Framework 6 中的連接字串和模型
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: c60acb965b7062f3cd35dab94ee8dfe48394969a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063383"
---
# <a name="connection-strings-and-models"></a>連接字串和模型
本主題說明 Entity Framework 如何探索要使用的資料庫連接，以及您可以如何加以變更。 本主題涵蓋使用 Code First 和 EF 設計工具建立的模型。  

一般來說，Entity Framework 的應用程式會使用衍生自 DbCoNtext 的類別。 這個衍生類別會呼叫基底 DbCoNtext 類別上的其中一個函式來控制：  

- 內容將如何連接至資料庫，也就是如何找到/使用連接字串  
- 內容是否會使用 Code First 來計算模型，或載入以 EF 設計工具建立的模型  
- 其他 advanced 選項  

下列片段顯示 DbCoNtext 的函式可以使用的一些方式。  

## <a name="use-code-first-with-connection-by-convention"></a>依慣例使用 Code First 與連接  

如果您未在應用程式中進行任何其他設定，則在 DbCoNtext 上呼叫無參數的函式將會導致 DbCoNtext 在 Code First 模式中執行，並具有依照慣例建立的資料庫連接。 例如︰  

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

在此範例中，DbCoNtext 會使用您衍生內容類別的命名空間限定名稱（BloggingCoNtext）做為資料庫名稱，並使用 SQL Express 或 LocalDB 建立這個資料庫的連接字串。 如果兩者都已安裝，則會使用 SQL Express。  

Visual Studio 2010 預設包含 SQL Express，Visual Studio 2012 和更新版本則包含 LocalDB。 在安裝期間，EntityFramework NuGet 套件會檢查有哪些資料庫伺服器可供使用。 接著，NuGet 套件將會藉由設定依慣例建立連線時 Code First 使用的預設資料庫伺服器來更新設定檔。 如果 SQL Express 正在執行，將會使用它。 如果無法使用 SQL Express，則會改為將 LocalDB 註冊為預設值。 如果設定檔已包含預設連接處理站的設定，就不會進行任何變更。  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>使用 Code First 搭配依慣例和指定的資料庫名稱進行連接  

如果您未在應用程式中進行任何其他設定，則在 DbCoNtext 上使用您想要使用的資料庫名稱來呼叫字串的函式，將會導致 DbCoNtext 在 Code First 模式中執行，並將慣例建立的資料庫連接用於該名稱的資料庫。 例如︰  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

在此範例中，DbCoNtext 會使用 "BloggingDatabase" 做為資料庫名稱，並使用隨 Visual Studio 2010) 安裝的 SQL Express (，或使用 Visual Studio 2012) 安裝的 LocalDB (來建立這個資料庫的連接字串。 如果兩者都已安裝，則會使用 SQL Express。  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>在 app.config/web.config 檔案中搭配連接字串使用 Code First  

您可以選擇將連接字串放在 app.config 或 web.config 檔案中。 例如︰  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

這是告訴 DbCoNtext 使用 SQL Express 或 LocalDB 以外之資料庫伺服器的簡單方式，上述範例會指定 SQL Server Compact Edition 資料庫。  

如果連接字串的名稱與您的內容名稱相符 (不論是否具有命名空間限定性) ，當使用無參數的函式時，DbCoNtext 會發現它。 如果連接字串名稱與內容的名稱不同，您可以將連接字串名稱傳遞至 DbCoNtext 的函式，以告知 DbCoNtext 在 Code First 模式中使用此連接。 例如︰  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

或者，您可以使用 "name =" 形式 \<connection string name\> 傳遞給 DbCoNtext 函式的字串。 例如︰  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

這種形式會明確指出您預期在設定檔中找到連接字串。 如果找不到具有指定名稱的連接字串，則會擲回例外狀況。  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>使用 app.config/web.config 檔案中連接字串的資料庫/Model First  

使用 EF 設計工具建立的模型不同于 Code First，因為您的模型已存在，而且不會在應用程式執行時從程式碼產生。 模型通常會以 EDMX 檔案的形式存在於您的專案中。  

設計工具會將 EF 連接字串新增至您的 app.config 或 web.config 檔。 此連接字串是特殊的，其中包含如何在 EDMX 檔案中尋找資訊的相關資訊。 例如︰  

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

EF 設計工具也會產生程式碼，藉由將連接字串名稱傳遞至 DbCoNtext 的函式，告知 DbCoNtext 使用此連接。 例如︰  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbCoNtext 知道要載入現有的模型 (而不是使用 Code First 從程式碼) 計算它，因為連接字串是 EF 連接字串，其中包含要使用之模型的詳細資料。  

## <a name="other-dbcontext-constructor-options"></a>其他 DbCoNtext 的函式選項  

DbCoNtext 類別包含其他的函式和使用模式，可啟用一些更先進的案例。 其中的一些改進和變更如下：  

- 您可以使用 DbModelBuilder 類別來建立 Code First 模型，而不需要具現化 DbCoNtext 實例。 結果是 DbModel 物件。 當您準備好建立 DbCoNtext 實例時，您可以將這個 DbModel 物件傳遞給其中一個 DbCoNtext 的函式。  
- 您可以將完整的連接字串傳遞至 DbCoNtext，而不只是資料庫或連接字串名稱。 依預設，此連接字串會與 SqlClient 提供者一起使用;這可以藉由將 IConnectionFactory 的不同實值設定到內容來變更。DefaultConnectionFactory。  
- 您可以使用現有的 DbConnection 物件，方法是將它傳遞至 DbCoNtext 的函式。 如果連線物件是 EntityConnection 的實例，則會使用連接中指定的模型，而不是使用 Code First 來計算模型。 如果物件是某個其他型別的實例（例如 SqlConnection），則內容會將它用於 Code First 模式。  
- 您可以將現有的 ObjectCoNtext 傳遞至 DbCoNtext 的函式，以建立包裝現有內容的 DbCoNtext。 這可用於使用 ObjectCoNtext 的現有應用程式，但想要在應用程式的某些部分中利用 DbCoNtext。  
