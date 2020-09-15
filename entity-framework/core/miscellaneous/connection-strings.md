---
title: 連接字串-EF Core
description: 使用 Entity Framework Core 管理不同環境下的連接字串
author: rowanmiller
ms.date: 10/27/2016
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: e4283ada88a557e4f1e3eeea3de2634a7d0dce61
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071676"
---
# <a name="connection-strings"></a>連接字串

大部分的資料庫提供者都需要某種形式的連接字串，才能連接到資料庫。 有時此連接字串包含需要保護的機密資訊。 當您在環境之間移動應用程式時，您可能也需要變更連接字串，例如開發、測試和生產環境。

## <a name="winforms--wpf-applications"></a>WinForms & WPF 應用程式

WinForms、WPF 和 ASP.NET 4 應用程式都有已嘗試和測試的連接字串模式。 如果您使用 ASP.NET) ，則應該將連接字串新增至應用程式的 App.config 檔 ( # A1。 如果您的連接字串包含敏感性資訊，例如使用者名稱和密碼，您可以使用 [Secret Manager 工具](/aspnet/core/security/app-secrets#secret-manager)保護設定檔的內容。

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
> `providerName`由於資料庫提供者是透過程式碼設定的，因此 EF Core 儲存在 App.config 中的連接字串不需要設定。

然後，您可以 `ConfigurationManager` 在內容的方法中使用 API 來讀取連接字串 `OnConfiguring` 。 您可能需要加入架構元件的參考，才能 `System.Configuration` 使用此 API。

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

UWP 應用程式中的連接字串通常是只指定本機檔案名的 SQLite 連接。 它們通常不包含機密資訊，而且不需要在部署應用程式時變更。 因此，這些連接字串通常可以留在程式碼中，如下所示。 如果您想要將它們移出程式碼，則 UWP 支援設定的概念，請參閱 [uwp 檔的應用程式設定一節](/windows/uwp/app-settings/store-and-retrieve-app-data) 以取得詳細資料。

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

在 ASP.NET Core 設定系統非常有彈性，而且可以將連接字串儲存在 `appsettings.json` 、環境變數、使用者秘密存放區或其他設定來源中。 如需詳細資訊，請參閱 [ASP.NET Core 檔的設定一節](/aspnet/core/fundamentals/configuration) 。

例如，您可以使用 [Secret Manager 工具](/aspnet/core/security/app-secrets#secret-manager) 來儲存資料庫密碼，然後在範例中，使用只包含的連接字串 `Name=<database-alias>` 。

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

或下列範例顯示儲存在中的連接字串 `appsettings.json` 。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

然後，通常會在中 `Startup.cs` 使用從設定讀取的連接字串來設定內容。 請注意， `GetConnectionString()` 方法會尋找其索引鍵為的設定值 `ConnectionStrings:<connection string name>` 。 您必須匯入 [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) 命名空間，才能使用此擴充方法。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
