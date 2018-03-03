---
title: "Pomelo MySQL 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d0198c04-d30d-4419-98f8-a54690cea3c8
ms.technology: entity-framework-core
uid: core/providers/pomelo/index
ms.openlocfilehash: 9ddcda1ae7b058c01937867817e2666b97e7f37a
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="pomelo-ef-core-database-provider-for-mysql"></a><span data-ttu-id="43486-102">Pomelo EF Core Database Provider for MySQL</span><span class="sxs-lookup"><span data-stu-id="43486-102">Pomelo EF Core Database Provider for MySQL</span></span>

<span data-ttu-id="43486-103">此資料庫提供者可讓 Entity Framework Core 與 MySQL 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="43486-103">This database provider allows Entity Framework Core to be used with MySQL.</span></span> <span data-ttu-id="43486-104">此提供者會維護為 [Pomelo Foundation 專案](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的一部分。</span><span class="sxs-lookup"><span data-stu-id="43486-104">The provider is maintained as part of the [Pomelo Foundation Project](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql).</span></span>

> [!NOTE]  
>
> <span data-ttu-id="43486-105">此提供者不會作為 Entity Framework Core 專案的一部分進行維護。</span><span class="sxs-lookup"><span data-stu-id="43486-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="43486-106">考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="43486-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="43486-107">安裝</span><span class="sxs-lookup"><span data-stu-id="43486-107">Install</span></span>

<span data-ttu-id="43486-108">安裝 [Pomelo.EntityFrameworkCore.MySql NuGet 套件](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)。</span><span class="sxs-lookup"><span data-stu-id="43486-108">Install the [Pomelo.EntityFrameworkCore.MySql NuGet package](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql).</span></span>

``` powershell
Install-Package Pomelo.EntityFrameworkCore.MySql
```

## <a name="get-started"></a><span data-ttu-id="43486-109">開始使用</span><span class="sxs-lookup"><span data-stu-id="43486-109">Get Started</span></span>

<span data-ttu-id="43486-110">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="43486-110">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="43486-111">快速入門文件</span><span class="sxs-lookup"><span data-stu-id="43486-111">Getting started documentation</span></span>](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md#getting-started)

* [<span data-ttu-id="43486-112">Yuuko 部落格範例應用程式</span><span class="sxs-lookup"><span data-stu-id="43486-112">Yuuko Blog sample application</span></span>](https://github.com/PomeloFoundation/YuukoBlog)

## <a name="supported-database-engines"></a><span data-ttu-id="43486-113">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="43486-113">Supported Database Engines</span></span>

* <span data-ttu-id="43486-114">MySQL</span><span class="sxs-lookup"><span data-stu-id="43486-114">MySQL</span></span>
* <span data-ttu-id="43486-115">MariaDB</span><span class="sxs-lookup"><span data-stu-id="43486-115">MariaDB</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="43486-116">支援的平台</span><span class="sxs-lookup"><span data-stu-id="43486-116">Supported Platforms</span></span>

* <span data-ttu-id="43486-117">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="43486-117">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="43486-118">.NET 核心</span><span class="sxs-lookup"><span data-stu-id="43486-118">.NET Core</span></span>

* <span data-ttu-id="43486-119">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="43486-119">Mono (4.2.0 onwards)</span></span>
