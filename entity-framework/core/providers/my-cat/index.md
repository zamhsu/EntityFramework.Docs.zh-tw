---
title: "Pomelo MyCat 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 02/27/2017
ms.assetid: ad798f02-a7a4-45c1-b0a9-8e92f5dc6ff0
ms.technology: entity-framework-core
uid: core/providers/my-cat/index
ms.openlocfilehash: e13da3ab47e56d1b869e445f2398eda6ea84a79f
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="pomelo-mycat-ef-core-database-provider"></a><span data-ttu-id="f915e-102">Pomelo MyCat EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="f915e-102">Pomelo MyCat EF Core Database Provider</span></span>

<span data-ttu-id="f915e-103">此資料庫提供者可讓 Entity Framework Core 與 [MyCat](https://github.com/MyCATApache/Mycat-Server) 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="f915e-103">This database provider allows Entity Framework Core to be used with [MyCat](https://github.com/MyCATApache/Mycat-Server).</span></span> <span data-ttu-id="f915e-104">此提供者會維護為 [Pomelo Foundation 專案](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy)的一部分。</span><span class="sxs-lookup"><span data-stu-id="f915e-104">The provider is maintained as part of the [Pomelo Foundation Project](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy).</span></span>

> [!NOTE]  
> <span data-ttu-id="f915e-105">此提供者不會作為 Entity Framework Core 專案的一部分進行維護。</span><span class="sxs-lookup"><span data-stu-id="f915e-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="f915e-106">考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="f915e-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="f915e-107">安裝</span><span class="sxs-lookup"><span data-stu-id="f915e-107">Install</span></span>

<span data-ttu-id="f915e-108">下載並執行[專案網站中的最新版本](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy/releases)。</span><span class="sxs-lookup"><span data-stu-id="f915e-108">Download and run [the latest release from the project site](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy/releases).</span></span>

## <a name="get-started"></a><span data-ttu-id="f915e-109">開始使用</span><span class="sxs-lookup"><span data-stu-id="f915e-109">Get Started</span></span>

<span data-ttu-id="f915e-110">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="f915e-110">The following resources will help you get started with this provider.</span></span>
 * [<span data-ttu-id="f915e-111">設定步驟</span><span class="sxs-lookup"><span data-stu-id="f915e-111">Setup steps</span></span>](https://github.com/aspnet/EntityFramework.Docs/issues/252)
 * [<span data-ttu-id="f915e-112">快速入門指南</span><span class="sxs-lookup"><span data-stu-id="f915e-112">Getting started video</span></span>](https://www.youtube.com/watch?v=q0CXfFNtMZo)

## <a name="supported-database-engines"></a><span data-ttu-id="f915e-113">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="f915e-113">Supported Database Engines</span></span>

* <span data-ttu-id="f915e-114">MyCat</span><span class="sxs-lookup"><span data-stu-id="f915e-114">MyCat</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="f915e-115">支援的平台</span><span class="sxs-lookup"><span data-stu-id="f915e-115">Supported Platforms</span></span>

* <span data-ttu-id="f915e-116">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="f915e-116">.NET Framework (4.5.1 onwards)</span></span>
* <span data-ttu-id="f915e-117">.NET 核心</span><span class="sxs-lookup"><span data-stu-id="f915e-117">.NET Core</span></span>
* <span data-ttu-id="f915e-118">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="f915e-118">Mono (4.2.0 onwards)</span></span>
