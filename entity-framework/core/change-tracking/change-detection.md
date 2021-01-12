---
title: 變更偵測和通知-EF Core
description: 使用 DetectChanges 或通知來偵測屬性和關聯性變更
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129682"
---
# <a name="change-detection-and-notifications"></a>變更偵測和通知

每個 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例都會追蹤對實體所做的變更。 接著，這些追蹤的實體會在呼叫時驅動資料庫的變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。 這涵蓋在 [EF Core 的變更追蹤](xref:core/change-tracking/index)中，本檔假設您瞭解 Entity Framework Core (EF Core) 變更追蹤的實體狀態和基本概念。

追蹤屬性和關聯性變更需要 DbCoNtext 能夠偵測這些變更。 本檔涵蓋此偵測的發生方式，以及如何使用屬性通知或變更追蹤 proxy 來強制立即偵測變更。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications)，以執行並偵測到本檔中的所有程式碼。

## <a name="snapshot-change-tracking"></a>快照集變更追蹤

根據預設，EF Core 會在第一次由 DbCoNtext 實例追蹤時，建立每個實體屬性值的快照集。 然後，此快照中儲存的值會與實體的目前值進行比較，以判斷哪些屬性值已變更。

當呼叫 SaveChanges 以確保在將更新傳送至資料庫之前偵測到所有變更的值時，就會發生這項變更的偵測。 不過，變更的偵測也會在其他時間發生，以確保應用程式能夠使用最新的追蹤資訊。 您可以藉由呼叫來隨時強制偵測變更 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 。

### <a name="when-change-detection-is-needed"></a>需要變更偵測時

在未 _使用 EF Core 進行這項變更的情況下_ 變更屬性或導覽時，需要進行變更的偵測。 例如，請考慮載入 blog 和 post，然後對這些實體進行變更：

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

在呼叫之前查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 顯示尚未偵測到所做的變更，因此不會反映在實體狀態和已修改的屬性資料中：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

具體而言，blog 專案的狀態仍然是 `Unchanged` ，而且新的貼文不會顯示為追蹤的實體。  (精明會注意到屬性會報告其新的值，即使 EF Core 尚未偵測到這些變更也是一樣。 這是因為 debug view 正在直接從實體實例讀取目前的值。 ) 

在呼叫 DetectChanges 之後，與 debug view 對比：

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

現在，已正確地將 blog 標記為 `Modified` ，並偵測到新文章，並將其追蹤為 `Added` 。

在本節一開始，我們說明不使用 _EF Core 來進行變更_ 時，需要偵測變更。 這是上述程式碼中發生的情況。 也就是說，屬性和導覽的變更會 _直接在實體實例上_ 進行，而不是使用任何 EF Core 方法。

將此與下列程式碼相比較，以相同方式修改實體，但這次使用 EF Core 方法：

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

在此情況下，變更追蹤器偵錯工具會顯示已知所有實體狀態和屬性修改，即使尚未進行變更的偵測也是一樣。 這是因為 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> 是 EF Core 方法，這表示 EF Core 立即知道這個方法所做的變更。 同樣地，呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 可讓 EF Core 立即知道新的實體並適當地進行追蹤。

> [!TIP]
> 請勿嘗試使用 EF Core 方法來進行實體變更，以避免偵測變更。 這麼做通常比較麻煩，而且執行的效能比一般方式變更實體還低。 本檔的目的是要在偵測到所需的變更時通知。 目的不是要鼓勵避免變更偵測。

### <a name="methods-that-automatically-detect-changes"></a>自動偵測變更的方法

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> 這可能會影響結果的方法會自動呼叫。 方法如下：

- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> ，確保在更新資料庫之前偵測到所有變更。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> ，確保實體狀態和修改的屬性都是最新的。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>，以確定結果正確無誤。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>，可確保主體/父實體的正確實體狀態，再進行階層級。
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>，以確保追蹤的圖表是最新的。

