---
title: 建立和卸除 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/7/2018
ms.openlocfilehash: 40d9e3aa0aba1bf2bc341f01dd815ed7cb7b48fa
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688625"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="7eed4-102">建立和卸除 Api</span><span class="sxs-lookup"><span data-stu-id="7eed4-102">Create and Drop APIs</span></span>

<span data-ttu-id="7eed4-103">EnsureCreated 和 EnsureDeleted 方法提供的輕量型替代品[移轉](migrations/index.md)管理資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="7eed4-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="7eed4-104">當資料是暫時性的且可以卸除結構描述變更時，這些方法是在案例中有用的。</span><span class="sxs-lookup"><span data-stu-id="7eed4-104">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="7eed4-105">比方說在原型設計，在測試中，或用於本機快取。</span><span class="sxs-lookup"><span data-stu-id="7eed4-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="7eed4-106">某些提供者 （尤其是非關聯式的） 不支援移轉。</span><span class="sxs-lookup"><span data-stu-id="7eed4-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="7eed4-107">對於這些提供者，EnsureCreated 通常是最簡單的方式，來初始化資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="7eed4-107">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="7eed4-108">EnsureCreated 的移轉，以及不合作無間。</span><span class="sxs-lookup"><span data-stu-id="7eed4-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="7eed4-109">如果您使用移轉，請勿使用 EnsureCreated 來初始化結構描述。</span><span class="sxs-lookup"><span data-stu-id="7eed4-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="7eed4-110">從 EnsureCreated 轉換到移轉不是順暢的體驗。</span><span class="sxs-lookup"><span data-stu-id="7eed4-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="7eed4-111">簡單的方法是卸除資料庫，並重新建立它使用移轉。</span><span class="sxs-lookup"><span data-stu-id="7eed4-111">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="7eed4-112">如果您預期在未來使用移轉，最好是剛開始移轉，而不是使用 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="7eed4-112">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="7eed4-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="7eed4-113">EnsureDeleted</span></span>

<span data-ttu-id="7eed4-114">若有的話，EnsureDeleted 方法將會卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="7eed4-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="7eed4-115">如果您沒有適當的權限，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7eed4-115">If you don't have the appropriate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="7eed4-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="7eed4-116">EnsureCreated</span></span>

<span data-ttu-id="7eed4-117">EnsureCreated 會建立資料庫，如果它不存在，並初始化資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="7eed4-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="7eed4-118">如果有任何資料表 （包括資料表的另一個 DbContext 類別），結構描述不會初始化。</span><span class="sxs-lookup"><span data-stu-id="7eed4-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="7eed4-119">這些方法的非同步版本也會提供。</span><span class="sxs-lookup"><span data-stu-id="7eed4-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="7eed4-120">SQL 指令碼</span><span class="sxs-lookup"><span data-stu-id="7eed4-120">SQL Script</span></span>

<span data-ttu-id="7eed4-121">若要取得 EnsureCreated 所使用的 SQL，您可以使用 GenerateCreateScript 方法。</span><span class="sxs-lookup"><span data-stu-id="7eed4-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="7eed4-122">多個 DbContext 類別</span><span class="sxs-lookup"><span data-stu-id="7eed4-122">Multiple DbContext classes</span></span>

<span data-ttu-id="7eed4-123">在資料庫中有任何資料表時，僅適用於 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="7eed4-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="7eed4-124">如有需要您可以撰寫您自己的檢查，以查看結構描述是否需要初始化，並使用基礎 IRelationalDatabaseCreator 服務來初始化結構描述。</span><span class="sxs-lookup"><span data-stu-id="7eed4-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
