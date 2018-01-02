---
title: "SQLite 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
ms.technology: entity-framework-core
uid: core/providers/sqlite/index
ms.openlocfilehash: 0f3905a491fb5f7a657ce9037d166771e1f326d8
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="2821b-102">SQLite EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="2821b-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="2821b-103">此資料庫提供者可讓 Entity Framework Core 與 SQLite 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="2821b-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="2821b-104">此提供者會作為 [EntityFramework GitHub 專案](https://github.com/aspnet/EntityFramework)的一部分進行維護。</span><span class="sxs-lookup"><span data-stu-id="2821b-104">The provider is maintained as part of the [EntityFramework GitHub project](https://github.com/aspnet/EntityFramework).</span></span>

## <a name="install"></a><span data-ttu-id="2821b-105">Install</span><span class="sxs-lookup"><span data-stu-id="2821b-105">Install</span></span>

<span data-ttu-id="2821b-106">安裝 [Microsoft.EntityFrameworkCore.Sqlite NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="2821b-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="get-started"></a><span data-ttu-id="2821b-107">開始使用</span><span class="sxs-lookup"><span data-stu-id="2821b-107">Get Started</span></span>

<span data-ttu-id="2821b-108">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="2821b-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="2821b-109">UWP 上的本機 SQLite</span><span class="sxs-lookup"><span data-stu-id="2821b-109">Local SQLite on UWP</span></span>](../../get-started/uwp/getting-started.md)

* [<span data-ttu-id="2821b-110">新 SQLite 資料庫的 .NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="2821b-110">.NET Core Application to New SQLite Database</span></span>](../../get-started/netcore/new-db-sqlite.md)

* [<span data-ttu-id="2821b-111">Unicorn Clicker 應用程式範例</span><span class="sxs-lookup"><span data-stu-id="2821b-111">Unicorn Clicker Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornClicker/UWP)

* [<span data-ttu-id="2821b-112">Unicorn Packer 應用程式範例</span><span class="sxs-lookup"><span data-stu-id="2821b-112">Unicorn Packer Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornPacker)

## <a name="supported-database-engines"></a><span data-ttu-id="2821b-113">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="2821b-113">Supported Database Engines</span></span>

* <span data-ttu-id="2821b-114">SQLite (3.7 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="2821b-114">SQLite (3.7 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="2821b-115">支援的平台</span><span class="sxs-lookup"><span data-stu-id="2821b-115">Supported Platforms</span></span>

* <span data-ttu-id="2821b-116">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="2821b-116">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="2821b-117">.NET Core</span><span class="sxs-lookup"><span data-stu-id="2821b-117">.NET Core</span></span>

* <span data-ttu-id="2821b-118">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="2821b-118">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="2821b-119">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="2821b-119">Universal Windows Platform</span></span>

## <a name="limitations"></a><span data-ttu-id="2821b-120">限制</span><span class="sxs-lookup"><span data-stu-id="2821b-120">Limitations</span></span>

<span data-ttu-id="2821b-121">如需 SQLite 提供者的部分重要限制，請參閱 [SQLite 限制](limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="2821b-121">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
