---
title: 關聯性-EF Core
description: 如何在使用 Entity Framework Core 時，設定實體類型之間的關聯性
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 927457c2a5b5ef4a5061fe2e5d28f864eb95c55f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526728"
---
# <a name="relationships"></a><span data-ttu-id="d80fd-103">關聯性</span><span class="sxs-lookup"><span data-stu-id="d80fd-103">Relationships</span></span>

<span data-ttu-id="d80fd-104">關聯性會定義兩個實體之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="d80fd-105">在關係資料庫中，這是以 foreign key 條件約束來表示。</span><span class="sxs-lookup"><span data-stu-id="d80fd-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="d80fd-106">本文中的大部分範例都會使用一對多關聯性來示範概念。</span><span class="sxs-lookup"><span data-stu-id="d80fd-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="d80fd-107">如需一對一和多對多關聯性的範例，請參閱本文結尾的[其他關聯性模式](#other-relationship-patterns)一節。</span><span class="sxs-lookup"><span data-stu-id="d80fd-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="d80fd-108">詞彙的定義</span><span class="sxs-lookup"><span data-stu-id="d80fd-108">Definition of terms</span></span>

<span data-ttu-id="d80fd-109">有數個詞彙用來描述關聯性</span><span class="sxs-lookup"><span data-stu-id="d80fd-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="d80fd-110">**相依實體：** 這是包含外鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="d80fd-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="d80fd-111">有時稱為關聯性的「子系」。</span><span class="sxs-lookup"><span data-stu-id="d80fd-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="d80fd-112">**主體實體：** 這是包含主要/替代索引鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="d80fd-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="d80fd-113">有時稱為關聯性的「父系」。</span><span class="sxs-lookup"><span data-stu-id="d80fd-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="d80fd-114">**主要金鑰：** 可唯一識別主體實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="d80fd-115">這可能是主要金鑰或替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d80fd-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="d80fd-116">**外鍵：** 相依實體中用來儲存相關實體之主體索引鍵值的屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="d80fd-117">**導覽屬性：** 定義于主體和/或相依實體（參考相關實體）上的屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="d80fd-118">**集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。</span><span class="sxs-lookup"><span data-stu-id="d80fd-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="d80fd-119">**參考導覽屬性：** 保存單一相關實體之參考的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="d80fd-120">**反向導覽屬性：** 在討論特定導覽屬性時，此詞彙是指關聯性另一端的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="d80fd-121">**自我參考關聯性：** 相依和主體實體類型相同的關聯性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="d80fd-122">下列程式碼顯示和之間的一對多關聯性 `Blog``Post`</span><span class="sxs-lookup"><span data-stu-id="d80fd-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="d80fd-123">`Post`是相依實體</span><span class="sxs-lookup"><span data-stu-id="d80fd-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="d80fd-124">`Blog`是主體實體</span><span class="sxs-lookup"><span data-stu-id="d80fd-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="d80fd-125">`Blog.BlogId`是主要金鑰（在此案例中，它是主鍵，而不是替代索引鍵）</span><span class="sxs-lookup"><span data-stu-id="d80fd-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="d80fd-126">`Post.BlogId`是外鍵</span><span class="sxs-lookup"><span data-stu-id="d80fd-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="d80fd-127">`Post.Blog`是參考導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d80fd-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="d80fd-128">`Blog.Posts`是集合導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d80fd-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="d80fd-129">`Post.Blog`是的反向導覽屬性 `Blog.Posts` （反之亦然）</span><span class="sxs-lookup"><span data-stu-id="d80fd-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="d80fd-130">慣例</span><span class="sxs-lookup"><span data-stu-id="d80fd-130">Conventions</span></span>

<span data-ttu-id="d80fd-131">根據預設，當在類型上發現導覽屬性時，就會建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="d80fd-132">如果所指向的型別無法由目前的資料庫提供者對應為純量型別，則會將屬性視為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="d80fd-133">依照慣例探索到的關聯性一律會以主體實體的主要金鑰為目標。</span><span class="sxs-lookup"><span data-stu-id="d80fd-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="d80fd-134">若要以替代金鑰為目標，必須使用流暢的 API 來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="d80fd-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="d80fd-135">完整定義的關聯性</span><span class="sxs-lookup"><span data-stu-id="d80fd-135">Fully defined relationships</span></span>

<span data-ttu-id="d80fd-136">關聯性最常見的模式是讓關聯性兩端定義的導覽屬性，以及相依實體類別中定義的外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="d80fd-137">如果在兩個類型之間找到一對導覽屬性，則會將它們設定為相同關聯性的反向導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="d80fd-138">如果相依實體包含名稱符合這些模式之一的屬性，則會將它設定為外鍵：</span><span class="sxs-lookup"><span data-stu-id="d80fd-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="d80fd-139">在此範例中，反白顯示的屬性將用來設定關聯性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="d80fd-140">如果屬性是主要索引鍵，或者是與主體索引鍵不相容的類型，則不會將它設定為外鍵。</span><span class="sxs-lookup"><span data-stu-id="d80fd-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="d80fd-141">在 EF Core 3.0 之前，名為與主體索引鍵屬性完全相同的屬性也會與[外鍵相符](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span><span class="sxs-lookup"><span data-stu-id="d80fd-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="d80fd-142">沒有外鍵屬性</span><span class="sxs-lookup"><span data-stu-id="d80fd-142">No foreign key property</span></span>

<span data-ttu-id="d80fd-143">雖然建議在相依實體類別中定義外鍵屬性，但它並不是必要的。</span><span class="sxs-lookup"><span data-stu-id="d80fd-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="d80fd-144">如果找不到外鍵屬性，則會以名稱導入[陰影外鍵屬性](shadow-properties.md)， `<navigation property name><principal key property name>` `<principal entity name><principal key property name>` 如果相依類型上沒有任何導覽，則會引進。</span><span class="sxs-lookup"><span data-stu-id="d80fd-144">If no foreign key property is found, a [shadow foreign key property](shadow-properties.md) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="d80fd-145">在此範例中，陰影外鍵是 `BlogId` 因為預先填入導覽名稱會是多餘的。</span><span class="sxs-lookup"><span data-stu-id="d80fd-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="d80fd-146">如果已經有同名的屬性，則陰影屬性名稱的後面會加上一個數位。</span><span class="sxs-lookup"><span data-stu-id="d80fd-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="d80fd-147">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d80fd-147">Single navigation property</span></span>

<span data-ttu-id="d80fd-148">只包含一個導覽屬性（沒有反向導覽，而且沒有外鍵屬性）就足以擁有依照慣例定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="d80fd-149">您也可以有一個導覽屬性和一個外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="d80fd-150">限制</span><span class="sxs-lookup"><span data-stu-id="d80fd-150">Limitations</span></span>

<span data-ttu-id="d80fd-151">當兩個類型之間有多個導覽屬性定義時（亦即，不只是一個指向彼此的一對流覽），導覽屬性所表示的關聯性不明確。</span><span class="sxs-lookup"><span data-stu-id="d80fd-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="d80fd-152">您將需要手動設定它們來解決不明確的問題。</span><span class="sxs-lookup"><span data-stu-id="d80fd-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="d80fd-153">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="d80fd-153">Cascade delete</span></span>

<span data-ttu-id="d80fd-154">依照慣例，串聯刪除會針對必要關聯性設定為*cascade* ，並針對選擇性關聯性*deletebehavior.clientsetnull* 。</span><span class="sxs-lookup"><span data-stu-id="d80fd-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="d80fd-155">*Cascade*表示也會刪除相依的實體。</span><span class="sxs-lookup"><span data-stu-id="d80fd-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="d80fd-156">*Deletebehavior.clientsetnull*表示未載入記憶體的相依實體會維持不變，必須手動刪除或更新，以指向有效的主體實體。</span><span class="sxs-lookup"><span data-stu-id="d80fd-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="d80fd-157">若為載入記憶體中的實體，EF Core 會嘗試將外鍵屬性設定為 null。</span><span class="sxs-lookup"><span data-stu-id="d80fd-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="d80fd-158">如需必要和選擇性關聯性之間的差異，請參閱[必要和選擇性關聯](#required-and-optional-relationships)性一節。</span><span class="sxs-lookup"><span data-stu-id="d80fd-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="d80fd-159">如需不同刪除行為和慣例所使用之預設值的詳細資訊，請參閱[Cascade Delete](../saving/cascade-delete.md) 。</span><span class="sxs-lookup"><span data-stu-id="d80fd-159">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="d80fd-160">手動設定</span><span class="sxs-lookup"><span data-stu-id="d80fd-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="d80fd-161">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d80fd-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="d80fd-162">若要在流暢的 API 中設定關聯性，您一開始會先識別構成關聯性的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="d80fd-163">`HasOne`或者，在 `HasMany` 您開始設定的實體類型上，識別導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="d80fd-164">接著，您可以將呼叫連結至 `WithOne` 或 `WithMany` ，以識別反向導覽。</span><span class="sxs-lookup"><span data-stu-id="d80fd-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="d80fd-165">`HasOne`/`WithOne`用於參考導覽屬性，並 `HasMany` / `WithMany` 用於集合導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="d80fd-166">資料註解</span><span class="sxs-lookup"><span data-stu-id="d80fd-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="d80fd-167">您可以使用資料批註來設定相依和主體實體上的導覽屬性如何配對。</span><span class="sxs-lookup"><span data-stu-id="d80fd-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="d80fd-168">當兩個實體類型之間有一對以上的導覽屬性時，通常就會執行這項作業。</span><span class="sxs-lookup"><span data-stu-id="d80fd-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="d80fd-169">您只能在相依實體的屬性上使用 [Required]，以影響關聯性的必要性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="d80fd-170">[必要] 通常會忽略主體實體的導覽，但可能會導致實體成為相依專案。</span><span class="sxs-lookup"><span data-stu-id="d80fd-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="d80fd-171">資料批註 `[ForeignKey]` 和可以 `[InverseProperty]` 在 `System.ComponentModel.DataAnnotations.Schema` 命名空間中使用。</span><span class="sxs-lookup"><span data-stu-id="d80fd-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="d80fd-172">`[Required]`可在 `System.ComponentModel.DataAnnotations` 命名空間中使用。</span><span class="sxs-lookup"><span data-stu-id="d80fd-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="d80fd-173">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d80fd-173">Single navigation property</span></span>

<span data-ttu-id="d80fd-174">如果您只有一個導覽屬性，則會有和的無參數多載 `WithOne` `WithMany` 。</span><span class="sxs-lookup"><span data-stu-id="d80fd-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="d80fd-175">這表示概念上的另一端有參考或集合，但實體類別中沒有包含任何導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="d80fd-176">設定導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d80fd-176">Configuring navigation properties</span></span>

<span data-ttu-id="d80fd-177">建立導覽屬性之後，您可能需要進一步設定它。</span><span class="sxs-lookup"><span data-stu-id="d80fd-177">After the navigation property has been created, you may need to further configure it.</span></span> <span data-ttu-id="d80fd-178">在 EFCore 5.0 中，新增了新的流暢 API，可讓您執行該設定。</span><span class="sxs-lookup"><span data-stu-id="d80fd-178">In EFCore 5.0, new Fluent API is added to allow you to perform that configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> <span data-ttu-id="d80fd-179">這個呼叫不能用來建立導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="d80fd-180">它只會用來設定導覽屬性，先前已藉由定義關聯性或從慣例來建立。</span><span class="sxs-lookup"><span data-stu-id="d80fd-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="d80fd-181">外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="d80fd-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="d80fd-182">流暢的 API （簡單金鑰）</span><span class="sxs-lookup"><span data-stu-id="d80fd-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="d80fd-183">您可以使用流暢的 API 來設定應該使用哪一個屬性做為指定關聯性的外鍵屬性：</span><span class="sxs-lookup"><span data-stu-id="d80fd-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="d80fd-184">流暢 API （複合索引鍵）</span><span class="sxs-lookup"><span data-stu-id="d80fd-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="d80fd-185">您可以使用流暢的 API 來設定應該使用哪些屬性做為指定關聯性的複合外鍵屬性：</span><span class="sxs-lookup"><span data-stu-id="d80fd-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="d80fd-186">資料批註（簡單索引鍵）</span><span class="sxs-lookup"><span data-stu-id="d80fd-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="d80fd-187">您可以使用資料批註來設定應該使用哪一個屬性做為指定關聯性的外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="d80fd-188">這通常是在不依照慣例探索外鍵屬性時完成：</span><span class="sxs-lookup"><span data-stu-id="d80fd-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="d80fd-189">`[ForeignKey]`批註可以放在關聯性中的任一導覽屬性上。</span><span class="sxs-lookup"><span data-stu-id="d80fd-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="d80fd-190">不需要移至相依實體類別中的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="d80fd-191">`[ForeignKey]`在導覽屬性上使用指定的屬性不需要存在於相依型別上。</span><span class="sxs-lookup"><span data-stu-id="d80fd-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist on the dependent type.</span></span> <span data-ttu-id="d80fd-192">在此情況下，將會使用指定的名稱來建立陰影外鍵。</span><span class="sxs-lookup"><span data-stu-id="d80fd-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="d80fd-193">陰影外鍵</span><span class="sxs-lookup"><span data-stu-id="d80fd-193">Shadow foreign key</span></span>

<span data-ttu-id="d80fd-194">您可以使用的字串多載 `HasForeignKey(...)` ，將陰影屬性設定為外鍵（如需詳細資訊，請參閱[陰影屬性](shadow-properties.md)）。</span><span class="sxs-lookup"><span data-stu-id="d80fd-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="d80fd-195">我們建議您先將 shadow 屬性明確加入至模型，再將其當做外鍵使用（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="d80fd-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="d80fd-196">外鍵條件約束名稱</span><span class="sxs-lookup"><span data-stu-id="d80fd-196">Foreign key constraint name</span></span>

<span data-ttu-id="d80fd-197">依照慣例，以關係資料庫為目標時，foreign key 條件約束會命名為 FK_ <dependent type name> _<principal type name>_ <foreign key property name> 。</span><span class="sxs-lookup"><span data-stu-id="d80fd-197">By convention, when targeting a relational database, foreign key constraints are named FK_<dependent type name>_<principal type name>_<foreign key property name>.</span></span> <span data-ttu-id="d80fd-198">對於複合外鍵， <foreign key property name> 會變成以底線分隔的外鍵屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="d80fd-198">For composite foreign keys <foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="d80fd-199">您也可以設定條件約束名稱，如下所示：</span><span class="sxs-lookup"><span data-stu-id="d80fd-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="d80fd-200">沒有導覽屬性</span><span class="sxs-lookup"><span data-stu-id="d80fd-200">Without navigation property</span></span>

<span data-ttu-id="d80fd-201">您不一定需要提供導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="d80fd-202">您可以直接在關聯性的一端提供外鍵。</span><span class="sxs-lookup"><span data-stu-id="d80fd-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="d80fd-203">主要金鑰</span><span class="sxs-lookup"><span data-stu-id="d80fd-203">Principal key</span></span>

<span data-ttu-id="d80fd-204">如果您想要外鍵參考主鍵以外的屬性，您可以使用流暢的 API 來設定關聯性的主體索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="d80fd-205">您設定為主體金鑰的屬性會自動設定為[替代金鑰](alternate-keys.md)。</span><span class="sxs-lookup"><span data-stu-id="d80fd-205">The property that you configure as the principal key will automatically be setup as an [alternate key](alternate-keys.md).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="d80fd-206">簡單金鑰</span><span class="sxs-lookup"><span data-stu-id="d80fd-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="d80fd-207">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="d80fd-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="d80fd-208">指定主體索引鍵屬性的順序必須符合為外鍵指定的順序。</span><span class="sxs-lookup"><span data-stu-id="d80fd-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="d80fd-209">必要和選擇性的關聯性</span><span class="sxs-lookup"><span data-stu-id="d80fd-209">Required and optional relationships</span></span>

<span data-ttu-id="d80fd-210">您可以使用流暢的 API 來設定關聯性是否為必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="d80fd-211">最後，這會控制外鍵屬性是否為必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="d80fd-212">當您使用陰影狀態外鍵時，這是最有用的。</span><span class="sxs-lookup"><span data-stu-id="d80fd-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="d80fd-213">如果您的實體類別中有外鍵屬性，則關聯性的必要性取決於外鍵屬性是否為必要或選擇性（如需詳細資訊，請參閱[必要和選擇性屬性](required-optional.md)）。</span><span class="sxs-lookup"><span data-stu-id="d80fd-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="d80fd-214">`IsRequired(false)`除非另有設定，否則呼叫也會使外鍵屬性變成選擇性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-214">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="d80fd-215">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="d80fd-215">Cascade delete</span></span>

<span data-ttu-id="d80fd-216">您可以使用流暢的 API，為指定的關聯性明確設定串聯刪除行為。</span><span class="sxs-lookup"><span data-stu-id="d80fd-216">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="d80fd-217">如需每個選項的詳細討論，請參閱串聯[刪除](../saving/cascade-delete.md)。</span><span class="sxs-lookup"><span data-stu-id="d80fd-217">See [Cascade Delete](../saving/cascade-delete.md) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="d80fd-218">其他關聯性模式</span><span class="sxs-lookup"><span data-stu-id="d80fd-218">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="d80fd-219">一對一</span><span class="sxs-lookup"><span data-stu-id="d80fd-219">One-to-one</span></span>

<span data-ttu-id="d80fd-220">一對一關聯性在兩端都有參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-220">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="d80fd-221">它們遵循與一對多關聯性相同的慣例，但是在外鍵屬性上引進了唯一的索引，以確保只有一個相依性與每個主體相關。</span><span class="sxs-lookup"><span data-stu-id="d80fd-221">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="d80fd-222">EF 會根據其偵測外鍵屬性的能力，選擇要相依的其中一個實體。</span><span class="sxs-lookup"><span data-stu-id="d80fd-222">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="d80fd-223">如果選擇錯誤的實體做為相依專案，您可以使用流暢的 API 來修正此問題。</span><span class="sxs-lookup"><span data-stu-id="d80fd-223">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="d80fd-224">設定與流暢 API 的關聯性時，您會使用 `HasOne` 和 `WithOne` 方法。</span><span class="sxs-lookup"><span data-stu-id="d80fd-224">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="d80fd-225">設定外鍵時，您需要指定相依實體類型-請注意下列清單中提供給的泛型參數 `HasForeignKey` 。</span><span class="sxs-lookup"><span data-stu-id="d80fd-225">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="d80fd-226">在一對多關聯性中，明確地指出具有參考導覽的實體是相依的，而具有該集合的實體是主體。</span><span class="sxs-lookup"><span data-stu-id="d80fd-226">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="d80fd-227">但這並不是一對一關聯性，因此需要明確定義。</span><span class="sxs-lookup"><span data-stu-id="d80fd-227">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="d80fd-228">多對多</span><span class="sxs-lookup"><span data-stu-id="d80fd-228">Many-to-many</span></span>

<span data-ttu-id="d80fd-229">尚未支援多對多關聯性，但沒有實體類別來表示聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="d80fd-229">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="d80fd-230">不過，您可以藉由包含聯結資料表的實體類別並對應兩個不同的一對多關聯性，來代表多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="d80fd-230">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
