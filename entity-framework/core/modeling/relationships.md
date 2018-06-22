---
title: 關聯性的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
ms.technology: entity-framework-core
uid: core/modeling/relationships
ms.openlocfilehash: 1732d32643effb0f12111191ed4ba3abb4182d93
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26053028"
---
# <a name="relationships"></a><span data-ttu-id="0dbf1-102">關聯性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-102">Relationships</span></span>

<span data-ttu-id="0dbf1-103">關聯性定義的兩個實體如何與彼此相關。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="0dbf1-104">在關聯式資料庫中，這是外部索引鍵條件約束所表示。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="0dbf1-105">大部分的這篇文章中的範例會使用一對多關聯性，來示範概念。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="0dbf1-106">如需一對一和多對多關聯性的範例，請參閱[其他關聯性模式](#other-relationship-patterns)發行項的最後一節。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="0dbf1-107">詞彙定義</span><span class="sxs-lookup"><span data-stu-id="0dbf1-107">Definition of Terms</span></span>

<span data-ttu-id="0dbf1-108">有許多的詞彙用於描述關聯性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="0dbf1-109">**相依的實體：** 這是包含外部索引鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="0dbf1-110">有時稱為 'child' 的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="0dbf1-111">**主要實體：** 這是包含的主要/替代索引鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="0dbf1-112">有時稱為 'parent' 的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="0dbf1-113">**外部索引鍵：** 中用來儲存與相關實體的主要索引鍵內容值的相依實體屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="0dbf1-114">**主體的金鑰：** 屬性可唯一識別與主要實體。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="0dbf1-115">這可能是主索引鍵或替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="0dbf1-116">**導覽屬性：** 包含相關的公司實體參考的主體和 （或） 相依實體上定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="0dbf1-117">**集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="0dbf1-118">**參考導覽屬性：** 保留單一相關實體的參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="0dbf1-119">**反向導覽屬性：** 時討論特定的導覽屬性時，此一詞是指關聯性另一端的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="0dbf1-120">下列程式碼清單會顯示一對多關聯性之間`Blog`和`Post`</span><span class="sxs-lookup"><span data-stu-id="0dbf1-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="0dbf1-121">`Post`為相依實體</span><span class="sxs-lookup"><span data-stu-id="0dbf1-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="0dbf1-122">`Blog`是主要實體</span><span class="sxs-lookup"><span data-stu-id="0dbf1-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="0dbf1-123">`Post.BlogId`外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="0dbf1-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="0dbf1-124">`Blog.BlogId`是 （在此情況下它是主索引鍵，而不是替代索引鍵） 的主要索引鍵</span><span class="sxs-lookup"><span data-stu-id="0dbf1-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="0dbf1-125">`Post.Blog`是參考導覽屬性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="0dbf1-126">`Blog.Posts`是集合導覽屬性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="0dbf1-127">`Post.Blog`是反向導覽屬性的`Blog.Posts`（反之亦然）</span><span class="sxs-lookup"><span data-stu-id="0dbf1-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="0dbf1-128">慣例</span><span class="sxs-lookup"><span data-stu-id="0dbf1-128">Conventions</span></span>

<span data-ttu-id="0dbf1-129">依照慣例，在類型上探索到的導覽屬性時將會建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="0dbf1-130">屬性會被視為導覽屬性，如果它所指向的類型可以對應為純量型別目前的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="0dbf1-131">關聯性探索到的慣例會永遠以目標主要實體的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="0dbf1-132">若要以其他的索引鍵為目標，額外的設定必須使用 Fluent API 執行。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="0dbf1-133">完整定義的關聯性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-133">Fully Defined Relationships</span></span>

<span data-ttu-id="0dbf1-134">關聯性的最常見的模式是已定義這兩個端點的關聯性和相依的實體類別中定義的外部索引鍵屬性的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="0dbf1-135">如果找不到導覽屬性配對是兩個類型之間，它們將會設定為相同關聯性的反向導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="0dbf1-136">如果相依的實體包含一個名為屬性`<primary key property name>`， `<navigation property name><primary key property name>`，或`<principal entity name><primary key property name>`接著，它會設定為外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="0dbf1-137">如果有多個定義兩個類型之間的導覽屬性 (也就是多個相異點彼此的巡覽組) 慣例再建立任何關聯性，您必須手動將其設定為識別方式導覽屬性配對。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-137">If there are multiple navigation properties defined between two types (i.e. more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="0dbf1-138">沒有外部索引鍵屬性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-138">No Foreign Key Property</span></span>

<span data-ttu-id="0dbf1-139">雖然建議有相依的實體類別中定義的外部索引鍵屬性，並不需要。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="0dbf1-140">如果不找到任何外部索引鍵屬性，將介紹陰影的外部索引鍵屬性具有名稱`<navigation property name><principal key property name>`(請參閱[遮蔽屬性](shadow-properties.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="0dbf1-141">單一瀏覽屬性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-141">Single Navigation Property</span></span>

<span data-ttu-id="0dbf1-142">包括一個導覽屬性 （沒有反向導覽，以及任何外部索引鍵屬性），即已足夠有依慣例定義關聯性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="0dbf1-143">您也可以讓單一導覽屬性和外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="0dbf1-144">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="0dbf1-144">Cascade Delete</span></span>

<span data-ttu-id="0dbf1-145">依照慣例，cascade delete 將會設定為*Cascade*必要的關聯性和*ClientSetNull*選擇性的關聯性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="0dbf1-146">*Cascade*表示相依實體也會一併刪除。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="0dbf1-147">*ClientSetNull*方法不會載入記憶體的相依實體將會保留不變且必須以手動方式刪除，或更新為指向有效的主要實體。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="0dbf1-148">載入到記憶體的實體，EF 核心會嘗試將外部索引鍵屬性設定為 null。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="0dbf1-149">請參閱[必要和選擇性的關聯性](#required-and-optional-relationships)必要和選擇性的關聯性之間的差異 > 一節。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="0dbf1-150">請參閱[Cascade Delete](../saving/cascade-delete.md)的更多詳細的不同刪除行為和使用慣例的預設值。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="0dbf1-151">資料註釋</span><span class="sxs-lookup"><span data-stu-id="0dbf1-151">Data Annotations</span></span>

<span data-ttu-id="0dbf1-152">有兩個可用來設定關聯性的資料註解`[ForeignKey]`和`[InverseProperty]`。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="0dbf1-153">[ForeignKey]</span><span class="sxs-lookup"><span data-stu-id="0dbf1-153">[ForeignKey]</span></span>

<span data-ttu-id="0dbf1-154">若要設定哪些屬性應作為外部索引鍵屬性指定的關聯性，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-154">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="0dbf1-155">這通常是依照慣例探索不到外部索引鍵屬性時。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-155">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?name=Entities&highlight=17)]

> [!TIP]  
> <span data-ttu-id="0dbf1-156">`[ForeignKey]`註解可以放在關聯性中的任一個導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-156">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="0dbf1-157">不需要相依的實體類別中的導覽屬性上移。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-157">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="0dbf1-158">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="0dbf1-158">[InverseProperty]</span></span>

<span data-ttu-id="0dbf1-159">您可以使用資料註解來設定相依及主體實體上的導覽屬性如何配對。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-159">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="0dbf1-160">這通常是一對以上兩個實體類型之間的導覽屬性時。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-160">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?name=Entities&highlight=20,23)]

