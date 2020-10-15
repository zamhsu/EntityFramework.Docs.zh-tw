---
title: 連接字串-EF Core
description: 使用 Entity Framework Core 管理不同環境下的連接字串
author: bricelam
ms.date: 10/27/2016
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: f657d39f66e6a757380ca25436a638b47c11cd12
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062317"
---
# <a name="connection-strings"></a><span data-ttu-id="82bd0-103">連接字串</span><span class="sxs-lookup"><span data-stu-id="82bd0-103">Connection Strings</span></span>

<span data-ttu-id="82bd0-104">大部分的資料庫提供者都需要某種形式的連接字串，才能連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="82bd0-104">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="82bd0-105">有時此連接字串包含需要保護的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="82bd0-105">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="82bd0-106">當您在環境之間移動應用程式時，您可能也需要變更連接字串，例如開發、測試和生產環境。</span><span class="sxs-lookup"><span data-stu-id="82bd0-106">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="82bd0-107">WinForms & WPF 應用程式</span><span class="sxs-lookup"><span data-stu-id="82bd0-107">WinForms & WPF Applications</span></span>

<span data-ttu-id="82bd0-108">WinForms、WPF 和 ASP.NET 4 應用程式都有已嘗試和測試的連接字串模式。</span><span class="sxs-lookup"><span data-stu-id="82bd0-108">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="82bd0-109">如果您使用 ASP.NET) ，則應該將連接字串新增至應用程式的 App.config 檔 ( # A1。</span><span class="sxs-lookup"><span data-stu-id="82bd0-109">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="82bd0-110">如果您的連接字串包含敏感性資訊，例如使用者名稱和密碼，您可以使用 [Secret Manager 工具](/aspnet/core/security/app-secrets#secret-manager)保護設定檔的內容。</span><span class="sxs-lookup"><span data-stu-id="82bd0-110">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]
> <span data-ttu-id="82bd0-111">`providerName`由於資料庫提供者是透過程式碼設定的，因此 EF Core 儲存在 App.config 中的連接字串不需要設定。</span><span class="sxs-lookup"><span data-stu-id="82bd0-111">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="82bd0-112">然後，您可以 `ConfigurationManager` 在內容的方法中使用 API 來讀取連接字串 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="82bd0-112">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="82bd0-113">您可能需要加入架構元件的參考，才能 `System.Configuration` 使用此 API。</span><span class="sxs-lookup"><span data-stu-id="82bd0-113">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

```csharp
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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="82bd0-114">通用 Windows 平台 (UWP)</span><span class="sxs-lookup"><span data-stu-id="82bd0-114">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="82bd0-115">UWP 應用程式中的連接字串通常是只指定本機檔案名的 SQLite 連接。</span><span class="sxs-lookup"><span data-stu-id="82bd0-115">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="82bd0-116">它們通常不包含機密資訊，而且不需要在部署應用程式時變更。</span><span class="sxs-lookup"><span data-stu-id="82bd0-116">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="82bd0-117">因此，這些連接字串通常可以留在程式碼中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="82bd0-117">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="82bd0-118">如果您想要將它們移出程式碼，則 UWP 支援設定的概念，請參閱 [uwp 檔的應用程式設定一節](/windows/uwp/app-settings/store-and-retrieve-app-data) 以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="82bd0-118">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

```csharp
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

## <a name="aspnet-core"></a><span data-ttu-id="82bd0-119">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82bd0-119">ASP.NET Core</span></span>

<span data-ttu-id="82bd0-120">在 ASP.NET Core 設定系統非常有彈性，而且可以將連接字串儲存在 `appsettings.json` 、環境變數、使用者秘密存放區或其他設定來源中。</span><span class="sxs-lookup"><span data-stu-id="82bd0-120">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="82bd0-121">如需詳細資訊，請參閱 [ASP.NET Core 檔的設定一節](/aspnet/core/fundamentals/configuration) 。</span><span class="sxs-lookup"><span data-stu-id="82bd0-121">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="82bd0-122">例如，您可以使用 [Secret Manager 工具](/aspnet/core/security/app-secrets#secret-manager) 來儲存資料庫密碼，然後在範例中，使用只包含的連接字串 `Name=<database-alias>` 。</span><span class="sxs-lookup"><span data-stu-id="82bd0-122">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="82bd0-123">或下列範例顯示儲存在中的連接字串 `appsettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="82bd0-123">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

```json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="82bd0-124">然後，通常會在中 `Startup.cs` 使用從設定讀取的連接字串來設定內容。</span><span class="sxs-lookup"><span data-stu-id="82bd0-124">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="82bd0-125">請注意， `GetConnectionString()` 方法會尋找其索引鍵為的設定值 `ConnectionStrings:<connection string name>` 。</span><span class="sxs-lookup"><span data-stu-id="82bd0-125">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="82bd0-126">您必須匯入 [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) 命名空間，才能使用此擴充方法。</span><span class="sxs-lookup"><span data-stu-id="82bd0-126">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
