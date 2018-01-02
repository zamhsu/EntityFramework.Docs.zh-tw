---
title: "IBM Data Server (DB2) 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 02/15/2017
ms.assetid: 825e5332-5aa3-4600-9efb-ab71aaff59ec
ms.technology: entity-framework-core
uid: core/providers/ibm/index
ms.openlocfilehash: a9caa8df63850d4f6b5f2164dad7ac5af7504076
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="ibm-data-server-db2-ef-core-database-providers"></a><span data-ttu-id="296d2-102">IBM Data Server (DB2) EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="296d2-102">IBM Data Server (DB2) EF Core Database Providers</span></span>

<span data-ttu-id="296d2-103">此資料庫提供者可讓 Entity Framework Core 與 IBM Data Server 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="296d2-103">This database provider allows Entity Framework Core to be used with IBM Data Server.</span></span> <span data-ttu-id="296d2-104">此提供者由 IBM 進行維護。</span><span class="sxs-lookup"><span data-stu-id="296d2-104">The provider is maintained by IBM.</span></span>

> [!NOTE]  
> <span data-ttu-id="296d2-105">此提供者不會作為 Entity Framework Core 專案的一部分進行維護。</span><span class="sxs-lookup"><span data-stu-id="296d2-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="296d2-106">考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="296d2-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="296d2-107">Install</span><span class="sxs-lookup"><span data-stu-id="296d2-107">Install</span></span>

<span data-ttu-id="296d2-108">若要在 Windows 上使用 IBM Data Server，請安裝 [IBM.EntityFrameworkCore NuGet 套件](https://www.nuget.org/packages/IBM.EntityFrameworkCore)。</span><span class="sxs-lookup"><span data-stu-id="296d2-108">To work with IBM Data Server on Windows, install the [IBM.EntityFrameworkCore NuGet package](https://www.nuget.org/packages/IBM.EntityFrameworkCore).</span></span>

``` powershell
Install-Package IBM.EntityFrameworkCore
```

<span data-ttu-id="296d2-109">若要在 Linux 上使用 IBM Data Server，請安裝 [IBM.EntityFrameworkCore-lnx NuGet 套件](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)。</span><span class="sxs-lookup"><span data-stu-id="296d2-109">To work with IBM Data Server on Linux, install the [IBM.EntityFrameworkCore-lnx NuGet package](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx).</span></span>

``` powershell
Install-Package IBM.EntityFrameworkCore-lnx
```

## <a name="get-started"></a><span data-ttu-id="296d2-110">開始使用</span><span class="sxs-lookup"><span data-stu-id="296d2-110">Get Started</span></span>

[<span data-ttu-id="296d2-111">.NET Core 的 IBM .NET 提供者使用者入門</span><span class="sxs-lookup"><span data-stu-id="296d2-111">Getting started with IBM .NET Provider for .NET Core</span></span>](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/DB2DotnetCore?lang=en)

## <a name="supported-database-engines"></a><span data-ttu-id="296d2-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="296d2-112">Supported Database Engines</span></span>

* <span data-ttu-id="296d2-113">zOS</span><span class="sxs-lookup"><span data-stu-id="296d2-113">zOS</span></span>
* <span data-ttu-id="296d2-114">LUW 包含 IBM dashDB</span><span class="sxs-lookup"><span data-stu-id="296d2-114">LUW including IBM dashDB</span></span>
* <span data-ttu-id="296d2-115">IBM I</span><span class="sxs-lookup"><span data-stu-id="296d2-115">IBM I</span></span>
* <span data-ttu-id="296d2-116">Informix</span><span class="sxs-lookup"><span data-stu-id="296d2-116">Informix</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="296d2-117">支援的平台</span><span class="sxs-lookup"><span data-stu-id="296d2-117">Supported Platforms</span></span>

* <span data-ttu-id="296d2-118">.NET Framework (4.6 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="296d2-118">.NET Framework (4.6 onwards)</span></span>
* <span data-ttu-id="296d2-119">.NET Core</span><span class="sxs-lookup"><span data-stu-id="296d2-119">.NET Core</span></span>
