---
title: 交易 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
uid: core/saving/transactions
ms.openlocfilehash: 952cb891d145a47666f1d506ec00f066be9f245d
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654745"
---
# <a name="using-transactions"></a>使用交易

交易可讓系統以不可部分完成的方式處理數個資料庫作業。 如果認可交易，就會對資料庫成功套用所有作業。 如果復原交易，則不會對資料庫套用任何作業。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) \(英文\)。

## <a name="default-transaction-behavior"></a>預設交易行為

根據預設，如果資料庫提供者支援交易，就會在交易中套用對 `SaveChanges()` 單一呼叫中的所有變更。 如果其中任何一項變更失敗，系統就會復原交易，而不會對資料庫套用任何變更。 這意謂著會保證 `SaveChanges()` 要不就是完全成功，要不就是發生錯誤時讓資料庫維持原封不動。

對大多數應用程式來說，此預設行為已足以應付需求。 您應該只有在應用程式需求認為有必要時，才手動控制交易。

## <a name="controlling-transactions"></a>控制交易

您可以使用 `DbContext.Database` API 來開始、認可及復原交易。 下列範例示範在單一交易中執行兩個 `SaveChanges()` 作業和一個 LINQ 查詢。

並非所有資料庫提供者都支援交易。 呼叫交易 API 時，有些提供者可能會擲回例外狀況或不執行任何作業。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a>跨內容交易 (僅適用於關聯式資料庫)

您也可以跨多個內容執行個體共用交易。 只有使用關聯式資料庫提供者時才有提供此功能，因為它會要求使用關聯式資料庫資料庫特定的 `DbTransaction` 和 `DbConnection`。

若要共用交易，內容必須同時共用 `DbConnection` 和 `DbTransaction`。

### <a name="allow-connection-to-be-externally-provided"></a>允許從外部提供連線

必須能夠在建構內容時將連線傳遞給內容，才能共用 `DbConnection`。

若要允許從外部提供 `DbConnection`，最簡單的方式就是停止使用 `DbContext.OnConfiguring` 方法來設定內容，然後從外部建立 `DbContextOptions` 並將其傳遞給內容建構函式。

> [!TIP]  
> `DbContextOptionsBuilder` 是您在 `DbContext.OnConfiguring` 中用來設定內容的 API，現在您將從外部使用它來建立 `DbContextOptions`。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

替代方法是繼續使用 `DbContext.OnConfiguring`，但接受 `DbConnection`，這會在儲存後於 `DbContext.OnConfiguring` 中使用。

``` csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a>共用連線和交易

您現在可以建立多個共用相同連線的內容執行個體。 然後使用 `DbContext.Database.UseTransaction(DbTransaction)` API 將兩個內容都登錄在同一個交易中。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>使用外部 DbTransactions (僅適用於關聯式資料庫)

如果您使用多個資料存取技術來存取關聯式資料庫，則可能會想要在這些不同技術所執行的作業之間共用交易。

下列範例示範如何在同一個交易中執行 ADO.NET SqlClient 作業和 Entity Framework Core 作業。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a>使用 System.Transactions

> [!NOTE]  
> 此功能是 EF Core 2.1 中的新功能。

如果您需要跨較大的範圍進行協調，可以使用環境交易。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

此外，也可以登錄在明確交易中。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a>System.Transactions 的限制  

1. EF Core 需倚賴資料庫提供者實作對 System.Transactions 的支援。 雖然支援在 .NET Framework 的 ADO.NET 提供者之間很常見，但此 API 是最近才新增至 .NET Core 中，因此支援尚不普遍。 如果提供者未實作對 System.Transactions 的支援，則對這些 API 發出的呼叫將可能完全被忽略。 .NET Core 的 SqlClient 從 2.1 版起便不支援它。 .NET Core 2.0 的 SqlClient 將會在您嘗試使用該功能時擲回例外狀況。

   > [!IMPORTANT]  
   > 建議您先測試該 API 是否可與您的提供者正確搭配運作，再倚賴它來管理交易。 如果無法正確搭配運作，建議您與資料庫提供者的維護人員連絡。

2. 從 2.1 版開始，.NET Core 中的 System.Transactions 實作便不包含對分散式交易的支援，因此您無法使用 `TransactionScope` 或 `CommittableTransaction` 來跨多個資源管理員協調交易。
