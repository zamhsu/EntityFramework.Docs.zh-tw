---
title: EF Core 3.0 的中斷性變更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 7ed55d4cae36f6b25059a5b218db4b0d5e2fb266
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419740"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a>EF Core 3.0 (目前為預覽版) 包含的中斷性變更

> [!IMPORTANT]
> 請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。

當您將針對 EF Core 2.2.x 開發的應用程式升級為 3.0.0 時，下列 API 和行為變更可能會中斷這些應用程式。
這些變更預期只會影響[提供者變更](../../providers/provider-log.md)底下記載的資料庫提供者。
本文不會記載從某一 3.0 預覽版引進另一個 3.0 預覽版的新功能中斷。

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>不會再於用戶端評估 LINQ 查詢

[追蹤問題 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另請參閱問題 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

這項變更將於 EF Core 3.0-preview 4 中引進。

**舊行為**

3.0 以前，在 EF Core 無法將屬於查詢的運算式轉換成 SQL 或參數時，它會自動在用戶端評估運算式。
根據預設，對可能相當耗費資源的運算式進行用戶端評估只會觸發警告。

**新行為**

從 3.0 開始，EF Core 只允許在用戶端評估最上層投影的運算式 (查詢中的最後一個 `Select()` 呼叫)。
當其他查詢部分中的運算式無法轉換成 SQL 或參數時，則會擲回例外狀況。

**原因**

查詢的自動用戶端評估可執行許多查詢，即使無法轉譯查詢的重要部分也一樣。
此行為可能會導致非預期且可能造成傷害的行為，而且該行為可能只會出現在生產環境中。
例如，`Where()` 呼叫中無法轉譯的條件可能會導致資料表中的所有資料列從資料庫伺服器移轉，並在用戶端套用篩選。
如果資料表只包含幾個開發中的資料列，此情況可能很容易未被察覺；但當應用程式移至生產環境時，由於資料表可能包含數百萬個資料列，因此影響會很大。
用戶端評估警告也證明很容易在開發期間遭到忽略。

此外，自動用戶端評估可能會導致改善特定運算式的查詢轉譯造成版本間非預期的中斷性變更問題。

**風險降低**

如果無法完整轉譯查詢，請以可轉譯的格式來重寫查詢，或是使用 `AsEnumerable()`、`ToList()` 或類似函數來明確將資料帶回用戶端，以便接著使用 LINQ-to-Objects 加以處理。

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core 不再屬於 ASP.NET Core 共用架構

[追蹤問題 Announcements#325](https://github.com/aspnet/Announcements/issues/325)

這項變更已於 ASP.NET Core 3.0-preview 1 推出。 

**舊行為**

在 ASP.NET Core 3.0 以前，當您新增 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 的套件參考時，它會包含 EF Core 及部分 EF Core 資料提供者 (例如 SQL Server 提供者)。

**新行為**

從 3.0 開始，ASP.NET Core 共用架構不會包含 EF Core 或任何 EF Core 資料提供者。

**原因**

在這項變更之前，取得 EF Core 會根據應用程式是否以 ASP.NET Core 和 SQL Server 為目標而需要不同的步驟。 此外，升級 ASP.NET Core 會強制升級 EF Core 和 SQL Server 提供者，這不一定符合需求。

透過這項變更，取得 EF Core 的體驗對所有提供者、支援的 .NET 實作和應用程式類型都相同。
開發人員現在也可以精確控制何時升級 EF Core 和 EF Core 資料提供者。

**風險降低**

若要在 ASP.NET Core 3.0 應用程式或其他支援的應用程式中使用 EF Core，請明確將套件參考加入應用程式會使用的 EF Core 資料庫提供者。

## <a name="query-execution-is-logged-at-debug-level"></a>查詢執行會在 Debug 層級記錄

[追蹤問題 #14523](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，查詢和其他命令的執行會在 `Info` 層級記錄。

**新行為**

從 EF Core 3.0 開始，命令/SQL 執行的記錄是在 `Debug` 層級。

**原因**

這項變更的目的是為了減少 `Info` 記錄層級的干擾。

**風險降低**

此記錄事件是由 `RelationalEventId.CommandExecuting` 定義，事件識別碼為 20100。
若要重新在 `Info` 層級記錄 SQL，請明確在 `OnConfiguring` 或 `AddDbContext` 中設定層級。
例如：
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>實體執行個體上不會再設定暫存索引鍵值

[追蹤問題 #12378](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

這項變更已於 EF Core 3.0-preview 2 推出。

**舊行為**

在 EF Core 3.0 以前，會對所有索引鍵屬性指派暫存值，這些屬性稍後會有資料庫產生的實值。
這些暫存值通常是龐大的負值。

**新行為**

從 3.0 開始，EF Core 會將暫存索引鍵值儲存為實體追蹤資訊的一部分，至於索引鍵屬性本身則保持不變。

**原因**

這項變更的目的是為了防止在將某個 `DbContext` 執行個體先前追蹤的實體移至不同的 `DbContext` 執行個體時，錯誤地把暫存索引鍵值變成永久值。 

**風險降低**

若應用程式會將主索引鍵指派給外部索引鍵以形成實體間關聯，則可能會在主索引鍵是由存放區產生並屬於 `Added` 狀態的實體時採用舊行為。
這可透過下列方式來避免：
* 不使用存放區產生的索引鍵。
* 設定導覽屬性以形成關聯性，而不是設定外部索引鍵值。
* 從實體的追蹤資訊取得實際暫存索引鍵值。
例如，`context.Entry(blog).Property(e => e.Id).CurrentValue` 會傳回暫存值，即使尚未設定 `blog.Id` 本身也一樣。

## <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges 接受存放區產生的索引鍵值

[追蹤問題 #14616](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，`DetectChanges` 所發現未被追蹤的實體會以 `Added` 狀態追蹤，並在呼叫 `SaveChanges` 時以新的資料列插入。

**新行為**

從 EF Core 3.0 開始，如果實體使用產生的索引鍵值並已設定一些索引鍵值，則實體會以 `Modified` 狀態追蹤。
這表示實體的資料列假設存在，而且會在呼叫 `SaveChanges` 時更新。
如果未設定索引鍵值，或者如果實體類型未使用產生的索引鍵，則新的實體仍會如同舊版以 `Added` 追蹤。

**原因**

這項變更的目的是為了更輕鬆一致地使用中斷連接的實體圖形，同時使用存放區產生的索引鍵。

**風險降低**

如果實體類型已設定為使用產生的索引鍵，但針對新的執行個體明確設定了索引鍵值，這項變更可能會中斷應用程式。
修正方法是明確設定索引鍵屬性不使用產生的值。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

或者，使用資料註解：

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a>現在預設會立即發生串聯刪除

[追蹤問題 #10114](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 3.0 以前，除非呼叫 SaveChanges，否則 EF Core 不會套用串聯動作 (刪除必要主體或提供必要主體關聯性時刪除相依實體)。

**新行為**

從 3.0 開始，EF Core 會在偵測到觸發條件時立即套用串聯動作。
例如，呼叫 `context.Remove()` 刪除主要實體會導致所有追蹤的相關必要相依項目也會立即設定為 `Deleted`。

**原因**

這項變更的目的是為了改善資料繫結和稽核情節的體驗，在這些情節中了解呼叫 `SaveChanges`「之前」將刪除哪些實體是很重要的。

**風險降低**

透過設定 `context.ChangedTracker` 可以還原舊行為。
例如：

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a>查詢類型會與實體類型合併

[追蹤問題 #14194](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，[查詢類型](xref:core/modeling/query-types)可讓您查詢未以結構化方式定義主索引鍵的資料。
換句話說，查詢類型是用於對應沒有索引鍵的實體類型 (較有可能來自檢視，但也有可能來自資料表)，而一般實體類型是用於索引鍵可供使用時 (較有可能來自資料表，但也有可能來自檢視)。

**新行為**

查詢類型現在會成為沒有主索引鍵的實體類型。
無索引鍵的實體類型功能與舊版查詢類型相同。

**原因**

這項變更的目的是為了降低查詢類型用途的混淆。
具體來說，它們是無索引鍵的實體類型，因此本質上是唯讀的，但不應該只因為實體類型必須是唯讀就使用。
同樣地，它們通常會對應至檢視，但這只是因為檢視通常未定義索引鍵。

**風險降低**

API 的下列組件現已淘汰：
* **`ModelBuilder.Query<>()`** - 必須改為呼叫 `ModelBuilder.Entity<>().HasNoKey()` 將實體類型標示為沒有索引鍵。
為了避免在必須有主索引鍵但不符合慣例時設定錯誤，目前仍未將此設定為慣例。
* **`DbQuery<>`** - 應改用 `DbSet<>`。
* **`DbContext.Query<>()`** - 應改用 `DbContext.Set<>()`。

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a>自有類型關聯性的設定 API 已變更

[追蹤問題 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[追蹤問題 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[追蹤問題 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，會在呼叫 `OwnsOne` 或 `OwnsMany` 之後直接執行自有關聯性的設定。 

**新行為**

從 EF Core 3.0 開始，現在會有 Fluent API 使用 `WithOwner()` 將導覽屬性設定為擁有者。
例如：

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

擁有者與自有之間關聯性的相關設定現在應該在 `WithOwner()` 之後鏈結，類似於其他關聯性的設定方式。
但自有類型本身的設定仍會在 `OwnsOne()/OwnsMany()` 之後鏈結。
例如：

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

此外，使用自有類型目標呼叫 `Entity()`、`HasOne()` 或 `Set()` 現在會擲回例外狀況。

**原因**

這項變更的目的是為了更清楚地劃分設定自有類型本身，以及設定自有類型的「關聯性」。
如此可避免 `HasForeignKey` 等方法的模稜兩可和混淆。

**風險降低**

將自有類型關聯性的設定變更為使用新的 API 介面，如上述範例所示。

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>外部索引鍵屬性慣例不會再比對與主體屬性相同的名稱

[追蹤問題 #13274](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

請考慮下列模型：
```C#
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}

```
在 EF Core 3.0 以前，`CustomerId` 屬性依照慣例會用於外部索引鍵。
不過，如果 `Order` 是自有類型，則這也會將 `CustomerId` 設為主索引鍵，而這通常不符合預期。

**新行為**

從 3.0 開始，如果屬性的名稱與主體屬性相同，依照慣例，EF Core 不會嘗試將屬性用於外部索引鍵。
但仍會比對與主體屬性名稱串連的主體類型名稱，以及與主體屬性名稱模式串連的導覽名稱。
例如：

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**原因**

這項變更的目的是為了避免錯誤地在自有類型上定義主索引鍵屬性。

**風險降低**

如果屬性預定會作為外部索引鍵，並因此成為主索引鍵的一部分，請明確進行這類設定。

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>每個屬性會使用獨立的記憶體內部整數索引鍵產生

[追蹤問題 #6872](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

這項變更將於 EF Core 3.0-preview 4 中引進。

**舊行為**

在 EF Core 3.0 以前，會針對所有記憶體內部整數索引鍵屬性使用一個共用值產生器。

**新行為**

從 EF Core 3.0 開始，當使用記憶體內部資料庫時，每個整數索引鍵屬性都會取得自己的值產生器。
此外，如果已刪除資料庫，則會重設所有資料表的索引鍵產生。

**原因**

這項變更的目的是為了讓記憶體內部索引鍵產生與實際資料庫索引鍵產生更加一致，並改善在使用記憶體內部資料庫時隔離個別測試的能力。

**風險降低**

這可能會中斷需要設定特定記憶體內部索引鍵值的應用程式。
請考慮改為不依賴特定索引鍵值，或更新以符合新行為。

## <a name="backing-fields-are-used-by-default"></a>預設會使用支援欄位

[追蹤問題 #12430](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

這項變更已於 EF Core 3.0-preview 2 推出。

**舊行為**

在 3.0 以前，即使屬性的支援欄位已知，EF Core 預設仍會使用屬性 getter 和 setter 方法來讀取和寫入屬性值。
例外是查詢執行，其中如果支援欄位已知，則會直接設定。

**新行為**

從 EF Core 3.0 開始，如果屬性的支援欄位已知，則一律會使用支援欄位來讀取和寫入該屬性。
如果應用程式需要將額外的行為編碼到 getter 或 setter 方法中，這可能會導致應用程式中斷。

**原因**

這項變更的目的是為了防止 EF Core 預設在執行涉及實體的資料庫作業時，錯誤地觸發商務邏輯。

**風險降低**

透過在 modelBuilder Fluent API 中設定屬性存取模式可以還原 3.0 以前的行為。
例如：

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>找到多個相容的支援欄位時擲回

[追蹤問題 #12523](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

這項變更將於 EF Core 3.0-preview 4 中引進。

**舊行為**

在 EF Core 3.0 以前，如果有多個欄位符合尋找屬性支援欄位的規則，則會根據特定優先順序來選擇一個欄位。
這可能會導致在模稜兩可的情況下使用錯誤的欄位。

**新行為**

從 EF Core 3.0 開始，如果有多個欄位符合相同的屬性，則會擲回例外狀況。

**原因**

這項變更的目的是為了避免在只能有一個正確欄位的情況下，自動使用某個欄位而非另一個欄位。

**風險降低**

若屬性的支援欄位模稜兩可，則必須明確指定要使用的欄位。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool 再也不會呼叫 AddLogging 與 AddMemoryCache

[追蹤問題 #14756](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

這項變更將於 EF Core 3.0-preview 4 中引進。

**舊行為**

在 EF Core 3.0 之前，呼叫 `AddDbContext` 或 `AddDbContextPool` 也會透過呼叫 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 與 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 來向 DI 註冊記錄與記憶體快取服務。

**新行為**

從 EF Core 3.0 開始，`AddDbContext` 與 `AddDbContextPool` 再也不會向相依性插入 (DI) 註冊這些服務。

**原因**

EF Core 3.0 不會要求這些服務必須存在於應用程式的 DI 容器中。 不過，若 `ILoggerFactory` 已在應用程式的 DI 容器中註冊，則它仍會被 EF Core 使用。

**風險降低**

若您的應用程式需要這些服務，請使用  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 明確地向 DI 容器註冊它們。

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry 現在會執行本機 DetectChanges

[追蹤問題 #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，呼叫 `DbContext.Entry` 會導致偵測所有追蹤實體的變更。
這可確保在 `EntityEntry` 中公開的狀態為最新狀態。

**新行為**

從 EF Core 3.0 開始，呼叫 `DbContext.Entry` 現在只會嘗試在指定實體及其相關的任何追蹤主要實體中偵測變更。
這表示呼叫此方法可能還無法偵測到其他位置的變更，因此可能會影響應用程式狀態。

請注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 設定為 `false`，甚至是此本機變更偵測都會停用。

其他導致變更偵測的方法 (例如 `ChangeTracker.Entries` 和 `SaveChanges`) 仍會對所有追蹤實體進行完整的 `DetectChanges`。

**原因**

這項變更的目的是為了改善使用 `context.Entry` 的預設效能。

**風險降低**

在呼叫 `Entry` 之前明確呼叫 `ChgangeTracker.DetectChanges()` 可確保 3.0 以前的行為。

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>字串和位元組陣列索引鍵預設不是由用戶端產生

[追蹤問題 #14617](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

這項變更將於 EF Core 3.0-preview 4 中引進。

**舊行為**

在 EF Core 3.0 以前，可以使用 `string` 和 `byte[]` 索引鍵屬性，而不需要明確設定非 Null 值。
在此情況下，會在用戶端以 GUID 形式產生索引鍵值，再序列化為 `byte[]` 的位元組。

**新行為**

從 EF Core 3.0 開始，系統會擲回例外狀況，指出尚未設定任何索引鍵值。

**原因**

這項變更是因為用戶端產生的 `string`/`byte[]` 值通常不太有用，而且預設行為使它很難以一般方式來推論產生的索引鍵值。

**風險降低**

藉由明確指定索引鍵屬性應該在未設定其他非 Null 值時使用產生的值，即可取得 3.0 以前的行為。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

或者，使用資料註解：

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory 現在是限定範圍的服務

[追蹤問題 #14698](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，`ILoggerFactory` 會註冊為單一服務。

**新行為**

從 EF Core 3.0 開始，`ILoggerFactory` 現在會註冊為限定範圍。

**原因**

這項變更的目的是為了允許記錄器與 `DbContext` 執行個體產生關聯，這可啟用其他功能，並避免某些異常行為案例，例如內部服務提供者遽增。

**風險降低**

這項變更不應影響應用程式程式碼，除非在 EF Core 內部服務提供者上使用自訂服務註冊該程式碼。
但這並不常見。
在這些情況下，大部分的項目仍會運作，但相依於 `ILoggerFactory` 的任何單一服務需要變更，才能以不同方式取得 `ILoggerFactory`。

如果您遇到上述情況，請在 [EF Core GitHub 問題追蹤器](https://github.com/aspnet/EntityFrameworkCore/issues)上提出問題，讓我們知道您使用 `ILoggerFactory` 的方式，以便進一步了解未來如何才不會再次中斷。

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a>IDbContextOptionsExtensionWithDebugInfo 已合併到 IDbContextOptionsExtension 中

[追蹤問題 #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

`IDbContextOptionsExtensionWithDebugInfo` 是從 `IDbContextOptionsExtension` 擴充的額外選擇性介面，以避免在 2.x 發行週期內對介面進行中斷性變更。

**新行為**

這些介面現在會一起合併到 `IDbContextOptionsExtension` 中。

**原因**

這項變更是因為這些介面在概念上是一個介面。

**風險降低**

您必須更新 `IDbContextOptionsExtension` 的所有實作，才能支援新成員。

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>消極式載入 Proxy 停止假設導覽屬性已完全載入

[追蹤問題 #12780](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

這項變更將於 EF Core 3.0-preview 4 中引進。

**舊行為**

在 EF Core 3.0 以前，一旦處置 `DbContext` 之後，就無從得知實體上取自該內容的指定導覽屬性是否已完全載入。
Proxy 會改為假設如有非 Null 值，會載入參考導覽；如果不是空的，則會載入集合導覽。
在這些情況下，嘗試消極式載入不會執行任何作業。

**新行為**

從 EF Core 3.0 開始，Proxy 會追蹤是否載入導覽屬性。
這表示嘗試存取在處置內容之後載入的導覽屬性一律不會執行任何作業，即使已載入的導覽是空的或 Null 也一樣。
相反地，如果在處置內容之後嘗試存取未載入的導覽屬性，則會擲回例外狀況，即使導覽屬性不是空集合也一樣。
如果發生這種情況，則表示應用程式程式碼嘗試在無效的時間使用消極式載入，應用程式應該變更為不要這麼做。

**原因**

這項變更的目的是為了在已處置的 `DbContext` 執行個體上嘗試消極式載入時，使行為一致且正確。

**風險降低**

將應用程式程式碼更新為不要嘗試對已處置的內容進行消極式載入，或將此設定為不執行任何作業，如例外狀況訊息中所述。

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>過度建立內部服務提供者現在預設是錯誤

[追蹤問題 #10236](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，當應用程式建立異常數目的內部服務提供者時，會記錄一則警告。

**新行為**

從 EF Core 3.0 開始，此警告現在會視為錯誤，並會擲回例外狀況。 

**原因**

這項變更的目的是為了透過更明確公開此異常案例，藉以開發更完善的應用程式程式碼。

**風險降低**

遇到此錯誤時的最適當動作是了解根本原因，並停止建立這麼多的內部服務提供者。
不過，透過設定 `DbContextOptionsBuilder` 可以將錯誤轉換回警告。
例如：

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>使用單一字串呼叫之 HasOne/HasMany 的新行為

[追蹤問題 #9171](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

這項變更將於 EF Core 3.0-preview 4 中引進。

**舊行為**

在 EF Core 3.0 之前，使用單一字串呼叫 `HasOne` 或 `HasMany` 的程式碼會以令人困惑的方式解譯。
例如：
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

程式碼看起來像是它使用 `Entrance` 瀏覽屬性將 `Samurai` 與一些其他實體類型相關，這可能是私用屬性。

在現實中，此程式碼會在不使用瀏覽屬性的情況下嘗試建立與一些實體 (稱為 `Entrance`) 的關係。

**新行為**

從 EF Core 3.0 開始，上述程式碼現在會執行像以前一樣的動作。

**原因**

舊行為令人非常困惑，特別是當讀取設定程式碼與尋找錯誤時。

**風險降低**

這只會造成已明確針對類型名稱使用字串設定關係，而未明確指定瀏覽屬性的應用程式中斷。
這不是常見情況。
先前的行為可透過明確地傳遞瀏覽屬性名稱的 `null` 來取得。
例如：

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>Relational:TypeMapping 註解現僅為 TypeMapping

[追蹤問題 #9913](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

這項變更已於 EF Core 3.0-preview 2 推出。

**舊行為**

類型對應註解的註解名稱之前是 "Relational:TypeMapping"。

**新行為**

類型對應註解的註解名稱現在是 "TypeMapping"。

**原因**

類型對應現在不只用於關聯式資料庫提供者。

**風險降低**

這只會中斷直接將類型對應當做註解存取的應用程式，但這並不常見。
最適當的修正動作是使用 API 介面存取類型對應，而不是直接使用註解。

## <a name="totable-on-a-derived-type-throws-an-exception"></a>衍生類型上的 ToTable 會擲回例外狀況 

[追蹤問題 #11811](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，會忽略衍生類型上呼叫的 `ToTable()`，因為唯一的繼承對應策略是對此案例無效的 TPH。 

**新行為**

從 EF Core 3.0 開始，以及在更新版本中準備新增 TPT 和 TPC 支援時，在衍生類型上呼叫的 `ToTable()` 現在會擲回例外狀況，以避免未來發生非預期的對應變更。

**原因**

目前無法將衍生類型對應至不同的資料表。
這項變更可避免未來有效執行時的中斷情況。

**風險降低**

避免嘗試將衍生類型對應至其他資料表。

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex 已取代為 HasIndex 

[追蹤問題 #12366](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，`ForSqlServerHasIndex().ForSqlServerInclude()` 可讓您設定搭配 `INCLUDE` 使用的資料行。

**新行為**

從 EF Core 3.0 開始，關聯式層級現在支援對索引使用 `Include`。
使用 `HasIndex().ForSqlServerInclude()`。

**原因**

這項變更的目的是為了能夠使用 `Includes` 將所有資料庫提供者的索引 API 合併到一個位置。

**風險降低**

使用新的 API，如上所示。

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core 不會再傳送 SQLite FK 強制的 pragma

[追蹤問題 #12151](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

這項變更已於 EF Core 3.0-preview 3 推出。

**舊行為**

在 EF Core 3.0 以前，當開啟 SQLite 連線時，EF Core 會傳送 `PRAGMA foreign_keys = 1`。

**新行為**

從 EF Core 3.0 開始，當開啟 SQLite 連線時，EF Core 不會再傳送 `PRAGMA foreign_keys = 1`。

**原因**

這項變更是因為 EF Core 預設會使用 `SQLitePCLRaw.bundle_e_sqlite3`，這也表示預設會開啟 FK 強制，而不需要在每次開啟連線時明確啟用。

**風險降低**

根據預設，會在預設用於 EF Core 的 SQLitePCLRaw.bundle_e_sqlite3 中啟用外部索引鍵。
在其他情況下，則可以藉由在您的連接字串中指定 `Foreign Keys=True` 來啟用外部索引鍵。

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite 現在相依於 SQLitePCLRaw.bundle_e_sqlite3

**舊行為**

在 EF Core 3.0 以前，EF Core 會使用 `SQLitePCLRaw.bundle_green`。

**新行為**

從 EF Core 3.0 開始，EF Core 會使用 `SQLitePCLRaw.bundle_e_sqlite3`。

**原因**

這項變更的目的是為了讓用於 iOS 的 SQLite 版本與其他平台一致。

**風險降低**

若要在 iOS 上使用原生 SQLite 版本，請設定 `Microsoft.Data.Sqlite` 使用不同的 `SQLitePCLRaw` 套件組合。

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>GUID 值現在於 SQLite 上的儲存形式為 TEXT

[追蹤問題 #15078](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

這項變更已於 EF Core 3.0-preview 4 推出。

**舊行為**

GUID 值先前在 SQLite 上的儲存形式為 BLOB 值。

**新行為**

GUID 值現在的儲存形式為 TEXT。

**原因**

GUID 的二進位格式未標準化。 以 TEXT 的形式儲存值會提高資料庫與其他技術的相容性。

**風險降低**

您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite 依然可以同時從 BLOB 及 TEXT 資料行讀取 GUID 值；但因為參數和常數的預設格式已變更，所以您可能需要對多數涉及 GUID 的案例採取動作。

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Char 值現在於 SQLite 上會儲存為文字

[追蹤問題 #15020](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

這項變更已於 EF Core 3.0-preview 4 推出。

**舊行為**

Char 值原先在 SQLite 上儲存為整數值。 舉例來說，char 值 *A* 原先會儲存為整數值 65。

**新行為**

Char 值現在會儲存為 TEXT。

**原因**

將值儲存為 TEXT 不但更加自然，也使資料庫與其他技術的相容性更高。

**風險降低**

您可以參考以下方式執行 SQL，來將現有的資料庫移轉至新的格式。

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

在 EF Core 中，您也可以在這些屬性上設定值轉換器來繼續使用原本的行為。

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite 也保留了讀取 INTEGER 和 TEXT 欄位字元值的功能，所以部分案例可能不需要任何動作。

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>移轉識別碼現在會使用不因文化特性而異的行事曆產生

[追蹤問題 #12978](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

這項變更已於 EF Core 3.0-preview 4 推出。

**舊行為**

移轉識別碼原先會使用目前文化特性 (Culture) 的行事曆產生。

**新行為**

移轉識別碼現在一律會使用不因文化特性而異的行事曆 (西曆) 產生。

**原因**

更新資料庫或解決合併衝突時，移轉的順序相當重要。 使用無差異的行事曆可避免順序問題，使小組成員系統行事曆不同的問題不會發生。

**風險降低**

這項變更會影響年份大於西曆行事曆的非西曆行事曆使用者 (例如泰國佛曆)。 現有的移轉識別碼必須更新，以使新的移轉會在現有的移轉之後排序。

您可在移轉設計工具檔案的移轉屬性中找到移轉識別碼。

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

移轉歷程記錄資料表也必須更新。

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>已重新命名 LogQueryPossibleExceptionWithAggregateOperator

[追蹤問題 #10985](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

這項變更已於 EF Core 3.0-preview 4 推出。

**變更**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` 已經重新命名為 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。

**原因**

使這個警告事件的命名與其他所有警告事件一致。

**風險降低**

使用新的名稱。 (注意，事件識別碼未變更。)

## <a name="clarify-api-for-foreign-key-constraint-names"></a>讓 API 的外部索引鍵限制式名稱更清楚

[追蹤問題 #10730](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

這項變更已於 EF Core 3.0-preview 4 推出。

**舊行為**

在 EF Core 3.0 前，外部索引鍵限制式名稱僅為 "name"。 例如：

```C#
var constraintName = myForeignKey.Name;
```

**新行為**

從 EF Core 3.0 開始，外部索引鍵限制式名稱現為 "constaint name"。 例如：

```C#
var constraintName = myForeignKey.ConstraintName;
```

**原因**

這項變更可讓此領域中的命名一致，同時清楚指出這是外部索引鍵限制式的名稱，而非定義外部索引鍵的資料行或屬性名稱。

**風險降低**

使用新的名稱。
