---
title: 連接字串-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: ed89d6d09b15b0dea7fd8bc3ff3e3f631495ecb7
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149122"
---
# <a name="connection-strings"></a><span data-ttu-id="89315-102">連接字串</span><span class="sxs-lookup"><span data-stu-id="89315-102">Connection Strings</span></span>

<span data-ttu-id="89315-103">大部分的資料庫提供者都需要某種形式的連接字串，才能連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="89315-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="89315-104">有時候，此連接字串會包含需要保護的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="89315-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="89315-105">當您在環境之間移動應用程式時，您可能也需要變更連接字串，例如開發、測試和實際執行。</span><span class="sxs-lookup"><span data-stu-id="89315-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="89315-106">WPF 應用程式的 WinForms &</span><span class="sxs-lookup"><span data-stu-id="89315-106">WinForms & WPF Applications</span></span>

<span data-ttu-id="89315-107">WinForms、WPF 和 ASP.NET 4 應用程式已嘗試並測試連接字串模式。</span><span class="sxs-lookup"><span data-stu-id="89315-107">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="89315-108">如果您使用 ASP.NET，則應該將連接字串新增至應用程式的 app.config 檔案（web.config）。</span><span class="sxs-lookup"><span data-stu-id="89315-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="89315-109">如果您的連接字串包含敏感性資訊，例如使用者名稱和密碼，您可以使用[秘密管理員工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)來保護設定檔的內容。</span><span class="sxs-lookup"><span data-stu-id="89315-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager).</span></span>

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> <span data-ttu-id="89315-110">在`providerName` app.config 中儲存的 EF Core 連接字串上不需要此設定，因為資料庫提供者是透過程式碼來設定。</span><span class="sxs-lookup"><span data-stu-id="89315-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="89315-111">接著，您可以在內容的`ConfigurationManager` `OnConfiguring`方法中使用 API 來讀取連接字串。</span><span class="sxs-lookup"><span data-stu-id="89315-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="89315-112">您可能需要加入`System.Configuration`架構元件的參考，才能夠使用此 API。</span><span class="sxs-lookup"><span data-stu-id="89315-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="89315-113">通用 Windows 平台 (UWP)</span><span class="sxs-lookup"><span data-stu-id="89315-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="89315-114">UWP 應用程式中的連接字串通常是只指定本機檔案名的 SQLite 連接。</span><span class="sxs-lookup"><span data-stu-id="89315-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="89315-115">它們通常不包含機密資訊，而且不需要在部署應用程式時變更。</span><span class="sxs-lookup"><span data-stu-id="89315-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="89315-116">因此，這些連接字串通常會留在程式碼中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="89315-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="89315-117">如果您想要將它們移出程式碼，UWP 會支援設定的概念，請參閱[uwp 檔的應用程式設定一節](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)，以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="89315-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a><span data-ttu-id="89315-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89315-118">ASP.NET Core</span></span>

<span data-ttu-id="89315-119">在 ASP.NET Core 設定系統非常有彈性，且連接字串可以儲存在、環境變數`appsettings.json`、使用者秘密存放區或其他設定來源中。</span><span class="sxs-lookup"><span data-stu-id="89315-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="89315-120">如需詳細資訊，請參閱[ASP.NET Core 檔的設定一節](https://docs.asp.net/en/latest/fundamentals/configuration.html)。</span><span class="sxs-lookup"><span data-stu-id="89315-120">See the [Configuration section of the ASP.NET Core documentation](https://docs.asp.net/en/latest/fundamentals/configuration.html) for more details.</span></span> <span data-ttu-id="89315-121">下列範例會顯示中`appsettings.json`儲存的連接字串。</span><span class="sxs-lookup"><span data-stu-id="89315-121">The following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="89315-122">內容通常會在中設定`Startup.cs` ，且連接字串會從設定中讀取。</span><span class="sxs-lookup"><span data-stu-id="89315-122">The context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="89315-123">請注意`GetConnectionString()` ，方法會尋找其金鑰為`ConnectionStrings:<connection string name>`的設定值。</span><span class="sxs-lookup"><span data-stu-id="89315-123">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="89315-124">您需要匯入設定[命名空間](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration)，才能使用此擴充方法。</span><span class="sxs-lookup"><span data-stu-id="89315-124">You need to import the [Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
