---
title: 定序和區分大小寫-EF Core
description: 在資料庫和使用 Entity Framework Core 的查詢中設定定序和區分大小寫
author: roji
ms.date: 04/27/2020
uid: core/miscellaneous/collations-and-case-sensitivity
ms.openlocfilehash: 48e0a6b316742dc1467134ae81f90086bb93d011
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543532"
---
# <a name="collations-and-case-sensitivity"></a>定序和區分大小寫

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。

資料庫中的文字處理可能很複雜，而且需要更多的使用者注意。 針對其中一件事，資料庫處理文字的方式會有很大的差異;例如，有些資料庫預設會區分大小寫 (例如 Sqlite、于 postgresql) ，其他資料庫則是不區分大小寫的 (SQL Server、MySQL) 。 此外，由於索引使用方式的關係，區分大小寫和類似的層面可能會對查詢效能造成最大的影響：雖然 `string.Lower` 在區分大小寫的資料庫中使用強制進行不區分大小寫的比較可能很吸引人，但是這樣做可能會讓您的應用程式無法使用索引。 此頁面詳細說明如何設定區分大小寫或更一般的定序，以及如何以有效率的方式來設定，而不會影響查詢效能。

## <a name="introduction-to-collations"></a>定序簡介

文字處理的基本概念是定序 *，這* 是一組規則，用來決定文字值的排序方式和比較是否相等。 例如，雖然不區分大小寫的定序會忽略大寫和小寫字母之間的差異以進行相等比較，但區分大小寫的定序並不會。 不過，由於區分大小寫區分文化特性 (例如 `i` ，並 `I` 以土耳其文) 表示不同的字母，因此存在多個不區分大小寫的定序，每個定序都有自己的一組規則。 定序的範圍也會延伸至不區分大小寫，以及字元資料的其他方面;例如，在德文中，有時會 (，但不一定會) 希望視為 `ä` `ae` 相同。 最後，定序也會定義文字值的 *排序* 方式：在德文之後的地方 `ä` `a` ，瑞典文會將它放在字母結尾。

資料庫中的所有文字作業都會使用定序（不論是明確或隱含）來判斷作業如何比較和排序字串。 可用定序及其命名配置的實際清單是資料庫專屬的，請參閱 [下一節](#database-specific-information) ，以取得各種資料庫相關檔頁面的連結。 幸運的是，資料庫通常允許在資料庫或資料行層級定義預設的定序，並明確指定要在查詢中針對特定作業使用哪一個定序。

## <a name="database-collation"></a>資料庫定序

在大部分的資料庫系統中，會在資料庫層級定義預設的定序。除非覆寫，否則該定序會隱含地套用至該資料庫內發生的所有文字作業。 資料庫定序通常會在資料庫建立時設定 (透過 `CREATE DATABASE` DDL 語句) ，而且如果未指定，則會預設為在安裝時所決定的某個伺服器層級值。 例如，SQL Server 中的預設伺服器層級定序是 `SQL_Latin1_General_CP1_CI_AS` ，這是不區分大小寫、區分重音的定序。 雖然資料庫系統通常允許變更現有資料庫的定序，但這麼做可能會造成複雜的情況;建議您在建立資料庫之前挑選定序。

使用 EF Core 遷移來管理您的資料庫架構時，您模型的方法中的下列內容會將 `OnModelCreating` SQL Server 資料庫設定為使用區分大小寫的定序：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=DatabaseCollation)]

## <a name="column-collation"></a>資料行定序

您也可以在文字資料行上定義定序，並覆寫資料庫預設值。 如果某些資料行必須區分大小寫，而且資料庫的其餘部分必須區分大小寫，這項功能就很有用。

使用 EF Core 遷移來管理您的資料庫架構時，下列內容會將屬性的資料行設定為不區分大小寫的 `Name` 資料庫中的資料行，否則會設定為區分大小寫：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=ColumnCollation)]

## <a name="explicit-collation-in-a-query"></a>查詢中的明確定序

在某些情況下，您必須使用不同的定序來查詢相同的資料行。 例如，一個查詢可能需要在資料行上執行區分大小寫的比較，而另一個查詢可能需要在相同的資料行上執行不區分大小寫的比較。 您可以在查詢本身內明確指定定序來完成這項作業：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

這會 `COLLATE` 在 SQL 查詢中產生子句，此子句會套用區分大小寫的定序，而不論資料行或資料庫層級所定義的定序為何：

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>明確定序和索引

索引是資料庫效能中最重要的因素之一：使用索引有效率地執行的查詢，可以方格到沒有該索引的終止。 索引會隱含地繼承其資料行的定序;這表示資料行上的所有查詢都會自動使用在該資料行上定義的索引（假設查詢未指定不同的定序）。 在查詢中指定明確定序通常會防止該查詢使用該資料行上定義的索引，因為定序將不再相符;因此，建議您在使用這項功能時務必小心。 最好是在資料行 (或資料庫) 層級定義定序，讓所有的查詢都能隱含地使用該定序，並受益于任何索引。

請注意，在建立索引時，有些資料庫允許定義定序 (例如于 postgresql、Sqlite) 。 這可讓您在相同的資料行上定義多個索引，以不同的定序來加速作業 (例如，區分大小寫和不區分大小寫的比較) 。 如需詳細資訊，請參閱資料庫提供者的檔。

> [!WARNING]
> 一律檢查查詢的查詢計劃，並確定在大量資料上執行的效能關鍵查詢中，使用了適當的索引。 透過 (或藉由呼叫) 來覆寫查詢中的區分大小寫， `EF.Functions.Collate` `string.ToLower` 可能會對應用程式的效能產生極大的影響。

## <a name="translation-of-built-in-net-string-operations"></a>內建 .NET 字串作業的轉譯

在 .NET 中，字串相等預設會區分大小寫： `s1 == s2` 執行需要字串相同的序數比較。 由於資料庫的預設定序會有所不同，而且因為很適合使用索引，所以 EF Core 不會嘗試將簡單的相等性轉譯為資料庫區分大小寫的作業： c # 相等會直接轉譯為 SQL 相等，視使用中的特定資料庫及其定序設定而定，這可能會區分大小寫。

此外，.NET 也提供接受列舉的多載 [`string.Equals`](/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) ，可讓您 [`StringComparison`](/dotnet/api/system.stringcomparison) 指定區分大小寫和文化特性進行比較。 根據設計，EF Core refrains 將這些多載轉譯成 SQL，並嘗試使用它們，將會導致例外狀況。 EF Core 不知道應該使用何種區分大小寫或不區分大小寫的定序。 更重要的是，在大部分情況下，套用定序可能會導致索引使用，大幅影響非常基本且常用的 .NET 結構效能。 若要強制查詢使用區分大小寫或不區分大小寫的比較，請依照 `EF.Functions.Collate` [上述詳細](#explicit-collations-and-indexes)說明明確地指定定序。

## <a name="additional-resources"></a>其他資源

### <a name="database-specific-information"></a>資料庫特定資訊

* [有關定序的 SQL Server 檔](/sql/relational-databases/collations/collation-and-unicode-support)。
* [有關定序的資料 Sqlite 檔](/dotnet/standard/data/sqlite/collation)。
* [有關定序的于 postgresql 檔](https://www.postgresql.org/docs/current/collation.html)。
* [有關定序的 MySQL 檔](https://dev.mysql.com/doc/refman/en/charset-general.html)。

### <a name="other-resources"></a>其他資源

* [EF Core 的站立會議課程](https://www.youtube.com/watch?v=OgMhLVa_VfA&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=1)，介紹定序以及探索效能和索引編制方面。
