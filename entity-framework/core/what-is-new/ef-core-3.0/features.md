---
title: EF Core 3.0 的新功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: d938f17daecd5031147951d0018602c5635de41d
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149093"
---
# <a name="new-features-included-in-ef-core-30"></a>EF Core 3.0 中包含的新功能

下列清單包含為 EF Core 3.0 新規劃的重要功能。

EF Core 3.0 是一個主要版本，也包含許多[重大變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)，也就是可能對現有應用程式造成負面影響的 API 改進。  

## <a name="linq-improvements"></a>LINQ 改善 

LINQ 可讓您撰寫資料庫查詢，而不需要離開您所選擇的語言，利用豐富型別資訊來提供 IntelliSense 和編譯時間型別檢查。
但是 LINQ 也可以讓您撰寫不限數目的複雜查詢，其中包含任意運算式（方法呼叫或作業）。
處理所有這些組合一直是 LINQ 提供者的重大挑戰。
在 EF Core 3.0 中，我們已重寫 LINQ 執行，讓您能夠將更多運算式轉譯為 SQL，以在更多情況下產生有效率的查詢，以避免無法偵測到效率不佳的查詢，並讓我們更輕鬆地引進新的查詢功能和效能 improvementswithout 中斷現有的應用程式和資料提供者。

### <a name="client-evaluation"></a>用戶端評估

EF Core 3.0 中的主要設計變更，必須與它如何處理無法轉譯成 SQL 或參數的 LINQ 運算式有關：

在前幾個版本中，EF Core 只會找出查詢的哪些部分可以轉譯成 SQL，並在用戶端上執行查詢的其餘部分。
在某些情況下，這種類型的用戶端執行可能是必要的，但在許多其他情況下，它可能會導致沒有效率的查詢。
例如，如果 EF Core 2.2 無法轉譯`Where()`呼叫中的述詞，它會執行不含篩選的 SQL 語句、讀取資料庫中所有的資料列，然後在記憶體中進行篩選。
如果資料庫包含少量的資料列，這可能是可接受的，但如果資料庫包含大量或多個資料列，則會導致嚴重的效能問題，甚至是應用程式失敗。
在 EF Core 3.0 中，我們已將用戶端評估限制為僅適用于最上層投射（最後一個`Select()`呼叫）。
當 EF Core 3.0 偵測到無法在查詢中的任何地方轉譯的運算式時，就會擲回執行時間例外狀況。

## <a name="cosmos-db-support"></a>Cosmos DB 支援 

適用于 EF Core 的 Cosmos DB 提供者可讓熟悉 EF 程式設計模型的開發人員輕鬆地以應用程式資料庫為目標 Azure Cosmos DB。
目標是讓 .NET 開發人員能更輕鬆地發揮 Cosmos DB 的一些優點，例如全域散發、"Always On" 可用性、彈性的延展性，以及低延遲。
提供者將啟用大部分的 EF Core 功能，例如自動變更追蹤、LINQ 與值轉換 (根據 Cosmos DB 中的 SQL API)。

## <a name="c-80-support"></a>C# 8.0 支援

EF Core 3.0 會利用8.0 中C#的一些新功能：

### <a name="asynchronous-streams"></a>非同步資料流

非同步查詢結果現在會使用新的標準`IAsyncEnumerable<T>`介面公開，並可使用`await foreach`加以取用。

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders)
{
  Process(o);
} 
```

### <a name="nullable-reference-types"></a>可為 Null 的參考型別 

當您的程式碼中啟用這項新功能時，EF Core 可能會導致參考類型的屬性（例如，字串或導覽屬性等基本類型）的 null 屬性，決定資料庫中資料行和關聯性的 null 屬性。

## <a name="interception"></a>攔截

EF Core 3.0 中的新攔截 API，可讓您以程式設計方式觀察和修改低層級資料庫作業的結果，這是 EF Core 正常運作的一部分，例如開啟連接、initating 交易，以及執行命令。 

## <a name="reverse-engineering-of-database-views"></a>資料庫檢視的反向工程

沒有索引鍵的實體類型（先前稱為[查詢類型](xref:core/modeling/keyless-entity-types)）代表可從資料庫讀取但無法更新的資料。
這項特性讓它們成為在大多數情況下對應資料庫檢視的絕佳大小，因此，我們會在反向工程資料庫流覽時，自動建立不含索引鍵的實體類型。

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>現在可選用以主體來共用資料表的相依實體

從 EF Core 3.0 開始，如果 `OrderDetails` 由 `Order` 所擁有，或明確地對應到相同的資料表，即可新增 `Order` 而無需 `OrderDetails` 及所有 `OrderDetails` 屬性，但主索引鍵將會對應至可為 Null 的資料行。

進行查詢時，如果任何其必要的屬性不具有值，或如果其具有主索引鍵以外的非必要屬性，且所有屬性皆為 `null`，則 EF Core 會將 `OrderDetails` 設定為 `null`。

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>.NET Core 上的 EF 6.3

我們了解許多現有的應用程式都使用舊版 EF，而且因為要獲得 .NET Core 的優點而將它們移植到 EF Core 有時候需要非常多的精力。
基於這個理由，我們已啟用 EF 6 的 newewst 版本，以便在 .NET Core 3.0 上執行。
有一些限制，例如：
- 需要新的提供者，才能在 .NET Core 上工作
- SQL Server 的空間支援將不會啟用

## <a name="postponed-features"></a>延後的功能

原先規劃用於 EF Core 3.0 的一些功能已延後到未來的版本： 

- 能夠在遷移中忽略模型的各個部分，以[#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725)的方式進行追蹤。
- 屬性包實體，視為兩個不同的問題來追蹤： [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)關於共用類型實體，以及有關索引屬性對應支援的[#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 。
