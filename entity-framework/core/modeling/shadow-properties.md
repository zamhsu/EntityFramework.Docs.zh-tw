---
title: 陰影屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 5fdc4c50c295f73d0fa5eef3518adf4d3eb95599
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197697"
---
# <a name="shadow-properties"></a><span data-ttu-id="f2826-102">陰影屬性</span><span class="sxs-lookup"><span data-stu-id="f2826-102">Shadow Properties</span></span>

<span data-ttu-id="f2826-103">陰影屬性是未在您的 .NET 實體類別中定義，但已針對 EF Core 模型中的實體類型定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="f2826-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="f2826-104">這些屬性的值和狀態純粹是在變更追蹤程式中進行維護。</span><span class="sxs-lookup"><span data-stu-id="f2826-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="f2826-105">當資料庫中有資料不應在對應的實體類型上公開時，陰影屬性會很有用。</span><span class="sxs-lookup"><span data-stu-id="f2826-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="f2826-106">它們最常用於外鍵屬性，其中兩個實體之間的關聯性是由資料庫中的外鍵值所表示，但是在實體類型上使用實體類型之間的導覽屬性來管理關聯性。</span><span class="sxs-lookup"><span data-stu-id="f2826-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="f2826-107">您可以透過`ChangeTracker` API 取得和變更陰影屬性值。</span><span class="sxs-lookup"><span data-stu-id="f2826-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="f2826-108">您可以透過`EF.Property`靜態方法，在 LINQ 查詢中參考陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="f2826-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="f2826-109">慣例</span><span class="sxs-lookup"><span data-stu-id="f2826-109">Conventions</span></span>

<span data-ttu-id="f2826-110">當探索到關聯性，但在相依實體類別中找不到外鍵屬性時，可以依照慣例建立陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="f2826-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="f2826-111">在此情況下，將會引進陰影外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="f2826-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="f2826-112">影子外鍵屬性將會命名`<navigation property name><principal key property name>` （指向主體實體的相依實體導覽，用於命名）。</span><span class="sxs-lookup"><span data-stu-id="f2826-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="f2826-113">如果主體索引鍵屬性名稱包含導覽屬性的名稱，則名稱會`<principal key property name>`是。</span><span class="sxs-lookup"><span data-stu-id="f2826-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="f2826-114">如果相依實體上沒有導覽屬性，則會在其位置使用主體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="f2826-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="f2826-115">例如，下列程式代碼清單會導致`BlogId`將陰影屬性引進`Post`至實體。</span><span class="sxs-lookup"><span data-stu-id="f2826-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/ShadowForeignKey.cs)] -->
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

## <a name="data-annotations"></a><span data-ttu-id="f2826-116">資料註釋</span><span class="sxs-lookup"><span data-stu-id="f2826-116">Data Annotations</span></span>

<span data-ttu-id="f2826-117">不能使用資料批註來建立陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="f2826-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="f2826-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f2826-118">Fluent API</span></span>

<span data-ttu-id="f2826-119">您可以使用流暢的 API 來設定陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="f2826-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="f2826-120">一旦呼叫了的字串`Property`多載，您就可以將任何其他屬性的設定呼叫鏈在一起。</span><span class="sxs-lookup"><span data-stu-id="f2826-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="f2826-121">如果提供給`Property`方法的名稱符合現有屬性的名稱（陰影屬性或實體類別上定義的內容），則程式碼會設定該現有的屬性，而不會引進新的陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="f2826-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/ShadowProperty.cs?highlight=7,8)] -->
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
