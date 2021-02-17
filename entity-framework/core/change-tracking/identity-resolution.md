---
title: 身分識別解析-EF Core
description: 使用主鍵值將多個實體實例解析成單一實例
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: d4c8f935c8d0ab92eaecd8fc7a4156bd824713d4
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543610"
---
# <a name="identity-resolution-in-ef-core"></a>EF Core 中的身分識別解析

<xref:Microsoft.EntityFrameworkCore.DbContext>只能追蹤一個具有任何指定主鍵值的實體實例。 這表示多個具有相同索引鍵值的實體實例，必須解析為單一實例。 這稱為「識別解析」。 身分識別解析可確保 Entity Framework Core (EF Core) 正在追蹤一致的圖形，而實體的關聯性或屬性值沒有任何多義性。

> [!TIP]
> 本檔假設您已瞭解實體狀態以及 EF Core 變更追蹤的基本概念。 如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore)，以執行並偵測到本檔中的所有程式碼。

## <a name="introduction"></a>簡介

下列程式碼會查詢實體，然後嘗試附加具有相同主鍵值的不同實例：

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

執行此程式碼會導致下列例外狀況：

> InvalidOperationException：無法追蹤實體類型 ' Blog ' 的實例，因為已追蹤另一個具有索引鍵值 ' {Id： 1} ' 的實例。 附加現有的實體時，請確定只附加一個具有指定索引鍵值的實體實例。

EF Core 需要單一實例，因為：

- 多個實例之間的屬性值可能不同。 更新資料庫時，EF Core 必須知道要使用哪些屬性值。
- 在多個實例之間，與其他實體的關聯性可能會不同。 例如，"blogA" 可能與不同于 "blogB" 的貼文集合相關。

上述例外狀況通常會在下列情況中發生：

- 嘗試更新實體時
- 嘗試追蹤實體的序列化圖形時
- 無法設定未自動產生的索引鍵值時
- 針對多個工作單位重複使用 DbCoNtext 實例時

下列各節將討論這些情況。

## <a name="updating-an-entity"></a>更新實體

有幾種不同的方法可以用新的值更新實體，如 EF Core 和[明確追蹤實體](xref:core/change-tracking/explicit-tracking)中的[變更追蹤](xref:core/change-tracking/index)所述。 這些方法在識別解析的內容中如下所述。 要注意的一個重點是，每個方法都使用查詢或呼叫其中一個或的呼叫 `Update` `Attach` ，但 **_永遠不會同時使用這兩_** 種方法。

### <a name="call-update"></a>呼叫更新

通常，要更新的實體不是來自我們將用於 SaveChanges 的 DbCoNtext 查詢。 例如，在 web 應用程式中，可以從 POST 要求中的資訊建立實體實例。 處理這項工作的最簡單方式是使用 <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> 或 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> 。 例如：

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

在此案例中：

- 只會建立實體的單一實例。
- 在進行更新的過程中， **不** 會從資料庫查詢實體實例。
- 所有的屬性值都會在資料庫中更新，不論它們是否真的變更。
- 進行一個資料庫來回行程。

### <a name="query-then-apply-changes"></a>查詢然後套用變更

通常不知道當實體是從 POST 要求中的資訊建立時，哪些屬性值實際上已經變更或類似。 您通常只需要更新資料庫中的所有值，就像我們在上一個範例中所做的一樣。 但是，如果應用程式正在處理許多實體，而且只有少數的實體有實際的變更，則限制傳送的更新可能會很有用。 這可以藉由執行查詢來追蹤目前存在於資料庫中的實體，然後將變更套用至這些追蹤的實體來達成。 例如：

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

在此案例中：

- 只會追蹤實體的單一實例;查詢從資料庫傳回的 `Find` 。
- `Update``Attach`**不** 使用、等等。
- 資料庫中只會更新已實際變更的屬性值。
- 進行兩次資料庫來回行程。

EF Core 有一些協助程式來傳送屬性值（如下所示）。 例如， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> 將會從指定的物件複製所有值，然後在追蹤的物件上設定這些值：

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

`SetValues` 接受各種物件類型，包括資料傳輸物件 (Dto) 屬性名稱符合實體類型的屬性。 例如：

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

或具有屬性值之名稱/值專案的字典：

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

如需使用屬性值的詳細資訊，請參閱 [存取追蹤的實體](xref:core/change-tracking/entity-entries) 。

### <a name="use-original-values"></a>使用原始值

到目前為止，每個方法在進行更新之前都已執行查詢，或更新所有屬性值，而不論它們是否已變更。 若只要更新已變更但未查詢為更新一部分的值，則需要已變更哪些屬性值的特定資訊。 取得這項資訊的常見方式是傳回 HTTP Post 或類似的目前值和原始值。 例如：

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

在此程式碼中，會先附加具有已修改值的實體。 這會導致 EF Core 追蹤狀態中的實體， `Unchanged` 也就是沒有任何屬性值標示為已修改。 然後，會將原始值的字典套用至此追蹤的實體。 這會將不同的目前值和原始值標示為已修改的屬性。 具有相同目前值和原始值的屬性將不會標示為已修改。

