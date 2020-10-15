---
title: Microsoft SQL Server 資料庫提供者-索引-EF Core
description: Entity Framework Core SQL Server 提供者特有的索引功能
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: a01ab3d12f5bf5f05f0925c93d90c0ee40fe977c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061862"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="ebf97-103">Entity Framework Core SQL Server 提供者特有的索引功能</span><span class="sxs-lookup"><span data-stu-id="ebf97-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="ebf97-104">此頁面詳細說明 SQL Server 提供者特定的索引設定選項。</span><span class="sxs-lookup"><span data-stu-id="ebf97-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="ebf97-105">叢集</span><span class="sxs-lookup"><span data-stu-id="ebf97-105">Clustering</span></span>

<span data-ttu-id="ebf97-106">叢集索引將資料表或檢視中的資料列依其索引鍵值排序與儲存。</span><span class="sxs-lookup"><span data-stu-id="ebf97-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="ebf97-107">為您的資料表建立正確的叢集索引，可以大幅提升查詢的速度，因為資料已經以最佳順序配置。</span><span class="sxs-lookup"><span data-stu-id="ebf97-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="ebf97-108">因為資料列本身只能以一種順序排序，所以每個資料表只能有一個叢集索引。</span><span class="sxs-lookup"><span data-stu-id="ebf97-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="ebf97-109">如需詳細資訊，請參閱 [SQL Server 叢集和非叢集索引的檔](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)集。</span><span class="sxs-lookup"><span data-stu-id="ebf97-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="ebf97-110">依預設，資料表的主鍵資料行是由叢集索引隱含支援，而其他所有索引則為非叢集。</span><span class="sxs-lookup"><span data-stu-id="ebf97-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="ebf97-111">您可以設定要叢集化的索引或索引鍵，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ebf97-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

## <a name="fill-factor"></a><span data-ttu-id="ebf97-112">填滿因數</span><span class="sxs-lookup"><span data-stu-id="ebf97-112">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="ebf97-113">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="ebf97-113">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="ebf97-114">索引填滿因數選項是為了微調索引資料儲存和效能而提供。</span><span class="sxs-lookup"><span data-stu-id="ebf97-114">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="ebf97-115">如需詳細資訊，請參閱 [關於填滿因數的 SQL Server 檔](/sql/relational-databases/indexes/specify-fill-factor-for-an-index)。</span><span class="sxs-lookup"><span data-stu-id="ebf97-115">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="ebf97-116">您可以設定索引的填滿因數，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ebf97-116">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="ebf97-117">線上建立</span><span class="sxs-lookup"><span data-stu-id="ebf97-117">Online creation</span></span>

<span data-ttu-id="ebf97-118">ONLINE 選項允許在索引建立期間，並行使用者存取基礎資料表或叢集索引資料以及任何相關聯的非叢集索引，讓使用者可以繼續更新和查詢基礎資料。</span><span class="sxs-lookup"><span data-stu-id="ebf97-118">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="ebf97-119">當您離線執行資料定義語言 (DDL) 作業 (例如建立或重建叢集索引) 時，這些作業會保有基礎資料和關聯索引的獨佔鎖定。</span><span class="sxs-lookup"><span data-stu-id="ebf97-119">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="ebf97-120">如需詳細資訊，請參閱 [線上索引選項的 SQL Server 檔](/sql/relational-databases/indexes/perform-index-operations-online)。</span><span class="sxs-lookup"><span data-stu-id="ebf97-120">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="ebf97-121">您可以使用 ONLINE 選項來設定索引，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ebf97-121">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
