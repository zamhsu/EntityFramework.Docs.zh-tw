---
title: "Microsoft SQL Server Compact Edition 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 073f0004-3eb5-4618-ab93-0674910e1819
ms.technology: entity-framework-core
uid: core/providers/sql-compact/index
ms.openlocfilehash: d8b73621bdd363efec5bb7728886e0a0f6bdcf76
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="microsoft-sql-server-compact-edition-ef-core-database-provider"></a>Microsoft SQL Server Compact Edition EF Core 資料庫提供者

此資料庫提供者可讓 Entity Framework Core 與 SQL Server Compact Edition 搭配使用。 此提供者會維護為 [ErikEJ/EntityFramework.SqlServerCompact GitHub 專案](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)的一部分。

> [!NOTE]  
> 但此提供者不會維護為 Entity Framework Core 專案的一部分。 考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保它們符合您的需求。

## <a name="install"></a>Install

若要使用 SQL Server Compact Edition 4.0，請安裝 [EntityFrameworkCore.SqlServerCompact40 NuGet 套件](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)。

``` powershell
Install-Package EntityFrameworkCore.SqlServerCompact40
```

若要使用 SQL Server Compact Edition 3.5，請安裝 [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)。

``` powershell
Install-Package EntityFrameworkCore.SqlServerCompact35
```

## <a name="get-started"></a>開始使用

請參閱[專案網站上的快速入門文件](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)

## <a name="supported-database-engines"></a>支援的資料庫引擎

* SQL Server Compact Edition 3.5

* SQL Server Compact Edition 4.0

## <a name="supported-platforms"></a>支援的平台

* .NET Framework (4.5.1 和更新版本)