在此案例中：

- 使用 Attach 只追蹤實體的單一實例。
- 在進行更新的過程中， **不** 會從資料庫查詢實體實例。
- 套用原始值可確保資料庫中只會更新實際變更的屬性值。
- 進行一個資料庫來回行程。

如同上一節中的範例，原始值不需要以字典的形式傳遞;實體實例或 DTO 也可以運作。

> [!TIP]
> 雖然這個方法有吸引人的特性，但它確實需要在 web 用戶端之間傳送實體的原始值。 請仔細考慮這項額外的複雜性是否值得優勢;對於許多應用程式來說，其中一種較簡單的方法是更實用的方法。

## <a name="attaching-a-serialized-graph"></a>附加序列化圖形

EF Core 適用于透過外鍵和導覽屬性連接的實體圖形，如 [變更外鍵和](xref:core/change-tracking/relationship-changes)導覽所述。 如果這些圖形是使用 EF Core （例如，從 JSON 檔案建立）之外建立的，則它們可以有相同實體的多個實例。 這些重複專案必須先解析成單一實例，然後才能追蹤圖形。

### <a name="graphs-with-no-duplicates"></a>沒有重複專案的圖表

繼續進行之前，請務必先辨識：

- 序列化程式通常具有在圖形中處理迴圈和重複實例的選項。
- 當做圖形根使用的物件選擇通常有助於減少或移除重複專案。

可能的話，請使用序列化選項，並選擇不會產生重複專案的根。 例如，下列程式碼會使用 [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) ，將 blog 清單與其相關文章進行序列化：

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

此程式碼所產生的 JSON 為：

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

請注意，JSON 中沒有重複的 blog 或文章。 這表示簡單的呼叫 `Update` 將會運作，以更新資料庫中的這些實體：

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a>處理重複專案

上述範例中的程式碼會將每個 blog 序列化為其相關文章。 如果變更為使用其相關聯的 blog 來將每個貼文序列化，則會將重複項導入序列化的 JSON 中。 例如：

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

序列化的 JSON 現在看起來像這樣：

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

請注意，圖形現在包含多個具有相同金鑰值的 Blog 實例，以及多個具有相同索引鍵值的 Post 實例。 如同我們在前一個範例中所做的一樣，嘗試追蹤此圖形將會擲回：

> InvalidOperationException：無法追蹤實體類型 ' Post ' 的實例，因為已追蹤另一個具有索引鍵值 ' {Id： 2} ' 的實例。 附加現有的實體時，請確定只附加一個具有指定索引鍵值的實體實例。

我們可以透過兩種方式來修正此問題：

- 使用保留參考的 JSON 序列化選項
- 在追蹤圖形時執行身分識別解析

#### <a name="preserve-references"></a>保留參考

Json.NET 提供 `PreserveReferencesHandling` 處理此的選項。 例如：

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

產生的 JSON 現在看起來像這樣：

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

請注意，此 JSON 已將重複專案取代為參考 `"$ref": "5"` ，例如，參考圖形中已存在的實例。 您可以再次使用的簡單呼叫來追蹤此圖形 `Update` ，如上所示。

<xref:System.Text.Json>.Net 基類程式庫 (BCL) 的支援具有類似的選項，因此會產生相同的結果。 例如：

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a>解決重複專案

如果無法在序列化程式中消除重複專案，則會 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> 提供方法來處理此情況。 TrackGraph 的運作方式類似 `Add` ，但 `Attach` `Update` 它會在追蹤之前為每個實體實例產生回呼。 此回呼可以用來追蹤或忽略實體。 例如：

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

針對圖形中的每個實體，此程式碼將會：

- 尋找實體的實體類型和金鑰值
- 在變更追蹤程式中使用此索引鍵查閱實體
  - 如果找到實體，則不會採取進一步的動作，因為實體重複
  - 如果找不到實體，則會將狀態設定為，以追蹤該實體。 `Modified`

執行此程式碼的輸出為：

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> 這段 **程式碼假設所有的重複專案都完全相同**。 這可讓您安全地選擇其中一個重複專案，以在捨棄其他重複專案時進行追蹤。 如果重複專案可能不同，則程式碼將需要決定要使用哪一個，以及如何將屬性和導覽值結合在一起。

> [!NOTE]
> 為了簡單起見，此程式碼假設每個實體都有一個稱為的 primary key 屬性 `Id` 。 這可能會編寫至抽象基類或介面。 或者，您可以從中繼資料取得 primary key 屬性或屬性， <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> 讓此程式碼能夠使用任何類型的實體。

## <a name="failing-to-set-key-values"></a>無法設定索引鍵值

實體類型通常會設定為使用 [自動產生](xref:core/modeling/generated-properties)的索引鍵值。 這是非複合索引鍵的整數和 GUID 屬性的預設值。 但是，如果實體類型未設定為使用自動產生的索引鍵值，則必須在追蹤實體之前設定明確的索引鍵值。 例如，使用下列實體類型：

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

