---
title: Microsoft SQL Server 資料庫提供者 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
ms.technology: entity-framework-core
uid: core/providers/sql-server/index
ms.openlocfilehash: 2ed7c0dd127db03d5e7340fde1ef83cf01b30135
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678646"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="b8771-102">Microsoft SQL Server EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="b8771-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="b8771-103">此資料庫提供者可讓 Entity Framework Core 與 Microsoft SQL Server (包括 SQL Azure) 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="b8771-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="b8771-104">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="b8771-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="b8771-105">安裝</span><span class="sxs-lookup"><span data-stu-id="b8771-105">Install</span></span>

<span data-ttu-id="b8771-106">安裝 [Microsoft.EntityFrameworkCore.SqlServer NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="b8771-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="get-started"></a><span data-ttu-id="b8771-107">開始使用</span><span class="sxs-lookup"><span data-stu-id="b8771-107">Get Started</span></span>

<span data-ttu-id="b8771-108">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="b8771-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="b8771-109">.NET Framework 使用者入門 (主控台、WinForms、WPF 等等)</span><span class="sxs-lookup"><span data-stu-id="b8771-109">Getting Started on .NET Framework (Console, WinForms, WPF, etc.)</span></span>](../../get-started/full-dotnet/index.md)

* [<span data-ttu-id="b8771-110">ASP.NET Core 使用者入門</span><span class="sxs-lookup"><span data-stu-id="b8771-110">Getting Started on ASP.NET Core</span></span>](../../get-started/aspnetcore/index.md)

* [<span data-ttu-id="b8771-111">UnicornStore 應用程式範例</span><span class="sxs-lookup"><span data-stu-id="b8771-111">UnicornStore Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornStore)

## <a name="supported-database-engines"></a><span data-ttu-id="b8771-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="b8771-112">Supported Database Engines</span></span>

* <span data-ttu-id="b8771-113">Microsoft SQL Server (2008 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="b8771-113">Microsoft SQL Server (2008 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="b8771-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="b8771-114">Supported Platforms</span></span>

* <span data-ttu-id="b8771-115">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="b8771-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="b8771-116">.NET 核心</span><span class="sxs-lookup"><span data-stu-id="b8771-116">.NET Core</span></span>

* <span data-ttu-id="b8771-117">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="b8771-117">Mono (4.2.0 onwards)</span></span>

      Caution: Using this provider on Mono will make use of the Mono SQL Client implementation, which has a number of known issues. For example, it does not support secure connections (SSL).
