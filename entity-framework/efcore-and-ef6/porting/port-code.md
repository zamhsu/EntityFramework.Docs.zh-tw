---
title: 從 EF6 移植到 EF Core-移植以程式碼為基礎的模型-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181220"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>將以 EF6 程式碼為基礎的模型移植至 EF Core

如果您已閱讀所有注意事項，而且您已準備好進行通訊，以下是一些可協助您開始使用的指導方針。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF Core NuGet 封裝

若要使用 EF Core，請安裝您想要使用之資料庫提供者的 NuGet 套件。 例如，以 SQL Server 為目標時，您會安裝 `Microsoft.EntityFrameworkCore.SqlServer`。 如需詳細資訊，請參閱[資料庫提供者](../../core/providers/index.md)。

如果您打算使用遷移，則也應該安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。

它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF Core 和 EF6 可以使用-並存相同的應用程式中。 不過，如果您不打算在應用程式的任何區域中使用 EF6，則卸載封裝將有助於在需要注意的程式碼片段上提供編譯錯誤。

## <a name="swap-namespaces"></a>交換命名空間

您在 EF6 中使用的大部分 Api 都位於 `System.Data.Entity` 命名空間（和相關的子命名空間）中。 第一個程式碼變更是要交換到 `Microsoft.EntityFrameworkCore` 命名空間。 您通常會從衍生的內容程式碼檔案開始，然後從該處著手，以解決發生的編譯錯誤。

## <a name="context-configuration-connection-etc"></a>內容設定（連接等）

如[確保 EF Core 適用于您的應用程式](ensure-requirements.md)，EF Core 在偵測資料庫以進行連接時，會比較不神奇。 您必須覆寫衍生內容上的 `OnConfiguring` 方法，並使用資料庫提供者特定的 API 來設定資料庫的連接。

大部分的 EF6 應用程式會將連接字串儲存在應用程式 `App/Web.config` 檔案中。 在 EF Core 中，您會使用 `ConfigurationManager` API 來讀取此連接字串。 您可能需要新增對 `System.Configuration` framework 元件的參考，才能使用此 API。

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

## <a name="update-your-code"></a>更新您的程式碼

此時，請務必解決編譯錯誤和檢查程式碼，以查看行為變更是否會影響您。

## <a name="existing-migrations"></a>現有的遷移

沒有任何可行的方法可以將現有的 EF6 遷移移植到 EF Core。

可能的話，最好假設先前從 EF6 進行的所有遷移都已套用至資料庫，然後使用 EF Core 開始從該點遷移架構。 若要這麼做，您可以使用 `Add-Migration` 命令，在模型移植至 EF Core 後新增遷移。 接著，您會從 scaffold 遷移的 `Up` 和 @no__t 1 方法移除所有程式碼。 當 scaffold 初始遷移時，後續的遷移會與模型進行比較。

## <a name="test-the-port"></a>測試埠

只在您的應用程式會編譯，因為並不表示它成功匯出至 EF Core。 您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。
