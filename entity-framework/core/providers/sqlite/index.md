---
title: SQLite 資料庫提供者 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
ms.technology: entity-framework-core
uid: core/providers/sqlite/index
ms.openlocfilehash: 2e392f382f0e6f4d092a362c44f2149eb336db17
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678744"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="0ee6a-102">SQLite EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="0ee6a-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="0ee6a-103">此資料庫提供者可讓 Entity Framework Core 與 SQLite 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="0ee6a-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="0ee6a-104">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="0ee6a-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="0ee6a-105">安裝</span><span class="sxs-lookup"><span data-stu-id="0ee6a-105">Install</span></span>

<span data-ttu-id="0ee6a-106">安裝 [Microsoft.EntityFrameworkCore.Sqlite NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="0ee6a-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="get-started"></a><span data-ttu-id="0ee6a-107">開始使用</span><span class="sxs-lookup"><span data-stu-id="0ee6a-107">Get Started</span></span>

<span data-ttu-id="0ee6a-108">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="0ee6a-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="0ee6a-109">UWP 上的本機 SQLite</span><span class="sxs-lookup"><span data-stu-id="0ee6a-109">Local SQLite on UWP</span></span>](../../get-started/uwp/getting-started.md)

* [<span data-ttu-id="0ee6a-110">新 SQLite 資料庫的 .NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="0ee6a-110">.NET Core Application to New SQLite Database</span></span>](../../get-started/netcore/new-db-sqlite.md)

* [<span data-ttu-id="0ee6a-111">Unicorn Clicker 應用程式範例</span><span class="sxs-lookup"><span data-stu-id="0ee6a-111">Unicorn Clicker Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornClicker/UWP)

* [<span data-ttu-id="0ee6a-112">Unicorn Packer 應用程式範例</span><span class="sxs-lookup"><span data-stu-id="0ee6a-112">Unicorn Packer Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornPacker)

## <a name="supported-database-engines"></a><span data-ttu-id="0ee6a-113">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="0ee6a-113">Supported Database Engines</span></span>

* <span data-ttu-id="0ee6a-114">SQLite (3.7 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="0ee6a-114">SQLite (3.7 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="0ee6a-115">支援的平台</span><span class="sxs-lookup"><span data-stu-id="0ee6a-115">Supported Platforms</span></span>

* <span data-ttu-id="0ee6a-116">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="0ee6a-116">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="0ee6a-117">.NET 核心</span><span class="sxs-lookup"><span data-stu-id="0ee6a-117">.NET Core</span></span>

* <span data-ttu-id="0ee6a-118">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="0ee6a-118">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="0ee6a-119">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="0ee6a-119">Universal Windows Platform</span></span>

## <a name="limitations"></a><span data-ttu-id="0ee6a-120">限制</span><span class="sxs-lookup"><span data-stu-id="0ee6a-120">Limitations</span></span>

<span data-ttu-id="0ee6a-121">如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="0ee6a-121">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
