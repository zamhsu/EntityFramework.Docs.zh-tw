---
title: 包括 & 排除屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: cd111af891ef0bbaccf515eed0c1991f105bd362
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197420"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="34f3b-102">包含與排除屬性</span><span class="sxs-lookup"><span data-stu-id="34f3b-102">Including & Excluding Properties</span></span>

<span data-ttu-id="34f3b-103">將屬性包含在模型中，表示 EF 具有該屬性的相關中繼資料，並會嘗試讀取及寫入資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="34f3b-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="34f3b-104">慣例</span><span class="sxs-lookup"><span data-stu-id="34f3b-104">Conventions</span></span>

<span data-ttu-id="34f3b-105">依照慣例，具有 getter 和 setter 的公用屬性將會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="34f3b-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="34f3b-106">資料註釋</span><span class="sxs-lookup"><span data-stu-id="34f3b-106">Data Annotations</span></span>

<span data-ttu-id="34f3b-107">您可以使用資料批註，從模型中排除屬性。</span><span class="sxs-lookup"><span data-stu-id="34f3b-107">You can use Data Annotations to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a><span data-ttu-id="34f3b-108">Fluent API</span><span class="sxs-lookup"><span data-stu-id="34f3b-108">Fluent API</span></span>

<span data-ttu-id="34f3b-109">您可以使用流暢的 API，從模型中排除屬性。</span><span class="sxs-lookup"><span data-stu-id="34f3b-109">You can use the Fluent API to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?highlight=12,13)]
