---
title: 索引鍵 （主要）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 9e6946100ebabc6ba57cb792b3672219098b1e21
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994017"
---
# <a name="keys-primary"></a><span data-ttu-id="93bd5-102">索引鍵 （主要）</span><span class="sxs-lookup"><span data-stu-id="93bd5-102">Keys (primary)</span></span>

<span data-ttu-id="93bd5-103">索引鍵做為主要的唯一識別碼，每個實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="93bd5-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="93bd5-104">使用關聯式資料庫時這會對應至的概念*主索引鍵*。</span><span class="sxs-lookup"><span data-stu-id="93bd5-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="93bd5-105">您也可以設定不是主索引鍵的唯一識別碼 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="93bd5-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

## <a name="conventions"></a><span data-ttu-id="93bd5-106">慣例</span><span class="sxs-lookup"><span data-stu-id="93bd5-106">Conventions</span></span>

<span data-ttu-id="93bd5-107">依照慣例，屬性名為`Id`或`<type name>Id`會設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="93bd5-107">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="93bd5-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="93bd5-108">Data Annotations</span></span>

<span data-ttu-id="93bd5-109">您可以使用資料註解來設定實體的索引鍵的單一屬性。</span><span class="sxs-lookup"><span data-stu-id="93bd5-109">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/KeySingle.cs?highlight=3,4)] -->
``` csharp
class Car
{
    [Key]
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="93bd5-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="93bd5-110">Fluent API</span></span>

<span data-ttu-id="93bd5-111">您可以使用 Fluent API 來設定實體的索引鍵的單一屬性。</span><span class="sxs-lookup"><span data-stu-id="93bd5-111">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeySingle.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => c.LicensePlate);
    }
}

class Car
{
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<span data-ttu-id="93bd5-112">您也可以使用 Fluent API 來設定多個屬性 （又稱為複合索引鍵） 的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="93bd5-112">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="93bd5-113">複合索引鍵可以只設定使用 Fluent API-慣例永遠不會設定複合索引鍵而且您可以使用資料註解來設定其中一個。</span><span class="sxs-lookup"><span data-stu-id="93bd5-113">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeyComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public string State { get; set; }
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```
