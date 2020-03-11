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
# <a name="connection-strings-and-models"></a><span data-ttu-id="a9654-102">連接字串和模型</span><span class="sxs-lookup"><span data-stu-id="a9654-102">Connection strings and models</span></span>
<span data-ttu-id="a9654-103">本主題涵蓋 Entity Framework 如何探索要使用的資料庫連接，以及您可以如何變更它。</span><span class="sxs-lookup"><span data-stu-id="a9654-103">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="a9654-104">使用 Code First 和 EF 設計工具建立的模型都涵蓋在本主題中。</span><span class="sxs-lookup"><span data-stu-id="a9654-104">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="a9654-105">Entity Framework 的應用程式通常會使用衍生自 DbCoNtext 的類別。</span><span class="sxs-lookup"><span data-stu-id="a9654-105">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="a9654-106">此衍生類別會呼叫基底 DbCoNtext 類別上的其中一個函式來控制：</span><span class="sxs-lookup"><span data-stu-id="a9654-106">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="a9654-107">內容將如何連接至資料庫，也就是如何找到/使用連接字串</span><span class="sxs-lookup"><span data-stu-id="a9654-107">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="a9654-108">內容是否會使用 Code First 來計算模型，或載入使用 EF 設計工具建立的模型</span><span class="sxs-lookup"><span data-stu-id="a9654-108">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="a9654-109">其他 advanced 選項</span><span class="sxs-lookup"><span data-stu-id="a9654-109">Additional advanced options</span></span>  

<span data-ttu-id="a9654-110">下列片段顯示一些可使用 DbCoNtext 的函式的方式。</span><span class="sxs-lookup"><span data-stu-id="a9654-110">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="a9654-111">依慣例使用 Code First 搭配連接</span><span class="sxs-lookup"><span data-stu-id="a9654-111">Use Code First with connection by convention</span></span>  

<span data-ttu-id="a9654-112">如果您尚未在應用程式中進行任何其他設定，則在 DbCoNtext 上呼叫無參數的函式會導致 DbCoNtext 在 Code First 模式中執行，並以慣例建立資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="a9654-112">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="a9654-113">例如：</span><span class="sxs-lookup"><span data-stu-id="a9654-113">For example:</span></span>  

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

<span data-ttu-id="a9654-114">在此範例中，DbCoNtext 會使用衍生內容類別的命名空間限定名稱（BloggingCoNtext）作為資料庫名稱，並使用 SQL Express 或 LocalDB 建立此資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="a9654-114">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="a9654-115">如果兩者都已安裝，則會使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="a9654-115">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="a9654-116">Visual Studio 2010 預設包含 SQL Express，而 Visual Studio 2012 和更新版本則包含 LocalDB。</span><span class="sxs-lookup"><span data-stu-id="a9654-116">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="a9654-117">在安裝期間，EntityFramework NuGet 套件會檢查是否有可用的資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="a9654-117">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="a9654-118">然後，NuGet 封裝會藉由設定預設的資料庫伺服器（依慣例建立連接時 Code First 使用）來更新設定檔。</span><span class="sxs-lookup"><span data-stu-id="a9654-118">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="a9654-119">如果 SQL Express 正在執行，將會使用它。</span><span class="sxs-lookup"><span data-stu-id="a9654-119">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="a9654-120">如果 SQL Express 無法使用，則 LocalDB 會改為註冊為預設值。</span><span class="sxs-lookup"><span data-stu-id="a9654-120">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="a9654-121">如果設定檔案已包含預設連線處理站的設定，則不會進行任何變更。</span><span class="sxs-lookup"><span data-stu-id="a9654-121">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="a9654-122">使用 Code First 搭配依照慣例和指定的資料庫名稱來連接</span><span class="sxs-lookup"><span data-stu-id="a9654-122">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="a9654-123">如果您尚未在應用程式中進行任何其他設定，則使用您想要使用的資料庫名稱在 DbCoNtext 上呼叫字串的函式，將會導致 DbCoNtext 在 Code First 模式中執行，並以慣例建立資料庫連接到該名稱。</span><span class="sxs-lookup"><span data-stu-id="a9654-123">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="a9654-124">例如：</span><span class="sxs-lookup"><span data-stu-id="a9654-124">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="a9654-125">在此範例中，DbCoNtext 會使用 "BloggingDatabase" 作為資料庫名稱，並使用 SQL Express （隨 Visual Studio 2010）或 LocalDB （隨 Visual Studio 2012 安裝）來建立此資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="a9654-125">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="a9654-126">如果兩者都已安裝，則會使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="a9654-126">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="a9654-127">搭配 app.config/web.config 檔案中的連接字串使用 Code First</span><span class="sxs-lookup"><span data-stu-id="a9654-127">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="a9654-128">您可以選擇將連接字串放在 app.config 或 web.config 檔案中。</span><span class="sxs-lookup"><span data-stu-id="a9654-128">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="a9654-129">例如：</span><span class="sxs-lookup"><span data-stu-id="a9654-129">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="a9654-130">這是一個簡單的方法，告訴 DbCoNtext 使用 SQL Express 或 LocalDB 以外的資料庫伺服器，上述範例會指定 SQL Server Compact 版本資料庫。</span><span class="sxs-lookup"><span data-stu-id="a9654-130">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="a9654-131">如果連接字串的名稱符合您的內容名稱（不論有無命名空間限定性），則在使用無參數的函式時，DbCoNtext 就會找到它。</span><span class="sxs-lookup"><span data-stu-id="a9654-131">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="a9654-132">如果連接字串名稱與您的內容名稱不同，則您可以將連接字串名稱傳遞至 DbCoNtext 的函式，告訴 DbCoNtext 在 Code First 模式中使用此連接。</span><span class="sxs-lookup"><span data-stu-id="a9654-132">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="a9654-133">例如：</span><span class="sxs-lookup"><span data-stu-id="a9654-133">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="a9654-134">或者，您可以針對傳遞給 DbCoNtext 函式的字串使用 "name =\<連接字串名稱\>" 格式。</span><span class="sxs-lookup"><span data-stu-id="a9654-134">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="a9654-135">例如：</span><span class="sxs-lookup"><span data-stu-id="a9654-135">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="a9654-136">此表單會明確指出您預期在設定檔中找到連接字串。</span><span class="sxs-lookup"><span data-stu-id="a9654-136">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="a9654-137">如果找不到具有指定名稱的連接字串，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a9654-137">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="a9654-138">在 app.config/web.config 檔案中使用連接字串的資料庫/Model First</span><span class="sxs-lookup"><span data-stu-id="a9654-138">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="a9654-139">使用 EF 設計工具建立的模型與 Code First 不同之處在于，您的模型已經存在，而且不會在應用程式執行時從程式碼產生。</span><span class="sxs-lookup"><span data-stu-id="a9654-139">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="a9654-140">模型通常會以 .EDMX 檔案的形式存在於您的專案中。</span><span class="sxs-lookup"><span data-stu-id="a9654-140">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="a9654-141">設計工具會將 EF 連接字串新增至您的 app.config 或 web.config 檔案。</span><span class="sxs-lookup"><span data-stu-id="a9654-141">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="a9654-142">此連接字串的特殊之處在于，其中包含如何在您的 EDMX 檔案中尋找資訊的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="a9654-142">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="a9654-143">例如：</span><span class="sxs-lookup"><span data-stu-id="a9654-143">For example:</span></span>  

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

