---
title: SQLite 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 31de8449a12a10d4f98ebb4bb6125389606e9bbd
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993998"
---
# <a name="sqlite-ef-core-database-provider"></a>SQLite EF Core 資料庫提供者

此資料庫提供者可讓 Entity Framework Core 與 SQLite 搭配使用。 [Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。

## <a name="install"></a>安裝

安裝 [Microsoft.EntityFrameworkCore.Sqlite NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="get-started"></a>開始使用

下列資源將協助您開始使用此提供者。
* [UWP 上的本機 SQLite](../../get-started/uwp/getting-started.md)

* [新 SQLite 資料庫的 .NET Core 應用程式](../../get-started/netcore/new-db-sqlite.md)

* [Unicorn Clicker 應用程式範例](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornClicker/UWP)

* [Unicorn Packer 應用程式範例](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornPacker)

## <a name="supported-database-engines"></a>支援的資料庫引擎

* SQLite (3.7 及更新版本)

## <a name="supported-platforms"></a>支援的平台

* .NET Framework (4.5.1 及更新版本)

* .NET Core

* Mono (4.2.0 及更新版本)

* 通用 Windows 平台

## <a name="limitations"></a>限制

如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](limitations.md)。
