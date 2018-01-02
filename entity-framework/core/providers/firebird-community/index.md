---
title: "FirebirdSQL 資料庫提供者 - EF Core"
author: ralmsdeveloper
ms.author: ralmsdeveloper
ms.date: 11/22/2017
ms.assetid: d0168c04-d30d-4219-98f8-a54690cea3c6
ms.technology: entity-framework-core
uid: core/providers/firebird-community/index
ms.openlocfilehash: 682988a91ef04dbd552588a537f53124b931f17d
ms.sourcegitcommit: 1cbd3d3cd92bdaf8223b8821c58200bcfed10ede
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="firebird-ef-core-database-provider"></a><span data-ttu-id="517f5-102">Firebird EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="517f5-102">Firebird EF Core Database Provider</span></span>

<span data-ttu-id="517f5-103">此資料庫提供者可讓 Entity Framework Core 與 FirebirdSQL 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="517f5-103">This database provider allows Entity Framework Core to be used with FirebirdSQL.</span></span> <span data-ttu-id="517f5-104">此提供者會維護為 [ralmsdeveloper/EntityFrameworkCore.FirebirdSQL GitHub 專案](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL)的一部分。</span><span class="sxs-lookup"><span data-stu-id="517f5-104">The provider is maintained as part of the [ralmsdeveloper/EntityFrameworkCore.FirebirdSQL GitHub Project](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL).</span></span>

> [!NOTE]  
>
> <span data-ttu-id="517f5-105">但此提供者不會維護為 Entity Framework Core 專案的一部分。</span><span class="sxs-lookup"><span data-stu-id="517f5-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="517f5-106">考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保它們符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="517f5-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="517f5-107">Install</span><span class="sxs-lookup"><span data-stu-id="517f5-107">Install</span></span>

<span data-ttu-id="517f5-108">安裝 [EntityFrameworkCore.FirebirdSQL NuGet 套件](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSQL)。</span><span class="sxs-lookup"><span data-stu-id="517f5-108">Install the [EntityFrameworkCore.FirebirdSQL NuGet package](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSQL).</span></span>

``` powershell
Install-Package EntityFrameworkCore.FirebirdSQL
```

## <a name="get-started"></a><span data-ttu-id="517f5-109">開始使用</span><span class="sxs-lookup"><span data-stu-id="517f5-109">Get Started</span></span>

<span data-ttu-id="517f5-110">請參閱[專案網站上的快速入門文件](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)</span><span class="sxs-lookup"><span data-stu-id="517f5-110">See the [getting started documentation on the project site](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="517f5-111">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="517f5-111">Supported Database Engines</span></span>

* <span data-ttu-id="517f5-112">FirebirdSql 2.5</span><span class="sxs-lookup"><span data-stu-id="517f5-112">FirebirdSql 2.5</span></span>
* <span data-ttu-id="517f5-113">FirebirdSql 3.X</span><span class="sxs-lookup"><span data-stu-id="517f5-113">FirebirdSql 3.X</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="517f5-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="517f5-114">Supported Platforms</span></span>

* <span data-ttu-id="517f5-115">.NET Framework (4.5.1 和更新版本)</span><span class="sxs-lookup"><span data-stu-id="517f5-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="517f5-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="517f5-116">.NET Core</span></span>

* <span data-ttu-id="517f5-117">Mono (4.2.0 和更新版本)</span><span class="sxs-lookup"><span data-stu-id="517f5-117">Mono (4.2.0 onwards)</span></span>
