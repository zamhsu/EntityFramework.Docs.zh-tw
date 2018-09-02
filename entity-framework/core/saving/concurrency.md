---
title: 處理並行存取衝突 - EF Core
author: rowanmiller
ms.date: 03/03/2018
uid: core/saving/concurrency
ms.openlocfilehash: e050b17bfa31a4785161c700bc0355e83162b405
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993108"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="7b542-102">處理並行存取衝突</span><span class="sxs-lookup"><span data-stu-id="7b542-102">Handling Concurrency Conflicts</span></span>

> [!NOTE]
> <span data-ttu-id="7b542-103">本頁記載並行存取在 EF Core 中的運作方式，以及如何處理您應用程式中的並行存取衝突。</span><span class="sxs-lookup"><span data-stu-id="7b542-103">This page documents how concurrency works in EF Core and how to handle concurrency conflicts in your application.</span></span> <span data-ttu-id="7b542-104">如需有關如何在模型中設定並行存取語彙基元的詳細資料，請參閱[並行存取語彙基元](xref:core/modeling/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="7b542-104">See [Concurrency Tokens](xref:core/modeling/concurrency) for details on how to configure concurrency tokens in your model.</span></span>

> [!TIP]
> <span data-ttu-id="7b542-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="7b542-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) on GitHub.</span></span>

<span data-ttu-id="7b542-106">「資料庫並行存取」係指多個處理程序或使用者同時存取或變更資料庫中的相同資料。</span><span class="sxs-lookup"><span data-stu-id="7b542-106">_Database concurrency_ refers to situations in which multiple processes or users access or change the same data in a database at the same time.</span></span> <span data-ttu-id="7b542-107">「並行存取控制」係指在發生並行變更時用來確保資料一致性的特定機制。</span><span class="sxs-lookup"><span data-stu-id="7b542-107">_Concurrency control_ refers to specific mechanisms used to ensure data consistency in presence of concurrent changes.</span></span>

<span data-ttu-id="7b542-108">EF Core 實作「開放式並行存取控制」，意謂著它會讓多個處理程序或使用者獨立進行變更，而無同步處理或鎖定的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="7b542-108">EF Core implements _optimistic concurrency control_, meaning that it will let multiple processes or users make changes independently without the overhead of synchronization or locking.</span></span> <span data-ttu-id="7b542-109">在理想的情況下，這些變更不會互相影響，因此將能夠成功。</span><span class="sxs-lookup"><span data-stu-id="7b542-109">In the ideal situation, these changes will not interfere with each other and therefore will be able to succeed.</span></span> <span data-ttu-id="7b542-110">在最糟的情況下，會有兩個或更多個處理程序嘗試進行衝突變更，而只有其中一個應該會成功。</span><span class="sxs-lookup"><span data-stu-id="7b542-110">In the worst case scenario, two or more processes will attempt to make conflicting changes, and only one of them should succeed.</span></span>

## <a name="how-concurrency-control-works-in-ef-core"></a><span data-ttu-id="7b542-111">並行存取控制在 EF Core 中如何運作</span><span class="sxs-lookup"><span data-stu-id="7b542-111">How concurrency control works in EF Core</span></span>

<span data-ttu-id="7b542-112">設定為並行存取語彙基元的屬性會用來實作開放式並行存取控制：每當在 `SaveChanges` 期間執行更新或刪除作業時，資料庫上的並行存取語彙基元值都會與 EF Core 所讀取的原始值進行比較。</span><span class="sxs-lookup"><span data-stu-id="7b542-112">Properties configured as concurrency tokens are used to implement optimistic concurrency control: whenever an update or delete operation is performed during `SaveChanges`, the value of the concurrency token on the database is compared against the original value read by EF Core.</span></span>

- <span data-ttu-id="7b542-113">如果值相符，作業便能完成。</span><span class="sxs-lookup"><span data-stu-id="7b542-113">If the values match, the operation can complete.</span></span>
- <span data-ttu-id="7b542-114">如果值不相符，EF Core 就會假設另一位使用者已執行衝突作業，而將目前的交易中止。</span><span class="sxs-lookup"><span data-stu-id="7b542-114">If the values do not match, EF Core assumes that another user has performed a conflicting operation and aborts the current transaction.</span></span>

<span data-ttu-id="7b542-115">當另一位使用者已執行與目前作業衝突的作業時，此情況稱為「並行存取衝突」。</span><span class="sxs-lookup"><span data-stu-id="7b542-115">The situation when another user has performed an operation that conflicts with the current operation is known as _concurrency conflict_.</span></span>

<span data-ttu-id="7b542-116">資料庫提供者需負責實作並行存取語彙基元值的比較。</span><span class="sxs-lookup"><span data-stu-id="7b542-116">Database providers are responsible for implementing the comparison of concurrency token values.</span></span>

