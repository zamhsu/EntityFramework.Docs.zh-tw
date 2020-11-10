---
title: InMemory 資料庫提供者 - EF Core
description: Entity Framework Core 記憶體內部資料庫提供者的相關資訊
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1af75088ae892e3b428caf6bdb31dd2b750a05fe
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430166"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="731bb-103">EF Core 記憶體中資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="731bb-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="731bb-104">此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="731bb-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="731bb-105">記憶體內部資料庫可用於測試，雖然記憶體內部模式中的 SQLite 提供者可能是更適當的測試取代關係資料庫。</span><span class="sxs-lookup"><span data-stu-id="731bb-105">The in-memory database can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="731bb-106">記憶體內部資料庫是專為測試所設計。</span><span class="sxs-lookup"><span data-stu-id="731bb-106">The in-memory database is designed for testing only.</span></span> <span data-ttu-id="731bb-107">[Entity Framework Core 專案](https://github.com/dotnet/efcore)的維護包含此提供者。</span><span class="sxs-lookup"><span data-stu-id="731bb-107">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="731bb-108">安裝</span><span class="sxs-lookup"><span data-stu-id="731bb-108">Install</span></span>

<span data-ttu-id="731bb-109">安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="731bb-109">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="731bb-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="731bb-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="731bb-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="731bb-111">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="731bb-112">開始使用</span><span class="sxs-lookup"><span data-stu-id="731bb-112">Get Started</span></span>

<span data-ttu-id="731bb-113">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="731bb-113">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="731bb-114">使用 InMemory 測試</span><span class="sxs-lookup"><span data-stu-id="731bb-114">Testing with InMemory</span></span>](xref:core/testing/in-memory)
* [<span data-ttu-id="731bb-115">UnicornStore 範例應用程式測試</span><span class="sxs-lookup"><span data-stu-id="731bb-115">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="731bb-116">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="731bb-116">Supported Database Engines</span></span>

<span data-ttu-id="731bb-117">同進程記憶體資料庫，專為測試目的而設計。</span><span class="sxs-lookup"><span data-stu-id="731bb-117">In-process memory database, designed for testing purposes only.</span></span>
