---
title: Microsoft SQL Server 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: 1e75bc4bf334b1a60d13a2ec9ef314e3afcf0273
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812097"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="7e6c1-102">Microsoft SQL Server EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="7e6c1-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="7e6c1-103">此資料庫提供者可讓 Entity Framework Core 與 Microsoft SQL Server (包括 SQL Azure) 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="7e6c1-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="7e6c1-104">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="7e6c1-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="7e6c1-105">安裝</span><span class="sxs-lookup"><span data-stu-id="7e6c1-105">Install</span></span>

<span data-ttu-id="7e6c1-106">安裝 [Microsoft.EntityFrameworkCore.SqlServer NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="7e6c1-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

# <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="7e6c1-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e6c1-107">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

# <a name="visual-studiotabvs"></a>[<span data-ttu-id="7e6c1-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e6c1-108">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="7e6c1-109">從 3.0.0 開始，提供者會參考 Microsoft.Data.SqlClient (先前的版本取決於 System.Data.SqlClient)。</span><span class="sxs-lookup"><span data-stu-id="7e6c1-109">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="7e6c1-110">若您的專案直接相依於 SqlClient，請確定它參考正確的套件。</span><span class="sxs-lookup"><span data-stu-id="7e6c1-110">If your project takes a direct dependency on SqlClient, make sure it references the correct package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="7e6c1-111">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="7e6c1-111">Supported Database Engines</span></span>

* <span data-ttu-id="7e6c1-112">Microsoft SQL Server (2012 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="7e6c1-112">Microsoft SQL Server (2012 onwards)</span></span>
