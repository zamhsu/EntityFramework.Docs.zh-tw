---
title: 連接字串-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 062a7f292d16deb3840fd116f270edb11c6e0687
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672915"
---
# <a name="connection-strings"></a>連接字串

大部分的資料庫提供者都需要某種形式的連接字串，才能連接到資料庫。 有時候，此連接字串會包含需要保護的機密資訊。 當您在環境之間移動應用程式時，您可能也需要變更連接字串，例如開發、測試和實際執行。

## <a name="winforms--wpf-applications"></a>WPF 應用程式的 WinForms &

WinForms、WPF 和 ASP.NET 4 應用程式已嘗試並測試連接字串模式。 如果您使用 ASP.NET，則應該將連接字串新增至應用程式的 app.config 檔案（web.config）。 如果您的連接字串包含敏感性資訊，例如使用者名稱和密碼，您可以使用[秘密管理員工具](/aspnet/core/security/app-secrets#secret-manager)來保護設定檔的內容。

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
> 在 `providerName` app.config 中儲存的 EF Core 連接字串上不需要此設定，因為資料庫提供者是透過程式碼來設定。

接著，您可以 `ConfigurationManager` 在內容的方法中使用 API 來讀取連接字串 `OnConfiguring` 。 您可能需要加入 `System.Configuration` 架構元件的參考，才能夠使用此 API。

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

## <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 應用程式中的連接字串通常是只指定本機檔案名的 SQLite 連接。 它們通常不包含機密資訊，而且不需要在部署應用程式時變更。 因此，這些連接字串通常會留在程式碼中，如下所示。 如果您想要將它們移出程式碼，UWP 會支援設定的概念，請參閱[uwp 檔的應用程式設定一節](/windows/uwp/app-settings/store-and-retrieve-app-data)，以取得詳細資料。

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

## <a name="aspnet-core"></a>ASP.NET Core

在 ASP.NET Core 設定系統非常有彈性，且連接字串可以儲存在 `appsettings.json` 、環境變數、使用者秘密存放區或其他設定來源中。 如需詳細資訊，請參閱[ASP.NET Core 檔的設定一節](/aspnet/core/fundamentals/configuration)。

例如，您可以使用[秘密管理員工具](/aspnet/core/security/app-secrets#secret-manager)來儲存您的資料庫密碼，然後在 [樣板] 中，使用只包含的連接字串 `Name=<database-alias>` 。

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

或者，下列範例會顯示中儲存的連接字串 `appsettings.json` 。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

然後，通常會在中將內容設定為， `Startup.cs` 並從設定讀取連接字串。 請注意， `GetConnectionString()` 方法會尋找其金鑰為的設定值 `ConnectionStrings:<connection string name>` 。 您需要匯入設定[命名空間](/dotnet/api/microsoft.extensions.configuration)，才能使用此擴充方法。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
