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
# <a name="query-null-semantics"></a>查詢 null 語義

## <a name="introduction"></a>簡介

SQL 資料庫會在3值邏輯上運作 (`true` 、 `false` 、 `null` 在執行比較時) ，而不是 c # 的布林邏輯。 將 LINQ 查詢轉譯為 SQL 時，EF Core 會針對查詢的某些元素引進額外的 null 檢查，藉以嘗試補償差異。
為了說明這一點，讓我們定義下列實體：

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

併發出數個查詢：

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

前兩個查詢會產生簡單的比較。 在第一個查詢中，這兩個數據行都是不可為 null 的，因此不需要 null 檢查。 在第二個查詢中， `NullableInt` 可以包含 `null` （但 `Id` 不可為 null）; 相 `null` 較于非 null，則會產生 `null` 結果，而這會由作業篩選掉 `WHERE` 。 因此，不需要任何額外的條款。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

第三個查詢引進 null 檢查。 當 `NullableInt` 是 `null` 所產生的比較時 `Id <> NullableInt` `null` ，會依作業篩選掉 `WHERE` 。 不過，從布林值邏輯的角度來看，這種情況應該會在結果中傳回。 因此 EF Core 新增必要的檢查，以確保這一點。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

當兩個數據行都可為 null 時，查詢四和五會顯示模式。 值得注意的是，此作業會 `<>` 產生比作業更複雜的 (以及可能較慢的) 查詢 `==` 。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a>函數中可為 null 值的處理方式

SQL 中的許多函式只有在 `null` 其中一些引數為時，才會傳回結果 `null` 。 EF Core 利用這個來產生更有效率的查詢。
下列查詢說明優化：

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

產生的 SQL 如下 (我們不需要評估函數，因為它在其中 `SUBSTRING` 一個引數為 null 時，只會是 null。 ) ：

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

優化也可以用於使用者定義函數。 如需詳細資訊，請參閱 [使用者定義函數對應](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) 頁面。

## <a name="writing-performant-queries"></a>撰寫效能查詢

- 比較不可為 null 的資料行比比較可為 null 的資料行更簡單且更快速。 請考慮盡可能將資料行標示為不可為 null。

- 檢查是否相等 (`==`) 比檢查不相等 () 更簡單且快速 `!=` ，因為查詢不需要區分 `null` 和 `false` 結果。 盡可能使用相等比較。 不過，單純 `==` 的否定比較與相同 `!=` ，因此不會導致效能改進。

- 在某些情況下，您可以明確地篩選出資料行中的值，以簡化複雜的比較-例如， `null` 當沒有 `null` 值存在或這些值與結果不相關時。 請考慮下列範例：

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

這些查詢會產生下列 SQL：

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

在第二個查詢中， `null` 會明確地從資料行篩選結果 `String1` 。 EF Core 可以在比較期間安全地將資料 `String1` 行視為不可為 null，以產生更簡單的查詢。

## <a name="using-relational-null-semantics"></a>使用關聯式 null 語義

您可以停用 null 比較補償，並直接使用關聯式 null 語義。 這可以藉由 `UseRelationalNulls(true)` 在方法內的 options builder 上呼叫方法來完成 `OnConfiguring` ：

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> 使用關聯式 null 語義時，您的 LINQ 查詢不再具有與 c # 相同的意義，而且可能會產生與預期不同的結果。 使用這個模式時，請務必小心。
