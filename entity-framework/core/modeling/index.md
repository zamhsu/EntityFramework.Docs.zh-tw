---
title: 建立和設定模型 - EF Core
description: 使用 Entity Framework Core 建立及設定模型的概觀
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 7f8ef17f33a294dc0b8cc9c4e514a8a29b761342
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063058"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="2cac4-103">建立和設定模型</span><span class="sxs-lookup"><span data-stu-id="2cac4-103">Creating and configuring a model</span></span>

<span data-ttu-id="2cac4-104">Entity Framework 會使用一組慣例，根據您實體類別的圖形建置模型。</span><span class="sxs-lookup"><span data-stu-id="2cac4-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="2cac4-105">您可以指定其他組態來補充及 (或) 覆寫慣例探索到的項目。</span><span class="sxs-lookup"><span data-stu-id="2cac4-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="2cac4-106">本文涵蓋可套用至將目標設為任何資料存放區之模型的組態，以及將目標設為任何關聯式資料庫時可套用的組態。</span><span class="sxs-lookup"><span data-stu-id="2cac4-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="2cac4-107">提供者也可以啟用特定資料存放區專屬的組態。</span><span class="sxs-lookup"><span data-stu-id="2cac4-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="2cac4-108">如需提供者專屬組態的文件，請參閱 [資料庫提供者](xref:core/providers/index) 一節。</span><span class="sxs-lookup"><span data-stu-id="2cac4-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="2cac4-109">您可以在 GitHub 上檢視本文的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) 。</span><span class="sxs-lookup"><span data-stu-id="2cac4-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="2cac4-110">使用 Fluent API 設定模型</span><span class="sxs-lookup"><span data-stu-id="2cac4-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="2cac4-111">您可以覆寫衍生內容中的  `OnModelCreating` 方法，並用  `ModelBuilder API` 來設定模型。</span><span class="sxs-lookup"><span data-stu-id="2cac4-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="2cac4-112">這是最強大的組態方法，讓您能夠指定組態而不修改實體類別。</span><span class="sxs-lookup"><span data-stu-id="2cac4-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="2cac4-113">Fluent API 組態具有最高的優先順序，且會覆寫慣例及資料註解。</span><span class="sxs-lookup"><span data-stu-id="2cac4-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="2cac4-114">使用資料註解</span><span class="sxs-lookup"><span data-stu-id="2cac4-114">Use data annotations to configure a model</span></span>

<span data-ttu-id="2cac4-115">您也可以將屬性 (又稱資料註解) 套用到類別及屬性。</span><span class="sxs-lookup"><span data-stu-id="2cac4-115">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="2cac4-116">資料註解會覆寫慣例，但會受到 Fluent API 組態覆寫。</span><span class="sxs-lookup"><span data-stu-id="2cac4-116">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