<span data-ttu-id="7b542-117">在關聯式資料庫上，EF Core 會檢查所有 `UPDATE` 或 `DELETE` 陳述式的 `WHERE` 子句中是否有並行存取語彙基元的值。</span><span class="sxs-lookup"><span data-stu-id="7b542-117">On relational databases EF Core includes a check for the value of the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` statements.</span></span> <span data-ttu-id="7b542-118">在執行這些陳述式之後，EF Core 會讀取受影響的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="7b542-118">After executing the statements, EF Core reads the number of rows that were affected.</span></span>

<span data-ttu-id="7b542-119">如果沒有任何資料列受影響，即表示偵測到並行存取衝突，而 EF Core 就會擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="7b542-119">If no rows are affected, a concurrency conflict is detected, and EF Core throws `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="7b542-120">例如，我們可以將 `Person` 上的 `LastName` 設定為並行存取語彙基元。</span><span class="sxs-lookup"><span data-stu-id="7b542-120">For example, we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="7b542-121">然後，在 Person 上進行的任何更新作業就會包含 `WHERE` 子句中的並行存取檢查：</span><span class="sxs-lookup"><span data-stu-id="7b542-121">Then any update operation on Person will include the concurrency check in the `WHERE` clause:</span></span>

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a><span data-ttu-id="7b542-122">解決並行存取衝突</span><span class="sxs-lookup"><span data-stu-id="7b542-122">Resolving concurrency conflicts</span></span>

<span data-ttu-id="7b542-123">接續先前的範例，如果一個使用者嘗試儲存對 `Person` 所做的一些變更，但另一個使用者已經變更 `LastName`，則系統將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7b542-123">Continuing with the previous example, if one user tries to save some changes to a `Person`, but another user has already changed the `LastName`, then an exception will be thrown.</span></span>

<span data-ttu-id="7b542-124">此時，應用程式可以直接通知使用者因變更發生衝突而導致更新失敗，然後繼續進行。</span><span class="sxs-lookup"><span data-stu-id="7b542-124">At this point, the application could simply inform the user that the update was not successful due to conflicting changes and move on.</span></span> <span data-ttu-id="7b542-125">但提示使用者確定此記錄仍代表相同的實際人員並重試作業，可能是較理想的做法。</span><span class="sxs-lookup"><span data-stu-id="7b542-125">But it may be desirable to prompt the user to ensure this record still represents the same actual person and to retry the operation.</span></span>

<span data-ttu-id="7b542-126">此程序是「解決並行存取衝突」的一個範例。</span><span class="sxs-lookup"><span data-stu-id="7b542-126">This process is an example of _resolving a concurrency conflict_.</span></span>

<span data-ttu-id="7b542-127">解決並行存取衝突牽涉到將來自目前 `DbContext` 的擱置中變更與資料庫中的值合併。</span><span class="sxs-lookup"><span data-stu-id="7b542-127">Resolving a concurrency conflict involves merging the pending changes from the current `DbContext` with the values in the database.</span></span> <span data-ttu-id="7b542-128">要合併的值將因應用程式而異，且可能由使用者輸入來指示。</span><span class="sxs-lookup"><span data-stu-id="7b542-128">What values get merged will vary based on the application and may be directed by user input.</span></span>

<span data-ttu-id="7b542-129">**有三組值可供協助解決並行存取衝突：**</span><span class="sxs-lookup"><span data-stu-id="7b542-129">**There are three sets of values available to help resolve a concurrency conflict:**</span></span>

* <span data-ttu-id="7b542-130">「目前值」係指應用程式嘗試寫入至資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="7b542-130">**Current values** are the values that the application was attempting to write to the database.</span></span>

* <span data-ttu-id="7b542-131">「原始值」係指在進行任何編輯之前，原先從資料庫擷取到的值。</span><span class="sxs-lookup"><span data-stu-id="7b542-131">**Original values** are the values that were originally retrieved from the database, before any edits were made.</span></span>

* <span data-ttu-id="7b542-132">「資料庫值」係指目前儲存在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="7b542-132">**Database values** are the values currently stored in the database.</span></span>

<span data-ttu-id="7b542-133">處理並行存取衝突的一般方法是：</span><span class="sxs-lookup"><span data-stu-id="7b542-133">The general approach to handle a concurrency conflicts is:</span></span>

1. <span data-ttu-id="7b542-134">在 `SaveChanges` 期間攔截 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="7b542-134">Catch `DbUpdateConcurrencyException` during `SaveChanges`.</span></span>
2. <span data-ttu-id="7b542-135">使用 `DbUpdateConcurrencyException.Entries` 為受影響的實體準備一組新的變更。</span><span class="sxs-lookup"><span data-stu-id="7b542-135">Use `DbUpdateConcurrencyException.Entries` to prepare a new set of changes for the affected entities.</span></span>
3. <span data-ttu-id="7b542-136">重新整理並行存取語彙基元的原始值以反映資料庫中的目前值。</span><span class="sxs-lookup"><span data-stu-id="7b542-136">Refresh the original values of the concurrency token to reflect the current values in the database.</span></span>
4. <span data-ttu-id="7b542-137">重試處理程序，直到沒有發生任何衝突為止。</span><span class="sxs-lookup"><span data-stu-id="7b542-137">Retry the process until no conflicts occur.</span></span>

<span data-ttu-id="7b542-138">在下列範例中，`Person.FirstName` 和 `Person.LastName` 被設定為並行存取語彙基元。</span><span class="sxs-lookup"><span data-stu-id="7b542-138">In the following example, `Person.FirstName` and `Person.LastName` are setup as concurrency tokens.</span></span> <span data-ttu-id="7b542-139">在您包含應用程式特定邏輯以選擇所要儲存值的位置中，有一個 `// TODO:` 註解。</span><span class="sxs-lookup"><span data-stu-id="7b542-139">There is a `// TODO:` comment in the location where you include application specific logic to choose the value to be saved.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
