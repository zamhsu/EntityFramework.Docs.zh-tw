---
title: 從 EF6 移植到 EF Core-移植程式碼為基礎的模型
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: a0fa4f9a7028f56666fb993185cb03eddb9a2cd1
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052948"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>移植到 EF Core EF6 程式碼式模型

如果您已閱讀所有警告，而且您準備好要連接埠，以下是一些可協助您開始指導方針。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF Core NuGet 封裝

若要使用 EF 核心，您可以安裝 NuGet 封裝您想要使用的資料庫提供者。 例如，當目標為 SQL Server，您會安裝`Microsoft.EntityFrameworkCore.SqlServer`。 請參閱[資料庫提供者](../../core/providers/index.md)如需詳細資訊。

如果您打算使用的移轉，則您也應該安裝`Microsoft.EntityFrameworkCore.Tools`封裝。

它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF 核心和 EF6 可以使用-並存相同的應用程式中。 不過，如果您不打算使用 EF6 應用程式的任何區域中，然後解除安裝封裝有助於讓上需要注意的程式碼片段的編譯錯誤。

## <a name="swap-namespaces"></a>交換命名空間

大部分應用程式開發介面使用 EF6 位於`System.Data.Entity`命名空間 （及相關的子命名空間）。 第一個程式碼變更為以交換`Microsoft.EntityFrameworkCore`命名空間。 您會通常會以您衍生的內容的程式碼檔案開頭，然後算出從該處發生時解決編譯錯誤。

## <a name="context-configuration-connection-etc"></a>內容組態 （連線等）。

中所述[確保 EF 核心會用於您的應用程式](ensure-requirements.md)，EF 核心有較少神奇偵測連接到資料庫。 您必須覆寫`OnConfiguring`上您的衍生的內容，並設定資料庫的連接使用資料庫提供者特定 API 方法。

大部分的 EF6 應用程式的應用程式中儲存連接字串`App/Web.config`檔案。 在 EF 核心您讀取此連接字串使用`ConfigurationManager`應用程式開發介面。 您可能需要將參考加入`System.Configuration`framework 組件，可以使用此 API。

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

此時，它是解決編譯錯誤，並檢閱查看如果的行為變更會影響您的程式碼。

## <a name="existing-migrations"></a>現有的移轉

此外並非真正 EF 核心 EF6 移轉現有的可行方式。

可能的話，最好假設 EF6 從所有先前移轉已經套用到資料庫並再開始移轉結構描述從該點使用 EF 核心。 若要這樣做，您可以使用`Add-Migration`命令來新增移轉後的模型移植到 EF 核心。 然後，您會移除所有的程式碼從`Up`和`Down`scaffold 移轉的方法。 該初始移轉已建立結構時，後續的移轉將會比較模型。

## <a name="test-the-port"></a>測試連接埠

只在您的應用程式會編譯，因為並不表示它成功匯出至 EF 核心。 您必須測試您的應用程式，以確保沒有任何行為變更已經造成不利影響您的應用程式的所有區域。
