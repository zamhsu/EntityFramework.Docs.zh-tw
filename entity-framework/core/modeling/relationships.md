---
title: 關聯性-EF Core
description: 使用 Entity Framework Core 時如何設定實體類型之間的關聯性
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: 71d960a15dfb938af1dcc7035dc2587df7ad4677
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063838"
---
# <a name="relationships"></a>關聯性

關聯性會定義兩個實體彼此之間的關聯性。 在關係資料庫中，這是由 foreign key 條件約束表示。

> [!NOTE]  
> 本文中大部分的範例都使用一對多關聯性來示範概念。 如需一對一和多對多關聯性的範例，請參閱本文結尾的 [其他關聯性模式](#other-relationship-patterns) 一節。

## <a name="definition-of-terms"></a>詞彙定義

有一些詞彙用來描述關聯性

* **相依實體：** 這是包含外鍵屬性的實體。 有時稱為關聯性的「子系」。

* **Principal 實體：** 這是包含主要/替代索引鍵屬性的實體。 有時稱為關聯性的「父系」。

* **主要金鑰：** 可唯一識別主體實體的屬性。 這可能是主鍵或替代索引鍵。

* **外鍵：** 相依實體中的屬性，用來儲存相關實體的主要金鑰值。

* **導覽屬性：** 在參考相關實體的主體和/或相依實體上定義的屬性。

  * **集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。

  * **參考導覽屬性：** 保存單一相關實體之參考的導覽屬性。

  * **反向導覽屬性：** 在討論特定的導覽屬性時，這個詞彙是指關聯性另一端的導覽屬性。
  
* **自我參考關聯性：** 相依和主體實體類型相同的關聯性。

下列程式碼顯示與之間的一對多關聯性 `Blog``Post`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* `Post` 是相依實體

* `Blog` 是主體實體

* `Blog.BlogId` 是主要金鑰 (在此案例中，它是主鍵，而不是替代索引鍵) 

* `Post.BlogId` 為外鍵

* `Post.Blog` 是參考導覽屬性

* `Blog.Posts` 是集合導覽屬性

* `Post.Blog` 這是 (的反向導覽屬性 `Blog.Posts` ，反之亦然) 

## <a name="conventions"></a>慣例

根據預設，當類型上有探索到導覽屬性時，會建立關聯性。 如果目前的資料庫提供者無法將屬性所指向的型別對應為純量類型，則會將屬性視為導覽屬性。

> [!NOTE]  
> 依慣例探索的關聯性一律會以主體實體的主要金鑰為目標。 若要以替代金鑰為目標，則必須使用流暢的 API 來執行其他設定。

### <a name="fully-defined-relationships"></a>完整定義的關聯性

最常見的關聯性模式是在關聯性的兩端定義導覽屬性，並在相依實體類別中定義外鍵屬性。

* 如果在兩個類型之間找到一組導覽屬性，則會將它們設定為相同關聯性的反向導覽屬性。

* 如果相依實體包含名稱符合其中一個模式的屬性，則會將它設定為外鍵：
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

在此範例中，反白顯示的屬性將用來設定關聯性。

> [!NOTE]
> 如果屬性是主鍵，或者是不與主體索引鍵相容的型別，則不會將它設定為外鍵。

> [!NOTE]
> 在 EF Core 3.0 之前，名為與 principal key 屬性完全相同的屬性也會與 [外鍵相符](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

### <a name="no-foreign-key-property"></a>沒有外鍵屬性

雖然建議將外鍵屬性定義在相依的實體類別中，但這不是必要的。 如果找不到任何外鍵屬性，則會以名稱導入 [陰影外鍵屬性](xref:core/modeling/shadow-properties) ， `<navigation property name><principal key property name>` `<principal entity name><principal key property name>` 如果相依類型上沒有導覽，則為。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

在此範例中，陰影外鍵是 `BlogId` 因為前面加上導覽名稱會是多餘的。

> [!NOTE]
> 如果已經存在具有相同名稱的屬性，則會將陰影屬性名稱加上數位。

### <a name="single-navigation-property"></a>單一導覽屬性

僅包含一個導覽屬性 (沒有反向導覽，而且沒有任何外鍵屬性) 足以擁有依慣例定義的關聯性。 您也可以擁有單一導覽屬性和外鍵屬性。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a>限制

當兩個型別之間有多個定義的導覽屬性時 (也就是，一對彼此指向的導覽，) 導覽屬性代表的關聯性不明確。 您將需要手動設定它們，以解決不明確的問題。

### <a name="cascade-delete"></a>串聯刪除

依照慣例，串聯刪除會針對必要的關聯性設定為 *cascade* ，並針對選擇性關聯性 *deletebehavior.clientsetnull* 。 *Cascade* 表示也會刪除相依實體。 *Deletebehavior.clientsetnull* 表示未載入記憶體的相依實體將保持不變，而且必須以手動方式刪除或更新以指向有效的主體實體。 若為載入記憶體中的實體，EF Core 會嘗試將外鍵屬性設定為 null。

如需必要和選擇性關聯性之間的差異，請參閱「 [必要」和「選擇性關聯](#required-and-optional-relationships) 性」一節。

如需不同刪除行為的詳細資訊以及慣例所使用的預設值，請參閱 [Cascade delete](xref:core/saving/cascade-delete) 。

## <a name="manual-configuration"></a>手動設定

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

若要在流暢的 API 中設定關聯性，您必須先識別構成關聯性的導覽屬性。 `HasOne` 或 `HasMany` 識別您要開始設定的實體類型上的導覽屬性。 然後，您可以將呼叫連結至 `WithOne` 或 `WithMany` ，以識別反向導覽。 `HasOne`/`WithOne`用於參考導覽屬性，並用於 `HasMany` / `WithMany` 集合導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

您可以使用資料批註來設定相依和主體實體上的導覽屬性配對。 這通常會在兩個實體類型之間有一對以上的導覽屬性時完成。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> 您只能對相依實體上的屬性使用 [必要]，以影響關聯性的 requiredness。 [必要] 通常會忽略主體實體的導覽，但可能會導致實體成為相依的實體。

> [!NOTE]
> 資料批註 `[ForeignKey]` 和 `[InverseProperty]` 可在 `System.ComponentModel.DataAnnotations.Schema` 命名空間中使用。 `[Required]` 可在 `System.ComponentModel.DataAnnotations` 命名空間中使用。

---

### <a name="single-navigation-property"></a>單一導覽屬性

如果您只有一個導覽屬性，則會有和的無參數多載 `WithOne` `WithMany` 。 這表示在關聯性的另一端有參考或集合的概念，但 entity 類別中沒有包含任何導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a>設定導覽屬性

> [!NOTE]
> 這項功能已在 EF Core 5.0 中新增。

建立導覽屬性之後，您可能需要進一步設定。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> 此呼叫不能用來建立導覽屬性。 它只會用來設定先前藉由定義關聯性或從慣例建立的導覽屬性。

### <a name="foreign-key"></a>外部索引鍵

#### <a name="fluent-api-simple-key"></a>[流暢的 API (簡單的金鑰) ](#tab/fluent-api-simple-key)

您可以使用流暢的 API 來設定應該使用哪一個屬性做為指定之關聯性的外鍵屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[流暢的 API (複合索引鍵) ](#tab/fluent-api-composite-key)

您可以使用流暢的 API 來設定應該使用哪些屬性做為指定之關聯性的複合外鍵屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[ (簡單索引鍵) 的資料批註 ](#tab/data-annotations-simple-key)

您可以使用資料批註來設定應該使用哪一個屬性做為指定之關聯性的外鍵屬性。 這通常是在不依照慣例探索外鍵屬性時完成：

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> `[ForeignKey]`批註可以放置於關聯性中的任何導覽屬性。 它不需要移至相依實體類別中的導覽屬性。

> [!NOTE]
> `[ForeignKey]`在導覽屬性上使用指定的屬性不需要存在於相依型別上。 在此情況下，將會使用指定的名稱來建立陰影外鍵。

---

#### <a name="shadow-foreign-key"></a>陰影外鍵

您可以使用的字串多載 `HasForeignKey(...)` ，將陰影屬性設定為外鍵 (如需詳細資訊，請參閱 [陰影屬性](xref:core/modeling/shadow-properties)) 。 我們建議您先明確地將 shadow 屬性加入至模型，再使用它做為外鍵 (，如下所示) 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a>Foreign key 條件約束名稱

依照慣例，以關係資料庫為目標時，foreign key 條件約束會命名為 FK_ <dependent type name> _<principal type name>_ <foreign key property name> 。 若為複合外鍵， <foreign key property name> 則會變成以底線分隔的外鍵屬性名稱清單。

您也可以設定條件約束名稱，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a>沒有導覽屬性

您不一定需要提供導覽屬性。 您可以直接在關聯性的一端提供外鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a>主體金鑰

如果您希望外鍵參考主鍵以外的屬性，您可以使用流暢的 API 來設定關聯性的主要索引鍵屬性。 您設定為主要金鑰的屬性會自動設定為 [替代索引鍵](xref:core/modeling/keys#alternate-keys)。

#### <a name="simple-key"></a>[簡單索引鍵](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[複合索引鍵](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> 您指定主體索引鍵屬性的順序必須符合為外鍵指定的順序。

---

### <a name="required-and-optional-relationships"></a>必要和選用的關聯性

您可以使用流暢的 API 來設定關聯性是否為必要或選擇性。 最後，這會控制外鍵屬性是否為必要或選擇性。 當您使用陰影狀態外鍵時，這非常有用。 如果您的實體類別中有外鍵屬性，則關聯性的 requiredness 取決於是否需要外鍵屬性或選擇性 (如需詳細資訊，請參閱 [必要和選擇性屬性](xref:core/modeling/entity-properties#required-and-optional-properties)) 。

外鍵屬性位於相依的實體類型上，因此如果設定為必要，則表示每個相依實體都必須有對應的主體實體。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> 呼叫 `IsRequired(false)` 也會將外鍵屬性設為選擇性，除非另有設定。

### <a name="cascade-delete"></a>串聯刪除

您可以使用流暢的 API 來明確設定指定之關聯性的串聯刪除行為。

如需每個選項的詳細討論，請參閱 [Cascade Delete](xref:core/saving/cascade-delete) 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a>其他關聯性模式

### <a name="one-to-one"></a>一對一

一對多關聯性的兩端都有參考導覽屬性。 它們遵循與一對多關聯性相同的慣例，但在外鍵屬性上引進了唯一的索引，以確保只有一個相依性與每個主體相關。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> EF 會根據其偵測外鍵屬性的能力，選擇要相依的其中一個實體。 如果選擇錯誤的實體做為相依的實體，您可以使用流暢的 API 來修正此問題。

設定與流暢 API 的關聯性時，您會使用 `HasOne` 和 `WithOne` 方法。

設定外鍵時，您需要指定相依的實體類型-請注意， `HasForeignKey` 在下列清單中提供的泛型參數。 在一對多關聯性中，可以明確指出具有參考導覽的實體是相依的，而集合中的實體是主體。 但這不是一對一關聯性，因此必須明確地定義它。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

根據預設，相依的端會被視為選擇性，但可以視需要進行設定。 但是 EF 不會驗證是否已提供相依實體，因此只有在資料庫對應允許強制執行此設定時，此設定才會有所差異。 常見的案例是依預設使用資料表分割的參考擁有類型。

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

使用此設定時，對應的 `ShippingAddress` 資料行會在資料庫中標示為不可為 null。

> [!NOTE]
> 如果您使用 [不可為 null 的參考型別](/dotnet/csharp/nullable-references) ，則 `IsRequired` 不需要呼叫。

> [!NOTE]
> 在 EF Core 5.0 中新增了是否需要相依的設定。

### <a name="many-to-many"></a>多對多

多對多關聯性需要兩端的集合導覽屬性。 如同其他類型的關聯性，將會依照慣例來探索它們。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

在資料庫中執行此關聯性的方式，是將包含和的外鍵加入資料表 `Post` `Tag` 。 例如，這是 EF 將在上述模型的關係資料庫中建立的。

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostId] int NOT NULL,
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostId], [TagId]),
    CONSTRAINT [FK_PostTag_Posts_PostId] FOREIGN KEY ([PostId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagId] FOREIGN KEY ([TagId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

就內部而言，EF 會建立實體型別來代表聯結資料表，而該聯結資料表將被稱為聯結實體型別。 沒有可用於此的特定 CLR 型別，因此 `Dictionary<string, object>` 會使用。 模型中可以有一個以上的多對多關聯性，因此，在此情況下，必須為聯結實體類型指定唯一的名稱 `PostTag` 。 允許這項功能的功能稱為共用類型實體類型。

「多對多導覽」稱為「略過導覽」，因為它們可有效略過聯結實體類型。 如果您正在採用大量設定，則可以從取得所有略過導覽 `GetSkipNavigations` 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

通常會將設定套用至聯結實體類型。 您可以透過來完成此動作 `UsingEntity` 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

您可以使用匿名型別，為聯結實體型別提供[模型種子資料](xref:core/modeling/data-seeding)。 您可以檢查模型的偵錯工具，以判斷依慣例建立的屬性名稱。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

額外的資料可以儲存在聯結實體型別中，但為了這樣做，最好是建立定制的 CLR 型別。 設定自訂聯結實體類型的關聯性時，必須明確指定這兩個外鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> 在 EF Core 5.0 中新增多對多關聯性的功能已加入至舊版，請使用下列方法。

您也可以藉由加入聯結實體型別並對應兩個不同的一對多關聯性，來表示多對多關聯性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