也有一些地方的偵測變更只會在單一實體實例上發生，而不是在整個追蹤實體的圖形上進行。 這些位置包括：

- 使用時 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> ，確保實體的狀態和已修改的屬性都是最新的。
- 使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> 、或等方法 `Property` `Collection` `Reference` `Member` 來確保屬性的修改、目前的值等都是最新的。
- 當相依/子系實體因為必要的關聯性被中斷而遭到刪除時。 這會偵測實體不應刪除的時間，因為它已重新成為父代。

您可以藉由呼叫來明確觸發單一實體之變更的本機偵測 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> 。

> [!NOTE]
> 本機偵測變更可能會遺失完整偵測會發現的一些變更。 當對其他實體未偵測到的變更所造成的串聯式動作影響到有問題的實體時，就會發生這種情況。 在這種情況下，應用程式可能需要明確地呼叫來強制執行所有實體的完整掃描 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 。

### <a name="disabling-automatic-change-detection"></a>停用自動變更偵測

偵測變更的效能不是大部分應用程式的瓶頸。 但是，偵測變更可能會成為追蹤上千個實體的部分應用程式的效能問題。  (確切的數位會視許多因素而定，例如實體中的屬性數目 ) 。基於這個原因，您可以使用來停用自動偵測變更 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> 。 例如，請考慮在多對多關聯性中處理與承載的聯結實體：

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

如我們在上一節中所知， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> 會自動偵測變更。 不過，在呼叫專案之後，程式碼不會進行任何實體或屬性狀態變更。  (在新增的實體上設定一般屬性值不會造成任何狀態變更。 ) 程式碼時，就會在向下呼叫基底 SaveChanges 方法時，停用不必要的自動變更偵測。 程式碼也會使用 try/finally 區塊，以確保即使 SaveChanges 失敗，也會還原預設設定。

> [!TIP]
> 請勿假設您的程式碼必須停用自動變更偵測才能正常執行。 只有當程式碼剖析應用程式追蹤有許多實體指出變更偵測的效能是問題時，才需要這麼做。

### <a name="detecting-changes-and-value-conversions"></a>偵測變更和值轉換

若要使用具有實體類型的快照集變更追蹤，EF Core 必須能夠：

- 追蹤實體時，建立每個屬性值的快照集
- 將此值與屬性的目前值進行比較
- 產生值的雜湊碼

這會由可直接對應至資料庫之類型的 EF Core 自動處理。 不過，當 [使用值轉換器來對應屬性](xref:core/modeling/value-conversions)時，該轉換器必須指定如何執行這些動作。 這可透過值比較子來達成，並在 [值](xref:core/modeling/value-comparers) 比較子檔中詳細說明。

## <a name="notification-entities"></a>通知實體

建議針對大部分的應用程式使用快照集變更追蹤。 不過，追蹤許多實體及/或對這些實體進行許多變更的應用程式，可能會受益于執行實體，這些實體會在其屬性和導覽值變更時自動通知 EF Core。 這些稱為「通知實體」。

### <a name="implementing-notification-entities"></a>執行通知實體

通知實體 <xref:System.ComponentModel.INotifyPropertyChanging> 會使用和 <xref:System.ComponentModel.INotifyPropertyChanged> 介面，這些都是 .net 基類庫 (BCL) 的一部分。 這些介面會定義在變更屬性值之前和之後必須引發的事件。 例如：

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

此外，任何集合導覽都必須執行 `INotifyCollectionChanged` ; 在上述範例中，您可以使用貼文來滿足這項工作 <xref:System.Collections.ObjectModel.ObservableCollection%601> 。 EF Core 也 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> 會隨附具有更有效率查詢的實作為穩定順序的成本。

大部分的通知程式碼通常都會移到未對應的基類中。 例如：

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a>設定通知實體

沒有方法可 EF Core 驗證， `INotifyPropertyChanging` 或 `INotifyPropertyChanged` 完全實作為與 EF Core 搭配使用。 尤其是，這些介面的某些用途只會在某些屬性上使用通知，而不是所有屬性 (包括 EF Core 所需的導覽) 。 基於這個理由，EF Core 不會自動連接到這些事件。

