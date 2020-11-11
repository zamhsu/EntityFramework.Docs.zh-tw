---
title: 關聯性-EF Core
description: 使用 Entity Framework Core 時如何設定實體類型之間的關聯性
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: c92fa1904436ec43ff8918354a99440079e2a96a
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503211"
---
# <a name="relationships"></a><span data-ttu-id="60345-103">關聯性</span><span class="sxs-lookup"><span data-stu-id="60345-103">Relationships</span></span>

<span data-ttu-id="60345-104">關聯性會定義兩個實體彼此之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="60345-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="60345-105">在關係資料庫中，這是由 foreign key 條件約束表示。</span><span class="sxs-lookup"><span data-stu-id="60345-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="60345-106">本文中大部分的範例都使用一對多關聯性來示範概念。</span><span class="sxs-lookup"><span data-stu-id="60345-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="60345-107">如需一對一和多對多關聯性的範例，請參閱本文結尾的 [其他關聯性模式](#other-relationship-patterns) 一節。</span><span class="sxs-lookup"><span data-stu-id="60345-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="60345-108">詞彙定義</span><span class="sxs-lookup"><span data-stu-id="60345-108">Definition of terms</span></span>

<span data-ttu-id="60345-109">有一些詞彙用來描述關聯性</span><span class="sxs-lookup"><span data-stu-id="60345-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="60345-110">**相依實體：** 這是包含外鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="60345-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="60345-111">有時稱為關聯性的「子系」。</span><span class="sxs-lookup"><span data-stu-id="60345-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="60345-112">**Principal 實體：** 這是包含主要/替代索引鍵屬性的實體。</span><span class="sxs-lookup"><span data-stu-id="60345-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="60345-113">有時稱為關聯性的「父系」。</span><span class="sxs-lookup"><span data-stu-id="60345-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="60345-114">**主要金鑰：** 可唯一識別主體實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="60345-115">這可能是主鍵或替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="60345-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="60345-116">**外鍵：** 相依實體中的屬性，用來儲存相關實體的主要金鑰值。</span><span class="sxs-lookup"><span data-stu-id="60345-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="60345-117">**導覽屬性：** 在參考相關實體的主體和/或相依實體上定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="60345-118">**集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。</span><span class="sxs-lookup"><span data-stu-id="60345-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="60345-119">**參考導覽屬性：** 保存單一相關實體之參考的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="60345-120">**反向導覽屬性：** 在討論特定的導覽屬性時，這個詞彙是指關聯性另一端的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="60345-121">**自我參考關聯性：** 相依和主體實體類型相同的關聯性。</span><span class="sxs-lookup"><span data-stu-id="60345-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="60345-122">下列程式碼顯示與之間的一對多關聯性 `Blog``Post`</span><span class="sxs-lookup"><span data-stu-id="60345-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="60345-123">`Post` 是相依實體</span><span class="sxs-lookup"><span data-stu-id="60345-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="60345-124">`Blog` 是主體實體</span><span class="sxs-lookup"><span data-stu-id="60345-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="60345-125">`Blog.BlogId` 是主要金鑰 (在此案例中，它是主鍵，而不是替代索引鍵) </span><span class="sxs-lookup"><span data-stu-id="60345-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="60345-126">`Post.BlogId` 為外鍵</span><span class="sxs-lookup"><span data-stu-id="60345-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="60345-127">`Post.Blog` 是參考導覽屬性</span><span class="sxs-lookup"><span data-stu-id="60345-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="60345-128">`Blog.Posts` 是集合導覽屬性</span><span class="sxs-lookup"><span data-stu-id="60345-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="60345-129">`Post.Blog` 這是 (的反向導覽屬性 `Blog.Posts` ，反之亦然) </span><span class="sxs-lookup"><span data-stu-id="60345-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="60345-130">慣例</span><span class="sxs-lookup"><span data-stu-id="60345-130">Conventions</span></span>

