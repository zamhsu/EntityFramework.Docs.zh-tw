---
title: 定序和區分大小寫-EF Core
description: 如何在資料庫和查詢中設定定序和區分大小寫
author: roji
ms.date: 04/27/2020
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/collations-and-case-sensitivity.md
ms.openlocfilehash: 1c9344b8e96142645f55e8d830dfd350dc10aa96
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666206"
---
# <a name="collations-and-case-sensitivity"></a>定序和區分大小寫

> [!NOTE]
> 這項功能會在 EF Core 5.0 中引進。

資料庫中的文字處理可能很複雜，而且需要更多的使用者注意，才會有疑問。 一件事，資料庫在處理文字的方式上有很大的差異，例如，雖然某些資料庫預設會區分大小寫（例如 Sqlite、于 postgresql），但有些則不區分大小寫（SQL Server、MySQL）。 此外，由於索引的使用方式，區分大小寫和類似的層面可能會對查詢效能造成太大的影響：雖然在區分大小寫的 `string.Lower` 資料庫中使用來強制進行不區分大小寫的比較很有吸引力，但這麼做可能會讓您的應用程式無法使用索引。 此頁面詳細說明如何設定區分大小寫，或更廣泛的定序，以及如何以有效率的方式執行此動作，而不會影響查詢效能。

## <a name="introduction-to-collations"></a>定序簡介

文字處理中的基本概念是定*序，這*是一組規則，可決定文字值的排序方式，並比較是否相等。 例如，雖然不區分大小寫的定序會略過大寫和小寫字母之間的差異，但對等比較而言，區分大小寫的定序則不會。 不過，由於區分大小寫區分文化特性（例如 `i` ， `I` 在土耳其文中以不同的字母表示），因此有多個不區分大小寫的定序，每一個都有自己的一組規則。 定序的範圍也會延伸至字元資料的其他層面，但不區分大小寫。例如，在德文中，有時候（但不一定）會想要將 `ä` 和視為 `ae` 相同。 最後，定序也會定義文字值的*排序*方式：雖然德文 `ä` 的地方 `a` ，瑞典文會將它放在字母的結尾。

資料庫中的所有文字作業會使用定序（不論是明確或隱含）來判斷作業比較和排序字串的方式。 可用定序和其命名配置的實際清單是資料庫專屬的;請參閱[下面的一節](#database-specific-information)，以取得各種資料庫相關檔頁面的連結。 幸運的是，資料庫通常允許在資料庫或資料行層級定義預設定序，以及明確指定哪些定序應用於查詢中的特定作業。

## <a name="database-collation"></a>資料庫定序

在大部分的資料庫系統中，預設定序定義于資料庫層級;除非覆寫，否則該定序會隱含套用至該資料庫內發生的所有文字作業。 資料庫定序通常是在資料庫建立時設定（透過 `CREATE DATABASE` DDL 語句），如果未指定，則會預設為在安裝時決定的某個伺服器層級值。 例如，SQL Server 中的預設伺服器層級定序是 `SQL_Latin1_General_CP1_CI_AS` ，這是不區分大小寫、區分重音的定序。 雖然資料庫系統通常會允許改變現有資料庫的定序，但這樣做可能會造成複雜的情況。建議您在建立資料庫之前，先挑選定序。

使用 EF Core 遷移來管理您的資料庫架構時，模型的方法中的下列設定會將 `OnModelCreating` SQL Server 資料庫設定為使用區分大小寫的定序：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=40)]

## <a name="column-collation"></a>資料行定序

定序也可以在文字資料行上定義，並覆寫資料庫預設值。 如果某些資料行需要區分大小寫，但其餘的資料庫需要區分大小寫，這會很有用。

當您使用 EF Core 遷移來管理您的資料庫架構時，下列各項會將屬性（property）的 `Name` 資料行設定為不區分大小寫，但在其他情況下，該資料庫會設為區分大小寫：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=OnModelCreating&highlight=6)]

## <a name="explicit-collation-in-a-query"></a>查詢中的明確定序

在某些情況下，您必須使用不同的查詢來查詢相同的資料行。 例如，一個查詢可能需要在資料行上執行區分大小寫的比較，而另一個則可能需要在相同的資料行上執行不區分大小寫的比較。 這可以藉由在查詢本身中明確指定定序來完成：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

這會 `COLLATE` 在 SQL 查詢中產生子句，而不論資料行或資料庫層級中定義的定序為何，都適用區分大小寫的定序：

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>明確定序和索引

索引是資料庫效能中最重要的因素之一-以索引有效率執行的查詢可以方格至沒有該索引的終止。 索引會隱含繼承其資料行的定序;這表示資料行上的所有查詢都可以自動使用該資料行上所定義的索引，前提是查詢並未指定不同的定序。 在查詢中指定明確定序，通常會防止該查詢使用該資料行上定義的索引，因為定序不會再相符。因此，建議您在使用這項功能時務必小心。 最好是在資料行（或資料庫）層級定義定序，讓所有查詢隱含地使用該定序，並受益于任何索引。

請注意，某些資料庫允許在建立索引時（例如于 postgresql、Sqlite）定義定序。 這可讓您在相同的資料行上定義多個索引，使用不同的定序加速作業（例如，區分大小寫和不區分大小寫的比較）。 如需詳細資訊，請參閱資料庫提供者的檔。

> [!WARNING]
> 請一律檢查查詢的查詢計劃，並確定在執行大量資料的效能關鍵查詢中，使用了適當的索引。 透過（或藉由呼叫）覆寫查詢中的區分大小寫， `EF.Functions.Collate` `string.ToLower` 可能會對應用程式的效能造成極大的影響。

## <a name="translation-of-built-in-net-string-operations"></a>內建 .NET 字串作業的轉譯

在 .NET 中，根據預設，字串相等會區分大小寫： `s1 == s2` 執行需要字串相同的序數比較。 由於資料庫的預設定序會有所不同，而且因為很適合使用索引，所以 EF Core 不會嘗試將簡單的相等性轉譯為資料庫區分大小寫的作業： c # 相等會直接轉譯為 SQL 相等，這可能會因為使用中的特定資料庫及其定序設定而不區分大小寫。

此外，.NET 也提供接受列舉的多載 [`string.Equals`](https://docs.microsoft.com/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) ，可讓您 [`StringComparison`](https://docs.microsoft.com/dotnet/api/system.stringcomparison) 指定比較的區分大小寫和文化特性。 根據設計，EF Core refrains 從將這些多載轉譯為 SQL，並嘗試使用它們將導致例外狀況。 一件事，EF Core 不知道應該使用哪一個區分大小寫或不區分大小寫定序。 更重要的是，在大多數情況下，套用定序可能會導致索引的使用，大幅影響非常基本且常用之 .NET 結構的效能。 若要強制查詢使用區分大小寫或不區分大小寫的比較，請依照 `EF.Functions.Collate` [上述詳細](#explicit-collations-and-indexes)說明，明確地指定定序。

## <a name="database-specific-information"></a>資料庫特定資訊

* [SQL Server 定序的相關檔](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support)。
* [有關定序的 Sqlite 檔](https://docs.microsoft.com/dotnet/standard/data/sqlite/collation)。
* [于 postgresql 定序的相關檔](https://www.postgresql.org/docs/current/collation.html)。
* 定序[的 MySQL 檔](https://dev.mysql.com/doc/refman/en/charset-general.html)。
