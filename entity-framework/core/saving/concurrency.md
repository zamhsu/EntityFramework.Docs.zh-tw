---
title: "處理並行衝突 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: 288d9c6fced5ebbaa2c366248c68547502c3698e
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="1f022-102">處理並行存取衝突</span><span class="sxs-lookup"><span data-stu-id="1f022-102">Handling Concurrency Conflicts</span></span>

> [!NOTE]
> <span data-ttu-id="1f022-103">此頁面會記載在 EF 核心中的並行存取如何運作以及如何處理您的應用程式中的並行存取衝突。</span><span class="sxs-lookup"><span data-stu-id="1f022-103">This page documents how concurrency works in EF Core and how to handle concurrency conflicts in your application.</span></span> <span data-ttu-id="1f022-104">請參閱[並行語彙基元](xref:core/modeling/concurrency)如需有關如何設定您的模型中的並行語彙基元的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="1f022-104">See [Concurrency Tokens](xref:core/modeling/concurrency) for details on how to configure concurrency tokens in your model.</span></span>

> [!TIP]
> <span data-ttu-id="1f022-105">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="1f022-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) on GitHub.</span></span>

<span data-ttu-id="1f022-106">_資料庫並行_指的情況下，多個處理程序或使用者存取或變更資料庫中相同的資料在相同的時間。</span><span class="sxs-lookup"><span data-stu-id="1f022-106">_Database concurrency_ refers to situations in which multiple processes or users access or change the same data in a database at the same time.</span></span> <span data-ttu-id="1f022-107">_並行控制_指的是用來確保資料一致性的並行的變更出現在特定的機制。</span><span class="sxs-lookup"><span data-stu-id="1f022-107">_Concurrency control_ refers to specific mechanisms used to ensure data consistency in presence of concurrent changes.</span></span>

<span data-ttu-id="1f022-108">EF 核心實作_開放式並行存取控制_，也就是說，它會讓多個處理序或使用者進行變更，無同步處理的額外負荷，或鎖定。</span><span class="sxs-lookup"><span data-stu-id="1f022-108">EF Core implements _optimistic concurrency control_, meaning that it will let multiple processes or users make changes independently without the overhead of synchronization or locking.</span></span> <span data-ttu-id="1f022-109">在理想的情況下，這些變更不會影響彼此，因此將無法成功。</span><span class="sxs-lookup"><span data-stu-id="1f022-109">In the ideal situation, these changes will not interfere with each other and therefore will be able to succeed.</span></span> <span data-ttu-id="1f022-110">在最差的情況下，兩個或多個處理程序會嘗試進行衝突的變更，只有其中應該會成功。</span><span class="sxs-lookup"><span data-stu-id="1f022-110">In the worst case scenario, two or more processes will attempt to make conflicting changes, and only one of them should succeed.</span></span>

## <a name="how-concurrency-control-works-in-ef-core"></a><span data-ttu-id="1f022-111">在 EF 核心中的並行存取控制如何運作</span><span class="sxs-lookup"><span data-stu-id="1f022-111">How concurrency control works in EF Core</span></span>

<span data-ttu-id="1f022-112">屬性設定為並行語彙基元可用於實作開放式並行存取控制： 每當執行 update 或 delete 作業期間`SaveChanges`，會比對原始資料庫上的並行語彙基元值EF 核心所讀取的值。</span><span class="sxs-lookup"><span data-stu-id="1f022-112">Properties configured as concurrency tokens are used to implement optimistic concurrency control: whenever an update or delete operation is performed during `SaveChanges`, the value of the concurrency token on the database is compared against the original value read by EF Core.</span></span>

- <span data-ttu-id="1f022-113">如果值相符，才能完成此作業。</span><span class="sxs-lookup"><span data-stu-id="1f022-113">If the values match, the operation can complete.</span></span>
- <span data-ttu-id="1f022-114">如果值不相符，EF 核心會假設其他使用者已執行衝突的作業，並中止目前交易。</span><span class="sxs-lookup"><span data-stu-id="1f022-114">If the values do not match, EF Core assumes that another user has performed a conflicting operation and aborts the current transaction.</span></span>

<span data-ttu-id="1f022-115">這種情況時另一位使用者已執行與目前作業衝突的作業稱為_並行衝突_。</span><span class="sxs-lookup"><span data-stu-id="1f022-115">The situation when another user has performed an operation that conflicts with the current operation is known as _concurrency conflict_.</span></span>

<span data-ttu-id="1f022-116">資料庫提供者會負責實作的並行語彙基元值比較。</span><span class="sxs-lookup"><span data-stu-id="1f022-116">Database providers are responsible for implementing the comparison of concurrency token values.</span></span>