<span data-ttu-id="60345-131">根據預設，當類型上有探索到導覽屬性時，會建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="60345-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="60345-132">如果目前的資料庫提供者無法將屬性所指向的型別對應為純量類型，則會將屬性視為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="60345-133">依慣例探索的關聯性一律會以主體實體的主要金鑰為目標。</span><span class="sxs-lookup"><span data-stu-id="60345-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="60345-134">若要以替代金鑰為目標，則必須使用流暢的 API 來執行其他設定。</span><span class="sxs-lookup"><span data-stu-id="60345-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="60345-135">完整定義的關聯性</span><span class="sxs-lookup"><span data-stu-id="60345-135">Fully defined relationships</span></span>

<span data-ttu-id="60345-136">最常見的關聯性模式是在關聯性的兩端定義導覽屬性，並在相依實體類別中定義外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="60345-137">如果在兩個類型之間找到一組導覽屬性，則會將它們設定為相同關聯性的反向導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="60345-138">如果相依實體包含名稱符合其中一個模式的屬性，則會將它設定為外鍵：</span><span class="sxs-lookup"><span data-stu-id="60345-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="60345-139">在此範例中，反白顯示的屬性將用來設定關聯性。</span><span class="sxs-lookup"><span data-stu-id="60345-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="60345-140">如果屬性是主鍵，或者是不與主體索引鍵相容的型別，則不會將它設定為外鍵。</span><span class="sxs-lookup"><span data-stu-id="60345-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="60345-141">在 EF Core 3.0 之前，名為與 principal key 屬性完全相同的屬性也會與 [外鍵相符](https://github.com/dotnet/efcore/issues/13274)</span><span class="sxs-lookup"><span data-stu-id="60345-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/dotnet/efcore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="60345-142">沒有外鍵屬性</span><span class="sxs-lookup"><span data-stu-id="60345-142">No foreign key property</span></span>

<span data-ttu-id="60345-143">雖然建議將外鍵屬性定義在相依的實體類別中，但這不是必要的。</span><span class="sxs-lookup"><span data-stu-id="60345-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="60345-144">如果找不到任何外鍵屬性，則會以名稱導入 [陰影外鍵屬性](xref:core/modeling/shadow-properties) ， `<navigation property name><principal key property name>` `<principal entity name><principal key property name>` 如果相依類型上沒有導覽，則為。</span><span class="sxs-lookup"><span data-stu-id="60345-144">If no foreign key property is found, a [shadow foreign key property](xref:core/modeling/shadow-properties) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="60345-145">在此範例中，陰影外鍵是 `BlogId` 因為前面加上導覽名稱會是多餘的。</span><span class="sxs-lookup"><span data-stu-id="60345-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="60345-146">如果已經存在具有相同名稱的屬性，則會將陰影屬性名稱加上數位。</span><span class="sxs-lookup"><span data-stu-id="60345-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="60345-147">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="60345-147">Single navigation property</span></span>

<span data-ttu-id="60345-148">僅包含一個導覽屬性 (沒有反向導覽，而且沒有任何外鍵屬性) 足以擁有依慣例定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="60345-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="60345-149">您也可以擁有單一導覽屬性和外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="60345-150">限制</span><span class="sxs-lookup"><span data-stu-id="60345-150">Limitations</span></span>

<span data-ttu-id="60345-151">當兩個型別之間有多個定義的導覽屬性時 (也就是，一對彼此指向的導覽，) 導覽屬性代表的關聯性不明確。</span><span class="sxs-lookup"><span data-stu-id="60345-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="60345-152">您將需要手動設定它們，以解決不明確的問題。</span><span class="sxs-lookup"><span data-stu-id="60345-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="60345-153">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="60345-153">Cascade delete</span></span>

