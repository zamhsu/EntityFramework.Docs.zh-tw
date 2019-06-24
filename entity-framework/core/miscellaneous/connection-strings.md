---
title: 連接字串的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 52a8527170845d3e73ebcec518713ade3f3844f0
ms.sourcegitcommit: 06073f8efde97dd5f540dbfb69f574d8380566fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67333846"
---
# <a name="connection-strings"></a><span data-ttu-id="4f549-102">連接字串</span><span class="sxs-lookup"><span data-stu-id="4f549-102">Connection Strings</span></span>

<span data-ttu-id="4f549-103">大部分的資料庫提供者需要某種形式的連接字串以連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="4f549-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="4f549-104">有時候此連接字串包含要保護的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="4f549-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="4f549-105">此外，您可能也需要變更連接字串，當您在環境中，例如開發、 測試和生產環境之間移動您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="4f549-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="net-framework-applications"></a><span data-ttu-id="4f549-106">.NET framework 應用程式</span><span class="sxs-lookup"><span data-stu-id="4f549-106">.NET Framework Applications</span></span>

<span data-ttu-id="4f549-107">.NET framework 應用程式，例如 WinForms、 WPF、 主控台和 ASP.NET 4 中，已嘗試且測試過的連接字串模式。</span><span class="sxs-lookup"><span data-stu-id="4f549-107">.NET Framework applications, such as WinForms, WPF, Console, and ASP.NET 4, have a tried and tested connection string pattern.</span></span> <span data-ttu-id="4f549-108">連接字串應該將您的應用程式的 App.config 檔 (Web.config 如果您使用 ASP.NET)。</span><span class="sxs-lookup"><span data-stu-id="4f549-108">The connection string should be added to your applications App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="4f549-109">如果您的連接字串中包含機密資訊，例如使用者名稱和密碼，您可以保護的組態檔中使用內容[受保護的組態](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4f549-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

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
> <span data-ttu-id="4f549-110">`providerName` EF Core 連接字串儲存在 App.config，因為資料庫提供者已透過程式碼不需要設定。</span><span class="sxs-lookup"><span data-stu-id="4f549-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="4f549-111">您可以接著讀取連接字串使用`ConfigurationManager`在您的內容中的 API`OnConfiguring`方法。</span><span class="sxs-lookup"><span data-stu-id="4f549-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="4f549-112">您可能需要將參考加入`System.Configuration`framework 組件，若要能夠使用此 API。</span><span class="sxs-lookup"><span data-stu-id="4f549-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="4f549-113">通用 Windows 平台 (UWP)</span><span class="sxs-lookup"><span data-stu-id="4f549-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="4f549-114">在 UWP 應用程式中的連接字串通常是只會指定本機檔案名稱的 SQLite 連線。</span><span class="sxs-lookup"><span data-stu-id="4f549-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="4f549-115">它們通常不包含機密資訊，並不需要變更，因為應用程式的部署。</span><span class="sxs-lookup"><span data-stu-id="4f549-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="4f549-116">因此，這些連接字串通常沒什麼問題可以留在程式碼，如下所示。</span><span class="sxs-lookup"><span data-stu-id="4f549-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="4f549-117">如果您想要移出程式碼 UWP 支援設定的概念，請參閱[UWP 文件中的 [應用程式設定] 區段](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="4f549-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

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

## <a name="aspnet-core"></a><span data-ttu-id="4f549-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f549-118">ASP.NET Core</span></span>

<span data-ttu-id="4f549-119">在 ASP.NET Core 組態系統是非常有彈性，以及連接字串可以儲存在`appsettings.json`，環境變數、 使用者密碼存放區或另一個設定來源。</span><span class="sxs-lookup"><span data-stu-id="4f549-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="4f549-120">請參閱[組態區段的 ASP.NET Core 文件](https://docs.asp.net/en/latest/fundamentals/configuration.html)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="4f549-120">See the [Configuration section of the ASP.NET Core documentation](https://docs.asp.net/en/latest/fundamentals/configuration.html) for more details.</span></span> <span data-ttu-id="4f549-121">下列範例顯示連接字串儲存在`appsettings.json`。</span><span class="sxs-lookup"><span data-stu-id="4f549-121">The following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="4f549-122">內容通常會設定於`Startup.cs`正在從組態讀取的連接字串。</span><span class="sxs-lookup"><span data-stu-id="4f549-122">The context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="4f549-123">附註`GetConnectionString()`方法會尋找其索引鍵的組態值`ConnectionStrings:<connection string name>`。</span><span class="sxs-lookup"><span data-stu-id="4f549-123">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="4f549-124">您需要匯入[Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration)才能使用此延伸模組方法的命名空間。</span><span class="sxs-lookup"><span data-stu-id="4f549-124">You need to import the [Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
