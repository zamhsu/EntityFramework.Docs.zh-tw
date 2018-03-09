---
title: "資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 2/23/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
ms.technology: entity-framework-core
uid: core/providers/index
ms.openlocfilehash: 6311f6a336198b45d307fa8c4318abd2e64e9df0
ms.sourcegitcommit: fc68321c211aca38f7b9dc3a75677c6ca1b2524b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="database-providers"></a>資料庫提供者

Entity Framework Core 能夠透過稱為資料庫提供者的外掛程式程式庫存取各種不同的資料庫。

## <a name="current-providers"></a>目前的提供者
> [!IMPORTANT]  
> EF Core 提供者是透過各種來源所建置。 [Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護並不包含所有提供者。 考慮使用提供者時，請務必評估品質、授權、支援等項目，以確保其符合您的需求。 同時也務必檢閱每個提供者的文件以了解詳細的版本相容性資訊。

| NuGet 封裝                                                                                                     | 支援的資料庫引擎 | 維護程式/廠商                                                           | 注意/需求           | 實用連結                                                                                                                                                              |
|:------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:-------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer) | SQL Server 2008 及更新版本    | [EF Core 專案](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                                | [docs](xref:core/providers/sql-server/index)                                                                                                                              |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)       | SQLite 3.7 及更新版本         | [EF Core 專案](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                                | [docs](xref:core/providers/sqlite/index)                                                                                                                                  |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)   | EF Core 記憶體內部資料庫 | [EF Core 專案](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | 僅供測試               | [docs](xref:core/providers/in-memory/index)                                                                                                                               |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)   | PostgreSQL                 | [Npgsql 開發小組](https://github.com/npgsql)                          |                                | [docs](http://www.npgsql.org/efcore/index.html)                                                                                                                           |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)               | MySQL、MariaDB             | [Pomelo Foundation 專案](https://github.com/PomeloFoundation)              |                                | [讀我檔案](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                      |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)               | MyCAT 伺服器               | [Pomelo Foundation 專案](https://github.com/PomeloFoundation)              | 發行前版本，最高 EF Core 1.1 | [讀我檔案](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                      |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)   | SQL Server Compact 4.0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework                 | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                            |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)   | SQL Server Compact 3.5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework                 | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                            |
| [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                   | MySQL                      | [MySQL 專案](http://dev.mysql.com) (Oracle)                                | 發行前版本                    | [docs](https://dev.mysql.com/doc/connector-net/en/)                                                                                                                       |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                | Firebird 2.5 及 3.x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           | EF Core 2.0 及更新版本            | [wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                            |
| [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore)                                 | Db2、Informix              | [IBM](https://ibm.com)                                                        | 最高 EF Core 1.1，Windows     | [常見問題集](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Instructions_for_downloading_and_using_DB2_NET_Core_provider_package) |
| [IBM.EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)                         | Db2、Informix              | [IBM](https://ibm.com)                                                        | 最高 EF Core 1.1、Linux       | [常見問題集](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Instructions_for_downloading_and_using_DB2_NET_Core_provider_package) |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                            | Oracle 9.2.0.4 及更新版本     | [DevArt](https://www.devart.com/)                                             | 已支付                           | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                    |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                    | PostgreSQL 8.0 及更新版本     | [DevArt](https://www.devart.com/)                                             | 已支付                           | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                            | SQLite 3 及更新版本           | [DevArt](https://www.devart.com/)                                             | 已支付                           | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                    |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                              | MySQL 5 及更新版本            | [DevArt](https://www.devart.com/)                                             | 已支付                           | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                     |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                | Microsoft Access 檔案     | [Bubi](https://github.com/bubibubi)                                           | EF Core 2.0、.NET Framework    | [讀我檔案](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                  |

## <a name="future-providers"></a>未來的提供者

### <a name="cosmos-db"></a>Cosmos DB

我們正在為 Cosmos DB 中的 DocumentDB API 開發 EF Core 提供者。 這會是我們產生的首個完整文件導向資料庫提供者，而我們會將本次作業所得到的經驗運用在改善 2.1 後的後續版本設計上。 目前規劃是在 2.1 的時間範圍預先發行提供者預覽。

### <a name="oracle"></a>Oracle
Oracle .NET 已宣布將於 2018 第三季左右發行 EF Core 2.0 的第一方提供者。 如需詳細資訊，請參閱其 [.NET Core 與 Entity Framework Core 的方針聲明](http://www.oracle.com/technetwork/topics/dotnet/tech-info/odpnet-dotnet-ef-core-sod-4395108.pdf)。
請將任何有關此提供者的問題 (包含版本時間軸) 指向 [Oracle 社群網站](https://community.oracle.com/)。

與此同時，EF 小組也生產了[適用於 Oracle 資料庫的範例 EF Core 提供者](https://github.com/aspnet/EntityFrameworkCore/blob/dev/samples/OracleProvider/README.md)。 此專案的目的並非產生 Microsoft 擁有的 EF Core 提供者，而是協助我們找出 EF Core 關聯式與基本功能間須要弭平的隔閡，進而提供更好的 Oracle 支援，並開始為 Oracle 或協力廠商開發 EF Core 的其他 Oracle 提供者。

我們會考慮採用能夠改善範例實作的貢獻。 同時也歡迎並鼓勵社群以範例作為出發點參與，來為 EF Core 建立開放原始碼 Oracle 提供者。

## <a name="adding-a-database-provider-to-your-application"></a>將資料庫提供者新增至應用程式

大多數 EF Core 資料庫提供者皆以 NuGet 套件的形式散發。 這表示您可在命令列中使用 `dotnet` 工具加以安裝：

``` console
dotnet add package provider_package_name
```

或者在 Visual Studio 中使用 NuGet 的套件管理員主控台：

``` powershell
install-package provider_package_name
```

若您使用相依性插入容器，請在安裝完成後以 `OnConfiguring` 方式或 `AddDbContext` 方式在 `DbContext` 中設定提供者。 例如， 下列程式行會以傳遞的連接字串設定 SQL Server 提供者：

``` csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```  

資料庫提供者能夠延伸 EF Core，來啟用特定資料庫套有的功能。 有些概念通用於大多數資料庫，並且包含在主要 EF Core 元件中。 這類概念包含以 LINQ 表示查詢、異動，以及在從資料庫載入物件之後追蹤其變更。 有些概念是特定提供者所特有。 例如，SQL Server 提供者可讓您[設定記憶體最佳化資料表](xref:core/providers/sql-server/memory-optimized-tables) (SQL Server 特有的功能)。 其他概念是提供者類別特有的。 例如，關聯式資料庫的 EF Core 提供者是根據一般 `Microsoft.EntityFrameworkCore.Relational` 程式庫所建置，而此程式庫提供 API 來設定資料表和資料行對應、外部索引鍵條件約束等等。提供者通常會以 NuGet 套件的形式散發。

> [!IMPORTANT]  
> 當 EF Core 的新修補程式版本發行時，通常會包含 `Microsoft.EntityFrameworkCore.Relational` 套件的更新。 當您新增關聯式資料庫提供者時，此套件會成為應用程式的可轉移相依性。 但是，因為許多提供者皆從 EF Core 獨立發行，所以可能不會更新為相依於該套件的較新修補程式版本。 為了確保您能夠修正所有 Bug，建議您將 `Microsoft.EntityFrameworkCore.Relational` 的修補程式版本新增為應用程式的直接相依性。
