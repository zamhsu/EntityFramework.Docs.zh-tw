---
title: SQLite 資料庫提供者 - EF Core
description: Entity Framework Core SQL 部資料庫提供者的相關資訊
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 8620f0e37825368cb3d7965a05118ab1297fb9e8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616563"
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

如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](xref:core/providers/sqlite/limitations)。
