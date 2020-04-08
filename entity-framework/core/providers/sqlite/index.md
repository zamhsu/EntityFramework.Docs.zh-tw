---
title: SQLite 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: e8c3d675322b163fdf1e2e7e01f3815e28f427a2
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413133"
---
# <a name="sqlite-ef-core-database-provider"></a>SQLite EF Core 資料庫提供者

此資料庫提供者可讓 Entity Framework Core 與 SQLite 搭配使用。 [Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。

## <a name="install"></a>安裝

安裝 [Microsoft.EntityFrameworkCore.Sqlite NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a>支援的資料庫引擎

* SQLite (3.7 及更新版本)

## <a name="limitations"></a>限制

如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](limitations.md)。
