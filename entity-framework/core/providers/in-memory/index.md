---
title: InMemory 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: ca802f55d973b9f79073c2507c1e0c7a853a1fce
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993316"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="6ab19-102">EF Core 記憶體中資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="6ab19-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="6ab19-103">此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ab19-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="6ab19-104">雖然記憶體內部模式中的 SQLite 提供者作為關聯式資料庫的替代測試項目可能較為合適，但此資料庫作為測試用途仍相當實用。</span><span class="sxs-lookup"><span data-stu-id="6ab19-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="6ab19-105">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="6ab19-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="6ab19-106">安裝</span><span class="sxs-lookup"><span data-stu-id="6ab19-106">Install</span></span>

<span data-ttu-id="6ab19-107">安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="6ab19-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="get-started"></a><span data-ttu-id="6ab19-108">開始使用</span><span class="sxs-lookup"><span data-stu-id="6ab19-108">Get Started</span></span>

<span data-ttu-id="6ab19-109">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="6ab19-109">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="6ab19-110">使用 InMemory 測試</span><span class="sxs-lookup"><span data-stu-id="6ab19-110">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)

* [<span data-ttu-id="6ab19-111">UnicornStore 範例應用程式測試</span><span class="sxs-lookup"><span data-stu-id="6ab19-111">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="6ab19-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="6ab19-112">Supported Database Engines</span></span>

* <span data-ttu-id="6ab19-113">內建記憶體中資料庫 (僅供測試用途所設計)</span><span class="sxs-lookup"><span data-stu-id="6ab19-113">Built-in in-memory database (designed for testing purposes only)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="6ab19-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="6ab19-114">Supported Platforms</span></span>

* <span data-ttu-id="6ab19-115">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="6ab19-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="6ab19-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ab19-116">.NET Core</span></span>

* <span data-ttu-id="6ab19-117">Mono (4.2.0 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="6ab19-117">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="6ab19-118">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="6ab19-118">Universal Windows Platform</span></span>
