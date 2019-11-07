---
title: 替代金鑰-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: e5bb0602adb465435c8e88d045395a9424b2d9a3
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655782"
---
# <a name="alternate-keys"></a><span data-ttu-id="24937-102">替代索引鍵</span><span class="sxs-lookup"><span data-stu-id="24937-102">Alternate Keys</span></span>

<span data-ttu-id="24937-103">除了主鍵以外，其他索引鍵可作為每個實體實例的替代唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="24937-103">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key.</span></span> <span data-ttu-id="24937-104">您可以使用替代索引鍵做為關聯性的目標。</span><span class="sxs-lookup"><span data-stu-id="24937-104">Alternate keys can be used as the target of a relationship.</span></span> <span data-ttu-id="24937-105">當使用關係資料庫時，這會對應至替代索引鍵資料行上唯一索引/條件約束的概念，以及一個或多個參考資料行的 foreign key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="24937-105">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]  
> <span data-ttu-id="24937-106">如果您只想要強制執行資料行的唯一性，則您需要唯一索引，而不是替代索引鍵，請參閱[索引](indexes.md)。</span><span class="sxs-lookup"><span data-stu-id="24937-106">If you just want to enforce uniqueness of a column then you want a unique index rather than an alternate key, see [Indexes](indexes.md).</span></span> <span data-ttu-id="24937-107">在 EF 中，其他索引鍵提供的功能比唯一索引更高，因為它們可以當做外鍵的目標使用。</span><span class="sxs-lookup"><span data-stu-id="24937-107">In EF, alternate keys provide greater functionality than unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="24937-108">您通常會在需要時為您引進替代金鑰，而不需要手動設定。</span><span class="sxs-lookup"><span data-stu-id="24937-108">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="24937-109">如需詳細資訊，請參閱[慣例](#conventions)。</span><span class="sxs-lookup"><span data-stu-id="24937-109">See [Conventions](#conventions) for more details.</span></span>

## <a name="conventions"></a><span data-ttu-id="24937-110">慣例</span><span class="sxs-lookup"><span data-stu-id="24937-110">Conventions</span></span>

<span data-ttu-id="24937-111">依照慣例，當您將不是主鍵的屬性識別為關聯性的目標時，會為您引進替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="24937-111">By convention, an alternate key is introduced for you when you identify a property, that is not the primary key, as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

## <a name="data-annotations"></a><span data-ttu-id="24937-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="24937-112">Data Annotations</span></span>

<span data-ttu-id="24937-113">無法使用資料批註來設定替代索引鍵。</span><span class="sxs-lookup"><span data-stu-id="24937-113">Alternate keys can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="24937-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="24937-114">Fluent API</span></span>

<span data-ttu-id="24937-115">您可以使用流暢的 API，將單一屬性設定為替代金鑰。</span><span class="sxs-lookup"><span data-stu-id="24937-115">You can use the Fluent API to configure a single property to be an alternate key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=7,8)]

<span data-ttu-id="24937-116">您也可以使用流暢的 API，將多個屬性設定為另一個索引鍵（也稱為複合的替代索引鍵）。</span><span class="sxs-lookup"><span data-stu-id="24937-116">You can also use the Fluent API to configure multiple properties to be an alternate key (known as a composite alternate key).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=7,8)]
