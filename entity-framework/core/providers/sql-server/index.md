---
title: Microsoft SQL Server 資料庫提供者 - EF Core
description: 資料庫提供者的文件，可讓 Entity Framework Core 與 Microsoft SQL Server 搭配使用
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: baae668a7ec255e35ab0e23e5c5ddfa47bda917e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413143"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="401a0-103">Microsoft SQL Server EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="401a0-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="401a0-104">此資料庫提供者可讓 Entity Framework Core 與 Microsoft SQL Server (包含 Azure SQL Database) 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="401a0-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="401a0-105">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="401a0-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="401a0-106">安裝</span><span class="sxs-lookup"><span data-stu-id="401a0-106">Install</span></span>

<span data-ttu-id="401a0-107">安裝 [Microsoft.EntityFrameworkCore.SqlServer NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="401a0-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="401a0-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="401a0-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="401a0-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="401a0-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="401a0-110">從 3.0.0 開始，提供者會參考 Microsoft.Data.SqlClient (先前的版本取決於 System.Data.SqlClient)。</span><span class="sxs-lookup"><span data-stu-id="401a0-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="401a0-111">若您的專案直接相依於 SqlClient，請確定該專案參考 Microsoft.Data.SqlClient 套件。</span><span class="sxs-lookup"><span data-stu-id="401a0-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="401a0-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="401a0-112">Supported Database Engines</span></span>

* <span data-ttu-id="401a0-113">Microsoft SQL Server (2012 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="401a0-113">Microsoft SQL Server (2012 onwards)</span></span>
