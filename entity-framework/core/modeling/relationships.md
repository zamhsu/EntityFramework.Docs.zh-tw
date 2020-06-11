---
title: 關聯性-EF Core
description: 如何在使用 Entity Framework Core 時，設定實體類型之間的關聯性
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 3ed25b752970cf0f55d38f1ce5b8383f1c90edc3
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664139"
---
# <a name="relationships"></a>關聯性

關聯性會定義兩個實體之間的關聯性。 在關係資料庫中，這是以 foreign key 條件約束來表示。

> [!NOTE]  
> 本文中的大部分範例都會使用一對多關聯性來示範概念。 如需一對一和多對多關聯性的範例，請參閱本文結尾的[其他關聯性模式](#other-relationship-patterns)一節。

## <a name="definition-of-terms"></a>詞彙的定義

有數個詞彙用來描述關聯性

* **相依實體：** 這是包含外鍵屬性的實體。 有時稱為關聯性的「子系」。

* **主體實體：** 這是包含主要/替代索引鍵屬性的實體。 有時稱為關聯性的「父系」。

* **主要金鑰：** 可唯一識別主體實體的屬性。 這可能是主要金鑰或替代索引鍵。

* **外鍵：** 相依實體中用來儲存相關實體之主體索引鍵值的屬性。

* **導覽屬性：** 定義于主體和/或相依實體（參考相關實體）上的屬性。

  * **集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。

  * **參考導覽屬性：** 保存單一相關實體之參考的導覽屬性。

  * **反向導覽屬性：** 在討論特定導覽屬性時，此詞彙是指關聯性另一端的導覽屬性。
  
* **自我參考關聯性：** 相依和主體實體類型相同的關聯性。

下列程式碼顯示和之間的一對多關聯性 `Blog``Post`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* `Post`是相依實體

* `Blog`是主體實體

* `Blog.BlogId`是主要金鑰（在此案例中，它是主鍵，而不是替代索引鍵）

* `Post.BlogId`是外鍵

* `Post.Blog`是參考導覽屬性

* `Blog.Posts`是集合導覽屬性

* `Post.Blog`是的反向導覽屬性 `Blog.Posts` （反之亦然）

## <a name="conventions"></a>慣例

根據預設，當在類型上發現導覽屬性時，就會建立關聯性。 如果所指向的型別無法由目前的資料庫提供者對應為純量型別，則會將屬性視為導覽屬性。

> [!NOTE]  
> 依照慣例探索到的關聯性一律會以主體實體的主要金鑰為目標。 若要以替代金鑰為目標，必須使用流暢的 API 來執行其他設定。

### <a name="fully-defined-relationships"></a>完整定義的關聯性

關聯性最常見的模式是讓關聯性兩端定義的導覽屬性，以及相依實體類別中定義的外鍵屬性。

* 如果在兩個類型之間找到一對導覽屬性，則會將它們設定為相同關聯性的反向導覽屬性。

* 如果相依實體包含名稱符合這些模式之一的屬性，則會將它設定為外鍵：
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

在此範例中，反白顯示的屬性將用來設定關聯性。

> [!NOTE]
> 如果屬性是主要索引鍵，或者是與主體索引鍵不相容的類型，則不會將它設定為外鍵。

> [!NOTE]
> 在 EF Core 3.0 之前，名為與主體索引鍵屬性完全相同的屬性也會與[外鍵相符](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

### <a name="no-foreign-key-property"></a>沒有外鍵屬性

雖然建議在相依實體類別中定義外鍵屬性，但它並不是必要的。 如果找不到外鍵屬性，則會以名稱導入[陰影外鍵屬性](shadow-properties.md)， `<navigation property name><principal key property name>` `<principal entity name><principal key property name>` 如果相依類型上沒有任何導覽，則會引進。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

在此範例中，陰影外鍵是 `BlogId` 因為預先填入導覽名稱會是多餘的。

> [!NOTE]
> 如果已經有同名的屬性，則陰影屬性名稱的後面會加上一個數位。

### <a name="single-navigation-property"></a>單一導覽屬性

只包含一個導覽屬性（沒有反向導覽，而且沒有外鍵屬性）就足以擁有依照慣例定義的關聯性。 您也可以有一個導覽屬性和一個外鍵屬性。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a>限制

當兩個類型之間有多個導覽屬性定義時（亦即，不只是一個指向彼此的一對流覽），導覽屬性所表示的關聯性不明確。 您將需要手動設定它們來解決不明確的問題。

### <a name="cascade-delete"></a>串聯刪除

依照慣例，串聯刪除會針對必要關聯性設定為*cascade* ，並針對選擇性關聯性*deletebehavior.clientsetnull* 。 *Cascade*表示也會刪除相依的實體。 *Deletebehavior.clientsetnull*表示未載入記憶體的相依實體會維持不變，必須手動刪除或更新，以指向有效的主體實體。 若為載入記憶體中的實體，EF Core 會嘗試將外鍵屬性設定為 null。

如需必要和選擇性關聯性之間的差異，請參閱[必要和選擇性關聯](#required-and-optional-relationships)性一節。

如需不同刪除行為和慣例所使用之預設值的詳細資訊，請參閱[Cascade Delete](../saving/cascade-delete.md) 。

## <a name="manual-configuration"></a>手動設定

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

若要在流暢的 API 中設定關聯性，您一開始會先識別構成關聯性的導覽屬性。 `HasOne`或者，在 `HasMany` 您開始設定的實體類型上，識別導覽屬性。 接著，您可以將呼叫連結至 `WithOne` 或 `WithMany` ，以識別反向導覽。 `HasOne`/`WithOne`用於參考導覽屬性，並 `HasMany` / `WithMany` 用於集合導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[資料註解](#tab/data-annotations)

您可以使用資料批註來設定相依和主體實體上的導覽屬性如何配對。 當兩個實體類型之間有一對以上的導覽屬性時，通常就會執行這項作業。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> 您只能在相依實體的屬性上使用 [Required]，以影響關聯性的必要性。 [必要] 通常會忽略主體實體的導覽，但可能會導致實體成為相依專案。

> [!NOTE]
> 資料批註 `[ForeignKey]` 和可以 `[InverseProperty]` 在 `System.ComponentModel.DataAnnotations.Schema` 命名空間中使用。 `[Required]`可在 `System.ComponentModel.DataAnnotations` 命名空間中使用。

---

### <a name="single-navigation-property"></a>單一導覽屬性

如果您只有一個導覽屬性，則會有和的無參數多載 `WithOne` `WithMany` 。 這表示概念上的另一端有參考或集合，但實體類別中沒有包含任何導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a>設定導覽屬性

建立導覽屬性之後，您可能需要進一步設定它。 在 EFCore 5.0 中，新增了新的流暢 API，可讓您執行該設定。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> 這個呼叫不能用來建立導覽屬性。 它只會用來設定導覽屬性，先前已藉由定義關聯性或從慣例來建立。

### <a name="foreign-key"></a>外部索引鍵

#### <a name="fluent-api-simple-key"></a>[流暢的 API （簡單金鑰）](#tab/fluent-api-simple-key)

您可以使用流暢的 API 來設定應該使用哪一個屬性做為指定關聯性的外鍵屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[流暢 API （複合索引鍵）](#tab/fluent-api-composite-key)

您可以使用流暢的 API 來設定應該使用哪些屬性做為指定關聯性的複合外鍵屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[資料批註（簡單索引鍵）](#tab/data-annotations-simple-key)

您可以使用資料批註來設定應該使用哪一個屬性做為指定關聯性的外鍵屬性。 這通常是在不依照慣例探索外鍵屬性時完成：

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> `[ForeignKey]`批註可以放在關聯性中的任一導覽屬性上。 不需要移至相依實體類別中的導覽屬性。

> [!NOTE]
> `[ForeignKey]`在導覽屬性上使用指定的屬性不需要存在於相依型別上。 在此情況下，將會使用指定的名稱來建立陰影外鍵。

---

#### <a name="shadow-foreign-key"></a>陰影外鍵

您可以使用的字串多載 `HasForeignKey(...)` ，將陰影屬性設定為外鍵（如需詳細資訊，請參閱[陰影屬性](shadow-properties.md)）。 我們建議您先將 shadow 屬性明確加入至模型，再將其當做外鍵使用（如下所示）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a>外鍵條件約束名稱

依照慣例，以關係資料庫為目標時，foreign key 條件約束會命名為 FK_ <dependent type name> _<principal type name>_ <foreign key property name> 。 對於複合外鍵， <foreign key property name> 會變成以底線分隔的外鍵屬性名稱清單。

您也可以設定條件約束名稱，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a>沒有導覽屬性

您不一定需要提供導覽屬性。 您可以直接在關聯性的一端提供外鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a>主要金鑰

如果您想要外鍵參考主鍵以外的屬性，您可以使用流暢的 API 來設定關聯性的主體索引鍵屬性。 您設定為主體金鑰的屬性會自動設定為[替代金鑰](alternate-keys.md)。

#### <a name="simple-key"></a>[簡單金鑰](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[複合索引鍵](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> 指定主體索引鍵屬性的順序必須符合為外鍵指定的順序。

---

### <a name="required-and-optional-relationships"></a>必要和選擇性的關聯性

您可以使用流暢的 API 來設定關聯性是否為必要或選擇性。 最後，這會控制外鍵屬性是否為必要或選擇性。 當您使用陰影狀態外鍵時，這是最有用的。 如果您的實體類別中有外鍵屬性，則關聯性的必要性取決於外鍵屬性是否為必要或選擇性（如需詳細資訊，請參閱[必要和選擇性屬性](required-optional.md)）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> `IsRequired(false)`除非另有設定，否則呼叫也會使外鍵屬性變成選擇性。

### <a name="cascade-delete"></a>串聯刪除

您可以使用流暢的 API，為指定的關聯性明確設定串聯刪除行為。

如需每個選項的詳細討論，請參閱串聯[刪除](../saving/cascade-delete.md)。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a>其他關聯性模式

### <a name="one-to-one"></a>一對一

一對一關聯性在兩端都有參考導覽屬性。 它們遵循與一對多關聯性相同的慣例，但是在外鍵屬性上引進了唯一的索引，以確保只有一個相依性與每個主體相關。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> EF 會根據其偵測外鍵屬性的能力，選擇要相依的其中一個實體。 如果選擇錯誤的實體做為相依專案，您可以使用流暢的 API 來修正此問題。

設定與流暢 API 的關聯性時，您會使用 `HasOne` 和 `WithOne` 方法。

設定外鍵時，您需要指定相依實體類型-請注意下列清單中提供給的泛型參數 `HasForeignKey` 。 在一對多關聯性中，明確地指出具有參考導覽的實體是相依的，而具有該集合的實體是主體。 但這並不是一對一關聯性，因此需要明確定義。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a>多對多

尚未支援多對多關聯性，但沒有實體類別來表示聯結資料表。 不過，您可以藉由包含聯結資料表的實體類別並對應兩個不同的一對多關聯性，來代表多對多關聯性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
