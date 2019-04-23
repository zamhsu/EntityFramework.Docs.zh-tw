---
title: 索引鍵 （主要）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 51d163b867085f42f415dbd7afa9e311ab1781a0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929832"
---
# <a name="keys-primary"></a><span data-ttu-id="ef82e-102">索引鍵 （主要）</span><span class="sxs-lookup"><span data-stu-id="ef82e-102">Keys (primary)</span></span>

<span data-ttu-id="ef82e-103">索引鍵做為主要的唯一識別碼，每個實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="ef82e-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="ef82e-104">使用關聯式資料庫時這會對應至的概念*主索引鍵*。</span><span class="sxs-lookup"><span data-stu-id="ef82e-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="ef82e-105">您也可以設定不是主索引鍵的唯一識別碼 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。</span><span class="sxs-lookup"><span data-stu-id="ef82e-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span> 

<span data-ttu-id="ef82e-106">其中一種下列方法可用來安裝/建立主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ef82e-106">One of the following methods can be used to setup/create a primary key.</span></span>

## <a name="conventions"></a><span data-ttu-id="ef82e-107">慣例</span><span class="sxs-lookup"><span data-stu-id="ef82e-107">Conventions</span></span>

<span data-ttu-id="ef82e-108">依照慣例，屬性名為`Id`或`<type name>Id`會設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ef82e-108">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="ef82e-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="ef82e-109">Data Annotations</span></span>

<span data-ttu-id="ef82e-110">您可以使用資料註解來設定實體的索引鍵的單一屬性。</span><span class="sxs-lookup"><span data-stu-id="ef82e-110">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="ef82e-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ef82e-111">Fluent API</span></span>

<span data-ttu-id="ef82e-112">您可以使用 Fluent API 來設定實體的索引鍵的單一屬性。</span><span class="sxs-lookup"><span data-stu-id="ef82e-112">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/KeySingle.cs?highlight=11,12)]

<span data-ttu-id="ef82e-113">您也可以使用 Fluent API 來設定多個屬性 （又稱為複合索引鍵） 的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ef82e-113">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="ef82e-114">複合索引鍵可以只設定使用 Fluent API-慣例永遠不會設定複合索引鍵而且您可以使用資料註解來設定其中一個。</span><span class="sxs-lookup"><span data-stu-id="ef82e-114">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/KeyComposite.cs?highlight=11,12)]
