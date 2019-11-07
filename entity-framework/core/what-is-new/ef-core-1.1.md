---
title: EF Core 1.1 中的新增功能 - EF Core
author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: d582712ed62443318f4b9e209511fb2a557d667e
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656004"
---
# <a name="new-features-in-ef-core-11"></a><span data-ttu-id="d9483-102">EF Core 1.1 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="d9483-102">New features in EF Core 1.1</span></span>

## <a name="modeling"></a><span data-ttu-id="d9483-103">模型化</span><span class="sxs-lookup"><span data-stu-id="d9483-103">Modeling</span></span>

### <a name="field-mapping"></a><span data-ttu-id="d9483-104">欄位對應</span><span class="sxs-lookup"><span data-stu-id="d9483-104">Field mapping</span></span>

<span data-ttu-id="d9483-105">可讓您設定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="d9483-105">Allows you to configure a backing field for a property.</span></span> <span data-ttu-id="d9483-106">對於唯讀屬性或具有 Get/Set 方法而非屬性的資料而言，這非常實用。</span><span class="sxs-lookup"><span data-stu-id="d9483-106">This can be useful for read-only properties, or data that has Get/Set methods rather than a property.</span></span>

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a><span data-ttu-id="d9483-107">對應到 SQL Server 中的記憶體最佳化資料表</span><span class="sxs-lookup"><span data-stu-id="d9483-107">Mapping to Memory-Optimized Tables in SQL Server</span></span>

<span data-ttu-id="d9483-108">您可以將實體所對應的資料表指定為記憶體最佳化。</span><span class="sxs-lookup"><span data-stu-id="d9483-108">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="d9483-109">根據您的模型使用 EF Core 來建立及維護資料庫 (透過移轉或 `Database.EnsureCreated()`)，將為這些實體建立記憶體最佳化資料表。</span><span class="sxs-lookup"><span data-stu-id="d9483-109">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="d9483-110">Change tracking</span><span class="sxs-lookup"><span data-stu-id="d9483-110">Change tracking</span></span>

### <a name="additional-change-tracking-apis-from-ef6"></a><span data-ttu-id="d9483-111">來自 EF6 的其他變更追蹤 API</span><span class="sxs-lookup"><span data-stu-id="d9483-111">Additional change tracking APIs from EF6</span></span>

<span data-ttu-id="d9483-112">例如 `Reload`、`GetModifiedProperties`、`GetDatabaseValues` 等。</span><span class="sxs-lookup"><span data-stu-id="d9483-112">Such as `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span></span>

## <a name="query"></a><span data-ttu-id="d9483-113">查詢</span><span class="sxs-lookup"><span data-stu-id="d9483-113">Query</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="d9483-114">明確式載入</span><span class="sxs-lookup"><span data-stu-id="d9483-114">Explicit Loading</span></span>

<span data-ttu-id="d9483-115">可讓您觸發先前從資料庫載入之實體上瀏覽屬性的母體擴展。</span><span class="sxs-lookup"><span data-stu-id="d9483-115">Allows you to trigger population of a navigation property on an entity that was previously loaded from the database.</span></span>

### <a name="dbsetfind"></a><span data-ttu-id="d9483-116">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="d9483-116">DbSet.Find</span></span>

<span data-ttu-id="d9483-117">提供根據實體主索引鍵值擷取實體的簡單方法。</span><span class="sxs-lookup"><span data-stu-id="d9483-117">Provides an easy way to fetch an entity based on its primary key value.</span></span>

## <a name="other"></a><span data-ttu-id="d9483-118">其他</span><span class="sxs-lookup"><span data-stu-id="d9483-118">Other</span></span>

### <a name="connection-resiliency"></a><span data-ttu-id="d9483-119">連線恢復功能</span><span class="sxs-lookup"><span data-stu-id="d9483-119">Connection resiliency</span></span>

<span data-ttu-id="d9483-120">自動重試失敗的資料庫命令。</span><span class="sxs-lookup"><span data-stu-id="d9483-120">Automatically retries failed database commands.</span></span> <span data-ttu-id="d9483-121">連線到其中的暫時性失敗為常見情況的 SQL Azure 時，這非常實用。</span><span class="sxs-lookup"><span data-stu-id="d9483-121">This is especially useful when connection to SQL Azure, where transient failures are common.</span></span>

### <a name="simplified-service-replacement"></a><span data-ttu-id="d9483-122">簡化的服務取代</span><span class="sxs-lookup"><span data-stu-id="d9483-122">Simplified service replacement</span></span>

<span data-ttu-id="d9483-123">讓您輕鬆取代 EF 所使用的服務。</span><span class="sxs-lookup"><span data-stu-id="d9483-123">Makes it easier to replace internal services that EF uses.</span></span>
