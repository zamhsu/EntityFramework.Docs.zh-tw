---
title: SQLite 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: e4cbdba46f901831892192a343db2920a5760042
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149269"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="f971b-102">SQLite EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="f971b-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="f971b-103">此資料庫提供者可讓 Entity Framework Core 與 SQLite 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="f971b-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="f971b-104">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="f971b-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="f971b-105">安裝</span><span class="sxs-lookup"><span data-stu-id="f971b-105">Install</span></span>

<span data-ttu-id="f971b-106">安裝 [Microsoft.EntityFrameworkCore.Sqlite NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="f971b-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="supported-database-engines"></a><span data-ttu-id="f971b-107">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="f971b-107">Supported Database Engines</span></span>

* <span data-ttu-id="f971b-108">SQLite (3.7 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="f971b-108">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="f971b-109">限制</span><span class="sxs-lookup"><span data-stu-id="f971b-109">Limitations</span></span>

<span data-ttu-id="f971b-110">如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="f971b-110">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
