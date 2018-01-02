---
title: "Microsoft SQL Server 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
ms.technology: entity-framework-core
uid: core/providers/sql-server/index
ms.openlocfilehash: b2faf932e0484da4df0c1774afa7ba7ae2d077a5
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="cf3b7-102">Microsoft SQL Server EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="cf3b7-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="cf3b7-103">此資料庫提供者可讓 Entity Framework Core 與 Microsoft SQL Server (包括 SQL Azure) 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="cf3b7-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="cf3b7-104">此提供者會作為 [EntityFramework GitHub 專案](https://github.com/aspnet/EntityFramework)的一部分進行維護。</span><span class="sxs-lookup"><span data-stu-id="cf3b7-104">The provider is maintained as part of the [EntityFramework GitHub project](https://github.com/aspnet/EntityFramework).</span></span>

## <a name="install"></a><span data-ttu-id="cf3b7-105">Install</span><span class="sxs-lookup"><span data-stu-id="cf3b7-105">Install</span></span>

<span data-ttu-id="cf3b7-106">安裝 [Microsoft.EntityFrameworkCore.SqlServer NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="cf3b7-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="get-started"></a><span data-ttu-id="cf3b7-107">開始使用</span><span class="sxs-lookup"><span data-stu-id="cf3b7-107">Get Started</span></span>

<span data-ttu-id="cf3b7-108">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="cf3b7-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="cf3b7-109">.NET Framework 使用者入門 (主控台、WinForms、WPF 等等)</span><span class="sxs-lookup"><span data-stu-id="cf3b7-109">Getting Started on .NET Framework (Console, WinForms, WPF, etc.)</span></span>](../../get-started/full-dotnet/index.md)

* [<span data-ttu-id="cf3b7-110">ASP.NET Core 使用者入門</span><span class="sxs-lookup"><span data-stu-id="cf3b7-110">Getting Started on ASP.NET Core</span></span>](../../get-started/aspnetcore/index.md)

* [<span data-ttu-id="cf3b7-111">UnicornStore 應用程式範例</span><span class="sxs-lookup"><span data-stu-id="cf3b7-111">UnicornStore Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornStore)

## <a name="supported-database-engines"></a><span data-ttu-id="cf3b7-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="cf3b7-112">Supported Database Engines</span></span>

* <span data-ttu-id="cf3b7-113">Microsoft SQL Server (2008 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="cf3b7-113">Microsoft SQL Server (2008 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="cf3b7-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="cf3b7-114">Supported Platforms</span></span>

* <span data-ttu-id="cf3b7-115">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="cf3b7-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="cf3b7-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf3b7-116">.NET Core</span></span>

* <span data-ttu-id="cf3b7-117">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="cf3b7-117">Mono (4.2.0 onwards)</span></span>

      Caution: Using this provider on Mono will make use of the Mono SQL Client implementation, which has a number of known issues. For example, it does not support secure connections (SSL).
