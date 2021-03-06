---
title: 資料庫提供者 - EF Core
description: 有關特定支援 Entity Framework Core 提供者與一般提供者的資訊
author: ajcvickers
ms.date: 12/17/2019
uid: core/providers/index
ms.openlocfilehash: 0ee69a44a3760c78f97d18698b2e7bc1fd8538d9
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166007"
---
# <a name="database-providers"></a>資料庫提供者

Entity Framework Core 能夠透過稱為資料庫提供者的外掛程式程式庫存取各種不同的資料庫。

## <a name="current-providers"></a>目前的提供者

> [!IMPORTANT]
> EF Core 提供者是透過各種來源所建置。 [Entity Framework Core 專案](https://github.com/dotnet/efcore)的維護並不包含所有提供者。 考慮使用提供者時，請務必評估品質、授權、支援等項目，以確保其符合您的需求。 同時也務必檢閱每個提供者的文件以了解詳細的版本相容性資訊。

> [!IMPORTANT]
> EF Core 提供者通常可跨次要版本運作，但無法跨主要版本運作。 例如，針對 EF Core 2.1 發行的提供者應該可用於 EF Core 2.2，但無法用於 EF Core 3.0。

| NuGet 封裝                                                                                                                                                                         | 支援的資料庫引擎      | 維護程式/廠商                                                                             | 注意/需求                       | 針對版本所建置 | 實用連結                                                                                                                                   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------|:------------------------------------------------------------------------------------------------|:-------------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)                                                                     | SQL Server 2012 及更新版本         | [EF Core 專案](https://github.com/dotnet/efcore/) (Microsoft)                                |                                            | 5.0               | [docs](xref:core/providers/sql-server/index)                                                                                                   |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)                                                                           | SQLite 3.7 及更新版本              | [EF Core 專案](https://github.com/dotnet/efcore/) (Microsoft)                                |                                            | 5.0               | [docs](xref:core/providers/sqlite/index)                                                                                                       |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)                                                                       | EF Core 記憶體內部資料庫      | [EF Core 專案](https://github.com/dotnet/efcore/) (Microsoft)                                | [限制](xref:core/testing/in-memory) | 5.0               | [docs](xref:core/providers/in-memory/index)                                                                                                    |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)                                                                           | Azure Cosmos DB SQL API         | [EF Core 專案](https://github.com/dotnet/efcore/) (Microsoft)                                |                                            | 5.0               | [docs](xref:core/providers/cosmos/index)                                                                                                       |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)                                                                         | PostgreSQL                      | [Npgsql 開發小組](https://github.com/npgsql)                                            |                                            | 5.0               | [docs](https://www.npgsql.org/efcore/index.html)                                                                                               |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                                                                                   | MySQL、MariaDB                  | [Pomelo Foundation 專案](https://github.com/PomeloFoundation)                                |                                            | 3.1               | [讀我檔案](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                           |
| [MySql. Microsoft.entityframeworkcore](https://www.nuget.org/packages/MySql.EntityFrameworkCore)                                                                                                 | MySQL                           | [MySQL 專案](https://dev.mysql.com) (Oracle)                                                 |                                            | 5.0               | [docs](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                     |
| [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                                                                                              | Oracle DB 11.2 與更新版本          | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                                     |                                            | 5.0               | [網站](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                   |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                                                                                  | MySQL 5 及更新版本                 | [DevArt](https://www.devart.com/)                                                               | 已支付                                       | 5.0               | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                          |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                                                                                                | Oracle DB 9.2.0.4 與更新版本       | [DevArt](https://www.devart.com/)                                                               | 已支付                                       | 5.0               | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                         |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                                                                                        | PostgreSQL 8.0 及更新版本          | [DevArt](https://www.devart.com/)                                                               | 已支付                                       | 5.0               | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                     |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                                                                                                | SQLite 3 及更新版本                | [DevArt](https://www.devart.com/)                                                               | 已支付                                       | 5.0               | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                         |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/)                                                                  | Firebird 3.0 及更新版本            | [Jiří Činčura](https://github.com/cincuranet)                                                   |                                            | 5.0               | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                       |
| [IBM.EntityFrameworkCore](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XFML)      | Db2、Informix                   | [IBM](https://ibm.com)                                                                          | 付費，Windows                              | 3.1               | [客戶網站](https://www.ibm.com/software/passportadvantage/pao_customer.html)                                                           |
| [IBM.EntityFrameworkCore-lnx](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XGML)  | Db2、Informix                   | [IBM](https://ibm.com)                                                                          | 付費，Linux                                | 3.1               | [客戶網站](https://www.ibm.com/software/passportadvantage/pao_customer.html)                                                           |
| [IBM.EntityFrameworkCore-osx](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XHML)  | Db2、Informix                   | [IBM](https://ibm.com)                                                                          | 付費，macOS                                | 3.1               | [客戶網站](https://www.ibm.com/software/passportadvantage/pao_customer.html)                                                           |
| [Teradata.EntityFrameworkCore](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                          | Teradata 資料庫 16.10 及更新版本 | [Teradata](https://downloads.teradata.com/download/connectivity/net-data-provider-for-teradata) |                                            | 3.1               | [網站](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                        |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                                                                                                    | 將資料儲存在檔案中            | [Morris Janatzek](https://github.com/morrisjdev)                                                | 適合用於開發                   | 3.0               | [讀我檔案](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                  |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                                                                                    | Microsoft Access 檔案          | [Bubi](https://github.com/bubibubi)                                                             | .NET Framework                             | 2.2               | [讀我檔案](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                       |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)                                                                       | SQL Server Compact 3.5          | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                                               | .NET Framework                             | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications) |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)                                                                       | SQL Server Compact 4.0          | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                                               | .NET Framework                             | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications) |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                                                                                          | Progress OpenEdge               | [Alex Wiese](https://github.com/alexwiese)                                                      |                                            | 2.1               | [讀我檔案](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                      |

## <a name="adding-a-database-provider-to-your-application"></a>將資料庫提供者新增至應用程式

EF Core 的大多數資料庫提供者會以 NuGet 套件形式發佈，可以安裝如下：

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package provider_package_name
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
install-package provider_package_name
```

***

若您使用相依性插入容器，請在安裝完成後以 `OnConfiguring` 方式或 `AddDbContext` 方式在 `DbContext` 中設定提供者。
例如，下列程式行會以傳遞的連接字串設定 SQL Server 提供者：

```csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```

資料庫提供者能夠延伸 EF Core，來啟用特定資料庫套有的功能。
有些概念通用於大多數資料庫，並且包含在主要 EF Core 元件中。
這類概念包含以 LINQ 表示查詢、異動，以及在從資料庫載入物件之後追蹤其變更。
有些概念是特定提供者所特有。
例如，SQL Server 提供者可讓您[設定記憶體最佳化資料表](xref:core/providers/sql-server/memory-optimized-tables) (SQL Server 特有的功能)。
其他概念是提供者類別特有的。
例如，關聯式資料庫的 EF Core 提供者是根據一般 `Microsoft.EntityFrameworkCore.Relational` 程式庫所建置，而此程式庫提供 API 來設定資料表和資料行對應、外部索引鍵條件約束等等。提供者通常會以 NuGet 套件的形式散發。

> [!IMPORTANT]
> 當 EF Core 的新修補程式版本發行時，通常會包含 `Microsoft.EntityFrameworkCore.Relational` 套件的更新。
> 當您新增關聯式資料庫提供者時，此套件會成為應用程式的可轉移相依性。
> 但是，因為許多提供者皆從 EF Core 獨立發行，所以可能不會更新為相依於該套件的較新修補程式版本。
> 為了確保您能夠修正所有 Bug，建議您將 `Microsoft.EntityFrameworkCore.Relational` 的修補程式版本新增為應用程式的直接相依性。
