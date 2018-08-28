---
title: 連接字串的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 942865effba7b491dd950886ea30b69a86f1186c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997667"
---
# <a name="connection-strings"></a>連接字串

大部分的資料庫提供者需要某種形式的連接字串以連接到資料庫。 有時候此連接字串包含要保護的機密資訊。 此外，您可能也需要變更連接字串，當您在環境中，例如開發、 測試和生產環境之間移動您的應用程式。

## <a name="net-framework-applications"></a>.NET framework 應用程式

.NET framework 應用程式，例如 WinForms、 WPF、 主控台和 ASP.NET 4 中，已嘗試且測試過的連接字串模式。 連接字串應該將您的應用程式的 App.config 檔 (Web.config 如果您使用 ASP.NET)。 如果您的連接字串中包含機密資訊，例如使用者名稱和密碼，您可以保護的組態檔中使用內容[受保護的組態](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)。

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
> `providerName` EF Core 連接字串儲存在 App.config，因為資料庫提供者已透過程式碼不需要設定。

您可以接著讀取連接字串使用`ConfigurationManager`在您的內容中的 API`OnConfiguring`方法。 您可能需要將參考加入`System.Configuration`framework 組件，若要能夠使用此 API。

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

在 UWP 應用程式中的連接字串通常是只會指定本機檔案名稱的 SQLite 連線。 它們通常不包含機密資訊，並不需要變更，因為應用程式的部署。 因此，這些連接字串通常沒什麼問題可以留在程式碼，如下所示。 如果您想要移出程式碼 UWP 支援設定的概念，請參閱[UWP 文件中的 [應用程式設定] 區段](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)如需詳細資訊。

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

在 ASP.NET Core 組態系統是非常有彈性，以及連接字串可以儲存在`appsettings.json`，環境變數、 使用者密碼存放區或另一個設定來源。 請參閱[組態區段的 ASP.NET Core 文件](https://docs.asp.net/en/latest/fundamentals/configuration.html)如需詳細資訊。 下列範例顯示連接字串儲存在`appsettings.json`。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

內容通常會設定於`Startup.cs`正在從組態讀取的連接字串。 附註`GetConnectionString()`方法會尋找其索引鍵的組態值`ConnectionStrings:<connection string name>`。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