## <a name="fluent-api"></a><span data-ttu-id="0dbf1-161">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="0dbf1-161">Fluent API</span></span>

<span data-ttu-id="0dbf1-162">若要設定關聯性 Fluent API 中，您必須啟動用來識別組成關聯性的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="0dbf1-163">`HasOne`或`HasMany`識別您在開始設定的實體類型上的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="0dbf1-164">您接著鏈結呼叫`WithOne`或`WithMany`來識別反向導覽。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="0dbf1-165">`HasOne`/`WithOne`用於參考導覽屬性和`HasMany` / `WithMany`用於集合導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?name=Model&highlight=8,9,10)]

### <a name="single-navigation-property"></a><span data-ttu-id="0dbf1-166">單一瀏覽屬性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-166">Single Navigation Property</span></span>

<span data-ttu-id="0dbf1-167">如果您只需要一個導覽屬性，則有無參數多載的`WithOne`和`WithMany`。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-167">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="0dbf1-168">這表示有在概念上是參考或關聯性另一端的集合，但沒有包含在實體類別中的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-168">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?name=Model&highlight=10)]

### <a name="foreign-key"></a><span data-ttu-id="0dbf1-169">外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="0dbf1-169">Foreign Key</span></span>

<span data-ttu-id="0dbf1-170">您可以使用 fluent 應用程式開發的應用程式開發介面來設定哪些屬性應為外部索引鍵屬性用於指定關聯性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-170">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?name=Model&highlight=11)]

