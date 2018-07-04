---
title: InMemory 資料庫提供者 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
ms.technology: entity-framework-core
uid: core/providers/in-memory/index
ms.openlocfilehash: 356af9390a8aafa5afe35f333cd1e6ac1988390d
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678982"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="e9ac9-102">EF Core 記憶體中資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="e9ac9-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="e9ac9-103">此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="e9ac9-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="e9ac9-104">雖然記憶體內部模式中的 SQLite 提供者作為關聯式資料庫的替代測試項目可能較為合適，但此資料庫作為測試用途仍相當實用。</span><span class="sxs-lookup"><span data-stu-id="e9ac9-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="e9ac9-105">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="e9ac9-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="e9ac9-106">安裝</span><span class="sxs-lookup"><span data-stu-id="e9ac9-106">Install</span></span>

<span data-ttu-id="e9ac9-107">安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="e9ac9-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="get-started"></a><span data-ttu-id="e9ac9-108">開始使用</span><span class="sxs-lookup"><span data-stu-id="e9ac9-108">Get Started</span></span>

<span data-ttu-id="e9ac9-109">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="e9ac9-109">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="e9ac9-110">使用 InMemory 測試</span><span class="sxs-lookup"><span data-stu-id="e9ac9-110">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)

* [<span data-ttu-id="e9ac9-111">UnicornStore 範例應用程式測試</span><span class="sxs-lookup"><span data-stu-id="e9ac9-111">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="e9ac9-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="e9ac9-112">Supported Database Engines</span></span>

* <span data-ttu-id="e9ac9-113">內建記憶體中資料庫 (僅供測試用途所設計)</span><span class="sxs-lookup"><span data-stu-id="e9ac9-113">Built-in in-memory database (designed for testing purposes only)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e9ac9-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="e9ac9-114">Supported Platforms</span></span>

* <span data-ttu-id="e9ac9-115">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="e9ac9-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="e9ac9-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9ac9-116">.NET Core</span></span>

* <span data-ttu-id="e9ac9-117">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="e9ac9-117">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="e9ac9-118">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="e9ac9-118">Universal Windows Platform</span></span>
