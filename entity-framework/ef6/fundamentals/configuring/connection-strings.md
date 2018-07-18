---
title: 連接字串 」 和 「 模型-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
caps.latest.revision: 3
ms.openlocfilehash: ca597e68a5b3e2085612669ee81da10ba6969eeb
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "39120555"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="deec2-102">連接字串和模型</span><span class="sxs-lookup"><span data-stu-id="deec2-102">Connection strings and models</span></span>
<span data-ttu-id="deec2-103">本主題涵蓋了 Entity Framework 會使用，哪一個資料庫連接的探索，並可以變更它的方式。</span><span class="sxs-lookup"><span data-stu-id="deec2-103">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="deec2-104">同時涵蓋在本主題中使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="deec2-104">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="deec2-105">通常 Entity Framework 應用程式會使用一個衍生自 DbContext 類別。</span><span class="sxs-lookup"><span data-stu-id="deec2-105">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="deec2-106">這個衍生的類別會呼叫建構函式的其中一個基底 DbContext 類別來控制：</span><span class="sxs-lookup"><span data-stu-id="deec2-106">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="deec2-107">內容連接到資料庫的方式 — 也就是如何連接字串是找到/已使用</span><span class="sxs-lookup"><span data-stu-id="deec2-107">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="deec2-108">是否將使用的內容計算使用 Code First 模型，或將使用 EF 設計工具建立模型</span><span class="sxs-lookup"><span data-stu-id="deec2-108">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="deec2-109">其他進階的選項</span><span class="sxs-lookup"><span data-stu-id="deec2-109">Additional advanced options</span></span>  

<span data-ttu-id="deec2-110">下列片段會示範幾種 DbContext 建構函式使用。</span><span class="sxs-lookup"><span data-stu-id="deec2-110">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="deec2-111">使用 Code First 與連線依慣例</span><span class="sxs-lookup"><span data-stu-id="deec2-111">Use Code First with connection by convention</span></span>  

<span data-ttu-id="deec2-112">如果您從未在您的應用程式中的任何其他設定，然後在 DbContext 上呼叫的無參數建構函式會造成 DbContext 執行 Code First 模式中依照慣例建立的資料庫連線。</span><span class="sxs-lookup"><span data-stu-id="deec2-112">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="deec2-113">例如: </span><span class="sxs-lookup"><span data-stu-id="deec2-113">For example:</span></span>  

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

<span data-ttu-id="deec2-114">在此範例中 DbContext 會使用您的衍生的內容 class—Demo.EF.BloggingContext—as 資料庫名稱的命名空間限定的名稱，並建立使用 SQL Express 或 LocalDB 此資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="deec2-114">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="deec2-115">如果兩者均安裝，將會使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="deec2-115">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="deec2-116">Visual Studio 2010 包含 SQL Express 預設和 Visual Studio 2012 和更新版本不包含 LocalDB。</span><span class="sxs-lookup"><span data-stu-id="deec2-116">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="deec2-117">在安裝期間，EntityFramework NuGet 套件會檢查之資料庫伺服器可用。</span><span class="sxs-lookup"><span data-stu-id="deec2-117">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="deec2-118">NuGet 套件會更新組態檔設定第一個程式碼會使用慣例來建立連接時的預設資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="deec2-118">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="deec2-119">如果 SQL Express 執行時，它會使用它。</span><span class="sxs-lookup"><span data-stu-id="deec2-119">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="deec2-120">如果未提供 SQL Express 然後 LocalDB 會註冊為預設值改為。</span><span class="sxs-lookup"><span data-stu-id="deec2-120">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="deec2-121">不會變更至組態檔若它已包含設定預設連接 factory。</span><span class="sxs-lookup"><span data-stu-id="deec2-121">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="deec2-122">使用 Code First 慣例和指定的資料庫名稱的連線</span><span class="sxs-lookup"><span data-stu-id="deec2-122">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="deec2-123">如果您從未在您的應用程式中的任何其他設定，然後在 DbContext 上呼叫字串建構函式，以您想要使用的資料庫名稱將會導致 DbContext 執行 Code First 模式中使用的資料庫的慣例所建立的資料庫連接該名稱。</span><span class="sxs-lookup"><span data-stu-id="deec2-123">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="deec2-124">例如: </span><span class="sxs-lookup"><span data-stu-id="deec2-124">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="deec2-125">在此範例中 DbContext"BloggingDatabase 」 做為資料庫名稱，並建立使用 SQL Express （與 Visual Studio 2010 一起安裝）] 或 [LocalDB （與 Visual Studio 2012 一起安裝） 此資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="deec2-125">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="deec2-126">如果兩者均安裝，將會使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="deec2-126">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="deec2-127">使用 Code First 與 app.config/web.config 檔案中的連接字串</span><span class="sxs-lookup"><span data-stu-id="deec2-127">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="deec2-128">您可以選擇將連接字串放在您的 app.config 或 web.config 檔案。</span><span class="sxs-lookup"><span data-stu-id="deec2-128">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="deec2-129">例如: </span><span class="sxs-lookup"><span data-stu-id="deec2-129">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="deec2-130">這是簡單的方式，向使用 SQL Express 或 LocalDB 以外的資料庫伺服器的 DbContext，上述範例中指定的 SQL Server Compact Edition 資料庫。</span><span class="sxs-lookup"><span data-stu-id="deec2-130">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="deec2-131">如果連接字串的名稱符合您的內容 （不論有命名空間限定性條件) 的名稱然後它會找到 DbContext 的無參數建構函式使用時。</span><span class="sxs-lookup"><span data-stu-id="deec2-131">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="deec2-132">如果連接字串名稱不同於您內容的名稱，您可以告訴 DbContext Code First 模式中使用這個連線，將連接字串名稱傳遞至 DbContext 建構函式。</span><span class="sxs-lookup"><span data-stu-id="deec2-132">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="deec2-133">例如: </span><span class="sxs-lookup"><span data-stu-id="deec2-133">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="deec2-134">或者，您可以使用格式 「 名稱 =\<連接字串名稱\>"傳遞至 DbContext 建構函式的字串。</span><span class="sxs-lookup"><span data-stu-id="deec2-134">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="deec2-135">例如: </span><span class="sxs-lookup"><span data-stu-id="deec2-135">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="deec2-136">此表單可讓您明確您預期要在組態檔中找到的連接字串。</span><span class="sxs-lookup"><span data-stu-id="deec2-136">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="deec2-137">如果找不到具有指定名稱的連接字串，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="deec2-137">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="deec2-138">資料庫/Model First app.config/web.config 檔案中的連接字串</span><span class="sxs-lookup"><span data-stu-id="deec2-138">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="deec2-139">在於您的模型已經存在，而且不從產生的程式碼應用程式執行時，使用 EF 設計工具建立的模型是不同的程式碼第一次。</span><span class="sxs-lookup"><span data-stu-id="deec2-139">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="deec2-140">此模型通常會有為 EDMX 檔案在您的專案。</span><span class="sxs-lookup"><span data-stu-id="deec2-140">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="deec2-141">設計工具會將您的 app.config 或 web.config 檔案的 EF 連接字串。</span><span class="sxs-lookup"><span data-stu-id="deec2-141">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="deec2-142">其中包含如何在 EDMX 檔案中尋找資訊的相關資訊，此連接字串是特殊的。</span><span class="sxs-lookup"><span data-stu-id="deec2-142">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="deec2-143">例如: </span><span class="sxs-lookup"><span data-stu-id="deec2-143">For example:</span></span>  

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

