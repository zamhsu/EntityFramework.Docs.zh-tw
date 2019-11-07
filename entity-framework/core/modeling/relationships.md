---
title: 關聯性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 1e59ce9e19c12aa5564bc8467dcfcb3be8ee8996
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655668"
---
# <a name="relationships"></a><span data-ttu-id="32f64-102">關聯性</span><span class="sxs-lookup"><span data-stu-id="32f64-102">Relationships</span></span>

<span data-ttu-id="32f64-103">關聯性會定義兩個實體之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="32f64-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="32f64-104">在關係資料庫中，這是以 foreign key 條件約束來表示。</span><span class="sxs-lookup"><span data-stu-id="32f64-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="32f64-105">本文中的大部分範例都會使用一對多關聯性來示範概念。</span><span class="sxs-lookup"><span data-stu-id="32f64-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="32f64-106">如需一對一和多對多關聯性的範例，請參閱本文結尾的[其他關聯性模式](#other-relationship-patterns)一節。</span><span class="sxs-lookup"><span data-stu-id="32f64-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="32f64-107">詞彙的定義</span><span class="sxs-lookup"><span data-stu-id="32f64-107">Definition of Terms</span></span>

<span data-ttu-id="32f64-108">有數個詞彙用來描述關聯性</span><span class="sxs-lookup"><span data-stu-id="32f64-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="32f64-109">**相依實體：** 這是包含外鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="32f64-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="32f64-110">有時稱為關聯性的「子系」。</span><span class="sxs-lookup"><span data-stu-id="32f64-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="32f64-111">**主體實體：** 這是包含主要/替代索引鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="32f64-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="32f64-112">有時稱為關聯性的「父系」。</span><span class="sxs-lookup"><span data-stu-id="32f64-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="32f64-113">**外鍵：** 相依實體中的屬性，用來儲存與實體相關之主要索引鍵屬性的值。</span><span class="sxs-lookup"><span data-stu-id="32f64-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="32f64-114">**主要金鑰：** 可唯一識別主體實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="32f64-115">這可能是主要金鑰或替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="32f64-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="32f64-116">**導覽屬性：** 在主體和/或相依實體上定義的屬性，其中包含相關實體的參考。</span><span class="sxs-lookup"><span data-stu-id="32f64-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="32f64-117">**集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。</span><span class="sxs-lookup"><span data-stu-id="32f64-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="32f64-118">**參考導覽屬性：** 保存單一相關實體之參考的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="32f64-119">**反向導覽屬性：** 在討論特定導覽屬性時，此詞彙是指關聯性另一端的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="32f64-120">下列程式代碼清單顯示 `Blog` 與 `Post` 之間的一對多關聯性</span><span class="sxs-lookup"><span data-stu-id="32f64-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="32f64-121">`Post` 是相依實體</span><span class="sxs-lookup"><span data-stu-id="32f64-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="32f64-122">`Blog` 是主體實體</span><span class="sxs-lookup"><span data-stu-id="32f64-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="32f64-123">`Post.BlogId` 是外鍵</span><span class="sxs-lookup"><span data-stu-id="32f64-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="32f64-124">`Blog.BlogId` 是主要金鑰（在此案例中，它是主鍵，而不是替代金鑰）</span><span class="sxs-lookup"><span data-stu-id="32f64-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="32f64-125">`Post.Blog` 是參考導覽屬性</span><span class="sxs-lookup"><span data-stu-id="32f64-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="32f64-126">`Blog.Posts` 是集合導覽屬性</span><span class="sxs-lookup"><span data-stu-id="32f64-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="32f64-127">`Post.Blog` 是 `Blog.Posts` 的反向導覽屬性（反之亦然）</span><span class="sxs-lookup"><span data-stu-id="32f64-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="32f64-128">慣例</span><span class="sxs-lookup"><span data-stu-id="32f64-128">Conventions</span></span>