<span data-ttu-id="60345-154">依照慣例，串聯刪除會針對必要的關聯性設定為 *cascade* ，並針對選擇性關聯性 *deletebehavior.clientsetnull* 。</span><span class="sxs-lookup"><span data-stu-id="60345-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="60345-155">*Cascade* 表示也會刪除相依實體。</span><span class="sxs-lookup"><span data-stu-id="60345-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="60345-156">*Deletebehavior.clientsetnull* 表示未載入記憶體的相依實體將保持不變，而且必須以手動方式刪除或更新以指向有效的主體實體。</span><span class="sxs-lookup"><span data-stu-id="60345-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="60345-157">若為載入記憶體中的實體，EF Core 會嘗試將外鍵屬性設定為 null。</span><span class="sxs-lookup"><span data-stu-id="60345-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="60345-158">如需必要和選擇性關聯性之間的差異，請參閱「 [必要」和「選擇性關聯](#required-and-optional-relationships) 性」一節。</span><span class="sxs-lookup"><span data-stu-id="60345-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="60345-159">如需不同刪除行為的詳細資訊以及慣例所使用的預設值，請參閱 [Cascade delete](xref:core/saving/cascade-delete) 。</span><span class="sxs-lookup"><span data-stu-id="60345-159">See [Cascade Delete](xref:core/saving/cascade-delete) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="60345-160">手動設定</span><span class="sxs-lookup"><span data-stu-id="60345-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="60345-161">Fluent API</span><span class="sxs-lookup"><span data-stu-id="60345-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="60345-162">若要在流暢的 API 中設定關聯性，您必須先識別構成關聯性的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="60345-163">`HasOne` 或 `HasMany` 識別您要開始設定的實體類型上的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="60345-164">然後，您可以將呼叫連結至 `WithOne` 或 `WithMany` ，以識別反向導覽。</span><span class="sxs-lookup"><span data-stu-id="60345-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="60345-165">`HasOne`/`WithOne`用於參考導覽屬性，並用於 `HasMany` / `WithMany` 集合導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="60345-166">資料註解</span><span class="sxs-lookup"><span data-stu-id="60345-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="60345-167">您可以使用資料批註來設定相依和主體實體上的導覽屬性配對。</span><span class="sxs-lookup"><span data-stu-id="60345-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="60345-168">這通常會在兩個實體類型之間有一對以上的導覽屬性時完成。</span><span class="sxs-lookup"><span data-stu-id="60345-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="60345-169">您只能對相依實體上的屬性使用 [必要]，以影響關聯性的 requiredness。</span><span class="sxs-lookup"><span data-stu-id="60345-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="60345-170">[必要] 通常會忽略主體實體的導覽，但可能會導致實體成為相依的實體。</span><span class="sxs-lookup"><span data-stu-id="60345-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="60345-171">資料批註 `[ForeignKey]` 和 `[InverseProperty]` 可在 `System.ComponentModel.DataAnnotations.Schema` 命名空間中使用。</span><span class="sxs-lookup"><span data-stu-id="60345-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="60345-172">`[Required]` 可在 `System.ComponentModel.DataAnnotations` 命名空間中使用。</span><span class="sxs-lookup"><span data-stu-id="60345-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="60345-173">單一導覽屬性</span><span class="sxs-lookup"><span data-stu-id="60345-173">Single navigation property</span></span>

<span data-ttu-id="60345-174">如果您只有一個導覽屬性，則會有和的無參數多載 `WithOne` `WithMany` 。</span><span class="sxs-lookup"><span data-stu-id="60345-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="60345-175">這表示在關聯性的另一端有參考或集合的概念，但 entity 類別中沒有包含任何導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="60345-176">設定導覽屬性</span><span class="sxs-lookup"><span data-stu-id="60345-176">Configuring navigation properties</span></span>

> [!NOTE]
> <span data-ttu-id="60345-177">這項功能已在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="60345-177">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="60345-178">建立導覽屬性之後，您可能需要進一步設定。</span><span class="sxs-lookup"><span data-stu-id="60345-178">After the navigation property has been created, you may need to further configure it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> <span data-ttu-id="60345-179">此呼叫不能用來建立導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="60345-180">它只會用來設定先前藉由定義關聯性或從慣例建立的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="60345-181">外部索引鍵</span><span class="sxs-lookup"><span data-stu-id="60345-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="60345-182">流暢的 API (簡單的金鑰) </span><span class="sxs-lookup"><span data-stu-id="60345-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="60345-183">您可以使用流暢的 API 來設定應該使用哪一個屬性做為指定之關聯性的外鍵屬性：</span><span class="sxs-lookup"><span data-stu-id="60345-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="60345-184">流暢的 API (複合索引鍵) </span><span class="sxs-lookup"><span data-stu-id="60345-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="60345-185">您可以使用流暢的 API 來設定應該使用哪些屬性做為指定之關聯性的複合外鍵屬性：</span><span class="sxs-lookup"><span data-stu-id="60345-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="60345-186"> (簡單索引鍵) 的資料批註 </span><span class="sxs-lookup"><span data-stu-id="60345-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="60345-187">您可以使用資料批註來設定應該使用哪一個屬性做為指定之關聯性的外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="60345-188">這通常是在不依照慣例探索外鍵屬性時完成：</span><span class="sxs-lookup"><span data-stu-id="60345-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="60345-189">`[ForeignKey]`批註可以放置於關聯性中的任何導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="60345-190">它不需要移至相依實體類別中的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="60345-191">`[ForeignKey]`在導覽屬性上使用指定的屬性不需要存在於相依型別上。</span><span class="sxs-lookup"><span data-stu-id="60345-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist on the dependent type.</span></span> <span data-ttu-id="60345-192">在此情況下，將會使用指定的名稱來建立陰影外鍵。</span><span class="sxs-lookup"><span data-stu-id="60345-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="60345-193">陰影外鍵</span><span class="sxs-lookup"><span data-stu-id="60345-193">Shadow foreign key</span></span>

<span data-ttu-id="60345-194">您可以使用的字串多載 `HasForeignKey(...)` ，將陰影屬性設定為外鍵 (如需詳細資訊，請參閱 [陰影屬性](xref:core/modeling/shadow-properties)) 。</span><span class="sxs-lookup"><span data-stu-id="60345-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](xref:core/modeling/shadow-properties) for more information).</span></span> <span data-ttu-id="60345-195">我們建議您先明確地將 shadow 屬性加入至模型，再使用它做為外鍵 (，如下所示) 。</span><span class="sxs-lookup"><span data-stu-id="60345-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="60345-196">Foreign key 條件約束名稱</span><span class="sxs-lookup"><span data-stu-id="60345-196">Foreign key constraint name</span></span>

<span data-ttu-id="60345-197">依照慣例，以關係資料庫為目標時，foreign key 條件約束會命名為 FK \_ \<dependent type name> \_ \<principal type name> \_ \<foreign key property name> 。</span><span class="sxs-lookup"><span data-stu-id="60345-197">By convention, when targeting a relational database, foreign key constraints are named FK\_\<dependent type name>\_\<principal type name>\_\<foreign key property name>.</span></span> <span data-ttu-id="60345-198">若為複合外鍵， \<foreign key property name> 則會變成以底線分隔的外鍵屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="60345-198">For composite foreign keys, \<foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="60345-199">您也可以設定條件約束名稱，如下所示：</span><span class="sxs-lookup"><span data-stu-id="60345-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="60345-200">沒有導覽屬性</span><span class="sxs-lookup"><span data-stu-id="60345-200">Without navigation property</span></span>

<span data-ttu-id="60345-201">您不一定需要提供導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="60345-202">您可以直接在關聯性的一端提供外鍵。</span><span class="sxs-lookup"><span data-stu-id="60345-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="60345-203">主體金鑰</span><span class="sxs-lookup"><span data-stu-id="60345-203">Principal key</span></span>

<span data-ttu-id="60345-204">如果您希望外鍵參考主鍵以外的屬性，您可以使用流暢的 API 來設定關聯性的主要索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="60345-205">您設定為主要金鑰的屬性會自動設定為 [替代索引鍵](xref:core/modeling/keys#alternate-keys)。</span><span class="sxs-lookup"><span data-stu-id="60345-205">The property that you configure as the principal key will automatically be setup as an [alternate key](xref:core/modeling/keys#alternate-keys).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="60345-206">簡單索引鍵</span><span class="sxs-lookup"><span data-stu-id="60345-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="60345-207">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="60345-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="60345-208">您指定主體索引鍵屬性的順序必須符合為外鍵指定的順序。</span><span class="sxs-lookup"><span data-stu-id="60345-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="60345-209">必要和選用的關聯性</span><span class="sxs-lookup"><span data-stu-id="60345-209">Required and optional relationships</span></span>

<span data-ttu-id="60345-210">您可以使用流暢的 API 來設定關聯性是否為必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="60345-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="60345-211">最後，這會控制外鍵屬性是否為必要或選擇性。</span><span class="sxs-lookup"><span data-stu-id="60345-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="60345-212">當您使用陰影狀態外鍵時，這非常有用。</span><span class="sxs-lookup"><span data-stu-id="60345-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="60345-213">如果您的實體類別中有外鍵屬性，則關聯性的 requiredness 取決於是否需要外鍵屬性或選擇性 (如需詳細資訊，請參閱 [必要和選擇性屬性](xref:core/modeling/entity-properties#required-and-optional-properties)) 。</span><span class="sxs-lookup"><span data-stu-id="60345-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](xref:core/modeling/entity-properties#required-and-optional-properties) for more information).</span></span>

<span data-ttu-id="60345-214">外鍵屬性位於相依的實體類型上，因此如果設定為必要，則表示每個相依實體都必須有對應的主體實體。</span><span class="sxs-lookup"><span data-stu-id="60345-214">The foreign key properties are located on the dependent entity type, so if they are configured as required it means that every dependent entity is required to have a corresponding principal entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="60345-215">呼叫 `IsRequired(false)` 也會將外鍵屬性設為選擇性，除非另有設定。</span><span class="sxs-lookup"><span data-stu-id="60345-215">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="60345-216">串聯刪除</span><span class="sxs-lookup"><span data-stu-id="60345-216">Cascade delete</span></span>

<span data-ttu-id="60345-217">您可以使用流暢的 API 來明確設定指定之關聯性的串聯刪除行為。</span><span class="sxs-lookup"><span data-stu-id="60345-217">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="60345-218">如需每個選項的詳細討論，請參閱 [Cascade Delete](xref:core/saving/cascade-delete) 。</span><span class="sxs-lookup"><span data-stu-id="60345-218">See [Cascade Delete](xref:core/saving/cascade-delete) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="60345-219">其他關聯性模式</span><span class="sxs-lookup"><span data-stu-id="60345-219">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="60345-220">一對一</span><span class="sxs-lookup"><span data-stu-id="60345-220">One-to-one</span></span>

<span data-ttu-id="60345-221">一對多關聯性的兩端都有參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-221">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="60345-222">它們遵循與一對多關聯性相同的慣例，但在外鍵屬性上引進了唯一的索引，以確保只有一個相依性與每個主體相關。</span><span class="sxs-lookup"><span data-stu-id="60345-222">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="60345-223">EF 會根據其偵測外鍵屬性的能力，選擇要相依的其中一個實體。</span><span class="sxs-lookup"><span data-stu-id="60345-223">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="60345-224">如果選擇錯誤的實體做為相依的實體，您可以使用流暢的 API 來修正此問題。</span><span class="sxs-lookup"><span data-stu-id="60345-224">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="60345-225">設定與流暢 API 的關聯性時，您會使用 `HasOne` 和 `WithOne` 方法。</span><span class="sxs-lookup"><span data-stu-id="60345-225">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="60345-226">設定外鍵時，您需要指定相依的實體類型-請注意， `HasForeignKey` 在下列清單中提供的泛型參數。</span><span class="sxs-lookup"><span data-stu-id="60345-226">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="60345-227">在一對多關聯性中，可以明確指出具有參考導覽的實體是相依的，而集合中的實體是主體。</span><span class="sxs-lookup"><span data-stu-id="60345-227">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="60345-228">但這不是一對一關聯性，因此必須明確地定義它。</span><span class="sxs-lookup"><span data-stu-id="60345-228">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

<span data-ttu-id="60345-229">根據預設，相依的端會被視為選擇性，但可以視需要進行設定。</span><span class="sxs-lookup"><span data-stu-id="60345-229">The dependent side is considered optional by default, but can be configured as required.</span></span> <span data-ttu-id="60345-230">但是 EF 不會驗證是否已提供相依實體，因此只有在資料庫對應允許強制執行此設定時，此設定才會有所差異。</span><span class="sxs-lookup"><span data-stu-id="60345-230">However EF will not validate whether a dependent entity was provided, so this configuration will only make a difference when the database mapping allows it to be enforced.</span></span> <span data-ttu-id="60345-231">常見的案例是依預設使用資料表分割的參考擁有類型。</span><span class="sxs-lookup"><span data-stu-id="60345-231">A common scenario for this are reference owned types that use table splitting by default.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

<span data-ttu-id="60345-232">使用此設定時，對應的 `ShippingAddress` 資料行會在資料庫中標示為不可為 null。</span><span class="sxs-lookup"><span data-stu-id="60345-232">With this configuration the columns corresponding to `ShippingAddress` will be marked as non-nullable in the database.</span></span>

> [!NOTE]
> <span data-ttu-id="60345-233">如果您使用 [不可為 null 的參考型別](/dotnet/csharp/nullable-references) ，則 `IsRequired` 不需要呼叫。</span><span class="sxs-lookup"><span data-stu-id="60345-233">If you are using [non-nullable reference types](/dotnet/csharp/nullable-references) calling `IsRequired` is not necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="60345-234">在 EF Core 5.0 中新增了是否需要相依的設定。</span><span class="sxs-lookup"><span data-stu-id="60345-234">The ability to configure whether the dependent is required was added in EF Core 5.0.</span></span>

### <a name="many-to-many"></a><span data-ttu-id="60345-235">多對多</span><span class="sxs-lookup"><span data-stu-id="60345-235">Many-to-many</span></span>

<span data-ttu-id="60345-236">多對多關聯性需要兩端的集合導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="60345-236">Many to many relationships require a collection navigation property on both sides.</span></span> <span data-ttu-id="60345-237">如同其他類型的關聯性，將會依照慣例來探索它們。</span><span class="sxs-lookup"><span data-stu-id="60345-237">They will be discovered by convention like other types of relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

<span data-ttu-id="60345-238">在資料庫中執行此關聯性的方式，是將包含和的外鍵加入資料表 `Post` `Tag` 。</span><span class="sxs-lookup"><span data-stu-id="60345-238">The way this relationship is implemented in the database is by a join table that contains foreign keys to both `Post` and `Tag`.</span></span> <span data-ttu-id="60345-239">例如，這是 EF 將在上述模型的關係資料庫中建立的。</span><span class="sxs-lookup"><span data-stu-id="60345-239">For example this is what EF will create in a relational database for the above model.</span></span>

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
    [PostsId] int NOT NULL,
    [TagsId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

<span data-ttu-id="60345-240">就內部而言，EF 會建立實體型別來代表聯結資料表，而該聯結資料表將被稱為聯結實體型別。</span><span class="sxs-lookup"><span data-stu-id="60345-240">Internally, EF creates an entity type to represent the join table that will be referred to as the join entity type.</span></span> <span data-ttu-id="60345-241">沒有可用於此的特定 CLR 型別，因此 `Dictionary<string, object>` 會使用。</span><span class="sxs-lookup"><span data-stu-id="60345-241">There is no specific CLR type that can be used for this, so `Dictionary<string, object>` is used.</span></span> <span data-ttu-id="60345-242">模型中可以有一個以上的多對多關聯性，因此，在此情況下，必須為聯結實體類型指定唯一的名稱 `PostTag` 。</span><span class="sxs-lookup"><span data-stu-id="60345-242">More than one many-to-many relationships can exist in the model, therefore the join entity type must be given a unique name, in this case `PostTag`.</span></span> <span data-ttu-id="60345-243">允許這項功能的功能稱為共用類型實體類型。</span><span class="sxs-lookup"><span data-stu-id="60345-243">The feature that allows this is called shared-type entity type.</span></span>

<span data-ttu-id="60345-244">「多對多導覽」稱為「略過導覽」，因為它們可有效略過聯結實體類型。</span><span class="sxs-lookup"><span data-stu-id="60345-244">The many to many navigations are called skip navigations as they effectively skip over the join entity type.</span></span> <span data-ttu-id="60345-245">如果您正在採用大量設定，則可以從取得所有略過導覽 `GetSkipNavigations` 。</span><span class="sxs-lookup"><span data-stu-id="60345-245">If you are employing bulk configuration all skip navigations can be obtained from `GetSkipNavigations`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

<span data-ttu-id="60345-246">通常會將設定套用至聯結實體類型。</span><span class="sxs-lookup"><span data-stu-id="60345-246">It is common to apply configuration to the join entity type.</span></span> <span data-ttu-id="60345-247">您可以透過來完成此動作 `UsingEntity` 。</span><span class="sxs-lookup"><span data-stu-id="60345-247">This action can be accomplished via `UsingEntity`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

<span data-ttu-id="60345-248">您可以使用匿名型別，為聯結實體型別提供[模型種子資料](xref:core/modeling/data-seeding)。</span><span class="sxs-lookup"><span data-stu-id="60345-248">[Model seed data](xref:core/modeling/data-seeding) can be provided for the join entity type by using anonymous types.</span></span> <span data-ttu-id="60345-249">您可以檢查模型的偵錯工具，以判斷依慣例建立的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="60345-249">You can examine the model debug view to determine the property names created by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

<span data-ttu-id="60345-250">額外的資料可以儲存在聯結實體型別中，但為了這樣做，最好是建立定制的 CLR 型別。</span><span class="sxs-lookup"><span data-stu-id="60345-250">Additional data can be stored in the join entity type, but for this it's best to create a bespoke CLR type.</span></span> <span data-ttu-id="60345-251">設定自訂聯結實體類型的關聯性時，必須明確指定這兩個外鍵。</span><span class="sxs-lookup"><span data-stu-id="60345-251">When configuring the relationship with a custom join entity type both foreign keys need to be specified explicitly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> <span data-ttu-id="60345-252">在 EF Core 5.0 中新增多對多關聯性的功能已加入至舊版，請使用下列方法。</span><span class="sxs-lookup"><span data-stu-id="60345-252">The ability to configure many-to-many relationships was added in EF Core 5.0, for previous version use the following approach.</span></span>

<span data-ttu-id="60345-253">您也可以藉由加入聯結實體型別並對應兩個不同的一對多關聯性，來表示多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="60345-253">You can also represent a many-to-many relationship by just adding the join entity type and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]

> [!NOTE]
> <span data-ttu-id="60345-254">尚未新增與資料庫中多對多關聯性的元件支援。</span><span class="sxs-lookup"><span data-stu-id="60345-254">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="60345-255">請參閱 [追蹤問題](https://github.com/dotnet/efcore/issues/22475)。</span><span class="sxs-lookup"><span data-stu-id="60345-255">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60345-256">其他資源</span><span class="sxs-lookup"><span data-stu-id="60345-256">Additional resources</span></span>

* <span data-ttu-id="60345-257">[EF Core 的站立會議研討會](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32)，深入探討多對多和基礎結構。</span><span class="sxs-lookup"><span data-stu-id="60345-257">[EF Core Community Standup session](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32), with a deep dive into Many-to-many and the infrastructure underpinning it.</span></span>