<span data-ttu-id="deec2-144">EF 設計工具也會產生程式碼，告訴她能夠使用此連接將連接字串名稱傳遞至 DbContext 建構函式的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="deec2-144">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="deec2-145">例如: </span><span class="sxs-lookup"><span data-stu-id="deec2-145">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="deec2-146">DbContext 知道要載入現有的模型 （而非計算從程式碼使用 Code First） 因為連接字串是包含要使用之模型的詳細資料的 EF 連接字串。</span><span class="sxs-lookup"><span data-stu-id="deec2-146">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="deec2-147">DbContext 建構函式的其他選項</span><span class="sxs-lookup"><span data-stu-id="deec2-147">Other DbContext constructor options</span></span>  

<span data-ttu-id="deec2-148">DbContext 類別包含其他建構函式和使用模式可讓某些更進階的案例。</span><span class="sxs-lookup"><span data-stu-id="deec2-148">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="deec2-149">這些因素包括：</span><span class="sxs-lookup"><span data-stu-id="deec2-149">Some of these are:</span></span>  

- <span data-ttu-id="deec2-150">您可以使用 DbModelBuilder 類別建置，Code First 模型而不具現化 DbContext 執行個體。</span><span class="sxs-lookup"><span data-stu-id="deec2-150">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="deec2-151">這樣的結果是 DbModel 物件。</span><span class="sxs-lookup"><span data-stu-id="deec2-151">The result of this is a DbModel object.</span></span> <span data-ttu-id="deec2-152">然後您可以為其中一個的 DbContext 建構函式傳遞此 DbModel 物件，當您準備好建立您的 DbContext 執行個體。</span><span class="sxs-lookup"><span data-stu-id="deec2-152">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="deec2-153">您可以傳遞至 DbContext 的完整連接字串，而非只是資料庫或連接字串名稱。</span><span class="sxs-lookup"><span data-stu-id="deec2-153">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="deec2-154">根據預設使用 System.Data.SqlClient 的提供者，使用此連接字串可以變更此設定 IConnectionFactory 至內容的不同實作。Database.DefaultConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="deec2-154">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="deec2-155">您可以使用現有的 DbConnection 物件傳遞至 DbContext 建構函式。</span><span class="sxs-lookup"><span data-stu-id="deec2-155">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="deec2-156">如果連接物件是 EntityConnection，執行個體，則在連接中指定的模型會使用，而不是計算模型，使用程式碼第一次。</span><span class="sxs-lookup"><span data-stu-id="deec2-156">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="deec2-157">如果該物件是某個其他型別的執行個體 — 比方說，SqlConnection — 內容 Code First 模式會使用它。</span><span class="sxs-lookup"><span data-stu-id="deec2-157">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="deec2-158">您可以將現有 ObjectContext 傳遞至 DbContext 建構函式建立 DbContext 包裝現有的內容。</span><span class="sxs-lookup"><span data-stu-id="deec2-158">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="deec2-159">這可以用於現有的應用程式，使用 ObjectContext，但其想要利用應用程式的某些部分中的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="deec2-159">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
