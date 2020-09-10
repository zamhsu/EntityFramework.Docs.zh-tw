---
title: 連接字串和模型-EF6
description: Entity Framework 6 中的連接字串和模型
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: 2203d7f2168dc9d4ae5a6b1914742c7c2b6fbf77
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618433"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="fa7c2-103">連接字串和模型</span><span class="sxs-lookup"><span data-stu-id="fa7c2-103">Connection strings and models</span></span>
<span data-ttu-id="fa7c2-104">本主題說明 Entity Framework 如何探索要使用的資料庫連接，以及您可以如何加以變更。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-104">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="fa7c2-105">本主題涵蓋使用 Code First 和 EF 設計工具建立的模型。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-105">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="fa7c2-106">一般來說，Entity Framework 的應用程式會使用衍生自 DbCoNtext 的類別。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-106">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="fa7c2-107">這個衍生類別會呼叫基底 DbCoNtext 類別上的其中一個函式來控制：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-107">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="fa7c2-108">內容將如何連接至資料庫，也就是如何找到/使用連接字串</span><span class="sxs-lookup"><span data-stu-id="fa7c2-108">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="fa7c2-109">內容是否會使用 Code First 來計算模型，或載入以 EF 設計工具建立的模型</span><span class="sxs-lookup"><span data-stu-id="fa7c2-109">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="fa7c2-110">其他 advanced 選項</span><span class="sxs-lookup"><span data-stu-id="fa7c2-110">Additional advanced options</span></span>  

<span data-ttu-id="fa7c2-111">下列片段顯示 DbCoNtext 的函式可以使用的一些方式。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-111">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="fa7c2-112">依慣例使用 Code First 與連接</span><span class="sxs-lookup"><span data-stu-id="fa7c2-112">Use Code First with connection by convention</span></span>  

<span data-ttu-id="fa7c2-113">如果您未在應用程式中進行任何其他設定，則在 DbCoNtext 上呼叫無參數的函式將會導致 DbCoNtext 在 Code First 模式中執行，並具有依照慣例建立的資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-113">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="fa7c2-114">例如：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-114">For example:</span></span>  

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

