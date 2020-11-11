---
title: 資料庫函數-EF Core
description: EF Core 查詢轉譯中支援的資料庫函數資訊
author: smitpatel
ms.date: 11/10/2020
uid: core/querying/database-functions
ms.openlocfilehash: 34ced2a602168f6ed84763c046ef581cb87026e8
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503793"
---
# <a name="database-functions"></a>資料庫函數

資料庫函式是 [c # 方法](/dotnet/csharp/programming-guide/classes-and-structs/methods)的相等資料庫。 您可以使用零個或多個參數叫用資料庫函式，它會根據參數值來計算結果。 大部分使用 SQL 進行查詢的資料庫都支援資料庫函數。 因此，EF Core 查詢轉譯產生的 SQL 也允許叫用資料庫函數。 C # 方法不需要嚴格地轉譯成 EF Core 中的資料庫函式。

- C # 方法可能沒有相等的資料庫函數。
  - <xref:System.String.IsNullOrEmpty%2A?displayProperty=nameWithType> 方法會轉譯為 null 檢查，並使用資料庫中的空字串（而不是函式）進行比較。
  - <xref:System.String.Equals(System.String,System.StringComparison)?displayProperty=nameWithType> 方法沒有相等的資料庫，因為在資料庫中無法輕鬆表示或模仿字串比較。
- 資料庫函式可能沒有相等的 c # 方法。 `??`C # 中沒有任何方法的運算子會轉譯為 `COALESCE` 資料庫中的函數。

## <a name="types-of-database-functions"></a>資料庫函式的類型

EF Core SQL 世代支援可在資料庫中使用的函數子集。 這項限制是因為能夠以 LINQ 表示指定資料庫函式的查詢。 此外，每個資料庫都有不同的資料庫函式支援，所以 EF Core 提供一個共同的子集。 資料庫提供者可自由擴充 EF Core SQL 產生，以支援更多的模式。 以下是 EF Core 支援和唯一識別的資料庫函數類型。 這些條款也有助於瞭解 EF Core 提供者內建的翻譯。

### <a name="built-in-vs-user-defined-functions"></a>內建 vs 使用者定義函數

內建函數隨附資料庫預先定義，但使用者定義的函數是由資料庫中的使用者明確定義。 當 EF Core 將查詢轉譯為使用資料庫函式時，它會使用內建函數來確定資料庫上的函數一律可供使用。 在某些資料庫中，必須區分內建函數，才能正確產生 SQL。 例如 SqlServer 需要使用架構限定名稱來叫用每個使用者定義函數。 但是 SqlServer 中的內建函數沒有架構。 于 postgresql 會在架構中定義內建函數， `public` 但是可以使用架構限定名稱來叫用。

### <a name="aggregate-vs-scalar-vs-table-valued-functions"></a>匯總 vs 純量和資料表值函數

- 純量函數會採用純量值（例如整數或字串）作為參數，並傳回純量值作為結果。 純量函數可用於 SQL 中可傳遞純量值的任何位置。
- 彙總函式會以純量值的資料流程作為參數，並傳回純量值作為結果。 彙總函式會套用至整個查詢結果集或套用運算子所產生的值群組 `GROUP BY` 。
- 資料表值函式會採用純量值做為參數 (s) ，並傳回資料列的資料流程作為結果。 資料表值函式會當做資料表來源 in 子句來使用 `FROM` 。

### <a name="niladic-functions"></a>Niladic 函式

Niladic 函式是特殊的資料庫函式，沒有任何參數，且必須在沒有括弧的情況下叫用。 它們類似于 c # 中實例上的屬性/欄位存取。 Niladic 函式與參數不相同的函式不同，因為後者需要空的括弧。 如果資料庫函式沒有特殊的名稱，則一律需要括弧。 以參數計數為基礎的另一個資料庫函數子集是 variadic 函式。 叫用時，Variadic 函式可採用不同數目的參數。

## <a name="database-function-mappings-in-ef-core"></a>EF Core 中的資料庫函數對應

EF Core 支援三種不同的 c # 函式和資料庫函數之間的對應方式。

### <a name="built-in-function-mapping"></a>內建函數對應

根據預設，EF Core 提供者會針對基本類型上的各種內建函數提供對應。 例如， <xref:System.String.ToLower?displayProperty=nameWithType> `LOWER` 在 SqlServer 中會轉譯為。 這項功能可讓使用者順暢地以 LINQ 撰寫查詢。 我們通常會在資料庫中提供轉譯，以提供與 c # 函式在用戶端上提供的結果相同的結果。 有時候，若要達到此目的，實際的翻譯可能會比資料庫函式更複雜一點。 在某些案例中，我們也會提供最適當的轉譯，而不是比對 c # 語法。 相同的功能也會用來提供某些 c # 成員存取的一般翻譯。 例如， <xref:System.String.Length?displayProperty=nameWithType> `LEN` 在 SqlServer 中會轉譯為。 除了提供者之外，外掛程式寫入器也可以新增額外的翻譯。 當外掛程式將更多類型的支援做為基本型別，且想要對其轉譯方法時，此擴充性相當實用。

### <a name="effunctions-mapping"></a>英 孚。函數對應

由於並非所有資料庫函式都有對等的 c # 函式，EF Core 提供者具有特殊的 c # 方法，可叫用特定的資料庫 這些方法會定義為 `EF.Functions` 要在 LINQ 查詢中使用的擴充方法。 這些方法是提供者專屬的，因為它們與特定的資料庫函式密切相關。 因此，適用于一個提供者的方法，可能不會用於任何其他提供者。 此外，因為這些方法的目的是要在已轉譯的查詢中叫用資料庫函式，所以嘗試在用戶端上評估這些函數會導致例外狀況。

### <a name="user-defined-function-mapping"></a>使用者定義函數對應

除了 EF Core 提供者所提供的對應之外，使用者也可以定義自訂對應。 使用者定義的對應會根據使用者需求來擴充查詢轉譯。 當資料庫中有使用者定義的函式時，如果使用者想要從其 LINQ 查詢叫用，這項功能就很有用。

## <a name="see-also"></a>另請參閱

- [SqlServer 內建函數對應](xref:core/providers/sql-server/functions)
- [Sqlite 內建函數對應](xref:core/providers/sqlite/functions)
- [Cosmos 內建函數對應](xref:core/providers/cosmos/functions)