<span data-ttu-id="0dbf1-171">下列程式碼清單示範如何設定複合外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-171">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?name=Model&highlight=13)]

<span data-ttu-id="0dbf1-172">您可以使用的字串多載`HasForeignKey(...)`陰影屬性設定為外部索引鍵 (請參閱[遮蔽屬性](shadow-properties.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-172">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="0dbf1-173">我們建議您明確陰影屬性加入至模型之前使用的外部索引鍵為 （如下所示）。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-173">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a><span data-ttu-id="0dbf1-174">主體金鑰</span><span class="sxs-lookup"><span data-stu-id="0dbf1-174">Principal Key</span></span>

<span data-ttu-id="0dbf1-175">如果您想要參考的屬性以外的主索引鍵的外部索引鍵，您可以使用 fluent 應用程式開發的應用程式開發介面來設定關聯性的主要索引鍵內容。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-175">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="0dbf1-176">您將設定為主要索引鍵會自動屬性是做為替代索引鍵的安裝程式 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-176">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

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

<span data-ttu-id="0dbf1-177">下列程式碼清單示範如何設定複合主體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-177">The following code listing shows how to configure a composite principal key.</span></span>

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
> <span data-ttu-id="0dbf1-178">您可以在其中指定主體的索引鍵屬性的順序必須符合指定的外部索引鍵的順序。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-178">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="0dbf1-179">必要和選擇性的關聯性</span><span class="sxs-lookup"><span data-stu-id="0dbf1-179">Required and Optional Relationships</span></span>

<span data-ttu-id="0dbf1-180">您可以設定關聯性是否必要或選擇性使用 fluent 應用程式開發的應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-180">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="0dbf1-181">最終這會控制外部索引鍵屬性是必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-181">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="0dbf1-182">當您使用陰影狀態外部索引鍵，這是最有用。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-182">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="0dbf1-183">如果您有實體類別中的外部索引鍵屬性，則關聯性的 requiredness 取決於外部索引鍵屬性是必要或選擇性 (請參閱[必要與選用屬性](required-optional.md)如需詳細資訊資訊）。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-183">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

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

### <a name="cascade-delete"></a><span data-ttu-id="0dbf1-184">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="0dbf1-184">Cascade Delete</span></span>

<span data-ttu-id="0dbf1-185">您可以使用 Fluent API 來明確設定指定的關聯性的串聯刪除行為。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-185">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="0dbf1-186">請參閱[Cascade Delete](../saving/cascade-delete.md)上儲存的資料區段的每個選項的詳細討論。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-186">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

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

## <a name="other-relationship-patterns"></a><span data-ttu-id="0dbf1-187">其他關聯性模式</span><span class="sxs-lookup"><span data-stu-id="0dbf1-187">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="0dbf1-188">一對一</span><span class="sxs-lookup"><span data-stu-id="0dbf1-188">One-to-one</span></span>

<span data-ttu-id="0dbf1-189">一對一關聯性兩端具有參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-189">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="0dbf1-190">它們遵循相同的慣例，作為一對多關聯性，但外部索引鍵屬性，以確保只有一個相關與每個主體上引進唯一索引。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-190">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

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
> <span data-ttu-id="0dbf1-191">EF 會選擇其中一個要能夠偵測到外部索引鍵屬性為基礎的相依實體。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-191">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="0dbf1-192">如果不正確的實體會被選為相依項，您可以使用 fluent 應用程式開發的應用程式開發介面來修正這個問題。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-192">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="0dbf1-193">設定關聯性 fluent 應用程式開發的應用程式開發介面，當您使用`HasOne`和`WithOne`方法。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-193">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="0dbf1-194">設定您需要指定的相依實體類型的外部索引鍵時發現提供給泛型參數`HasForeignKey`下面的清單中。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-194">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="0dbf1-195">在一對多關聯性會清除使用參考瀏覽該實體是相依，而與集合是主體。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-195">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="0dbf1-196">但這不是一對一的關聯性-因此，不需要明確定義它。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-196">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

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

### <a name="many-to-many"></a><span data-ttu-id="0dbf1-197">多對多</span><span class="sxs-lookup"><span data-stu-id="0dbf1-197">Many-to-many</span></span>

<span data-ttu-id="0dbf1-198">尚不支援多對多關聯性沒有實體類別，以代表聯結的資料表。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-198">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="0dbf1-199">不過，您可以藉由聯結資料表和對應兩個不同的一對多關聯性的實體類別表示多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0dbf1-199">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(t => new { t.PostId, t.TagId });

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
