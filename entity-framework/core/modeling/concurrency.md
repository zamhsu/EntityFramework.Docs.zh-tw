---
title: "並行語彙基元-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
ms.technology: entity-framework-core
uid: core/modeling/concurrency
ms.openlocfilehash: 6574a9098d38c4aa525ffb4896adb01082420b5f
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
---
# <a name="concurrency-tokens"></a><span data-ttu-id="f9461-102">並行語彙基元</span><span class="sxs-lookup"><span data-stu-id="f9461-102">Concurrency Tokens</span></span>

<span data-ttu-id="f9461-103">如果屬性設定為並行語彙基元 EF 會檢查沒有其他使用者在儲存變更至該記錄時，已修改該資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="f9461-103">If a property is configured as a concurrency token then EF will check that no other user has modified that value in the database when saving changes to that record.</span></span> <span data-ttu-id="f9461-104">EF 使用開放式並行存取模式，這表示它會假設的值未經變更，然後再次嘗試儲存資料，但它找到的值變更時，會擲回。</span><span class="sxs-lookup"><span data-stu-id="f9461-104">EF uses an optimistic concurrency pattern, meaning it will assume the value has not changed and try to save the data, but throw if it finds the value has been changed.</span></span>

<span data-ttu-id="f9461-105">比方說，我們可能想設定`LastName`上`Person`是並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="f9461-105">For example we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="f9461-106">這表示，如果某個使用者嘗試儲存的某些變更`Person`，但另一位使用者已變更`LastName`則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f9461-106">This means that if one user tries to save some changes to a `Person`, but another user has changed the `LastName` then an exception will be thrown.</span></span> <span data-ttu-id="f9461-107">這可能是需要這樣做，使您的應用程式可以提示使用者，以確保此記錄仍代表相同的實際人員之前將其變更。</span><span class="sxs-lookup"><span data-stu-id="f9461-107">This may be desirable so that your application can prompt the user to ensure this record still represents the same actual person before saving their changes.</span></span>

> [!NOTE]
> <span data-ttu-id="f9461-108">此頁面說明如何設定並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="f9461-108">This page documents how to configure concurrency tokens.</span></span> <span data-ttu-id="f9461-109">請參閱[處理並行](../saving/concurrency.md)如需如何在您的應用程式中使用開放式並行存取的範例。</span><span class="sxs-lookup"><span data-stu-id="f9461-109">See [Handling Concurrency](../saving/concurrency.md) for examples of how to use optimistic concurrency in your application.</span></span>

## <a name="how-concurrency-tokens-work-in-ef"></a><span data-ttu-id="f9461-110">並行語彙基元在 EF 中的運作方式</span><span class="sxs-lookup"><span data-stu-id="f9461-110">How concurrency tokens work in EF</span></span>

<span data-ttu-id="f9461-111">資料存放區可以檢查任何記錄正在更新或刪除仍有並行語彙基元內容原本從資料庫載入資料時所指定的相同值來強制執行並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="f9461-111">Data stores can enforce concurrency tokens by checking that any record being updated or deleted still has the same value for the concurrency token that was assigned when the context originally loaded the data from the database.</span></span>

<span data-ttu-id="f9461-112">例如，關聯式資料庫來達成此目的包括在並行語彙基元`WHERE`的任何子句`UPDATE`或`DELETE`命令和檢查已受影響的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="f9461-112">For example, relational databases achieve this by including the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` commands and checking the number of rows that were affected.</span></span> <span data-ttu-id="f9461-113">如果並行語彙基元仍符合，則會更新一個資料列。</span><span class="sxs-lookup"><span data-stu-id="f9461-113">If the concurrency token still matches then one row will be updated.</span></span> <span data-ttu-id="f9461-114">如果資料庫中的值已變更，會不更新任何資料列。</span><span class="sxs-lookup"><span data-stu-id="f9461-114">If the value in the database has changed, then no rows are updated.</span></span>

```sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="conventions"></a><span data-ttu-id="f9461-115">慣例</span><span class="sxs-lookup"><span data-stu-id="f9461-115">Conventions</span></span>

<span data-ttu-id="f9461-116">依照慣例，屬性會永遠不會設定為並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="f9461-116">By convention, properties are never configured as concurrency tokens.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="f9461-117">資料註釋</span><span class="sxs-lookup"><span data-stu-id="f9461-117">Data Annotations</span></span>

<span data-ttu-id="f9461-118">您可以使用資料註解將屬性設定為並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="f9461-118">You can use the Data Annotations to configure a property as a concurrency token.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a><span data-ttu-id="f9461-119">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="f9461-119">Fluent API</span></span>

<span data-ttu-id="f9461-120">您可以使用 fluent 應用程式開發的應用程式開發介面，將屬性設定為並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="f9461-120">You can use the Fluent API to configure a property as a concurrency token.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a><span data-ttu-id="f9461-121">時間戳記/資料列版本</span><span class="sxs-lookup"><span data-stu-id="f9461-121">Timestamp/row version</span></span>

<span data-ttu-id="f9461-122">時間戳記是屬性，產生新的值是由資料庫每次插入或更新資料列。</span><span class="sxs-lookup"><span data-stu-id="f9461-122">A timestamp is a property where a new value is generated by the database every time a row is inserted or updated.</span></span> <span data-ttu-id="f9461-123">屬性也會被視為並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="f9461-123">The property is also treated as a concurrency token.</span></span> <span data-ttu-id="f9461-124">這可確保如果其他人已修改的資料列，您嘗試更新您查詢的資料後，就會收到例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f9461-124">This ensures you will get an exception if anyone else has modified a row that you are trying to update since you queried for the data.</span></span>

<span data-ttu-id="f9461-125">如何達成這會決定所使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="f9461-125">How this is achieved is up to the database provider being used.</span></span> <span data-ttu-id="f9461-126">SQL Server 的時間戳記通常用在*byte []*屬性，將會安裝為*ROWVERSION*資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="f9461-126">For SQL Server, timestamp is usually used on a *byte[]* property, which will be setup as a *ROWVERSION* column in the database.</span></span>

### <a name="conventions"></a><span data-ttu-id="f9461-127">慣例</span><span class="sxs-lookup"><span data-stu-id="f9461-127">Conventions</span></span>

<span data-ttu-id="f9461-128">依照慣例，屬性會永遠不會設定為時間戳記。</span><span class="sxs-lookup"><span data-stu-id="f9461-128">By convention, properties are never configured as timestamps.</span></span>

### <a name="data-annotations"></a><span data-ttu-id="f9461-129">資料註釋</span><span class="sxs-lookup"><span data-stu-id="f9461-129">Data Annotations</span></span>

<span data-ttu-id="f9461-130">您可以使用資料註解屬性設定為時間戳記。</span><span class="sxs-lookup"><span data-stu-id="f9461-130">You can use Data Annotations to configure a property as a timestamp.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a><span data-ttu-id="f9461-131">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="f9461-131">Fluent API</span></span>

<span data-ttu-id="f9461-132">您可以使用 fluent 應用程式開發的應用程式開發介面的屬性設定為時間戳記。</span><span class="sxs-lookup"><span data-stu-id="f9461-132">You can use the Fluent API to configure a property as a timestamp.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
