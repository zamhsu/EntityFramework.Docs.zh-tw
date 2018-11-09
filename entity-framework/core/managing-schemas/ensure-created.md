---
title: 建立和卸除 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/10/2017
ms.openlocfilehash: 336f6fd655603a2474a58dfef377e121d9b04c3a
ms.sourcegitcommit: a088421ecac4f5dc5213208170490181ae2f5f0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285635"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="6480a-102">建立和卸除 Api</span><span class="sxs-lookup"><span data-stu-id="6480a-102">Create and Drop APIs</span></span>

<span data-ttu-id="6480a-103">EnsureCreated 和 EnsureDeleted 方法提供的輕量型替代品[移轉](migrations/index.md)管理資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="6480a-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="6480a-104">當資料是暫時性的且可以卸除結構描述變更時，這是在案例中有用的。</span><span class="sxs-lookup"><span data-stu-id="6480a-104">This is useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="6480a-105">比方說在原型設計，在測試中，或用於本機快取。</span><span class="sxs-lookup"><span data-stu-id="6480a-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="6480a-106">某些提供者 （尤其是非關聯式的） 不支援移轉。</span><span class="sxs-lookup"><span data-stu-id="6480a-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="6480a-107">對於這些 EnsureCreated 通常是最簡單的方式，來初始化資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="6480a-107">For these, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="6480a-108">EnsureCreated 的移轉，以及不合作無間。</span><span class="sxs-lookup"><span data-stu-id="6480a-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="6480a-109">如果您使用移轉，請勿使用 EnsureCreated 來初始化結構描述。</span><span class="sxs-lookup"><span data-stu-id="6480a-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="6480a-110">從 EnsureCreated 轉換到移轉不是順暢的體驗。</span><span class="sxs-lookup"><span data-stu-id="6480a-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="6480a-111">為了達成此目的的 simpelest 方式是卸除資料庫，並重新建立它使用移轉。</span><span class="sxs-lookup"><span data-stu-id="6480a-111">The simpelest way to achieve this is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="6480a-112">如果您預期在未來使用移轉，最好是剛開始移轉，而不是使用 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="6480a-112">If you anticipate using Migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="6480a-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="6480a-113">EnsureDeleted</span></span>

<span data-ttu-id="6480a-114">若有的話，EnsureDeleted 方法將會卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="6480a-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="6480a-115">如果您沒有適當權限，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="6480a-115">If you don't have the appropiate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="6480a-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="6480a-116">EnsureCreated</span></span>

<span data-ttu-id="6480a-117">EnsureCreated 會建立資料庫，如果它不存在，並初始化資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="6480a-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="6480a-118">如果有任何資料表 （包括資料表的另一個 DbContext 類別），結構描述不會初始化。</span><span class="sxs-lookup"><span data-stu-id="6480a-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="6480a-119">這些方法的非同步版本也會提供。</span><span class="sxs-lookup"><span data-stu-id="6480a-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="6480a-120">SQL 指令碼</span><span class="sxs-lookup"><span data-stu-id="6480a-120">SQL Script</span></span>

<span data-ttu-id="6480a-121">若要取得 EnsureCreated 所使用的 SQL，您可以使用 GenerateCreateScript 方法。</span><span class="sxs-lookup"><span data-stu-id="6480a-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="6480a-122">多個 DbContext 類別</span><span class="sxs-lookup"><span data-stu-id="6480a-122">Multiple DbContext classes</span></span>

<span data-ttu-id="6480a-123">在資料庫中有任何資料表時，僅適用於 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="6480a-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="6480a-124">如有需要您可以撰寫您自己的檢查，以查看結構描述是否需要初始化，並使用基礎 IRelationalDatabaseCreator 服務來初始化結構描述。</span><span class="sxs-lookup"><span data-stu-id="6480a-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
