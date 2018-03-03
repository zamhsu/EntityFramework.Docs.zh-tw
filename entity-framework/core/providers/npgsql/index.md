---
title: Npgsql Database Provider for PostgreSQL - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 5ecd1b22-c68e-4d87-ba39-b0761f4d5b90
ms.technology: entity-framework-core
uid: core/providers/npgsql/index
ms.openlocfilehash: acf2e18d7a608b0d75b571a5ac0199d84f86066b
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="npgsql-ef-core-database-provider-for-postgresql"></a><span data-ttu-id="00f17-102">Npgsql EF Core Database Provider for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="00f17-102">Npgsql EF Core Database Provider for PostgreSQL</span></span>

<span data-ttu-id="00f17-103">此資料庫提供者可讓 Entity Framework Core 與 PostgreSQL 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="00f17-103">This database provider allows Entity Framework Core to be used with PostgreSQL.</span></span> <span data-ttu-id="00f17-104">此提供者會維護為 [Npgsql 專案](http://www.npgsql.org)的一部分。</span><span class="sxs-lookup"><span data-stu-id="00f17-104">The provider is maintained as part of the [Npgsql project](http://www.npgsql.org).</span></span>

> [!NOTE]  
> <span data-ttu-id="00f17-105">但此提供者不會維護為 Entity Framework Core 專案的一部分。</span><span class="sxs-lookup"><span data-stu-id="00f17-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="00f17-106">考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="00f17-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="00f17-107">安裝</span><span class="sxs-lookup"><span data-stu-id="00f17-107">Install</span></span>

<span data-ttu-id="00f17-108">安裝 [Npgsql.EntityFrameworkCore.PostgreSQL NuGet 套件](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)。</span><span class="sxs-lookup"><span data-stu-id="00f17-108">Install the [Npgsql.EntityFrameworkCore.PostgreSQL NuGet package](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL).</span></span>

``` powershell
Install-Package Npgsql.EntityFrameworkCore.PostgreSQL
```

## <a name="get-started"></a><span data-ttu-id="00f17-109">開始使用</span><span class="sxs-lookup"><span data-stu-id="00f17-109">Get Started</span></span>

<span data-ttu-id="00f17-110">請參閱 [Npgsql 文件](http://www.npgsql.org/efcore/index.html)開始使用。</span><span class="sxs-lookup"><span data-stu-id="00f17-110">See the [Npgsql documentation](http://www.npgsql.org/efcore/index.html) to get started.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="00f17-111">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="00f17-111">Supported Database Engines</span></span>

* <span data-ttu-id="00f17-112">PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="00f17-112">PostgreSQL</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="00f17-113">支援的平台</span><span class="sxs-lookup"><span data-stu-id="00f17-113">Supported Platforms</span></span>

* <span data-ttu-id="00f17-114">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="00f17-114">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="00f17-115">.NET 核心</span><span class="sxs-lookup"><span data-stu-id="00f17-115">.NET Core</span></span>

* <span data-ttu-id="00f17-116">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="00f17-116">Mono (4.2.0 onwards)</span></span>
