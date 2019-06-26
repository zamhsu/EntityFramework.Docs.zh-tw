---
title: 關聯性的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 3731d30a15222a18ad6c729e010b9bf0994c82b2
ms.sourcegitcommit: 83c1e2fc034e5eb1fec1ebabc8d629ffcc7c0632
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67351354"
---
# <a name="relationships"></a><span data-ttu-id="e0fbb-102">關聯性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-102">Relationships</span></span>

<span data-ttu-id="e0fbb-103">關聯性會定義兩個實體如何與彼此相關。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="e0fbb-104">在關聯式資料庫中，這被以外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="e0fbb-105">大部分的這篇文章中的範例會使用一個對多關聯性，來示範概念。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="e0fbb-106">如需一對一和多對多關聯性的範例，請參閱[其他關聯性模式](#other-relationship-patterns)文章的最後一節。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="e0fbb-107">詞彙定義</span><span class="sxs-lookup"><span data-stu-id="e0fbb-107">Definition of Terms</span></span>

<span data-ttu-id="e0fbb-108">有幾個名詞，用來描述關聯性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="e0fbb-109">**相依的實體：** 這是包含外部索引鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="e0fbb-110">有時稱為 「 子 」 的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="e0fbb-111">**主要實體：** 這是包含的主要/替代索引鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="e0fbb-112">有時稱為 'parent' 的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="e0fbb-113">**外部索引鍵：** 在用來儲存與相關實體的主要索引鍵屬性的值為相依實體屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="e0fbb-114">**主體的金鑰：** 屬性，這些屬性可唯一識別主要實體。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="e0fbb-115">這可能是主索引鍵或替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="e0fbb-116">**導覽屬性：** 主體和 （或） 相依實體，其中包含相關的公司實體參考上定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="e0fbb-117">**集合導覽屬性：** 瀏覽屬性，其中包含許多相關實體的參考。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="e0fbb-118">**參考導覽屬性：** 保留單一相關實體的參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="e0fbb-119">**反向的導覽屬性：** 在討論時有特定的導覽屬性，這個詞彙是指關聯性另一端的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="e0fbb-120">下列程式碼清單會顯示一對多關聯性之間`Blog`和 `Post`</span><span class="sxs-lookup"><span data-stu-id="e0fbb-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="e0fbb-121">`Post` 是相依實體</span><span class="sxs-lookup"><span data-stu-id="e0fbb-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="e0fbb-122">`Blog` 是主要實體</span><span class="sxs-lookup"><span data-stu-id="e0fbb-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="e0fbb-123">`Post.BlogId` 外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="e0fbb-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="e0fbb-124">`Blog.BlogId` 是 （在此情況下它是主索引鍵，而不是替代索引鍵） 的主體金鑰</span><span class="sxs-lookup"><span data-stu-id="e0fbb-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="e0fbb-125">`Post.Blog` 是參考導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="e0fbb-126">`Blog.Posts` 集合導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="e0fbb-127">`Post.Blog` 是的反向的導覽屬性`Blog.Posts`（反之亦然）</span><span class="sxs-lookup"><span data-stu-id="e0fbb-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="e0fbb-128">慣例</span><span class="sxs-lookup"><span data-stu-id="e0fbb-128">Conventions</span></span>

<span data-ttu-id="e0fbb-129">依照慣例，在類型上探索到的導覽屬性時，將會建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="e0fbb-130">屬性會被視為一個導覽屬性，如果它所指向的類型不能為純量型別對應目前的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="e0fbb-131">慣例所探索到的關聯性會一律以目標主要實體的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="e0fbb-132">為目標的其他索引鍵，額外的組態必須使用 Fluent API 執行。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="e0fbb-133">完整定義的關聯性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-133">Fully Defined Relationships</span></span>

<span data-ttu-id="e0fbb-134">關聯性的最常見的模式是將這兩個端點的關聯性和相依的實體類別中定義的外部索引鍵屬性定義的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="e0fbb-135">如果兩個類型之間找到對導覽屬性，則它們會被設定為相同關聯性的反向的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="e0fbb-136">如果相依的實體包含屬性，名為`<primary key property name>`， `<navigation property name><primary key property name>`，或`<principal entity name><primary key property name>`接著，它會設定為外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="e0fbb-137">如果有多個定義兩個類型之間的導覽屬性 (也就是指向彼此的巡覽的多個不同的組)，然後依照慣例建立任何關聯性和您必須手動將其設定為識別如何導覽屬性配對。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-137">If there are multiple navigation properties defined between two types (that is, more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="e0fbb-138">沒有外部索引鍵屬性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-138">No Foreign Key Property</span></span>

<span data-ttu-id="e0fbb-139">雖然建議有相依的實體類別中定義的外部索引鍵屬性，並不需要。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="e0fbb-140">如果不找到任何外部索引鍵屬性，則會介紹陰影的外部索引鍵屬性同名`<navigation property name><principal key property name>`(請參閱 <<c2> [ 遮蔽屬性](shadow-properties.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="e0fbb-141">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-141">Single Navigation Property</span></span>

<span data-ttu-id="e0fbb-142">包括一個導覽屬性 （未反轉的導覽中和任何外部索引鍵屬性），就足以具有由慣例定義關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="e0fbb-143">您也可以有單一導覽屬性和外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="e0fbb-144">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="e0fbb-144">Cascade Delete</span></span>

<span data-ttu-id="e0fbb-145">依照慣例，串聯刪除將會設定為*Cascade*必要的關聯性並*ClientSetNull*選擇性的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="e0fbb-146">*Cascade*表示也會刪除相依實體。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="e0fbb-147">*ClientSetNull*方法不會載入到記憶體的相依實體將會保留不變且必須以手動方式刪除，或更新為指向有效的主體實體。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="e0fbb-148">載入到記憶體的實體，EF Core 會嘗試將外部索引鍵屬性設定為 null。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="e0fbb-149">請參閱[必要和選擇性的關聯性](#required-and-optional-relationships)一節以取得必要和選擇性的關聯性之間的差異。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="e0fbb-150">請參閱[串聯刪除](../saving/cascade-delete.md)的更多詳細的不同刪除行為和使用慣例的預設值。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="e0fbb-151">資料註釋</span><span class="sxs-lookup"><span data-stu-id="e0fbb-151">Data Annotations</span></span>

<span data-ttu-id="e0fbb-152">有兩個可用來設定關聯性的資料註解`[ForeignKey]`和`[InverseProperty]`。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span> <span data-ttu-id="e0fbb-153">這些是用於`System.ComponentModel.DataAnnotations.Schema`命名空間。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-153">These are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="e0fbb-154">[ForeignKey]</span><span class="sxs-lookup"><span data-stu-id="e0fbb-154">[ForeignKey]</span></span>

<span data-ttu-id="e0fbb-155">若要設定哪些屬性應為外部索引鍵屬性用於指定的關聯性，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-155">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="e0fbb-156">這通常是依照慣例找不到外部索引鍵屬性時。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-156">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> <span data-ttu-id="e0fbb-157">`[ForeignKey]`可以在其中一個關聯性中的導覽屬性上放置註解。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-157">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="e0fbb-158">它不需要相依的實體類別中的導覽屬性上移。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-158">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="e0fbb-159">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="e0fbb-159">[InverseProperty]</span></span>

<span data-ttu-id="e0fbb-160">若要設定的相依及主體實體的導覽屬性如何配對，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-160">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="e0fbb-161">這通常是多個對兩個實體類型之間的導覽屬性時。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-161">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a><span data-ttu-id="e0fbb-162">Fluent API</span><span class="sxs-lookup"><span data-stu-id="e0fbb-162">Fluent API</span></span>

<span data-ttu-id="e0fbb-163">若要設定關聯性，Fluent API 中，您會開始藉由識別組成關聯性的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-163">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="e0fbb-164">`HasOne` 或`HasMany`識別您開始組態的實體類型上的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-164">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="e0fbb-165">您接著鏈結的呼叫`WithOne`或`WithMany`識別反向導覽。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-165">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="e0fbb-166">`HasOne`/`WithOne` 用於參考導覽屬性和`HasMany` / `WithMany`用於集合導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-166">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a><span data-ttu-id="e0fbb-167">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-167">Single Navigation Property</span></span>

<span data-ttu-id="e0fbb-168">如果您只需要一個導覽屬性，則有無參數多載`WithOne`和`WithMany`。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-168">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="e0fbb-169">這表示有在概念上是參考或關聯性另一端的集合，但未包含在實體類別中的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-169">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a><span data-ttu-id="e0fbb-170">外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="e0fbb-170">Foreign Key</span></span>

<span data-ttu-id="e0fbb-171">您可以使用 Fluent API，若要設定哪些屬性應為外部索引鍵屬性用於指定的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-171">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?highlight=17)]

<span data-ttu-id="e0fbb-172">下列程式碼清單示範如何設定複合外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-172">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?highlight=20)]

<span data-ttu-id="e0fbb-173">您可以使用的字串多載`HasForeignKey(...)`若要設定陰影屬性作為外部索引鍵 (請參閱 <<c2> [ 遮蔽屬性](shadow-properties.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-173">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="e0fbb-174">我們建議您明確地將陰影屬性加入模型之前使用的外部索引鍵 （如下所示）。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-174">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a><span data-ttu-id="e0fbb-175">不含導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-175">Without Navigation Property</span></span>

<span data-ttu-id="e0fbb-176">您不一定需要提供的瀏覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-176">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="e0fbb-177">您可以只需要提供外部索引鍵，其中一端的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-177">You can simply provide a Foreign Key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a><span data-ttu-id="e0fbb-178">主體金鑰</span><span class="sxs-lookup"><span data-stu-id="e0fbb-178">Principal Key</span></span>

<span data-ttu-id="e0fbb-179">如果您想要參考的屬性以外的主索引鍵的外部索引鍵時，您可以使用 Fluent API 來設定主體的索引鍵屬性關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-179">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="e0fbb-180">主體的金鑰會自動設定的屬性是做為替代索引鍵的安裝程式 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-180">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/PrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => s.CarLicensePlate)
            .HasPrincipalKey(c => c.LicensePlate);
    }
}

public class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

<span data-ttu-id="e0fbb-181">下列程式碼清單示範如何設定複合的主體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-181">The following code listing shows how to configure a composite principal key.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CompositePrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => new { s.CarState, s.CarLicensePlate })
            .HasPrincipalKey(c => new { c.State, c.LicensePlate });
    }
}

public class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarState { get; set; }
    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

> [!WARNING]  
> <span data-ttu-id="e0fbb-182">您可以在其中指定主體的索引鍵屬性的順序必須符合指定的外部索引鍵的順序。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-182">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="e0fbb-183">必要和選擇性的關聯性</span><span class="sxs-lookup"><span data-stu-id="e0fbb-183">Required and Optional Relationships</span></span>

<span data-ttu-id="e0fbb-184">您可以使用 Fluent API 來設定關聯性是否必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-184">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="e0fbb-185">最後這會控制外部索引鍵屬性是必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-185">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="e0fbb-186">當您使用陰影狀態外部索引鍵，這是最有用。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-186">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="e0fbb-187">如果您有在您實體類別中的外部索引鍵屬性，則關聯性的 requiredness 取決於外部索引鍵屬性是必要或選擇性 (請參閱[必要和選擇性屬性](required-optional.md)如需詳細資訊資訊）。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-187">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

<!-- [!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/Required.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

### <a name="cascade-delete"></a><span data-ttu-id="e0fbb-188">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="e0fbb-188">Cascade Delete</span></span>

<span data-ttu-id="e0fbb-189">您可以使用 Fluent API 來明確設定指定的關聯性的串聯刪除行為。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-189">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="e0fbb-190">請參閱[串聯刪除](../saving/cascade-delete.md)上儲存的資料區段的每個選項的詳細討論。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-190">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CascadeDelete.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .OnDelete(DeleteBehavior.Cascade);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="other-relationship-patterns"></a><span data-ttu-id="e0fbb-191">其他關聯性模式</span><span class="sxs-lookup"><span data-stu-id="e0fbb-191">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="e0fbb-192">一對一</span><span class="sxs-lookup"><span data-stu-id="e0fbb-192">One-to-one</span></span>

<span data-ttu-id="e0fbb-193">一對一關聯性兩端有參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-193">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="e0fbb-194">它們會遵循相同的慣例，作為一對多關聯性，但唯一索引在外部索引鍵屬性，以確保只有一個相依與每個主體中引進。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-194">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/Relationships/OneToOne.cs?highlight=6,15,16)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

> [!NOTE]  
> <span data-ttu-id="e0fbb-195">EF 會選擇其中一個是它能夠偵測到外部索引鍵屬性為基礎的相依實體。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-195">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="e0fbb-196">如果錯誤的實體會被選為相依，您可以使用 Fluent API，若要修正此問題。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-196">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="e0fbb-197">設定使用 Fluent API 的關聯性，當您使用`HasOne`和`WithOne`方法。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-197">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="e0fbb-198">設定您要指定的相依實體型別-外部索引鍵時注意到提供給泛型參數`HasForeignKey`在下方的清單。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-198">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="e0fbb-199">在一對多關聯性很顯然，參考導覽的實體是相依，而與該集合是主體。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-199">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="e0fbb-200">但這不是因此在一對一關聯性; 因此不需要明確地定義它。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-200">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/OneToOne.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<BlogImage> BlogImages { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasOne(p => p.BlogImage)
            .WithOne(i => i.Blog)
            .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogForeignKey { get; set; }
    public Blog Blog { get; set; }
}
```

### <a name="many-to-many"></a><span data-ttu-id="e0fbb-201">多對多</span><span class="sxs-lookup"><span data-stu-id="e0fbb-201">Many-to-many</span></span>

<span data-ttu-id="e0fbb-202">尚不支援多對多關聯性，而不需要實體類別，以代表聯結的資料表。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-202">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="e0fbb-203">不過，您可以透過包含聯結資料表的對應兩個個別一對多關聯性的實體類別來表示多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0fbb-203">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(pt => new { pt.PostId, pt.TagId });

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Post)
            .WithMany(p => p.PostTags)
            .HasForeignKey(pt => pt.PostId);

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Tag)
            .WithMany(t => t.PostTags)
            .HasForeignKey(pt => pt.TagId);
    }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class Tag
{
    public string TagId { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class PostTag
{
    public int PostId { get; set; }
    public Post Post { get; set; }

    public string TagId { get; set; }
    public Tag Tag { get; set; }
}
```
