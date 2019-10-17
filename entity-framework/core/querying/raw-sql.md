---
title: 原始 SQL 查詢 - EF Core
author: smitpatel
ms.date: 10/08/2019
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: b7087771f1a9e8ee5e044cfea367d74a0b1c1d35
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445930"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢

Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。 如果您想要的查詢無法使用 LINQ 來表示，原始 SQL 查詢會很有用。 如果使用 LINQ 查詢會導致 SQL 查詢效率不佳，也會使用原始 SQL 查詢。 原始 SQL 查詢可以傳回屬於模型一部分的一般實體類型或[無索引鍵實體類型](xref:core/modeling/keyless-entity-types)。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/) \(英文\)。

## <a name="basic-raw-sql-queries"></a>基本的原始 SQL 查詢

您可以使用 `FromSqlRaw` 擴充方法，根據原始 SQL 查詢來開始 LINQ 查詢。 `FromSqlRaw` 只能用在直接在 `DbSet<>` 上的查詢根目錄。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

原始 SQL 查詢可以用來執行預存程序。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a>傳遞參數

> [!WARNING]
> **一律針對原始 SQL 查詢使用參數化**
>
> 將任何使用者提供的值引進原始 SQL 查詢時，必須小心避免 SQL 插入式攻擊。 除了驗證此類值不包含不正確字元，請一律使用參數化，將值與 SQL 文字分開傳送。
>
> 特別的是，絕對不要將未驗證之使用者提供值的串連或插入字串（`$""`）傳遞至 `FromSqlRaw` 或 `ExecuteSqlRaw`。 @No__t-0 和 @no__t 1 方法允許以防止 SQL 插入式攻擊的方式使用字串內插補點語法。

下列範例會將參數預留位置包含在 SQL 查詢字串中，並提供其他引數，藉以將單一參數傳遞至預存程式。 雖然此語法看起來可能像 `String.Format` 語法，但提供的值會包裝在 `DbParameter` 中，而產生的參數名稱會插入指定的 `{0}` 預留位置。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

`FromSqlInterpolated` 類似于 `FromSqlRaw`，但可讓您使用字串內插補點語法。 就像 `FromSqlRaw`，`FromSqlInterpolated` 只能用於查詢根目錄。 如同先前的範例，此值會轉換為 `DbParameter`，而且不容易受到 SQL 插入。

> [!NOTE]
> 在3.0 版之前，`FromSqlRaw` 和 `FromSqlInterpolated` 是名為 `FromSql` 的兩個多載。 如需詳細資訊，請參閱[先前的版本一節](#previous-versions)。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

您也可以建構 DbParameter，並將它提供為參數值。 因為使用了一般 SQL 參數預留位置，而不是字串預留位置，所以可以安全地使用 `FromSqlRaw`：

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

`FromSqlRaw` 可讓您在 SQL 查詢字串中使用具名引數，當預存程式具有選擇性參數時，這會很有用：

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

## <a name="composing-with-linq"></a>使用 LINQ 撰寫

您可以使用 LINQ 運算子在初始原始 SQL 查詢之上進行撰寫。 EF Core 會將它視為子查詢，並在資料庫中撰寫它。 下列範例會使用從資料表值函式（TVF）選取的原始 SQL 查詢。 然後使用 LINQ 來進行篩選和排序，以在其上撰寫。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

上述查詢會產生下列 SQL：

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a>包含相關資料

`Include` 方法可用來包含相關的資料，就如同所有其他的 LINQ 查詢一般：

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

使用 LINQ 撰寫時，您的原始 SQL 查詢必須是可組合的，因為 EF Core 會將提供的 SQL 視為子查詢。 以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。 此外，SQL 傳遞不應包含在子查詢上不正確任何字元或選項，例如：

- 尾端分號
- 在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)
- 在 SQL Server 上，未在 `SELECT` 子句中搭配 `OFFSET 0` 或 `TOP 100 PERCENT` 使用的 @no__t 0 子句

SQL Server 不允許撰寫預存程序呼叫，因此嘗試將其他查詢運算子套用至這類呼叫將會導致不正確 SQL。 在 `FromSqlRaw` 或 @no__t 3 方法之後，使用 `AsEnumerable` 或 `AsAsyncEnumerable` 方法，確保 EF Core 不會嘗試撰寫預存程式。

## <a name="change-tracking"></a>變更追蹤

使用 `FromSqlRaw` 或 @no__t 1 方法的查詢，會遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的變更追蹤規則。 例如，若查詢投影實體類型，就會依預設追蹤結果。

下列範例會使用從資料表值函式（TVF）選取的原始 SQL 查詢，然後使用 `AsNoTracking` 的呼叫來停用變更追蹤：

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a>限制

使用原始 SQL 查詢時有一些要注意的限制：

- SQL 查詢必須傳回實體類型之所有屬性的資料。
- 結果集中的資料行名稱必須符合屬性所對應的資料行名稱。 請注意，此行為與 EF6 不同。 原始 SQL 查詢的資料行對應和結果集資料行名稱的 EF6 忽略屬性必須符合屬性名稱。
- SQL 查詢不能包含相關資料。 不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。

## <a name="previous-versions"></a>舊版本

EF Core 2.2 版和更早版本有兩個名為 `FromSql` 的方法多載，其行為方式與較新的 `FromSqlRaw` 和 `FromSqlInterpolated` 相同。 當意圖是呼叫插入字串方法時，很容易不小心呼叫原始字串方法，另一種方式則是。 意外呼叫錯誤的多載，可能會導致查詢不會在應該發生時進行參數化。