請考慮嘗試追蹤兩個新實體實例的程式碼，而不設定索引鍵值：

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

這段程式碼將會擲回：

> InvalidOperationException：無法追蹤實體類型 ' 寵物 ' 的實例，因為已追蹤另一個具有索引鍵值 ' {Id： 0} ' 的實例。 附加現有的實體時，請確定只附加一個具有指定索引鍵值的實體實例。

修正此問題的方法是明確地設定索引鍵值，或將索引鍵屬性設定為使用產生的索引鍵值。 如需詳細資訊，請參閱 [產生的值](xref:core/modeling/generated-properties) 。

## <a name="overusing-a-single-dbcontext-instance"></a>過度使用單一 DbCoNtext 實例

<xref:Microsoft.EntityFrameworkCore.DbContext> 的設計目的是代表短期的工作單位（如 [DbCoNtext 初始化和](xref:core/dbcontext-configuration/index)設定所述），並在 [EF Core 的變更追蹤](xref:core/change-tracking/index)中詳細說明。 未遵循此指引可讓您輕鬆地遇到嘗試追蹤相同實體的多個實例的情況。 常見範例包括：

- 使用相同的 DbCoNtext 實例來設定測試狀態，然後執行測試。 這通常會導致 DbCoNtext 仍在測試設定中追蹤一個實體實例，然後嘗試在適當的測試中附加新的實例。 相反地，請使用不同的 DbCoNtext 實例來設定測試狀態和適當的測試程式碼。
- 使用存放庫中的共用 DbCoNtext 實例或類似的程式碼。 相反地，請確定您的存放庫針對每個工作單位使用單一 DbCoNtext 實例。

## <a name="identity-resolution-and-queries"></a>身分識別解析和查詢

當從查詢追蹤實體時，會自動進行識別解析。 這表示，如果已經追蹤具有指定之索引鍵值的實體實例，則會使用這個現有的追蹤實例，而不是建立新的實例。 這有一個重要的結果：如果資料庫中的資料已變更，則不會反映在查詢的結果中。 這是將新的 DbCoNtext 實例用於每個工作單位的好理由，如 [DbCoNtext 初始化和](xref:core/dbcontext-configuration/index)設定中所述，以及 [EF Core 的變更追蹤](xref:core/change-tracking/index)中的詳細說明。

> [!IMPORTANT]
> 請務必瞭解，EF Core 一律針對資料庫執行 DbSet 的 LINQ 查詢，而且只會根據資料庫中的內容傳回結果。 不過，對於追蹤查詢，如果傳回的實體已被追蹤，則會使用追蹤的實例，而不是從資料庫中的資料建立實例。

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> 或者， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> 當追蹤的實體需要以資料庫中的最新資料重新整理時，也可以使用。 如需詳細資訊，請參閱 [存取追蹤的實體](xref:core/change-tracking/entity-entries) 。

相較于追蹤查詢，無追蹤查詢不會執行識別解析。 這表示，在先前所述的 JSON 序列化案例中，無追蹤查詢可能會傳回重複的專案。 如果查詢結果將會序列化並傳送至用戶端，這通常不會有問題。

> [!TIP]
> 請勿定期執行無追蹤查詢，然後將傳回的實體附加至相同的內容。 這會比使用追蹤查詢更慢且更難。

無追蹤查詢不會執行身分識別解析，因為這樣會影響從查詢串流處理大量實體的效能。 這是因為身分識別解析需要追蹤每個傳回的實例，如此就可以使用它，而不是稍後建立重複的實例。

從 EF Core 5.0 開始，可以強制不追蹤查詢使用來執行識別解析 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> 。 然後，查詢會追蹤傳回的實例 (而不是以正常方式進行追蹤) 並確保查詢結果中不會建立重複的專案。

## <a name="overriding-object-equality"></a>覆寫物件相等

在比較實體實例時，EF Core 會使用 [參考相等](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) 。 即使實體類型覆寫 <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> 或變更物件相等，還是會發生這種情況。 不過，有一個地方覆寫相等可能會影響 EF Core 行為：當集合導覽使用覆寫的相等而非參考相等時，也會將多個實例報告為相同。

因此，建議您避免覆寫實體相等。 如果使用，請務必建立強制參考相等的集合導覽。 例如，建立使用參考相等的相等比較子：

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

從 .NET 5 開始 (，這會以的形式包含在 BCL 中 <xref:System.Collections.Generic.ReferenceEqualityComparer> 。 ) 

然後，您可以在建立集合導覽時使用這個比較子。 例如：

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a>比較索引鍵屬性

除了相等比較之外，也需要排序索引鍵值。 在對 SaveChanges 的單一呼叫中更新多個實體時，請務必避免鎖死。 用於主要、替代或外鍵屬性的所有型別，以及用於唯一索引的型別都必須實 <xref:System.IComparable%601> 和 <xref:System.IEquatable%601> 。 通常用來做為索引鍵的類型 (int、Guid、字串等 ) 已經支援這些介面。 自訂金鑰類型可能會新增這些介面。