<span data-ttu-id="32f64-129">依照慣例，在類型上發現導覽屬性時，將會建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="32f64-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="32f64-130">如果所指向的型別無法由目前的資料庫提供者對應為純量型別，則會將屬性視為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="32f64-131">依照慣例探索到的關聯性一律會以主體實體的主要金鑰為目標。</span><span class="sxs-lookup"><span data-stu-id="32f64-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="32f64-132">若要以替代金鑰為目標，必須使用流暢的 API 來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="32f64-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="32f64-133">完整定義的關聯性</span><span class="sxs-lookup"><span data-stu-id="32f64-133">Fully Defined Relationships</span></span>

<span data-ttu-id="32f64-134">關聯性最常見的模式是讓關聯性兩端定義的導覽屬性，以及相依實體類別中定義的外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="32f64-135">如果在兩個類型之間找到一對導覽屬性，則會將它們設定為相同關聯性的反向導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="32f64-136">如果相依實體包含名為 `<primary key property name>`、`<navigation property name><primary key property name>`或 `<principal entity name><primary key property name>` 的屬性，則會將它設定為外鍵。</span><span class="sxs-lookup"><span data-stu-id="32f64-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="32f64-137">如果在兩個類型之間定義了多個導覽屬性（也就是多個彼此指向的不同流覽），則不會依照慣例建立關聯性，而且您必須手動設定它們，以識別導覽屬性配對。</span><span class="sxs-lookup"><span data-stu-id="32f64-137">If there are multiple navigation properties defined between two types (that is, more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="32f64-138">沒有外鍵屬性</span><span class="sxs-lookup"><span data-stu-id="32f64-138">No Foreign Key Property</span></span>

<span data-ttu-id="32f64-139">雖然建議在相依實體類別中定義外鍵屬性，但它並不是必要的。</span><span class="sxs-lookup"><span data-stu-id="32f64-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="32f64-140">如果找不到任何外鍵屬性，將會以名稱 `<navigation property name><principal key property name>` 引進陰影外鍵屬性（如需詳細資訊，請參閱[陰影屬性](shadow-properties.md)）。</span><span class="sxs-lookup"><span data-stu-id="32f64-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="32f64-141">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="32f64-141">Single Navigation Property</span></span>

<span data-ttu-id="32f64-142">只包含一個導覽屬性（沒有反向導覽，而且沒有外鍵屬性）就足以擁有依照慣例定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="32f64-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="32f64-143">您也可以有一個導覽屬性和一個外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="32f64-144">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="32f64-144">Cascade Delete</span></span>

