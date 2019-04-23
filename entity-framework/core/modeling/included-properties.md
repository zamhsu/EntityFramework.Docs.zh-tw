---
title: 包含與排除內容-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: 022534091bb48e491c8808791a401216a339d7b0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929819"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="ffed2-102">包含與排除屬性</span><span class="sxs-lookup"><span data-stu-id="ffed2-102">Including & Excluding Properties</span></span>

<span data-ttu-id="ffed2-103">包括在模型中的屬性，表示 EF 具有該屬性的相關中繼資料，並會嘗試讀取和寫入，或將資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="ffed2-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="ffed2-104">慣例</span><span class="sxs-lookup"><span data-stu-id="ffed2-104">Conventions</span></span>

<span data-ttu-id="ffed2-105">依照慣例，getter 和 setter 的公用屬性，將會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="ffed2-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="ffed2-106">資料註釋</span><span class="sxs-lookup"><span data-stu-id="ffed2-106">Data Annotations</span></span>

<span data-ttu-id="ffed2-107">若要從模型中排除的屬性，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="ffed2-107">You can use Data Annotations to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a><span data-ttu-id="ffed2-108">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ffed2-108">Fluent API</span></span>

<span data-ttu-id="ffed2-109">您可以使用 Fluent API，若要從模型中排除的屬性。</span><span class="sxs-lookup"><span data-stu-id="ffed2-109">You can use the Fluent API to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreProperty.cs?highlight=12,13)]
