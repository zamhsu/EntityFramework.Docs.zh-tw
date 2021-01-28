---
title: 查詢中具有 Null 值的比較
description: Entity Framework Core 如何在查詢中處理 null 比較的資訊
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983348"
---
# <a name="query-null-semantics"></a><span data-ttu-id="87635-103">查詢 null 語義</span><span class="sxs-lookup"><span data-stu-id="87635-103">Query null semantics</span></span>

## <a name="introduction"></a><span data-ttu-id="87635-104">簡介</span><span class="sxs-lookup"><span data-stu-id="87635-104">Introduction</span></span>

<span data-ttu-id="87635-105">SQL 資料庫會在3值邏輯上運作 (`true` 、 `false` 、 `null` 在執行比較時) ，而不是 c # 的布林邏輯。</span><span class="sxs-lookup"><span data-stu-id="87635-105">SQL databases operate on 3-valued logic (`true`, `false`, `null`) when performing comparisons, as opposed to the boolean logic of C#.</span></span> <span data-ttu-id="87635-106">將 LINQ 查詢轉譯為 SQL 時，EF Core 會針對查詢的某些元素引進額外的 null 檢查，藉以嘗試補償差異。</span><span class="sxs-lookup"><span data-stu-id="87635-106">When translating LINQ queries to SQL, EF Core tries to compensate for the difference by introducing additional null checks for some elements of the query.</span></span>
<span data-ttu-id="87635-107">為了說明這一點，讓我們定義下列實體：</span><span class="sxs-lookup"><span data-stu-id="87635-107">To illustrate this, let's define the following entity:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

<span data-ttu-id="87635-108">併發出數個查詢：</span><span class="sxs-lookup"><span data-stu-id="87635-108">and issue several queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

<span data-ttu-id="87635-109">前兩個查詢會產生簡單的比較。</span><span class="sxs-lookup"><span data-stu-id="87635-109">The first two queries produce simple comparisons.</span></span> <span data-ttu-id="87635-110">在第一個查詢中，這兩個數據行都是不可為 null 的，因此不需要 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="87635-110">In the first query, both columns are non-nullable so null checks are not needed.</span></span> <span data-ttu-id="87635-111">在第二個查詢中， `NullableInt` 可以包含 `null` （但 `Id` 不可為 null）; 相 `null` 較于非 null，則會產生 `null` 結果，而這會由作業篩選掉 `WHERE` 。</span><span class="sxs-lookup"><span data-stu-id="87635-111">In the second query, `NullableInt` could contain `null`, but `Id` is non-nullable; comparing `null` to non-null yields `null` as a result, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="87635-112">因此，不需要任何額外的條款。</span><span class="sxs-lookup"><span data-stu-id="87635-112">So no additional terms are needed either.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

<span data-ttu-id="87635-113">第三個查詢引進 null 檢查。</span><span class="sxs-lookup"><span data-stu-id="87635-113">The third query introduces a null check.</span></span> <span data-ttu-id="87635-114">當 `NullableInt` 是 `null` 所產生的比較時 `Id <> NullableInt` `null` ，會依作業篩選掉 `WHERE` 。</span><span class="sxs-lookup"><span data-stu-id="87635-114">When `NullableInt` is `null` the comparison `Id <> NullableInt` yields `null`, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="87635-115">不過，從布林值邏輯的角度來看，這種情況應該會在結果中傳回。</span><span class="sxs-lookup"><span data-stu-id="87635-115">However, from the boolean logic perspective this case should be returned as part of the result.</span></span> <span data-ttu-id="87635-116">因此 EF Core 新增必要的檢查，以確保這一點。</span><span class="sxs-lookup"><span data-stu-id="87635-116">Hence EF Core adds the necessary check to ensure that.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

<span data-ttu-id="87635-117">當兩個數據行都可為 null 時，查詢四和五會顯示模式。</span><span class="sxs-lookup"><span data-stu-id="87635-117">Queries four and five show the pattern when both columns are nullable.</span></span> <span data-ttu-id="87635-118">值得注意的是，此作業會 `<>` 產生比作業更複雜的 (以及可能較慢的) 查詢 `==` 。</span><span class="sxs-lookup"><span data-stu-id="87635-118">It's worth noting that the `<>` operation produces more complicated (and potentially slower) query than the `==` operation.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a><span data-ttu-id="87635-119">函數中可為 null 值的處理方式</span><span class="sxs-lookup"><span data-stu-id="87635-119">Treatment of nullable values in functions</span></span>

<span data-ttu-id="87635-120">SQL 中的許多函式只有在 `null` 其中一些引數為時，才會傳回結果 `null` 。</span><span class="sxs-lookup"><span data-stu-id="87635-120">Many functions in SQL can only return a `null` result if some of their arguments are `null`.</span></span> <span data-ttu-id="87635-121">EF Core 利用這個來產生更有效率的查詢。</span><span class="sxs-lookup"><span data-stu-id="87635-121">EF Core takes advantage of this to produce more efficient queries.</span></span>
<span data-ttu-id="87635-122">下列查詢說明優化：</span><span class="sxs-lookup"><span data-stu-id="87635-122">The query below illustrates the optimization:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

