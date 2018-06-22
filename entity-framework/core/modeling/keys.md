---
title: 索引鍵 （主要）-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
ms.technology: entity-framework-core
uid: core/modeling/keys
ms.openlocfilehash: f3bf3c7f2a28e065b350fe000a5164406cd5ca08
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052568"
---
# <a name="keys-primary"></a><span data-ttu-id="c4a4c-102">索引鍵 （主要）</span><span class="sxs-lookup"><span data-stu-id="c4a4c-102">Keys (primary)</span></span>

<span data-ttu-id="c4a4c-103">索引鍵做為主要的唯一識別碼，每個實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="c4a4c-104">使用關聯式資料庫時這會對應到概念*主索引鍵*。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="c4a4c-105">您也可以設定不是主索引鍵的唯一識別碼 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

## <a name="conventions"></a><span data-ttu-id="c4a4c-106">慣例</span><span class="sxs-lookup"><span data-stu-id="c4a4c-106">Conventions</span></span>

<span data-ttu-id="c4a4c-107">依照慣例，屬性名稱為`Id`或`<type name>Id`將設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-107">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="c4a4c-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="c4a4c-108">Data Annotations</span></span>

<span data-ttu-id="c4a4c-109">若要設定單一屬性作為索引鍵的實體，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-109">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

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

## <a name="fluent-api"></a><span data-ttu-id="c4a4c-110">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="c4a4c-110">Fluent API</span></span>

<span data-ttu-id="c4a4c-111">您可以使用 fluent 應用程式開發的應用程式開發介面來設定單一屬性是實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-111">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

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

<span data-ttu-id="c4a4c-112">您也可以使用 fluent 應用程式開發的應用程式開發介面來設定多個屬性作為索引鍵的實體 （稱為複合索引鍵）。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-112">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="c4a4c-113">複合索引鍵只能設定使用 Fluent API-慣例將永遠不會設定複合索引鍵和您不可以使用資料註解設定一個。</span><span class="sxs-lookup"><span data-stu-id="c4a4c-113">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

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
