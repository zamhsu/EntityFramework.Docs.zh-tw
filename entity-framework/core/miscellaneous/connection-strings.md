---
title: 連接字串-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: ed89d6d09b15b0dea7fd8bc3ff3e3f631495ecb7
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416586"
---
# <a name="connection-strings"></a>連接字串

大部分的資料庫提供者都需要某種形式的連接字串，才能連接到資料庫。 有時候，此連接字串會包含需要保護的機密資訊。 當您在環境之間移動應用程式時，您可能也需要變更連接字串，例如開發、測試和實際執行。

## <a name="winforms--wpf-applications"></a>WPF 應用程式的 WinForms &

WinForms、WPF 和 ASP.NET 4 應用程式已嘗試並測試連接字串模式。 如果您使用 ASP.NET，則應該將連接字串新增至應用程式的 app.config 檔案（web.config）。 如果您的連接字串包含敏感性資訊，例如使用者名稱和密碼，您可以使用[秘密管理員工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)來保護設定檔的內容。

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
> 在 App.config 中儲存的 EF Core 連接字串上不需要 `providerName` 設定，因為資料庫提供者是透過程式碼來設定。

然後，您可以在內容的 `OnConfiguring` 方法中，使用 `ConfigurationManager` API 來讀取連接字串。 您可能需要加入 `System.Configuration` framework 元件的參考，才能夠使用此 API。

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

UWP 應用程式中的連接字串通常是只指定本機檔案名的 SQLite 連接。 它們通常不包含機密資訊，而且不需要在部署應用程式時變更。 因此，這些連接字串通常會留在程式碼中，如下所示。 如果您想要將它們移出程式碼，UWP 會支援設定的概念，請參閱[uwp 檔的應用程式設定一節](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)，以取得詳細資料。

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

在 ASP.NET Core 設定系統非常有彈性，且連接字串可以儲存在 `appsettings.json`、環境變數、使用者密碼存放區或其他設定來源中。 如需詳細資訊，請參閱[ASP.NET Core 檔的設定一節](https://docs.asp.net/en/latest/fundamentals/configuration.html)。 下列範例會顯示儲存在 `appsettings.json`中的連接字串。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

內容通常會在 `Startup.cs` 中設定，且連接字串會從 configuration 中讀取。 請注意，`GetConnectionString()` 方法會尋找其金鑰為 `ConnectionStrings:<connection string name>`的設定值。 您需要匯入設定[命名空間](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration)，才能使用此擴充方法。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
