---
title: 有效率的更新-EF Core
description: 使用 Entity Framework Core 有效率更新的效能指南
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: f8e222991af52cd7cae6089e95ad6634b6b949f8
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543129"
---
# <a name="efficient-updating"></a><span data-ttu-id="9b98d-103">有效率的更新</span><span class="sxs-lookup"><span data-stu-id="9b98d-103">Efficient Updating</span></span>

## <a name="batching"></a><span data-ttu-id="9b98d-104">批次處理</span><span class="sxs-lookup"><span data-stu-id="9b98d-104">Batching</span></span>

<span data-ttu-id="9b98d-105">EF Core 藉由在單一往返中自動將所有更新批次處理在一起，有助於將往返次數降至最低。</span><span class="sxs-lookup"><span data-stu-id="9b98d-105">EF Core helps minimize roundtrips by automatically batching together all updates in a single roundtrip.</span></span> <span data-ttu-id="9b98d-106">請考慮下列事項：</span><span class="sxs-lookup"><span data-stu-id="9b98d-106">Consider the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

<span data-ttu-id="9b98d-107">上述內容會從資料庫載入 blog、變更其 URL，然後新增兩個新的 blog;若要套用此項，請將兩個 SQL INSERT 語句和一個 UPDATE 語句傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="9b98d-107">The above loads a blog from the database, changes its URL, and then adds two new blogs; to apply this, two SQL INSERT statements and one UPDATE statement are sent to the database.</span></span> <span data-ttu-id="9b98d-108">它不會逐一傳送，因為加入了 Blog 實例，EF Core 在內部追蹤這些變更，並在呼叫時以單一往返方式執行這些變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="9b98d-108">Rather than sending them one by one, as Blog instances are added, EF Core tracks these changes internally, and executes them in a single roundtrip when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="9b98d-109">EF 在單一往返中批次處理的語句數目，取決於所使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="9b98d-109">The number of statements that EF batches in a single roundtrip depends on the database provider being used.</span></span> <span data-ttu-id="9b98d-110">例如，效能分析顯示在牽涉到4個以上的語句時，對 SQL Server 來說效率通常較低。</span><span class="sxs-lookup"><span data-stu-id="9b98d-110">For example, performance analysis has shown batching to be generally less efficient for SQL Server when less than 4 statements are involved.</span></span> <span data-ttu-id="9b98d-111">同樣地，在 SQL Server 的40語句前後，批次處理的優點也會降低，因此 EF Core 預設只會在單一批次中執行最多42語句，並在個別的往返中執行其他語句。</span><span class="sxs-lookup"><span data-stu-id="9b98d-111">Similarly, the benefits of batching degrade after around 40 statements for SQL Server, so EF Core will by default only execute up to 42 statements in a single batch, and execute additional statements in separate roundtrips.</span></span>

<span data-ttu-id="9b98d-112">使用者也可以調整這些閾值來達到可能較高的效能，但在修改這些閾值之前，請先仔細檢驗：</span><span class="sxs-lookup"><span data-stu-id="9b98d-112">Users can also tweak these thresholds to achieve potentially higher performance - but benchmark carefully before modifying these:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a><span data-ttu-id="9b98d-113">大量更新</span><span class="sxs-lookup"><span data-stu-id="9b98d-113">Bulk updates</span></span>

<span data-ttu-id="9b98d-114">讓我們假設您想要讓所有員工都能獲得提升。</span><span class="sxs-lookup"><span data-stu-id="9b98d-114">Let's assume you want to give all your employees a raise.</span></span> <span data-ttu-id="9b98d-115">在 EF Core 中，這項工作的典型執行如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b98d-115">A typical implementation for this in EF Core would look like the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

<span data-ttu-id="9b98d-116">雖然這是完全有效的程式碼，但讓我們從效能的觀點來分析它的功用：</span><span class="sxs-lookup"><span data-stu-id="9b98d-116">While this is perfectly valid code, let's analyze what it does from a performance perspective:</span></span>

* <span data-ttu-id="9b98d-117">執行資料庫往返，以載入所有相關的員工;請注意，這會將所有員工的資料列資料帶入用戶端，即使只需要薪資也是如此。</span><span class="sxs-lookup"><span data-stu-id="9b98d-117">A database roundtrip is performed, to load all the relevant employees; note that this brings all the Employees' row data to the client, even if only the salary will be needed.</span></span>
* <span data-ttu-id="9b98d-118">EF Core 的變更追蹤會在載入實體時建立快照集，然後將這些快照集與實例進行比較，以找出變更了哪些屬性。</span><span class="sxs-lookup"><span data-stu-id="9b98d-118">EF Core's change tracking creates snapshots when loading the entities, and then compares those snapshots to the instances to find out which properties changed.</span></span>
* <span data-ttu-id="9b98d-119">執行第二個資料庫往返以儲存所有變更。</span><span class="sxs-lookup"><span data-stu-id="9b98d-119">A second database roundtrip is performed to save all the changes.</span></span> <span data-ttu-id="9b98d-120">雖然所有變更都是在單次往返中完成，但是有了批次處理，但 EF Core 仍會傳送每個員工的 UPDATE 語句，這必須由資料庫執行。</span><span class="sxs-lookup"><span data-stu-id="9b98d-120">While all changes are done in a single roundtrip thanks to batching, EF Core still sends an UPDATE statement per employee, which must be executed by the database.</span></span>

<span data-ttu-id="9b98d-121">關係資料庫也支援 *大量更新*，因此可以將上述的單一 SQL 語句重寫為下列各項：</span><span class="sxs-lookup"><span data-stu-id="9b98d-121">Relational databases also support *bulk updates*, so the above could be rewritten as the following single SQL statement:</span></span>

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

<span data-ttu-id="9b98d-122">這會在單一往返中執行整個作業，而不需要將任何實際資料載入或傳送至資料庫，也不會使用 EF 的變更追蹤機制，這會造成額外的負荷。</span><span class="sxs-lookup"><span data-stu-id="9b98d-122">This performs the entire operation in a single roundtrip, without loading or sending any actual data to the database, and without making use of EF's change tracking machinery, which imposes an additional overhead.</span></span>

<span data-ttu-id="9b98d-123">可惜的是，EF 目前未提供用來執行大量更新的 Api。</span><span class="sxs-lookup"><span data-stu-id="9b98d-123">Unfortunately, EF doesn't currently provide APIs for performing bulk updates.</span></span> <span data-ttu-id="9b98d-124">在引入這些之前，您可以使用原始 SQL 來執行效能敏感性的作業：</span><span class="sxs-lookup"><span data-stu-id="9b98d-124">Until these are introduced, you can use raw SQL to perform the operation where performance is sensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
