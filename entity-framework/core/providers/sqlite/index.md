---
title: SQLite 資料庫提供者 - EF Core
description: Entity Framework Core SQL 部資料庫提供者的相關資訊
author: bricelam
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: 2d221168c093271d220d4d8fbc7779c1a5aab701
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063981"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="e3e9a-103">SQLite EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="e3e9a-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="e3e9a-104">此資料庫提供者可讓 Entity Framework Core 與 SQLite 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="e3e9a-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="e3e9a-105">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="e3e9a-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="e3e9a-106">安裝</span><span class="sxs-lookup"><span data-stu-id="e3e9a-106">Install</span></span>

<span data-ttu-id="e3e9a-107">安裝 [Microsoft.EntityFrameworkCore.Sqlite NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="e3e9a-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="e3e9a-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e3e9a-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="e3e9a-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3e9a-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="e3e9a-110">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="e3e9a-110">Supported Database Engines</span></span>

* <span data-ttu-id="e3e9a-111">SQLite (3.7 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="e3e9a-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="e3e9a-112">限制</span><span class="sxs-lookup"><span data-stu-id="e3e9a-112">Limitations</span></span>

<span data-ttu-id="e3e9a-113">如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](xref:core/providers/sqlite/limitations)。</span><span class="sxs-lookup"><span data-stu-id="e3e9a-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
