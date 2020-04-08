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
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="773a3-103">Microsoft SQL Server EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="773a3-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="773a3-104">此資料庫提供者可讓 Entity Framework Core 與 Microsoft SQL Server (包含 Azure SQL Database) 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="773a3-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="773a3-105">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="773a3-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="773a3-106">安裝</span><span class="sxs-lookup"><span data-stu-id="773a3-106">Install</span></span>

<span data-ttu-id="773a3-107">安裝 [Microsoft.EntityFrameworkCore.SqlServer NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="773a3-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="773a3-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="773a3-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="773a3-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="773a3-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="773a3-110">從 3.0.0 開始，提供者會參考 Microsoft.Data.SqlClient (先前的版本取決於 System.Data.SqlClient)。</span><span class="sxs-lookup"><span data-stu-id="773a3-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="773a3-111">若您的專案直接相依於 SqlClient，請確定該專案參考 Microsoft.Data.SqlClient 套件。</span><span class="sxs-lookup"><span data-stu-id="773a3-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="773a3-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="773a3-112">Supported Database Engines</span></span>

* <span data-ttu-id="773a3-113">Microsoft SQL Server (2012 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="773a3-113">Microsoft SQL Server (2012 onwards)</span></span>