相反地，EF Core 必須設定為使用這些通知實體。 這通常是透過呼叫來針對所有實體類型進行 <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> 。 例如：

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

 (也可以使用不同的實體類型來設定不同的策略 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> ，但這通常是這些的，因為那些不是通知實體的類型仍需要 DetectChanges ) 。

完整通知變更追蹤需要同時 `INotifyPropertyChanging` 執行和 `INotifyPropertyChanged` 。 這可讓您在屬性值變更之前儲存原始值，避免在追蹤實體時 EF Core 建立快照集的需求。 執行的實體類型 `INotifyPropertyChanged` 也可以搭配 EF Core 使用。 在這種情況下，當追蹤實體以追蹤原始值時，EF 仍會建立快照集，但接著會使用通知立即偵測變更，而不需要呼叫 DetectChanges。

<xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy>下表摘要說明不同的值。

| ChangeTrackingStrategy                              | 需要的介面                                      | 需要 DetectChanges | 快照集原始值
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| 快照式                                            | 無                                                   | 是                 | 是
| ChangedNotifications                                | INotifyPropertyChanged                                 | 否                  | 是
| ChangingAndChangedNotifications                     | INotifyPropertyChanged 和 INotifyPropertyChanging     | 否                  | 否
| ChangingAndChangedNotificationsWithOriginalValues   | INotifyPropertyChanged 和 INotifyPropertyChanging     | 否                  | 是

### <a name="using-notification-entities"></a>使用通知實體

通知實體的行為就像任何其他實體，但對實體實例所做的變更不需要呼叫來偵測 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 這些變更。 例如：

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

使用一般實體時， [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式會顯示在呼叫 DetectChanges 之前，不會偵測到這些變更。 使用通知實體時查看 [偵錯工具]，會顯示已立即偵測到這些變更：

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a>變更追蹤 proxy

> [!NOTE]
> EF Core 5.0 引進了變更追蹤 proxy。

EF Core 可以動態產生執行和的 proxy <xref:System.ComponentModel.INotifyPropertyChanging> 類型 <xref:System.ComponentModel.INotifyPropertyChanged> 。 這需要安裝 [Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet 套件，並啟用變更追蹤 proxy， <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

建立動態 proxy 牽涉到使用 [Castle](https://www.nuget.org/packages/Castle.Core/) 來建立新的動態 .net 型別 () ，它繼承自實體型別，然後覆寫所有屬性 setter。 因此，proxy 的實體類型必須是可繼承的型別，而且必須具有可覆寫的屬性。 此外，明確建立的集合導覽必須執行 <xref:System.Collections.Specialized.INotifyCollectionChanged> 下列範例：

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

變更追蹤 proxy 有一個重大的缺點，那就是 EF Core 必須一律追蹤 proxy 的實例，而不是基礎實體類型的實例。 這是因為基礎實體類型的實例不會產生通知，這表示將會遺漏對這些實體所做的變更。

EF Core 在查詢資料庫時自動建立 proxy 實例，所以這個缺點通常僅限於追蹤新的實體實例。 這些實例必須使用 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> 擴充方法建立，而 **不** 是使用一般的方式 `new` 。 這表示之前範例中的程式碼現在必須使用 `CreateProxy` ：

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a>變更追蹤事件

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType>當第一次追蹤實體時，EF Core 會引發事件。 未來的實體狀態變更會產生 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> 事件。 如需詳細資訊，請參閱 [EF Core 中的 .Net 事件](xref:core/logging-events-diagnostics/events) 。

> [!NOTE]
> `StateChanged`第一次追蹤實體時不會引發事件，即使狀態已從變更 `Detached` 為其中一個其他狀態也一樣。 請務必接聽 `StateChanged` 和 `Tracked` 事件，以取得所有相關的通知。
