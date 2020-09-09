---
title: 原始 SQL 查詢 - EF Core
description: 在 Entity Framework Core 中使用原始 SQL 查詢查詢
author: smitpatel
ms.date: 10/08/2019
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 3b95c15b2b07d1eeecf1603e6bfbb29f4931d5cc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617507"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢

Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。 如果您想要的查詢無法使用 LINQ 表示，原始 SQL 查詢會很有用。 如果使用 LINQ 查詢會導致 SQL 查詢效率不佳，也會使用原始 SQL 查詢。 原始 SQL 查詢可以傳回屬於模型一部分的一般實體類型或 [無索引鍵實體類型](xref:core/modeling/keyless-entity-types) 。

> [!TIP]  
> 您可以在 GitHub 上查看這篇文章的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) 。

## <a name="basic-raw-sql-queries"></a>基本的原始 SQL 查詢

您可以使用 `FromSqlRaw` 擴充方法，根據原始 SQL 查詢來開始 LINQ 查詢。 `FromSqlRaw` 只能在上直接位於的查詢根目錄上使用 `DbSet<>` 。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

原始 SQL 查詢可以用來執行預存程序。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a>傳遞參數

> [!WARNING]
> **一律針對原始 SQL 查詢使用參數化**
>
> 將任何使用者提供的值引進原始 SQL 查詢時，必須小心避免 SQL 插入式攻擊。 除了驗證這類值不包含不正確字元，請一律使用參數化，以將值與 SQL 文字分開。
>
> 尤其是，絕對不會將串連或插入的字串 (`$""`) ，並將未經驗證的使用者提供值傳遞至 `FromSqlRaw` 或 `ExecuteSqlRaw` 。 `FromSqlInterpolated`和 `ExecuteSqlInterpolated` 方法允許以防止 SQL 插入式攻擊的方式使用字串插補語法。

下列範例會藉由在 SQL 查詢字串中包含參數預留位置，並提供額外的引數，將單一參數傳遞至預存程式。 雖然此語法看起來可能像 `String.Format` 語法，但是提供的值會包裝在中， `DbParameter` 並在指定預留位置的位置插入所產生的參數名稱 `{0}` 。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

`FromSqlInterpolated` 類似于， `FromSqlRaw` 但可讓您使用字串插補語法。 就像 `FromSqlRaw` ， `FromSqlInterpolated` 只能在查詢根目錄上使用。 如同上述範例，此值會轉換為 `DbParameter` ，而且不容易遭受 SQL 插入式攻擊。

> [!NOTE]
> 在3.0 版之前， `FromSqlRaw` 以及 `FromSqlInterpolated` 兩個名為 `FromSql` 的多載。 如需詳細資訊，請參閱「 [先前的版本」一節](#previous-versions)。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

您也可以建構 DbParameter，並將它提供為參數值。 因為使用一般 SQL 參數預留位置，而不是字串預留位置，所以 `FromSqlRaw` 可以安全地使用：

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

`FromSqlRaw` 可讓您在 SQL 查詢字串中使用具名引數，這在預存程式具有選擇性參數時很有用：

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> **參數排序** Entity Framework Core 會根據陣列的順序來傳遞參數 `SqlParameter[]` 。 傳遞多個時 `SqlParameter` ，SQL 字串中的順序必須符合預存程式定義中參數的順序。 若未這麼做，可能會導致在執行程式時產生類型轉換例外狀況及/或非預期的行為。

## <a name="composing-with-linq"></a>使用 LINQ 撰寫

您可以使用 LINQ 運算子來撰寫初始原始 SQL 查詢的最上層。 EF Core 會將它視為子查詢，並在資料庫中撰寫它。 下列範例會使用從資料表值函數中選取的原始 SQL 查詢 (TVF) 。 然後使用 LINQ 來撰寫篩選和排序。

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

撰寫 LINQ 需要可組合您的原始 SQL 查詢，因為 EF Core 會將提供的 SQL 視為子查詢。 以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。 此外，SQL 傳遞的不應包含任何在子查詢中不正確字元或選項，例如：

- 尾端分號
- 在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)
- 在 SQL Server 上， `ORDER BY` 不會搭配 `OFFSET 0` `TOP 100 PERCENT` 子句或子句中使用的子句 `SELECT`

SQL Server 不允許撰寫預存程序呼叫，因此將其他查詢運算子套用到這類呼叫的任何嘗試都會導致不正確 SQL。 `AsEnumerable` `AsAsyncEnumerable` 請在或方法之後使用或方法 `FromSqlRaw` `FromSqlInterpolated` ，以確定 EF Core 不會嘗試在預存程式上撰寫。

## <a name="change-tracking"></a>變更追蹤

使用或方法的查詢，會 `FromSqlRaw` `FromSqlInterpolated` 遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的變更追蹤規則。 例如，若查詢投影實體類型，就會依預設追蹤結果。

下列範例會使用從資料表值函式中選取的原始 SQL 查詢 (TVF) ，然後透過呼叫來停用變更追蹤 `AsNoTracking` ：

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a>限制

使用原始 SQL 查詢時有一些要注意的限制：

- SQL 查詢必須傳回實體型別之所有屬性的資料。
- 結果集中的資料行名稱必須符合屬性所對應的資料行名稱。 請注意，這種行為不同于 EF6。 針對原始 SQL 查詢的資料行對應，EF6 忽略了屬性，而結果集資料行名稱必須與屬性名稱相符。
- SQL 查詢不能包含相關資料。 不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。

## <a name="previous-versions"></a>舊版

EF Core 2.2 版和更早版本有兩個名為的方法多載 `FromSql` ，其行為與較新的 `FromSqlRaw` 和相同 `FromSqlInterpolated` 。 當意圖是要呼叫插入字串方法，而另一種方法很容易時，很容易就會不小心呼叫原始字串方法。 不小心呼叫錯誤的多載可能會導致查詢不會在應該是時進行參數化。
