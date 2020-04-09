---
title: 從 EF6 移植到 EF 核心 - 移植基於代碼的模型 - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78419634"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>將基於 EF6 的模型移植到 EF 核心

如果您已經閱讀了所有注意事項,並且已準備好移植,那麼這裡有一些説明您入門的指南。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF 核心 NuGet 套件

要使用 EF Core,請為要使用的資料庫提供程式安裝 NuGet 套件。 例如, 在定位 SQL Server 時,`Microsoft.EntityFrameworkCore.SqlServer`將安裝 。 有關詳細資訊[,請參考資料庫提供者](../../core/providers/index.md)。

如果您計劃使用遷移,則還應安裝該`Microsoft.EntityFrameworkCore.Tools`包。

保留 EF6 NuGet 套件 (EntityFramework) 安裝是可以的,因為 EF Core 和 EF6 可以並行用於同一應用程式。 但是,如果您不打算在應用程式的任何區域使用 EF6,則卸載包將有助於在需要注意的代碼片段上出現編譯錯誤。

## <a name="swap-namespaces"></a>交換命名空間

在 EF6 中使用的大多數`System.Data.Entity`API 都在命名空間(和相關子命名空間)中。 第一個代碼更改是交換到`Microsoft.EntityFrameworkCore`命名空間。 您通常從派生的上下文代碼檔開始,然後從那裡工作,解決髮生編譯錯誤時的錯誤。

## <a name="context-configuration-connection-etc"></a>內容設定(連線等)

正如[《確保EF核心適用於您的應用程式](ensure-requirements.md)》中所述,EF Core 在檢測要連接到的資料庫方面沒有那麼神奇。 您需要重寫派生上下文中`OnConfiguring`的方法,並使用資料庫提供程式特定的 API 來設置到資料庫的連接。

大多數 EF6 應用程式將連接字串儲存在`App/Web.config`應用程式 檔案中。 在 EF Core`ConfigurationManager`中,使用 API 讀取此連接字串。 您可能需要添加對框架程式集的`System.Configuration`引用才能使用此 API。

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

## <a name="update-your-code"></a>更新代碼

此時,需要解決編譯錯誤並查看代碼,以查看行為更改是否會影響您。

## <a name="existing-migrations"></a>現有移轉

實際上沒有將現有 EF6 遷移移植到 EF Core 的可行方法。

如果可能,最好假設以前從 EF6 的所有遷移都已應用於資料庫,然後使用 EF Core 從該點開始遷移架構。 為此,在模型移植到`Add-Migration`EF Core 後,可以使用 命令添加遷移。 然後,您將從`Up`基架遷移和`Down`方法中刪除所有代碼。 後續遷移將與初始遷移被基架時模型進行比較。

## <a name="test-the-port"></a>測試連接埠

僅僅因為您的應用程式編譯,並不意味著它已成功移植到 EF Core。 您需要測試應用程式的所有區域,以確保沒有任何行為更改對應用程式造成負面影響。
