---
title: 建立和卸載 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 32ac6cd043df73cd041780ec4c8805675adc5ab1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416867"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="22942-102">建立及卸除 API</span><span class="sxs-lookup"><span data-stu-id="22942-102">Create and Drop APIs</span></span>

<span data-ttu-id="22942-103">EnsureCreated 和 EnsureDeleted 方法提供了輕量替代方案，可用於管理資料庫架構的[遷移](migrations/index.md)。</span><span class="sxs-lookup"><span data-stu-id="22942-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="22942-104">當資料是暫時性的，而且可以在架構變更時卸載，這些方法就很有用。</span><span class="sxs-lookup"><span data-stu-id="22942-104">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="22942-105">例如，在原型設計期間、測試中，或用於本機快取。</span><span class="sxs-lookup"><span data-stu-id="22942-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="22942-106">某些提供者（尤其是非關聯式的）不支援遷移。</span><span class="sxs-lookup"><span data-stu-id="22942-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="22942-107">對於這些提供者來說，EnsureCreated 通常是初始化資料庫架構的最簡單方式。</span><span class="sxs-lookup"><span data-stu-id="22942-107">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="22942-108">EnsureCreated 和遷移無法妥善搭配運作。</span><span class="sxs-lookup"><span data-stu-id="22942-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="22942-109">如果您使用的是「遷移」，請勿使用 EnsureCreated 來初始化架構。</span><span class="sxs-lookup"><span data-stu-id="22942-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="22942-110">從 EnsureCreated 轉換到遷移並不是順暢的體驗。</span><span class="sxs-lookup"><span data-stu-id="22942-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="22942-111">若要這麼做，最簡單的方法是卸載資料庫，然後使用遷移來重新建立它。</span><span class="sxs-lookup"><span data-stu-id="22942-111">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="22942-112">如果您未來想要使用遷移，最好只開始進行遷移，而不是使用 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="22942-112">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="22942-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="22942-113">EnsureDeleted</span></span>

<span data-ttu-id="22942-114">EnsureDeleted 方法會卸載資料庫（如果有的話）。</span><span class="sxs-lookup"><span data-stu-id="22942-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="22942-115">如果您沒有適當的許可權，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="22942-115">If you don't have the appropriate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="22942-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="22942-116">EnsureCreated</span></span>

<span data-ttu-id="22942-117">EnsureCreated 將會建立資料庫（如果不存在），並初始化資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="22942-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="22942-118">如果有任何資料表存在（包括另一個 DbCoNtext 類別的資料表），則不會初始化架構。</span><span class="sxs-lookup"><span data-stu-id="22942-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="22942-119">這些方法的非同步版本也可供使用。</span><span class="sxs-lookup"><span data-stu-id="22942-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="22942-120">SQL 指令碼</span><span class="sxs-lookup"><span data-stu-id="22942-120">SQL Script</span></span>

<span data-ttu-id="22942-121">若要取得 EnsureCreated 所使用的 SQL，您可以使用 GenerateCreateScript 方法。</span><span class="sxs-lookup"><span data-stu-id="22942-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="22942-122">多個 DbCoNtext 類別</span><span class="sxs-lookup"><span data-stu-id="22942-122">Multiple DbContext classes</span></span>

<span data-ttu-id="22942-123">只有在資料庫中沒有任何資料表時，EnsureCreated 才適用。</span><span class="sxs-lookup"><span data-stu-id="22942-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="22942-124">如有需要，您可以撰寫自己的檢查，以查看架構是否需要初始化，並使用基礎 IRelationalDatabaseCreator 服務來初始化架構。</span><span class="sxs-lookup"><span data-stu-id="22942-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
