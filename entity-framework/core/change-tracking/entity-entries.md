---
title: 存取追蹤的實體-EF Core
description: 使用 Entityentry.state、DbCoNtext 和 DbSet 存取追蹤的實體
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129683"
---
# <a name="accessing-tracked-entities"></a>存取追蹤的實體

有四個主要的 Api 可存取所追蹤的實體 <xref:Microsoft.EntityFrameworkCore.DbContext> ：

- <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>傳回指定之實體實例的實例。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>傳回所有追蹤實體的實例，或針對指定類型的所有已追蹤實體。
- <xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 和 <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> 依主鍵尋找單一實體，先查看追蹤的實體，然後視需要查詢資料庫。
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 針對 DbSet 所代表之實體類型的實體，傳回 (非 Entityentry.state) 實例的實際實體。

以下各節將更詳細地說明每一個。

> [!TIP]
> 本檔假設您已瞭解實體狀態以及 EF Core 變更追蹤的基本概念。 如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities)，以執行並偵測到本檔中的所有程式碼。

## <a name="using-dbcontextentry-and-entityentry-instances"></a>使用 DbCoNtext 和 Entityentry.state 實例

針對每個追蹤的實體，Entity Framework Core (EF Core) 追蹤：

- 實體的整體狀態。 這是、、 `Unchanged` 或中的其中一個 `Modified` `Added` `Deleted` ; 如需詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。
- 追蹤實體之間的關聯性。 例如，貼文所屬的 blog。
- 屬性的「目前值」。
- 當此資訊可用時，屬性的「原始值」。 原始值是在從資料庫查詢實體時所存在的屬性值。
- 自查詢之後，哪些屬性值已經過修改。
- 屬性值的其他相關資訊，例如值是否為 [暫時性](xref:core/change-tracking/miscellaneous#temporary-values)。

傳遞實體實例，以 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 提供對指定實體之這項資訊的存取權。 例如：

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

下列各節顯示如何使用 Entityentry.state 來存取和操作實體狀態，以及實體屬性和導覽的狀態。

### <a name="working-with-the-entity"></a>使用實體

最常見的用法 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 是存取實體的目前 <xref:Microsoft.EntityFrameworkCore.EntityState> 。 例如：

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

Entry 方法也可以用於尚未追蹤的實體。 這不 _會開始追蹤實體_;實體的狀態仍是 `Detatched` 。 不過，傳回的 Entityentry.state 可接著用來變更實體狀態，此時實體將會在給定狀態中追蹤。 例如，下列程式碼將會開始追蹤 Blog 實例，如下所示 `Added` ：

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> 不同于 EF6，設定個別實體的狀態並不會造成所有連接的實體被追蹤。 這會讓以這種方式將狀態設定為較低層級的作業，而不是呼叫 `Add` 、 `Attach` 或，這會 `Update` 在實體的整個圖形上操作。

下表摘要說明使用 Entityentry.state 來處理整個實體的方式：

| Entityentry.state 成員                                                                                         | 描述
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | 取得和設定 <xref:Microsoft.EntityFrameworkCore.EntityState> 實體的。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | 取得實體實例。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <xref:Microsoft.EntityFrameworkCore.DbContext>正在追蹤此實體的。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> 實體類型的中繼資料。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | 是否已設定實體的索引鍵值。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | 使用從資料庫讀取的值來覆寫屬性值。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | 只強制偵測此實體的變更;請參閱 [變更偵測和通知](xref:core/change-tracking/change-detection)。

### <a name="working-with-a-single-property"></a>使用單一屬性

的數個多載 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> 允許存取實體之個別屬性的相關資訊。 例如，使用強型別、流暢的 API：

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

屬性名稱可以改以字串的形式傳遞。 例如：

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

然後， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> 會使用傳回的來存取屬性的相關資訊。 例如，它可以用來取得及設定此實體上屬性的目前值：

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

以上所使用的兩個屬性方法都會傳回強型別泛型 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> 實例。 偏好使用此泛型型別是因為它允許存取屬性值，而不需要對實值型別進行 [裝箱](/dotnet/csharp/programming-guide/types/boxing-and-unboxing)。 但是，如果在編譯時期不知道實體或屬性的類型，則 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> 可以改為取得非泛型：

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

這可讓您存取任何屬性的屬性資訊（不論其型別為何），但會犧牲實值型別。 例如：

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

下表摘要說明 PropertyEntry 所公開的屬性資訊：

| PropertyEntry 成員                               | 描述
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | 取得和設定屬性的目前值。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | 取得和設定屬性的原始值（如果有的話）。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | 實體的反向參考 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> 屬性的中繼資料。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | 指出此屬性是否標示為已修改，並允許變更此狀態。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | 指出此屬性是否標記為 [暫存](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)，並允許變更此狀態。

注意：

- 屬性的原始值是在從資料庫查詢實體時，該屬性所擁有的值。 但是，如果實體已中斷連接，然後明確附加至另一個 DbCoNtext （例如，使用或），則無法使用原始值 `Attach` `Update` 。 在此情況下，傳回的原始值將會與目前的值相同。
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 只會更新標示為已修改的屬性。 設定 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> 為 true 以強制 EF Core 更新指定的屬性值，或將它設定為 false，以防止 EF Core 更新屬性值。
- [暫存值](xref:core/change-tracking/miscellaneous) 通常是由 EF Core [值](xref:core/modeling/generated-properties)產生器所產生。 設定屬性的目前值會將暫存值取代為指定的值，並將該屬性標示為不是暫時性的。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary>如果設定為 true，則即使在明確設定值之後，也會強制將值設為暫時性。

### <a name="working-with-a-single-navigation"></a>使用單一導覽

、和的數個多載 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> 可讓您存取個別流覽的相關資訊。

透過方法存取單一相關實體的參考導覽 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> 。 參考導覽指向一對多關聯性的「一」側邊，以及一對一關聯性的兩端。 例如：

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

當針對一對多和多對多關聯性的「多」端使用時，導覽也可以是相關實體的集合。 這些 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> 方法可用來存取集合導覽。 例如：

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

某些作業對所有導覽都很常見。 您可以使用方法來存取參考和集合導覽 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> 。 請注意，同時存取所有導覽時，只會提供非一般存取。 例如：

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

下表摘要說明使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> 、和的方式 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> ：

| NavigationEntry 成員                                                                                    | 描述
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | 取得和設定導覽的目前值。 這是集合導覽的整個集合。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> 導覽的中繼資料。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | 取得或設定值，這個值表示是否已從資料庫完全載入相關實體或集合。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | 從資料庫載入相關的實體或集合;請參閱 [明確載入相關資料](xref:core/querying/related-data/explicit)。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | 查詢 EF Core 會使用將此導覽載入為 `IQueryable` 可進一步組成的，請參閱 [明確載入相關資料](xref:core/querying/related-data/explicit)。

### <a name="working-with-all-properties-of-an-entity"></a>使用實體的所有屬性

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> 針對實體的每個屬性，傳回的。 這可以用來對實體的每個屬性執行動作。 例如，若要將任何 DateTime 屬性設定為 `DateTime.Now` ：

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

此外，Entityentry.state 還包含數個方法，可同時取得和設定所有屬性值。 這些方法會使用 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> 類別，此類別代表屬性及其值的集合。 您可以取得目前或原始值的 PropertyValues，或目前儲存在資料庫中的值。 例如：

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

這些 PropertyValues 物件並不太實用。 不過，它們可以合併以執行操作實體時所需的一般作業。 當使用資料傳輸物件以及解決 [開放式平行存取衝突](xref:core/saving/concurrency)時，這非常有用。 下列各節顯示一些範例。

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a>從實體或 DTO 設定目前或原始的值

您可以從另一個物件複製值，藉以更新實體的目前或原始值。 例如，假設有一個 `BlogDto` 資料傳輸物件 (DTO) 具有與實體類型相同的屬性：

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

這可以用來設定追蹤實體目前的值 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> ：

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

使用從服務呼叫取得的值或多層式應用程式中的用戶端來更新實體時，有時會使用這項技術。 請注意，使用的物件不一定要與實體具有相同的類型，只要它的屬性名稱符合實體的名稱即可。 在上述範例中，會使用 DTO 的實例 `BlogDto` 來設定追蹤實體的目前值 `Blog` 。

請注意，如果設定的值與目前的值不同，屬性就會標示為已修改。

#### <a name="setting-current-or-original-values-from-a-dictionary"></a>從字典設定目前或原始的值

先前的範例會從實體或 DTO 實例設定值。 當屬性值在字典中儲存為名稱/值組時，就可以使用相同的行為。 例如：

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a>從資料庫設定目前或原始的值

您可以藉由呼叫 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> 或 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> 並使用傳回的物件來設定目前或原始值，或同時使用傳回的物件，從資料庫的最新值更新實體的目前或原始值。 例如：

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>建立包含目前、原始或資料庫值的複製物件

從 CurrentValues、OriginalValues 或 GetDatabaseValues 傳回的 PropertyValues 物件，可以用來建立使用之實體的複製 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> 。 例如：

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

請注意， `ToObject` 會傳回 DbCoNtext 不會追蹤的新實例。 傳回的物件也沒有任何設定為其他實體的關聯性。

複製的物件有助於解決與資料庫並行更新相關的問題，尤其是當資料系結至特定類型的物件時。 如需詳細資訊，請參閱 [開放式並行](xref:core/saving/concurrency) 存取。

### <a name="working-with-all-navigations-of-an-entity"></a>使用實體的所有導覽

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> 針對每個實體導覽，傳回的。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> 並 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> 進行相同的動作，但僅限於參考或集合導覽。 這可以用來對實體的每個導覽執行動作。 例如，若要強制載入所有相關實體：

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a>使用實體的所有成員

一般屬性和導覽屬性有不同的狀態和行為。 因此，通常會分開處理導覽和非導覽，如上述各節所示。 但是，無論實體的成員是一般屬性或導覽，有時也會很有用。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType>提供此用途。 例如：

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

在範例的 blog 上執行此程式碼會產生下列輸出：

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> [ [變更追蹤](xref:core/change-tracking/debug-views) 器] 偵錯工具會顯示如下的資訊。 整個變更追蹤器的偵錯工具是從 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> 每個追蹤實體的個體產生的。

## <a name="find-and-findasync"></a>尋找和 FindAsync

<xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 和的 <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> 設計目的，是為了在已知單一實體的主要索引鍵時有效率地查閱。 Find 會先檢查是否已追蹤實體，如果是，則會立即傳回該實體。 只有在未在本機追蹤實體時，才會進行資料庫查詢。 例如，請考慮下列程式碼，此程式碼會針對相同的實體呼叫兩次尋找：

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

使用 SQLite 時，此程式碼 (包括 EF Core 記錄) 的輸出為：

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

請注意，第一次呼叫在本機找不到實體，因此會執行資料庫查詢。 相反地，第二個呼叫會傳回相同的實例，而不會查詢資料庫，因為它已經被追蹤。

如果未在本機追蹤具有指定索引鍵的實體，而且不存在於資料庫中，則 Find 會傳回 null。

### <a name="composite-keys"></a>複合索引鍵

Find 也可以搭配複合索引鍵使用。 例如，假設有一個 `OrderLine` 具有由訂單識別碼和產品識別碼組成之複合索引鍵的實體：

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

您必須在中設定複合索引鍵， <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> 以定義主要部分 _及其順序_。 例如：

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

請注意， `OrderId` 是金鑰的第一個部分，而 `ProductId` 是索引鍵的第二個部分。 傳遞要尋找的索引鍵值時，必須使用此順序。 例如：

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a>使用 ChangeTracker 存取所有已追蹤的實體

到目前為止，我們一次只存取了一個 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> 。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> 傳回 DbCoNtext 目前追蹤之每個實體的 Entityentry.state。 例如：

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

這會程式碼產生下列輸出：

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

請注意，系統會傳回 blog 和 post 的專案。 您可以改用泛型多載，將結果篩選成特定的實體類型 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> ：

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

這段程式碼的輸出顯示只會傳回文章：

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

此外，使用泛型多載會傳回泛型 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 實例。 這可讓您 `Id` 在此範例中，對屬性進行類似的存取。

用於篩選的泛型型別不需要是對應的實體類型;您可以改為使用未對應的基底類型或介面。 例如，如果模型中的所有實體類型都執行定義其索引鍵屬性的介面：

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

然後，您可以使用這個介面，以強型別方式來處理任何追蹤實體的索引鍵。 例如：

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a>使用 DbSet 來查詢追蹤的實體

EF Core 查詢一律會在資料庫上執行，而且只會傳回已儲存至資料庫的實體。 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 提供一種機制，可查詢本機、追蹤實體的 DbCoNtext。

因為 `DbSet.Local` 是用來查詢追蹤的實體，所以通常會將實體載入 DbCoNtext，然後使用這些載入的實體。 這特別適用于資料系結，但在其他情況下也會很有用。 例如，在下列程式碼中，會先針對所有的 blog 和 post 查詢資料庫。 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A>擴充方法是用來執行此查詢，其中包含內容所追蹤的結果，而不會直接傳回給應用程式。  (使用 `ToList` 或類似的效果相同，但有建立傳回清單的額外負荷，此處不需要。 ) 此範例會使用 `DbSet.Local` 來存取本機追蹤的實體：

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

請注意，不同的是 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> ，會 `DbSet.Local` 直接傳回實體實例。 當然，Entityentry.state 可以藉由呼叫來取得所傳回之實體的永遠。 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>

### <a name="the-local-view"></a>本機視圖

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 傳回可反映這些實體目前的本機追蹤實體的視圖 <xref:Microsoft.EntityFrameworkCore.EntityState> 。 具體來說，這表示：

- `Added` 包括實體。 請注意，這不是一般 EF Core 查詢的情況，因為 `Added` 實體尚未存在於資料庫中，因此不會由資料庫查詢傳回。
- `Deleted` 排除實體。 請注意，這並不是一般 EF Core 查詢的情況，因為 `Deleted` 實體仍然存在於資料庫中，因此 _會_ 由資料庫查詢傳回。

這所有的方法都 `DbSet.Local` 是在反映實體圖形目前概念狀態的資料上查看，其中 `Added` 包含包含的實體和 `Deleted` 排除的實體。 這會比對在呼叫 SaveChanges 之後預期的資料庫狀態。

這通常是資料系結的理想觀點，因為它會根據應用程式所做的變更，向使用者呈現其瞭解的資料。

下列程式碼示範我將一篇貼文標示為 `Deleted` ，然後新增貼文，將其標示為 `Added` ：

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

此程式碼的輸出為：

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

請注意，已刪除的文章會從本機視圖中移除，並包含新增的貼文。

### <a name="using-local-to-add-and-remove-entities"></a>使用 Local 來新增和移除實體

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 會傳回 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601> 的執行個體。 這是的實 <xref:System.Collections.Generic.ICollection%601> ，會在從集合中加入和移除實體時，產生並回應通知。  (此概念與相同 <xref:System.Collections.ObjectModel.ObservableCollection%601> ，但實作為現有 EF Core 變更追蹤專案的投影，而不是獨立集合。 ) 

本機視圖的通知會連結至 DbCoNtext 變更追蹤，讓本機視圖與 DbCoNtext 保持同步。 具體來說：

- 新增實體可 `DbSet.Local` 讓 DbCoNtext 追蹤，通常會處於 `Added` 狀態。  (如果實體已經有產生的索引鍵值，則會改為追蹤 `Unchanged` 。 ) 
- 從移除實體 `DbSet.Local` 會導致它標示為 `Deleted` 。
- DbCoNtext 所追蹤的實體會自動出現在 `DbSet.Local` 集合中。 例如，執行查詢來導入多個實體時，會自動導致本機視圖更新。
- 標示為的實體 `Deleted` 會自動從本機集合中移除。

這表示，您只要在集合中加入和移除，就可以使用本機 view 來操作追蹤的實體。 例如，讓我們修改先前的範例程式碼，以新增和移除本機集合中的貼文：

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

輸出會與上一個範例保持不變，因為對本機視圖所做的變更會與 DbCoNtext 同步。

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a>使用 Windows Forms 或 WPF 資料系結的本機視圖

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> 形成資料系結至 EF Core 實體的基礎。 但是，當使用 Windows Forms 和 WPF 時，最好使用與預期的特定類型的通知集合搭配使用。 本機視圖支援建立這些特定的集合類型：

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> 傳回 <xref:System.Collections.ObjectModel.ObservableCollection%601> WPF 資料系結的。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> 傳回 Windows Forms 資料系結的 <xref:System.ComponentModel.BindingList%601> 。

例如：

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

如需有關使用 EF Core 的 WPF 資料系結的詳細資訊，請參閱 [使用 wpf 開始](xref:core/get-started/wpf) 。

> [!TIP]
> 給定 DbSet 實例的本機視圖會在第一次存取時延遲建立，然後再進行快取。 LocalView 本身的建立速度很快，而且不會使用大量的記憶體。 不過，它會呼叫 [DetectChanges](xref:core/change-tracking/change-detection)，這對大量實體而言可能很慢。 和建立的集合 `ToObservableCollection` `ToBindingList` 也會延遲建立，然後再進行快取。 這兩種方法都會建立新的集合，在涉及上千個實體時，可能會很慢，而且會使用大量的記憶體。
