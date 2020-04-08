---
title: Microsoft SQL Server 資料庫提供者 - EF Core
description: 資料庫提供者的文件，可讓 Entity Framework Core 與 Microsoft SQL Server 搭配使用
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: baae668a7ec255e35ab0e23e5c5ddfa47bda917e
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413143"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core 資料庫提供者

此資料庫提供者可讓 Entity Framework Core 與 Microsoft SQL Server (包含 Azure SQL Database) 搭配使用。 [Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。

## <a name="install"></a>安裝

安裝 [Microsoft.EntityFrameworkCore.SqlServer NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> 從 3.0.0 開始，提供者會參考 Microsoft.Data.SqlClient (先前的版本取決於 System.Data.SqlClient)。 若您的專案直接相依於 SqlClient，請確定該專案參考 Microsoft.Data.SqlClient 套件。

## <a name="supported-database-engines"></a>支援的資料庫引擎

* Microsoft SQL Server (2012 及更新版本)
