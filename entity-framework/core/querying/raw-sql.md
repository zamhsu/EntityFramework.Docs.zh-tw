---
title: 原始 SQL 查詢 - EF Core
author: smitpatel
ms.date: 10/08/2019
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: a54bb67c0fce9d621382f6372e70fe4cdca48a20
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417667"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢

Entity Framework Core 可讓您在處理關聯式資料庫時，下拉至原始 SQL 查詢。 如果無法使用 LINQ 表示所需的查詢,則原始 SQL 查詢非常有用。 如果使用 LINQ 查詢會導致 SQL 查詢效率低下,則也使用原始 SQL 查詢。 原始 SQL 查詢可以傳回為模型一部份的一般實體類型或[無鑰匙實體類型](xref:core/modeling/keyless-entity-types)。

> [!TIP]  
> 您可以在 GitHub 上查看本文[的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/)。

## <a name="basic-raw-sql-queries"></a>基本的原始 SQL 查詢

可以使用`FromSqlRaw`擴充方法基於原始 SQL 查詢開始 LINQ 查詢。 `FromSqlRaw`只能用於查詢根,即直接在上`DbSet<>`。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

原始 SQL 查詢可以用來執行預存程序。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a>傳遞參數

> [!WARNING]
> **始終對原始 SQL 查詢使用參數化**
>
> 在原始 SQL 查詢中引入任何使用者提供的值時,必須注意避免 SQL 注入攻擊。 除了驗證此類值不包含無效字元外,始終使用參數化將值發送與 SQL 文本分開。
>
> 特別是,切勿將具有未經驗證的使用者提供的值的串聯或插值`$""`字串 ()`FromSqlRaw`傳遞`ExecuteSqlRaw`到或中。 `FromSqlInterpolated`和`ExecuteSqlInterpolated`方法允許使用字串插值語法,以防止 SQL 注入攻擊。

下面的範例透過在 SQL 查詢字串中包括參數占位元並提供其他參數,將單個參數傳遞給儲存過程。 雖然此語法可能類似於`String.Format`語法,但提供的值將包裝在和`DbParameter`生成參數名稱中,插入指定`{0}`占位符的位置符的位置。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

`FromSqlInterpolated`類似於`FromSqlRaw`但允許您使用字串插值語法。 就像`FromSqlRaw``FromSqlInterpolated`, 只能在查詢根上使用。 與前面的範例一樣,該值將轉換為,`DbParameter`並且不受 SQL 注入的影響。

> [!NOTE]
> 在版本`FromSqlRaw`3.0`FromSqlInterpolated`之前 ,`FromSql`並且是名為的兩個重載。 有關詳細資訊,請參閱[以前的版本部分](#previous-versions)。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

您也可以建構 DbParameter，並將它提供為參數值。 由於可以使用一般 SQL 參數占位元,而不是字串佔位`FromSqlRaw`元, 因此可以安全地使用:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

`FromSqlRaw`允許您在 SQL 查詢字串中使用命名參數,這在儲存過程具有可選參數時非常有用:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

## <a name="composing-with-linq"></a>使用 LINQ 撰寫

您可以使用 LINQ 運算子在初始原始 SQL 查詢的頂部進行組合。 EF Core 將將其視為子查詢,並在資料庫中進行組合。 下面的範例使用從表值函數 (TVF) 中選擇的原始 SQL 查詢。 然後使用 LINQ 進行篩選和排序。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

上面查詢產生以下 SQL:

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

使用 LINQ 組合需要原始 SQL 查詢是可組合的,因為 EF Core 會將提供的 SQL 視為子查詢。 以 `SELECT` 關鍵字為開頭的 SQL 查詢才可撰寫。 此外,SQL 傳遞不應包含任何在子查詢上無效的字元或選項,例如:

- 尾隨分號
- 在 SQL Server 上，結尾的查詢層級提示 (例如，`OPTION (HASH JOIN)`)
- 在 SQL`ORDER BY`Server`OFFSET 0``SELECT`上,子句`TOP 100 PERCENT`中未與 OR 一起使用

SQL Server 不允許對儲存過程呼叫進行群組,因此任何嘗試將其他查詢運算符應用於此類調用都將導致 SQL 無效。 使用`AsEnumerable``AsAsyncEnumerable`或`FromSqlRaw`方法`FromSqlInterpolated`, 以確保 EF Core 不會嘗試透過儲存過程進行撰寫。

## <a name="change-tracking"></a>變更追蹤

使用`FromSqlRaw``FromSqlInterpolated`或方法的查詢遵循與 EF Core 中的任何其他 LINQ 查詢完全相同的更改追蹤規則。 例如，若查詢投影實體類型，就會依預設追蹤結果。

下面的範例使用原始 SQL 查詢,該查詢從表值函數 (TVF) 中選擇,然後停用使用呼`AsNoTracking`叫 的更改追蹤 :

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a>限制

使用原始 SQL 查詢時有一些要注意的限制：

- SQL 查詢必須返回實體類型的所有屬性的數據。
- 結果集中的資料行名稱必須符合屬性所對應的資料行名稱。 請注意,此行為與 EF6 不同。 EF6 忽略了原始 SQL 查詢的列映射屬性,結果集列名稱必須與屬性名稱匹配。
- SQL 查詢不能包含相關數據。 不過，在許多情況下，您可以使用 `Include` 運算子來傳回相關資料以在查詢上方進行撰寫 (請參閱[包含相關資料](#including-related-data))。

## <a name="previous-versions"></a>舊版本

EF Core 版本 2.2 和`FromSql`早期有兩個 稱為 的方法重載,`FromSqlRaw``FromSqlInterpolated`其行為方式與較新的 和相同。 當意圖調用插值字串方法時,很容易意外地調用原始字串方法,而相反。 意外調用錯誤的重載可能會導致查詢在本應被參數化時。
