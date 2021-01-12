---
title: 建立和設定模型 - EF Core
description: 使用 Entity Framework Core 建立及設定模型的概觀
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: 709e2bde60c8e2c31f0a39390624c5d31a9cfa08
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129196"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="1c774-103">建立和設定模型</span><span class="sxs-lookup"><span data-stu-id="1c774-103">Creating and configuring a model</span></span>

<span data-ttu-id="1c774-104">Entity Framework 會使用一組慣例，根據您實體類別的圖形建置模型。</span><span class="sxs-lookup"><span data-stu-id="1c774-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="1c774-105">您可以指定其他組態來補充及 (或) 覆寫慣例探索到的項目。</span><span class="sxs-lookup"><span data-stu-id="1c774-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="1c774-106">本文涵蓋可套用至將目標設為任何資料存放區之模型的組態，以及將目標設為任何關聯式資料庫時可套用的組態。</span><span class="sxs-lookup"><span data-stu-id="1c774-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="1c774-107">提供者也可以啟用特定資料存放區專屬的組態。</span><span class="sxs-lookup"><span data-stu-id="1c774-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="1c774-108">針對提供者專屬組態的文件，請參閱[資料庫提供者](xref:core/providers/index)一節。</span><span class="sxs-lookup"><span data-stu-id="1c774-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]
> <span data-ttu-id="1c774-109">您可以在 GitHub 上查看這篇文章的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) 。</span><span class="sxs-lookup"><span data-stu-id="1c774-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="1c774-110">使用 Fluent API 設定模型</span><span class="sxs-lookup"><span data-stu-id="1c774-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="1c774-111">您可以覆寫衍生內容中的 `OnModelCreating` 方法，並使用 `ModelBuilder API` 來設定模型。</span><span class="sxs-lookup"><span data-stu-id="1c774-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="1c774-112">這是最強大的組態方法，讓您能夠指定組態而不修改實體類別。</span><span class="sxs-lookup"><span data-stu-id="1c774-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="1c774-113">Fluent API 組態具有最高的優先順序，且會覆寫慣例及資料註解。</span><span class="sxs-lookup"><span data-stu-id="1c774-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a><span data-ttu-id="1c774-114">群組設定</span><span class="sxs-lookup"><span data-stu-id="1c774-114">Grouping configuration</span></span>

<span data-ttu-id="1c774-115">若要減少方法的大小， <xref:System.Data.Entity.DbContext.OnModelCreating%2A> 可以將實體類型的所有設定都解壓縮到個別的類別來執行 <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> 。</span><span class="sxs-lookup"><span data-stu-id="1c774-115">To reduce the size of the <xref:System.Data.Entity.DbContext.OnModelCreating%2A> method all configuration for an entity type can be extracted to a separate class implementing <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

<span data-ttu-id="1c774-116">然後，直接 `Configure` 從叫用方法 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="1c774-116">Then just invoke the `Configure` method from `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

<span data-ttu-id="1c774-117">您可以套用在指定元件中執行的型別中所指定的所有設定 `IEntityTypeConfiguration` 。</span><span class="sxs-lookup"><span data-stu-id="1c774-117">It is possible to apply all configuration specified in types implementing `IEntityTypeConfiguration` in a given assembly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> <span data-ttu-id="1c774-118">設定的套用順序是未定義的，因此，只有當訂單不重要時，才應該使用此方法。</span><span class="sxs-lookup"><span data-stu-id="1c774-118">The order in which the configurations will be applied is undefined, therefore this method should only be used when the order doesn't matter.</span></span>

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="1c774-119">使用資料註解</span><span class="sxs-lookup"><span data-stu-id="1c774-119">Use data annotations to configure a model</span></span>

<span data-ttu-id="1c774-120">您也可以將屬性 (又稱資料註解) 套用到類別及屬性。</span><span class="sxs-lookup"><span data-stu-id="1c774-120">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="1c774-121">資料註解會覆寫慣例，但會受到 Fluent API 組態覆寫。</span><span class="sxs-lookup"><span data-stu-id="1c774-121">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