<span data-ttu-id="87635-123">產生的 SQL 如下 (我們不需要評估函數，因為它在其中 `SUBSTRING` 一個引數為 null 時，只會是 null。 ) ：</span><span class="sxs-lookup"><span data-stu-id="87635-123">The generated SQL is as follows (we don't need to evaluate the `SUBSTRING` function since it will be only null when either of the arguments to it is null.):</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

<span data-ttu-id="87635-124">優化也可以用於使用者定義函數。</span><span class="sxs-lookup"><span data-stu-id="87635-124">The optimization can also be used for user-defined functions.</span></span> <span data-ttu-id="87635-125">如需詳細資訊，請參閱 [使用者定義函數對應](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) 頁面。</span><span class="sxs-lookup"><span data-stu-id="87635-125">See [user defined function mapping](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) page for more details.</span></span>

## <a name="writing-performant-queries"></a><span data-ttu-id="87635-126">撰寫效能查詢</span><span class="sxs-lookup"><span data-stu-id="87635-126">Writing performant queries</span></span>

- <span data-ttu-id="87635-127">比較不可為 null 的資料行比比較可為 null 的資料行更簡單且更快速。</span><span class="sxs-lookup"><span data-stu-id="87635-127">Comparing non-nullable columns is simpler and faster than comparing nullable columns.</span></span> <span data-ttu-id="87635-128">請考慮盡可能將資料行標示為不可為 null。</span><span class="sxs-lookup"><span data-stu-id="87635-128">Consider marking columns as non-nullable whenever possible.</span></span>

- <span data-ttu-id="87635-129">檢查是否相等 (`==`) 比檢查不相等 () 更簡單且快速 `!=` ，因為查詢不需要區分 `null` 和 `false` 結果。</span><span class="sxs-lookup"><span data-stu-id="87635-129">Checking for equality (`==`) is simpler and faster than checking for non-equality (`!=`), because query doesn't need to distinguish between `null` and `false` result.</span></span> <span data-ttu-id="87635-130">盡可能使用相等比較。</span><span class="sxs-lookup"><span data-stu-id="87635-130">Use equality comparison whenever possible.</span></span> <span data-ttu-id="87635-131">不過，單純 `==` 的否定比較與相同 `!=` ，因此不會導致效能改進。</span><span class="sxs-lookup"><span data-stu-id="87635-131">However, simply negating `==` comparison is effectively the same as `!=`, so it doesn't result in performance improvement.</span></span>

- <span data-ttu-id="87635-132">在某些情況下，您可以明確地篩選出資料行中的值，以簡化複雜的比較-例如， `null` 當沒有 `null` 值存在或這些值與結果不相關時。</span><span class="sxs-lookup"><span data-stu-id="87635-132">In some cases, it is possible to simplify a complex comparison by filtering out `null` values from a column explicitly - for example when no `null` values are present or these values are not relevant in the result.</span></span> <span data-ttu-id="87635-133">請考慮下列範例：</span><span class="sxs-lookup"><span data-stu-id="87635-133">Consider the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

<span data-ttu-id="87635-134">這些查詢會產生下列 SQL：</span><span class="sxs-lookup"><span data-stu-id="87635-134">These queries produce the following SQL:</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

<span data-ttu-id="87635-135">在第二個查詢中， `null` 會明確地從資料行篩選結果 `String1` 。</span><span class="sxs-lookup"><span data-stu-id="87635-135">In the second query, `null` results are filtered out from `String1` column explicitly.</span></span> <span data-ttu-id="87635-136">EF Core 可以在比較期間安全地將資料 `String1` 行視為不可為 null，以產生更簡單的查詢。</span><span class="sxs-lookup"><span data-stu-id="87635-136">EF Core can safely treat the `String1` column as non-nullable during comparison, resulting in a simpler query.</span></span>

## <a name="using-relational-null-semantics"></a><span data-ttu-id="87635-137">使用關聯式 null 語義</span><span class="sxs-lookup"><span data-stu-id="87635-137">Using relational null semantics</span></span>

<span data-ttu-id="87635-138">您可以停用 null 比較補償，並直接使用關聯式 null 語義。</span><span class="sxs-lookup"><span data-stu-id="87635-138">It's possible to disable the null comparison compensation and use relational null semantics directly.</span></span> <span data-ttu-id="87635-139">這可以藉由 `UseRelationalNulls(true)` 在方法內的 options builder 上呼叫方法來完成 `OnConfiguring` ：</span><span class="sxs-lookup"><span data-stu-id="87635-139">This can be done by calling `UseRelationalNulls(true)` method on the options builder inside `OnConfiguring` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> <span data-ttu-id="87635-140">使用關聯式 null 語義時，您的 LINQ 查詢不再具有與 c # 相同的意義，而且可能會產生與預期不同的結果。</span><span class="sxs-lookup"><span data-stu-id="87635-140">When using relational null semantics, your LINQ queries no longer have the same meaning as they do in C#, and may yield different results than expected.</span></span> <span data-ttu-id="87635-141">使用這個模式時，請務必小心。</span><span class="sxs-lookup"><span data-stu-id="87635-141">Exercise caution when using this mode.</span></span>