<span data-ttu-id="a9654-144">EF 設計工具也會產生程式碼，告訴 DbCoNtext 使用這個連接的方法，是將連接字串名稱傳遞至 DbCoNtext 的函式。</span><span class="sxs-lookup"><span data-stu-id="a9654-144">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="a9654-145">例如：</span><span class="sxs-lookup"><span data-stu-id="a9654-145">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="a9654-146">DbCoNtext 知道要載入現有的模型（而不是使用 Code First 從程式碼計算），因為連接字串是 EF 連接字串，其中包含要使用之模型的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="a9654-146">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="a9654-147">其他 DbCoNtext 的函數選項</span><span class="sxs-lookup"><span data-stu-id="a9654-147">Other DbContext constructor options</span></span>  

<span data-ttu-id="a9654-148">DbCoNtext 類別包含其他的函式和使用模式，可啟用一些更先進的案例。</span><span class="sxs-lookup"><span data-stu-id="a9654-148">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="a9654-149">其中的一些改進和變更如下：</span><span class="sxs-lookup"><span data-stu-id="a9654-149">Some of these are:</span></span>  

- <span data-ttu-id="a9654-150">您可以使用 DbModelBuilder 類別來建立 Code First 模型，而不具現化 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="a9654-150">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="a9654-151">這個的結果是 DbModel 物件。</span><span class="sxs-lookup"><span data-stu-id="a9654-151">The result of this is a DbModel object.</span></span> <span data-ttu-id="a9654-152">當您準備好建立 DbCoNtext 實例時，可以將這個 DbModel 物件傳遞至其中一個 DbCoNtext 的函式。</span><span class="sxs-lookup"><span data-stu-id="a9654-152">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="a9654-153">您可以將完整連接字串傳遞至 DbCoNtext，而不只是資料庫或連接字串名稱。</span><span class="sxs-lookup"><span data-stu-id="a9654-153">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="a9654-154">根據預設，此連接字串會搭配 SqlClient 提供者使用。這可以藉由在內容上設定不同的 IConnectionFactory 執行來加以變更。DefaultConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="a9654-154">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="a9654-155">您可以藉由將現有的 DbConnection 物件傳遞至 DbCoNtext 的函式來使用它。</span><span class="sxs-lookup"><span data-stu-id="a9654-155">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="a9654-156">如果連線物件是 EntityConnection 的實例，則會使用連接中指定的模型，而不是使用 Code First 來計算模型。</span><span class="sxs-lookup"><span data-stu-id="a9654-156">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="a9654-157">如果物件是其他類型的實例（例如 SqlConnection），則內容會將它用於 Code First 模式。</span><span class="sxs-lookup"><span data-stu-id="a9654-157">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="a9654-158">您可以將現有的 ObjectCoNtext 傳遞至 DbCoNtext 的函式，以建立包裝現有內容的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="a9654-158">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="a9654-159">這可用於使用 ObjectCoNtext 的現有應用程式，但想要在應用程式的某些部分中利用 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="a9654-159">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
