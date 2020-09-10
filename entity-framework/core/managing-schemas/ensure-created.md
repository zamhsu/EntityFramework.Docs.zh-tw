---
title: 建立和卸載 Api-EF Core
description: 使用 Entity Framework Core 建立和卸載資料庫的 Api
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 25e7352269531e881e83e44ea90108f12d4dcbea
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619221"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="6be03-103">建立及卸除 API</span><span class="sxs-lookup"><span data-stu-id="6be03-103">Create and Drop APIs</span></span>

<span data-ttu-id="6be03-104">EnsureCreated 和 EnsureDeleted [方法可提供](xref:core/managing-schemas/migrations/index) 輕量替代方式來管理資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="6be03-104">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](xref:core/managing-schemas/migrations/index) for managing the database schema.</span></span> <span data-ttu-id="6be03-105">當資料是暫時性的，而且可以在架構變更時卸載時，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="6be03-105">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="6be03-106">例如，在原型設計、測試中或本機快取中。</span><span class="sxs-lookup"><span data-stu-id="6be03-106">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="6be03-107">有些提供者 (特別是非關聯式的提供者) 不支援遷移。</span><span class="sxs-lookup"><span data-stu-id="6be03-107">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="6be03-108">對於這些提供者來說，EnsureCreated 通常是初始化資料庫架構的最簡單方式。</span><span class="sxs-lookup"><span data-stu-id="6be03-108">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="6be03-109">EnsureCreated 和遷移無法順利搭配運作。</span><span class="sxs-lookup"><span data-stu-id="6be03-109">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="6be03-110">如果您使用的是遷移，請不要使用 EnsureCreated 來初始化架構。</span><span class="sxs-lookup"><span data-stu-id="6be03-110">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="6be03-111">從 EnsureCreated 轉換到遷移不是順暢的體驗。</span><span class="sxs-lookup"><span data-stu-id="6be03-111">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="6be03-112">若要這麼做，最簡單的方式是卸載資料庫，並使用遷移重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="6be03-112">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="6be03-113">如果您預期未來會使用遷移，最好是從遷移開始，而不是使用 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="6be03-113">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="6be03-114">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="6be03-114">EnsureDeleted</span></span>

<span data-ttu-id="6be03-115">EnsureDeleted 方法會卸載資料庫（如果存在的話）。</span><span class="sxs-lookup"><span data-stu-id="6be03-115">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="6be03-116">如果您沒有適當的許可權，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="6be03-116">If you don't have the appropriate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="6be03-117">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="6be03-117">EnsureCreated</span></span>

<span data-ttu-id="6be03-118">如果資料庫不存在，EnsureCreated 會建立資料庫，並將資料庫架構初始化。</span><span class="sxs-lookup"><span data-stu-id="6be03-118">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="6be03-119">如果有任何資料表 (包括) 其他 DbCoNtext 類別的資料表，則不會初始化架構。</span><span class="sxs-lookup"><span data-stu-id="6be03-119">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="6be03-120">您也可以使用這些方法的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="6be03-120">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="6be03-121">SQL 指令碼</span><span class="sxs-lookup"><span data-stu-id="6be03-121">SQL Script</span></span>

<span data-ttu-id="6be03-122">若要取得 EnsureCreated 所使用的 SQL，您可以使用 GenerateCreateScript 方法。</span><span class="sxs-lookup"><span data-stu-id="6be03-122">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="6be03-123">多個 DbCoNtext 類別</span><span class="sxs-lookup"><span data-stu-id="6be03-123">Multiple DbContext classes</span></span>

<span data-ttu-id="6be03-124">只有當資料庫中沒有任何資料表時，EnsureCreated 才適用。</span><span class="sxs-lookup"><span data-stu-id="6be03-124">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="6be03-125">如有需要，您可以撰寫自己的檢查，以查看架構是否需要初始化，並使用基礎 Irelationaldatabasecreator.hastables 服務來初始化架構。</span><span class="sxs-lookup"><span data-stu-id="6be03-125">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
