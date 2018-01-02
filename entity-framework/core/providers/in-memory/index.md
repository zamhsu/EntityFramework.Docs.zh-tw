---
title: "InMemory 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
ms.technology: entity-framework-core
uid: core/providers/in-memory/index
ms.openlocfilehash: a8e05f50837f3da554b338475d24215706dfa2ec
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="a9767-102">EF Core 記憶體中資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="a9767-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="a9767-103">此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="a9767-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="a9767-104">對使用 Entity Framework Core 的程式碼進行測試時，這十分有用。</span><span class="sxs-lookup"><span data-stu-id="a9767-104">This is useful when testing code that uses Entity Framework Core.</span></span> <span data-ttu-id="a9767-105">此提供者會維護為 [EntityFramework GitHub 專案](https://github.com/aspnet/EntityFramework)的一部分。</span><span class="sxs-lookup"><span data-stu-id="a9767-105">The provider is maintained as part of the [EntityFramework GitHub project](https://github.com/aspnet/EntityFramework).</span></span>

## <a name="install"></a><span data-ttu-id="a9767-106">Install</span><span class="sxs-lookup"><span data-stu-id="a9767-106">Install</span></span>

<span data-ttu-id="a9767-107">安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="a9767-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="get-started"></a><span data-ttu-id="a9767-108">開始使用</span><span class="sxs-lookup"><span data-stu-id="a9767-108">Get Started</span></span>

<span data-ttu-id="a9767-109">下列資源將協助您開始使用此提供者。</span><span class="sxs-lookup"><span data-stu-id="a9767-109">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="a9767-110">使用 InMemory 測試</span><span class="sxs-lookup"><span data-stu-id="a9767-110">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)

* [<span data-ttu-id="a9767-111">UnicornStore 範例應用程式測試</span><span class="sxs-lookup"><span data-stu-id="a9767-111">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="a9767-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="a9767-112">Supported Database Engines</span></span>

* <span data-ttu-id="a9767-113">內建記憶體中資料庫 (僅供測試用途所設計)</span><span class="sxs-lookup"><span data-stu-id="a9767-113">Built-in in-memory database (designed for testing purposes only)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="a9767-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="a9767-114">Supported Platforms</span></span>

* <span data-ttu-id="a9767-115">.NET Framework (4.5.1 和更新版本)</span><span class="sxs-lookup"><span data-stu-id="a9767-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="a9767-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9767-116">.NET Core</span></span>

* <span data-ttu-id="a9767-117">Mono (4.2.0 和更新版本)</span><span class="sxs-lookup"><span data-stu-id="a9767-117">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="a9767-118">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="a9767-118">Universal Windows Platform</span></span>
