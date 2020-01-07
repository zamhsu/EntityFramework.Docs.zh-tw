---
title: SQLite 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: e8c3d675322b163fdf1e2e7e01f3815e28f427a2
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502249"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="51ebc-102">SQLite EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="51ebc-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="51ebc-103">此資料庫提供者可讓 Entity Framework Core 與 SQLite 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="51ebc-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="51ebc-104">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="51ebc-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="51ebc-105">安裝</span><span class="sxs-lookup"><span data-stu-id="51ebc-105">Install</span></span>

<span data-ttu-id="51ebc-106">安裝 [Microsoft.EntityFrameworkCore.Sqlite NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="51ebc-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="51ebc-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="51ebc-107">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studiotabvs"></a>[<span data-ttu-id="51ebc-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51ebc-108">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="51ebc-109">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="51ebc-109">Supported Database Engines</span></span>

* <span data-ttu-id="51ebc-110">SQLite (3.7 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="51ebc-110">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="51ebc-111">限制</span><span class="sxs-lookup"><span data-stu-id="51ebc-111">Limitations</span></span>

<span data-ttu-id="51ebc-112">如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="51ebc-112">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
