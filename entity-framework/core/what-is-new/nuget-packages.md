---
title: EF Core NuGet 套件
description: 不同 Entity Framework Core NuGet 套件的總覽
author: ajcvickers
ms.date: 01/21/2021
uid: core/what-is-new/nuget-packages
ms.openlocfilehash: 25042eef49ae906e9243b782ddcd9186492eae04
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165915"
---
# <a name="ef-core-nuget-packages"></a>EF Core NuGet 套件

Entity Framework Core (EF Core) 會以 [NuGet](https://www.nuget.org/) 套件的形式寄出。 應用程式所需的套件取決於：

- 使用的資料庫系統類型 (SQL Server、SQLite 等 ) 
- 所需的 EF Core 功能

安裝套件的一般程式如下：

- 決定資料庫提供者並安裝適當的封裝 ([請參閱下文](#database-providers)) 
- 此外，如果使用關係資料庫提供者，請安裝[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)和[microsoft.entityframeworkcore。](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/) 這有助於確保使用的是一致的版本，也表示 NuGet 會讓您知道新套件版本的推出時間。
- （選擇性）決定您需要的工具類型，並為該 (安裝適當的套件， [如下所示](#tools)) 

如需開始使用 EF Core 的說明，請參閱 Entity Framework Core 的使用者入門 [教學](xref:core/get-started/overview/first-app) 課程。

## <a name="package-versions"></a>套件版本

請務必安裝 Microsoft 隨附之所有 EF Core 套件的相同版本。 例如，如果已安裝5.0.3 版的 Microsoft.entityframeworkcore，則所有其他 Microsoft.entityframeworkcore. * 套件也必須是5.0.3。

也請確定任何外部封裝都與所使用的 EF Core 版本相容。 尤其是，請檢查外部資料庫提供者是否支援您所使用的 EF Core 版本。 EF Core 的新主要版本通常需要更新的資料庫提供者。

> [!WARNING]
> NuGet 不會強制執行一致的套件版本。 請一律仔細檢查您在檔案中參考的套件版本 `.csproj` 或對等專案。

## <a name="database-providers"></a>資料庫提供者

EF Core 透過使用「資料庫提供者」支援不同的資料庫系統。 每個系統都有自己的資料庫提供者，其隨附于 NuGet 套件。 應用程式應該安裝其中一或多個提供者套件。

下表列出一般資料庫提供者。 如需更完整的可用提供者清單，請參閱 [資料庫提供者](xref:core/providers/index) 。

| 資料庫系統                   | 套件
|:----------------------------------|----------------------
| SQL Server 與 SQL Azure          | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)
| SQLite                            | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)
| Azure Cosmos DB                   | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)
| PostgreSQL                        | [Npgsql. Microsoft.entityframeworkcore. 于 postgresql](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)*
| MySQL                             | [Pomelo. Microsoft.entityframeworkcore MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)*
| 記憶體內部資料庫 EF Core * *      | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)

* 這些是由社區開發和交付的熱門、高品質、開放原始碼提供者。 列出的其他提供者是由 Microsoft 所發行。

* * 仔細考慮是否要使用記憶體中的提供者。 它不是針對生產用途而設計，也可能不是 [測試的最佳解決方案](xref:core/testing/index)。

## <a name="tools"></a>工具

您必須安裝適當的工具套件，才能從現有的資料庫使用工具進行 [EF Core 的遷移](xref:core/managing-schemas/migrations/index) 和 [反向工程 (的) ](xref:core/managing-schemas/scaffolding) ：

- 適用于 PowerShell[工具的 Microsoft.entityframeworkcore 工具](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)，可在 Visual Studio[封裝管理員主控台](/nuget/consume-packages/install-use-packages-powershell)中運作
- [dotnet-](https://www.nuget.org/packages/dotnet-ef/)適用于跨平臺命令列工具的 ef 和[Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design/)

如需有關使用 EF Core 工具的詳細資訊，請參閱 [Entity Framework Core 工具參考](xref:core/cli/index) ，包括如何正確地 `dotnet-ef` 在專案或全域安裝此工具。

> [!TIP]
> 根據預設，Microsoft.EntityFrameworkCore.Design 套件的安裝方式將不會與您的應用程式一起部署。 這也表示它的型別無法在其他專案中以可傳遞的方式使用。 `PackageReference` `.csproj` 如果您需要存取此封裝中的型別，請在檔案中使用一般檔案或對等專案。 如需詳細資訊，請參閱 [設計階段服務](xref:core/cli/services) 。

## <a name="extension-packages"></a>擴充套件

有許多適用于 Microsoft 和協力廠商以 NuGet 套件形式發佈 [的 EF Core 延伸](xref:core/extensions/index) 模組。 常用的封裝包括：

| 功能                                | 套件 | 其他相依性
|:---------------------------------------------|---------|------------------------
| 延遲載入和變更追蹤的 proxy | [Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) | [Castle 核心](https://www.nuget.org/packages/Castle.Core/)
| SQL Server 的空間支援               | [Microsoft.entityframeworkcore SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) 和 [NetTopologySuite SqlServerBytes](https://www.nuget.org/packages/NetTopologySuite.IO.SqlServerBytes/)
| SQLite 的空間支援                   | [Microsoft.entityframeworkcore. Sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) 和 [NetTopologySuite SpatiaLite](https://www.nuget.org/packages/NetTopologySuite.IO.SpatiaLite/)
| 于 postgresql 的空間支援               | [Npgsql. Microsoft.entityframeworkcore. 于 postgresql. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) 和 [NetTopologySuite PostGIS](https://www.nuget.org/packages/NetTopologySuite.IO.PostGIS/) (via [Npgsql. NetTopologySuite](https://www.nuget.org/packages/Npgsql.NetTopologySuite/)) 
| 適用于 MySQL 的空間支援                    | [Pomelo. Microsoft.entityframeworkcore. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/)

## <a name="other-packages"></a>其他套件

其他 EF Core 套件會以資料庫提供者封裝的相依性形式提取。 不過，您可能會想要為這些套件新增明確的套件參考，讓 NuGet 能在發行新版本時提供通知。

| 功能                                              | 套件
|:-----------------------------------------------------------|--------
| EF Core 基本功能                                | [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)
| 一般關係資料庫功能                   | [Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/)
| EF Core 屬性等的輕量套件。           | [Microsoft.entityframeworkcore 抽象概念](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)
| EF Core 使用方式的 Roslyn 程式碼分析器                    | [Microsoft.entityframeworkcore 分析器](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Analyzers/)
| 沒有原生 SQLite 相依性的 EF Core SQLite 提供者 | [Microsoft.entityframeworkcore。](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.Core/)

## <a name="packages-for-database-provider-testing"></a>資料庫提供者測試的封裝

下列封裝可用來測試在外部 GitHub 存放庫中建立的資料庫提供者。 如需範例，請參閱[efcore.pg](https://github.com/npgsql/efcore.pg)和[Pomelo。](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 應用程式不應安裝這些套件。

| 功能                                              | 套件
|:-----------------------------------------------------------|--------
| 測試任何資料庫提供者                            | [Microsoft.entityframeworkcore 的測試](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Specification.Tests/)
| 關係資料庫提供者的測試                    | [Microsoft.entityframeworkcore 的測試。](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational.Specification.Tests/)

## <a name="obsolete-packages"></a>淘汰的封裝

請勿 **安裝下列** 已淘汰的套件，如果目前已安裝在您的專案中，請將它們移除：

- Microsoft.EntityFrameworkCore.Relational.Design
- Microsoft.entityframeworkcore. DotNet
- Microsoft.EntityFrameworkCore.SqlServer.Design
- Microsoft.EntityFrameworkCore.Sqlite.Design
- Microsoft.entityframeworkcore： Design. 測試