<span data-ttu-id="fa7c2-115">在此範例中，DbCoNtext 會使用您衍生內容類別的命名空間限定名稱（BloggingCoNtext）做為資料庫名稱，並使用 SQL Express 或 LocalDB 建立這個資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-115">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="fa7c2-116">如果兩者都已安裝，則會使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-116">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="fa7c2-117">Visual Studio 2010 預設包含 SQL Express，Visual Studio 2012 和更新版本則包含 LocalDB。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-117">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="fa7c2-118">在安裝期間，EntityFramework NuGet 套件會檢查有哪些資料庫伺服器可供使用。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-118">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="fa7c2-119">接著，NuGet 套件將會藉由設定依慣例建立連線時 Code First 使用的預設資料庫伺服器來更新設定檔。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-119">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="fa7c2-120">如果 SQL Express 正在執行，將會使用它。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-120">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="fa7c2-121">如果無法使用 SQL Express，則會改為將 LocalDB 註冊為預設值。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-121">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="fa7c2-122">如果設定檔已包含預設連接處理站的設定，就不會進行任何變更。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-122">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="fa7c2-123">使用 Code First 搭配依慣例和指定的資料庫名稱進行連接</span><span class="sxs-lookup"><span data-stu-id="fa7c2-123">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="fa7c2-124">如果您未在應用程式中進行任何其他設定，則在 DbCoNtext 上使用您想要使用的資料庫名稱來呼叫字串的函式，將會導致 DbCoNtext 在 Code First 模式中執行，並將慣例建立的資料庫連接用於該名稱的資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-124">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="fa7c2-125">例如：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-125">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="fa7c2-126">在此範例中，DbCoNtext 會使用 "BloggingDatabase" 做為資料庫名稱，並使用隨 Visual Studio 2010) 安裝的 SQL Express (，或使用 Visual Studio 2012) 安裝的 LocalDB (來建立這個資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-126">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="fa7c2-127">如果兩者都已安裝，則會使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-127">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="fa7c2-128">在 app.config/web.config 檔案中搭配連接字串使用 Code First</span><span class="sxs-lookup"><span data-stu-id="fa7c2-128">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="fa7c2-129">您可以選擇將連接字串放在 app.config 或 web.config 檔案中。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-129">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="fa7c2-130">例如：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-130">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="fa7c2-131">這是告訴 DbCoNtext 使用 SQL Express 或 LocalDB 以外之資料庫伺服器的簡單方式，上述範例會指定 SQL Server Compact Edition 資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-131">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="fa7c2-132">如果連接字串的名稱與您的內容名稱相符 (不論是否具有命名空間限定性) ，當使用無參數的函式時，DbCoNtext 會發現它。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-132">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="fa7c2-133">如果連接字串名稱與內容的名稱不同，您可以將連接字串名稱傳遞至 DbCoNtext 的函式，以告知 DbCoNtext 在 Code First 模式中使用此連接。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-133">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="fa7c2-134">例如：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-134">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="fa7c2-135">或者，您可以使用 "name =" 形式 \<connection string name\> 傳遞給 DbCoNtext 函式的字串。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-135">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="fa7c2-136">例如：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-136">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="fa7c2-137">這種形式會明確指出您預期在設定檔中找到連接字串。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-137">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="fa7c2-138">如果找不到具有指定名稱的連接字串，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-138">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="fa7c2-139">使用 app.config/web.config 檔案中連接字串的資料庫/Model First</span><span class="sxs-lookup"><span data-stu-id="fa7c2-139">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="fa7c2-140">使用 EF 設計工具建立的模型不同于 Code First，因為您的模型已存在，而且不會在應用程式執行時從程式碼產生。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-140">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="fa7c2-141">模型通常會以 EDMX 檔案的形式存在於您的專案中。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-141">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="fa7c2-142">設計工具會將 EF 連接字串新增至您的 app.config 或 web.config 檔。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-142">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="fa7c2-143">此連接字串是特殊的，其中包含如何在 EDMX 檔案中尋找資訊的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-143">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="fa7c2-144">例如：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-144">For example:</span></span>  

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

<span data-ttu-id="fa7c2-145">EF 設計工具也會產生程式碼，藉由將連接字串名稱傳遞至 DbCoNtext 的函式，告知 DbCoNtext 使用此連接。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-145">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="fa7c2-146">例如：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-146">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="fa7c2-147">DbCoNtext 知道要載入現有的模型 (而不是使用 Code First 從程式碼) 計算它，因為連接字串是 EF 連接字串，其中包含要使用之模型的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-147">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="fa7c2-148">其他 DbCoNtext 的函式選項</span><span class="sxs-lookup"><span data-stu-id="fa7c2-148">Other DbContext constructor options</span></span>  

<span data-ttu-id="fa7c2-149">DbCoNtext 類別包含其他的函式和使用模式，可啟用一些更先進的案例。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-149">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="fa7c2-150">其中的一些改進和變更如下：</span><span class="sxs-lookup"><span data-stu-id="fa7c2-150">Some of these are:</span></span>  

- <span data-ttu-id="fa7c2-151">您可以使用 DbModelBuilder 類別來建立 Code First 模型，而不需要具現化 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-151">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="fa7c2-152">結果是 DbModel 物件。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-152">The result of this is a DbModel object.</span></span> <span data-ttu-id="fa7c2-153">當您準備好建立 DbCoNtext 實例時，您可以將這個 DbModel 物件傳遞給其中一個 DbCoNtext 的函式。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-153">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="fa7c2-154">您可以將完整的連接字串傳遞至 DbCoNtext，而不只是資料庫或連接字串名稱。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-154">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="fa7c2-155">依預設，此連接字串會與 SqlClient 提供者一起使用;這可以藉由將 IConnectionFactory 的不同實值設定到內容來變更。DefaultConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-155">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="fa7c2-156">您可以使用現有的 DbConnection 物件，方法是將它傳遞至 DbCoNtext 的函式。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-156">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="fa7c2-157">如果連線物件是 EntityConnection 的實例，則會使用連接中指定的模型，而不是使用 Code First 來計算模型。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-157">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="fa7c2-158">如果物件是某個其他型別的實例（例如 SqlConnection），則內容會將它用於 Code First 模式。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-158">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="fa7c2-159">您可以將現有的 ObjectCoNtext 傳遞至 DbCoNtext 的函式，以建立包裝現有內容的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-159">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="fa7c2-160">這可用於使用 ObjectCoNtext 的現有應用程式，但想要在應用程式的某些部分中利用 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="fa7c2-160">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