<span data-ttu-id="1f022-117">關聯式資料庫在 EF 核心包含值的並行語彙基元中的核取`WHERE`的任何子句`UPDATE`或`DELETE`陳述式。</span><span class="sxs-lookup"><span data-stu-id="1f022-117">On relational databases EF Core includes a check for the value of the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` statements.</span></span> <span data-ttu-id="1f022-118">在執行之後的陳述式，EF 核心會讀取受影響的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="1f022-118">After executing the statements, EF Core reads the number of rows that were affected.</span></span>

<span data-ttu-id="1f022-119">如果沒有資料列受到影響，偵測到並行衝突時，並 EF 核心擲回`DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="1f022-119">If no rows are affected, a concurrency conflict is detected, and EF Core throws `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="1f022-120">例如，我們可能會想要設定`LastName`上`Person`是並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="1f022-120">For example, we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="1f022-121">則人員的任何更新作業將會包含在並行存取檢查`WHERE`子句：</span><span class="sxs-lookup"><span data-stu-id="1f022-121">Then any update operation on Person will include the concurrency check in the `WHERE` clause:</span></span>

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a><span data-ttu-id="1f022-122">解決並行衝突</span><span class="sxs-lookup"><span data-stu-id="1f022-122">Resolving concurrency conflicts</span></span>

<span data-ttu-id="1f022-123">繼續上述範例中，如果某個使用者嘗試儲存的某些變更`Person`，但另一位使用者已在變更`LastName`將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1f022-123">Continuing with the previous example, if one user tries to save some changes to a `Person`, but another user has already changed the `LastName` the an exception will be thrown.</span></span>

<span data-ttu-id="1f022-124">此時，應用程式可能只是通知使用者更新但不會成功，因為有衝突的變更，並且上移動。</span><span class="sxs-lookup"><span data-stu-id="1f022-124">At this point, the application could simply inform the user that the update was not successful due to conflicting changes and move on.</span></span> <span data-ttu-id="1f022-125">但是，它可能會想要提示使用者以確保此記錄仍代表相同的實際人員，並再次嘗試操作。</span><span class="sxs-lookup"><span data-stu-id="1f022-125">But it may be desirable to prompt the user to ensure this record still represents the same actual person and to retry the operation.</span></span>

<span data-ttu-id="1f022-126">此程序是範例_解決並行衝突_。</span><span class="sxs-lookup"><span data-stu-id="1f022-126">This process is an example of _resolving a concurrency conflict_.</span></span>

<span data-ttu-id="1f022-127">解決並行衝突包括合併暫止的變更，從目前`DbContext`與資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="1f022-127">Resolving a concurrency conflict involves merging the pending changes from the current `DbContext` with the values in the database.</span></span> <span data-ttu-id="1f022-128">合併哪些值會根據應用程式而異，而且可能指示由使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="1f022-128">What values get merged will vary based on the application and may be directed by user input.</span></span>

<span data-ttu-id="1f022-129">**有三組可用來協助解決並行衝突的值：**</span><span class="sxs-lookup"><span data-stu-id="1f022-129">**There are three sets of values available to help resolve a concurrency conflict:**</span></span>

* <span data-ttu-id="1f022-130">**目前的值**是應用程式嘗試寫入資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="1f022-130">**Current values** are the values that the application was attempting to write to the database.</span></span>

* <span data-ttu-id="1f022-131">**原始值**原先擷取從資料庫中，進行任何編輯之前的值。</span><span class="sxs-lookup"><span data-stu-id="1f022-131">**Original values** are the values that were originally retrieved from the database, before any edits were made.</span></span>

* <span data-ttu-id="1f022-132">**資料庫值**是目前資料庫中儲存的值。</span><span class="sxs-lookup"><span data-stu-id="1f022-132">**Database values** are the values currently stored in the database.</span></span>

<span data-ttu-id="1f022-133">處理並行存取衝突的一般方法是：</span><span class="sxs-lookup"><span data-stu-id="1f022-133">The general approach to handle a concurrency conflicts is:</span></span>

1. <span data-ttu-id="1f022-134">攔截`DbUpdateConcurrencyException`期間`SaveChanges`。</span><span class="sxs-lookup"><span data-stu-id="1f022-134">Catch `DbUpdateConcurrencyException` during `SaveChanges`.</span></span>
2. <span data-ttu-id="1f022-135">使用`DbUpdateConcurrencyException.Entries`準備受影響之實體的一組新的變更。</span><span class="sxs-lookup"><span data-stu-id="1f022-135">Use `DbUpdateConcurrencyException.Entries` to prepare a new set of changes for the affected entities.</span></span>
3. <span data-ttu-id="1f022-136">重新整理以反映資料庫中的目前值的並行語彙基元的原始值。</span><span class="sxs-lookup"><span data-stu-id="1f022-136">Refresh the original values of the concurrency token to reflect the current values in the database.</span></span>
4. <span data-ttu-id="1f022-137">重試處理程序，直到不發生任何衝突。</span><span class="sxs-lookup"><span data-stu-id="1f022-137">Retry the process until no conflicts occur.</span></span>

<span data-ttu-id="1f022-138">在下列範例中，`Person.FirstName`和`Person.LastName`是安裝做為並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="1f022-138">In the following example, `Person.FirstName` and `Person.LastName` are setup as concurrency tokens.</span></span> <span data-ttu-id="1f022-139">沒有`// TODO:`納入應用程式特定的邏輯，以選擇要儲存的值的位置中的註解。</span><span class="sxs-lookup"><span data-stu-id="1f022-139">There is a `// TODO:` comment in the location where you include application specific logic to choose the value to be saved.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