<span data-ttu-id="32f64-145">依照慣例，串聯刪除會針對必要關聯性設定為*cascade* ，並針對選擇性關聯性*deletebehavior.clientsetnull* 。</span><span class="sxs-lookup"><span data-stu-id="32f64-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="32f64-146">*Cascade*表示也會刪除相依的實體。</span><span class="sxs-lookup"><span data-stu-id="32f64-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="32f64-147">*Deletebehavior.clientsetnull*表示未載入記憶體的相依實體會維持不變，必須手動刪除或更新，以指向有效的主體實體。</span><span class="sxs-lookup"><span data-stu-id="32f64-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="32f64-148">若為載入記憶體中的實體，EF Core 會嘗試將外鍵屬性設定為 null。</span><span class="sxs-lookup"><span data-stu-id="32f64-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="32f64-149">如需必要和選擇性關聯性之間的差異，請參閱[必要和選擇性關聯](#required-and-optional-relationships)性一節。</span><span class="sxs-lookup"><span data-stu-id="32f64-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="32f64-150">如需不同刪除行為和慣例所使用之預設值的詳細資訊，請參閱[Cascade Delete](../saving/cascade-delete.md) 。</span><span class="sxs-lookup"><span data-stu-id="32f64-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="32f64-151">資料註釋</span><span class="sxs-lookup"><span data-stu-id="32f64-151">Data Annotations</span></span>

<span data-ttu-id="32f64-152">有兩個數據批註可用來設定關聯性、`[ForeignKey]` 和 `[InverseProperty]`。</span><span class="sxs-lookup"><span data-stu-id="32f64-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span> <span data-ttu-id="32f64-153">這些可在 `System.ComponentModel.DataAnnotations.Schema` 命名空間中取得。</span><span class="sxs-lookup"><span data-stu-id="32f64-153">These are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="32f64-154">[ForeignKey]</span><span class="sxs-lookup"><span data-stu-id="32f64-154">[ForeignKey]</span></span>

<span data-ttu-id="32f64-155">您可以使用資料批註來設定應該使用哪一個屬性做為指定關聯性的外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-155">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="32f64-156">這通常是在不依照慣例探索外鍵屬性時進行。</span><span class="sxs-lookup"><span data-stu-id="32f64-156">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> <span data-ttu-id="32f64-157">`[ForeignKey]` 注釋可以放在關聯性中的任一導覽屬性上。</span><span class="sxs-lookup"><span data-stu-id="32f64-157">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="32f64-158">不需要移至相依實體類別中的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-158">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="32f64-159">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="32f64-159">[InverseProperty]</span></span>

<span data-ttu-id="32f64-160">您可以使用資料批註來設定相依和主體實體上的導覽屬性如何配對。</span><span class="sxs-lookup"><span data-stu-id="32f64-160">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="32f64-161">當兩個實體類型之間有一對以上的導覽屬性時，通常就會執行這項作業。</span><span class="sxs-lookup"><span data-stu-id="32f64-161">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a><span data-ttu-id="32f64-162">Fluent API</span><span class="sxs-lookup"><span data-stu-id="32f64-162">Fluent API</span></span>

<span data-ttu-id="32f64-163">若要在流暢的 API 中設定關聯性，您一開始會先識別構成關聯性的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-163">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="32f64-164">`HasOne` 或 `HasMany` 會識別您開始設定之實體類型的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-164">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="32f64-165">接著，您會將呼叫連結至 `WithOne` 或 `WithMany`，以識別反向導覽。</span><span class="sxs-lookup"><span data-stu-id="32f64-165">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="32f64-166">`HasOne`/`WithOne` 用於參考導覽屬性，而 `HasMany`/`WithMany` 用於集合導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-166">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a><span data-ttu-id="32f64-167">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="32f64-167">Single Navigation Property</span></span>

<span data-ttu-id="32f64-168">如果您只有一個導覽屬性，則會有 `WithOne` 和 `WithMany`的無參數多載。</span><span class="sxs-lookup"><span data-stu-id="32f64-168">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="32f64-169">這表示概念上的另一端有參考或集合，但實體類別中沒有包含任何導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-169">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a><span data-ttu-id="32f64-170">外鍵</span><span class="sxs-lookup"><span data-stu-id="32f64-170">Foreign Key</span></span>

<span data-ttu-id="32f64-171">您可以使用流暢的 API 來設定應該使用哪一個屬性做為指定之關聯性的外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-171">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?highlight=17)]

<span data-ttu-id="32f64-172">下列程式代碼清單顯示如何設定複合外鍵。</span><span class="sxs-lookup"><span data-stu-id="32f64-172">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?highlight=20)]

<span data-ttu-id="32f64-173">您可以使用 `HasForeignKey(...)` 的字串多載，將陰影屬性設定為外鍵（如需詳細資訊，請參閱[陰影屬性](shadow-properties.md)）。</span><span class="sxs-lookup"><span data-stu-id="32f64-173">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="32f64-174">我們建議您先將 shadow 屬性明確加入至模型，再將其當做外鍵使用（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="32f64-174">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a><span data-ttu-id="32f64-175">沒有導覽屬性</span><span class="sxs-lookup"><span data-stu-id="32f64-175">Without Navigation Property</span></span>

<span data-ttu-id="32f64-176">您不一定需要提供導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-176">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="32f64-177">您可以直接在關聯性的一端提供外鍵。</span><span class="sxs-lookup"><span data-stu-id="32f64-177">You can simply provide a Foreign Key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a><span data-ttu-id="32f64-178">主要金鑰</span><span class="sxs-lookup"><span data-stu-id="32f64-178">Principal Key</span></span>

<span data-ttu-id="32f64-179">如果您想要外鍵參考主鍵以外的屬性，您可以使用流暢的 API 來設定關聯性的主體索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-179">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="32f64-180">您設定為主體金鑰的屬性會自動設定為替代金鑰（如需詳細資訊，請參閱[替代金鑰](alternate-keys.md)）。</span><span class="sxs-lookup"><span data-stu-id="32f64-180">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

