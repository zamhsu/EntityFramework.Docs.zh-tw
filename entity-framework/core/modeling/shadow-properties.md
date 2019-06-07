---
title: 遮蔽屬性的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 4029539f3642f539a427f5901577d4df96c00f30
ms.sourcegitcommit: 119058fefd7f35952048f783ada68be9aa612256
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66749708"
---
# <a name="shadow-properties"></a><span data-ttu-id="68044-102">陰影屬性</span><span class="sxs-lookup"><span data-stu-id="68044-102">Shadow Properties</span></span>

<span data-ttu-id="68044-103">陰影屬性是.NET 實體類別中未定義，但該 EF Core 模型中的實體類型定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="68044-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="68044-104">值，這些屬性的狀態會保持單純變更追蹤程式。</span><span class="sxs-lookup"><span data-stu-id="68044-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="68044-105">不會公開對應的實體類型的資料庫中的資料時，陰影屬性相當實用。</span><span class="sxs-lookup"><span data-stu-id="68044-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="68044-106">它們通常用於外部索引鍵的屬性，其中兩個實體之間的關聯性由在資料庫中，外部索引鍵值，但使用實體類型之間的導覽屬性的實體類型上管理關聯性。</span><span class="sxs-lookup"><span data-stu-id="68044-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="68044-107">取得與透過變更陰影屬性值`ChangeTracker`API。</span><span class="sxs-lookup"><span data-stu-id="68044-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="68044-108">您可以透過 LINQ 查詢中參考遮蔽屬性`EF.Property`靜態方法。</span><span class="sxs-lookup"><span data-stu-id="68044-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="68044-109">慣例</span><span class="sxs-lookup"><span data-stu-id="68044-109">Conventions</span></span>

<span data-ttu-id="68044-110">可以依照慣例建立陰影屬性，探索到的關聯性，但沒有外部索引鍵屬性相依實體類別中找到。</span><span class="sxs-lookup"><span data-stu-id="68044-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="68044-111">在此情況下，將會介紹陰影的外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="68044-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="68044-112">陰影的外部索引鍵屬性將會命名為`<navigation property name><principal key property name>`（相依的實體，指向主要實體，瀏覽用來命名）。</span><span class="sxs-lookup"><span data-stu-id="68044-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="68044-113">如果主體索引鍵屬性名稱包含導覽屬性的名稱，則只是名稱`<principal key property name>`。</span><span class="sxs-lookup"><span data-stu-id="68044-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="68044-114">如果相依的實體上沒有任何導覽屬性，則會使用主體的類型名稱在其位置。</span><span class="sxs-lookup"><span data-stu-id="68044-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="68044-115">例如，下列程式碼清單會導致`BlogId`陰影屬性來引進`Post`實體。</span><span class="sxs-lookup"><span data-stu-id="68044-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/ShadowForeignKey.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
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

## <a name="data-annotations"></a><span data-ttu-id="68044-116">資料註釋</span><span class="sxs-lookup"><span data-stu-id="68044-116">Data Annotations</span></span>

<span data-ttu-id="68044-117">無法使用資料註解建立陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="68044-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="68044-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="68044-118">Fluent API</span></span>

<span data-ttu-id="68044-119">您可以使用 Fluent API，若要設定陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="68044-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="68044-120">一旦您已呼叫的字串多載`Property`您也可以將任何就和其他屬性設定呼叫鏈結。</span><span class="sxs-lookup"><span data-stu-id="68044-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="68044-121">如果名稱提供給`Property`方法符合名稱的現有屬性 （陰影屬性或其中一個定義的實體類別上），然後將程式碼會設定該現有的屬性，而不是引進新的陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="68044-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/ShadowProperty.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property<DateTime>("LastUpdated");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
