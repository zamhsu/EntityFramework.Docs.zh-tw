---
title: InMemory 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: 4b35e8c4b29a951449d4a26c6e274eb3015069bc
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656029"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="39938-102">EF Core 記憶體中資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="39938-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="39938-103">此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="39938-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="39938-104">雖然記憶體內部模式中的 SQLite 提供者作為關聯式資料庫的替代測試項目可能較為合適，但此資料庫作為測試用途仍相當實用。</span><span class="sxs-lookup"><span data-stu-id="39938-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="39938-105">[Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="39938-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="39938-106">安裝</span><span class="sxs-lookup"><span data-stu-id="39938-106">Install</span></span>

<span data-ttu-id="39938-107">安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="39938-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="39938-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="39938-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="39938-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39938-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="39938-110">開始使用</span><span class="sxs-lookup"><span data-stu-id="39938-110">Get Started</span></span>

<span data-ttu-id="39938-111">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="39938-111">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="39938-112">使用 InMemory 測試</span><span class="sxs-lookup"><span data-stu-id="39938-112">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)
* [<span data-ttu-id="39938-113">UnicornStore 範例應用程式測試</span><span class="sxs-lookup"><span data-stu-id="39938-113">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="39938-114">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="39938-114">Supported Database Engines</span></span>

<span data-ttu-id="39938-115">同處理序記憶體資料庫 (專為測試目的而設計)</span><span class="sxs-lookup"><span data-stu-id="39938-115">In-process memory database (designed for testing purposes only)</span></span>