<span data-ttu-id="32f64-181">下列程式代碼清單顯示如何設定複合主體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="32f64-181">The following code listing shows how to configure a composite principal key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=Composite&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="32f64-182">指定主體索引鍵屬性的順序必須符合為外鍵指定的順序。</span><span class="sxs-lookup"><span data-stu-id="32f64-182">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="32f64-183">必要和選擇性的關聯性</span><span class="sxs-lookup"><span data-stu-id="32f64-183">Required and Optional Relationships</span></span>

<span data-ttu-id="32f64-184">您可以使用流暢的 API 來設定關聯性是否為必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="32f64-184">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="32f64-185">最後，這會控制外鍵屬性是否為必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="32f64-185">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="32f64-186">當您使用陰影狀態外鍵時，這是最有用的。</span><span class="sxs-lookup"><span data-stu-id="32f64-186">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="32f64-187">如果您的實體類別中有外鍵屬性，則關聯性的必要性取決於外鍵屬性是否為必要或選擇性（如需詳細資訊，請參閱[必要和選擇性屬性](required-optional.md)）。</span><span class="sxs-lookup"><span data-stu-id="32f64-187">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=11)]

### <a name="cascade-delete"></a><span data-ttu-id="32f64-188">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="32f64-188">Cascade Delete</span></span>

<span data-ttu-id="32f64-189">您可以使用流暢的 API，為指定的關聯性明確設定串聯刪除行為。</span><span class="sxs-lookup"><span data-stu-id="32f64-189">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="32f64-190">如需每個選項的詳細討論，請參閱儲存資料的重迭[刪除](../saving/cascade-delete.md)一節。</span><span class="sxs-lookup"><span data-stu-id="32f64-190">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=11)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="32f64-191">其他關聯性模式</span><span class="sxs-lookup"><span data-stu-id="32f64-191">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="32f64-192">一對一</span><span class="sxs-lookup"><span data-stu-id="32f64-192">One-to-one</span></span>

<span data-ttu-id="32f64-193">一對一關聯性在兩端都有參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="32f64-193">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="32f64-194">它們遵循與一對多關聯性相同的慣例，但是在外鍵屬性上引進了唯一的索引，以確保只有一個相依性與每個主體相關。</span><span class="sxs-lookup"><span data-stu-id="32f64-194">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=Property&highlight=6,15,16)]

> [!NOTE]  
> <span data-ttu-id="32f64-195">EF 會根據其偵測外鍵屬性的能力，選擇要相依的其中一個實體。</span><span class="sxs-lookup"><span data-stu-id="32f64-195">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="32f64-196">如果選擇錯誤的實體做為相依專案，您可以使用流暢的 API 來修正此問題。</span><span class="sxs-lookup"><span data-stu-id="32f64-196">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="32f64-197">設定與流暢 API 的關聯性時，您會使用 `HasOne` 和 `WithOne` 方法。</span><span class="sxs-lookup"><span data-stu-id="32f64-197">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="32f64-198">設定外鍵時，您需要指定相依實體類型-請注意，提供給下面清單中 `HasForeignKey` 的泛型參數。</span><span class="sxs-lookup"><span data-stu-id="32f64-198">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="32f64-199">在一對多關聯性中，明確地指出具有參考導覽的實體是相依的，而具有該集合的實體是主體。</span><span class="sxs-lookup"><span data-stu-id="32f64-199">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="32f64-200">但這並不是一對一關聯性，因此需要明確定義。</span><span class="sxs-lookup"><span data-stu-id="32f64-200">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="32f64-201">多對多</span><span class="sxs-lookup"><span data-stu-id="32f64-201">Many-to-many</span></span>

<span data-ttu-id="32f64-202">尚未支援多對多關聯性，但沒有實體類別來表示聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="32f64-202">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="32f64-203">不過，您可以藉由包含聯結資料表的實體類別並對應兩個不同的一對多關聯性，來代表多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="32f64-203">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)]
