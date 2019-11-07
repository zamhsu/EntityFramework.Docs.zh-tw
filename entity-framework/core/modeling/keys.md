---
title: 金鑰（主要）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 66c64c389294e8e109a614a2bea8311932660dea
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655940"
---
# <a name="keys-primary"></a><span data-ttu-id="b2914-102">索引鍵 (主要)</span><span class="sxs-lookup"><span data-stu-id="b2914-102">Keys (primary)</span></span>

<span data-ttu-id="b2914-103">索引鍵可作為每個實體實例的主要唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="b2914-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="b2914-104">當使用關係資料庫時，這會對應至*主鍵*的概念。</span><span class="sxs-lookup"><span data-stu-id="b2914-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="b2914-105">您也可以設定非主要金鑰的唯一識別碼（如需詳細資訊，請參閱[替代金鑰](alternate-keys.md)）。</span><span class="sxs-lookup"><span data-stu-id="b2914-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<span data-ttu-id="b2914-106">您可以使用下列其中一種方法來設定/建立主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="b2914-106">One of the following methods can be used to setup/create a primary key.</span></span>

## <a name="conventions"></a><span data-ttu-id="b2914-107">慣例</span><span class="sxs-lookup"><span data-stu-id="b2914-107">Conventions</span></span>

<span data-ttu-id="b2914-108">依照慣例，名為 `Id` 或 `<type name>Id` 的屬性會設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b2914-108">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyIdhighlight=3)]

## <a name="data-annotations"></a><span data-ttu-id="b2914-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="b2914-109">Data Annotations</span></span>

<span data-ttu-id="b2914-110">您可以使用資料批註，將單一屬性設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b2914-110">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="b2914-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b2914-111">Fluent API</span></span>

<span data-ttu-id="b2914-112">您可以使用流暢的 API，將單一屬性設定為實體的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b2914-112">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

<span data-ttu-id="b2914-113">您也可以使用流暢的 API，將多個屬性設定為實體的索引鍵（也稱為複合索引鍵）。</span><span class="sxs-lookup"><span data-stu-id="b2914-113">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="b2914-114">複合索引鍵只能使用流暢的 API 慣例來設定複合索引鍵，而且您不能使用資料批註來設定它。</span><span class="sxs-lookup"><span data-stu-id="b2914-114">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]
