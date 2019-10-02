---
title: Microsoft SQL Server 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: f0aa290e8c5166c278f8c9782c4304de5e91f26b
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813511"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="0b7aa-102">Microsoft SQL Server EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="0b7aa-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="0b7aa-103">此資料庫提供者可讓 Entity Framework Core 與 Microsoft SQL Server (包括 SQL Azure) 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="0b7aa-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="0b7aa-104">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="0b7aa-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="0b7aa-105">安裝</span><span class="sxs-lookup"><span data-stu-id="0b7aa-105">Install</span></span>

<span data-ttu-id="0b7aa-106">安裝 [Microsoft.EntityFrameworkCore.SqlServer NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="0b7aa-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

# <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="0b7aa-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0b7aa-107">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

# <a name="visual-studiotabvs"></a>[<span data-ttu-id="0b7aa-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b7aa-108">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="0b7aa-109">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="0b7aa-109">Supported Database Engines</span></span>

* <span data-ttu-id="0b7aa-110">Microsoft SQL Server (2012 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="0b7aa-110">Microsoft SQL Server (2012 onwards)</span></span>
