---
title: 從 EF6 移植至 EF Core-移植以程式碼為基礎的模型-EF
description: 將 Entity Framework 6 以程式碼為基礎的模型應用程式移植到 Entity Framework Core 的特定資訊
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: a5bbdc2ee95ea6bea96e24bee4588b524e0ffc58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073574"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>將 EF6 程式碼架構模型移植到 EF Core

如果您已經閱讀過所有的注意事項，並已準備好進行埠，則以下是協助您開始使用的一些指導方針。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF Core NuGet 套件

若要使用 EF Core，請為您要使用的資料庫提供者安裝 NuGet 套件。 例如，以 SQL Server 為目標時，您會安裝 `Microsoft.EntityFrameworkCore.SqlServer` 。 請參閱 [資料庫提供者](xref:core/providers/index) 以取得詳細資料。

如果您打算使用遷移，則也應該安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。

您可以將 EF6 NuGet 套件安裝 (EntityFramework) ，因為 EF Core 和 EF6 可在相同的應用程式中並存使用。 但是，如果您不打算在應用程式的任何區域中使用 EF6，則卸載套件將有助於在需要注意的程式碼片段上提供編譯錯誤。

## <a name="swap-namespaces"></a>交換命名空間

您在 EF6 中使用的大多數 Api 都位於 `System.Data.Entity` 命名空間 (以及相關的子命名空間) 。 第一個程式碼變更是交換到 `Microsoft.EntityFrameworkCore` 命名空間。 您通常會從衍生的內容程式碼檔案開始，然後從該處完成，以解決發生的編譯錯誤。

## <a name="context-configuration-connection-etc"></a>內容設定 (連接等 ) 

如所述， [您的應用程式可以使用 EF Core](xref:efcore-and-ef6/porting/index)，EF Core 在偵測要連接的資料庫時，會有較少的神奇之處。 您將需要覆寫 `OnConfiguring` 衍生內容上的方法，並使用資料庫提供者特定 API 來設定資料庫的連接。

大部分的 EF6 應用程式會將連接字串儲存在應用程式 `App/Web.config` 檔中。 在 EF Core 中，您會使用 API 來讀取此連接字串 `ConfigurationManager` 。 您可能需要加入架構元件的參考，才能 `System.Configuration` 使用此 API。

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

此時，請務必解決編譯錯誤並檢查程式碼，以查看行為變更是否會影響您。

## <a name="existing-migrations"></a>現有的遷移

將現有的 EF6 遷移移植到 EF Core 並不是一種可行的方法。

可能的話，最好是假設所有先前從 EF6 的遷移都已套用至資料庫，然後使用 EF Core 開始從該時間點遷移架構。 若要這樣做，您可以在 `Add-Migration` 模型移植到 EF Core 之後，使用命令來新增遷移。 然後，您可以從 `Up` scaffold 遷移的和方法中移除所有程式碼 `Down` 。 Scaffold 初始遷移時，後續的遷移將會與模型比較。

## <a name="test-the-port"></a>測試埠

這是因為您的應用程式會進行編譯，並不表示它已成功移植到 EF Core。 您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。
