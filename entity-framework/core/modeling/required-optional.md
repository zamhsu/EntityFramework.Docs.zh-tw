---
title: 必要/選用屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 7200cd2eeeba2f22365ef09b1f50edd077240130
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149156"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="5d7e1-102">必要和選擇性屬性</span><span class="sxs-lookup"><span data-stu-id="5d7e1-102">Required and Optional Properties</span></span>

<span data-ttu-id="5d7e1-103">如果屬性可包含`null`，則會將其視為選擇性。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="5d7e1-104">如果`null`不是要指派給屬性的有效值，則會將它視為必要的屬性。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

## <a name="conventions"></a><span data-ttu-id="5d7e1-105">慣例</span><span class="sxs-lookup"><span data-stu-id="5d7e1-105">Conventions</span></span>

<span data-ttu-id="5d7e1-106">依照慣例，其 .net 類型可以包含 null 的屬性會設定為選擇性（`string`、 `int?`、 `byte[]`等）。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-106">By convention, a property whose .NET type can contain null will be configured as optional (`string`, `int?`, `byte[]`, etc.).</span></span> <span data-ttu-id="5d7e1-107">CLR 類型不能包含 null 的屬性會設定為必要（`int`、 `decimal`、 `bool`等）。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-107">Properties whose CLR type cannot contain null will be configured as required (`int`, `decimal`, `bool`, etc.).</span></span>

> [!NOTE]  
> <span data-ttu-id="5d7e1-108">其 .NET 類型不能包含 null 的屬性無法設定為選擇性。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-108">A property whose .NET type cannot contain null cannot be configured as optional.</span></span> <span data-ttu-id="5d7e1-109">Entity Framework，一律會將屬性視為必要。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-109">The property will always be considered required by Entity Framework.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="5d7e1-110">資料註釋</span><span class="sxs-lookup"><span data-stu-id="5d7e1-110">Data Annotations</span></span>

<span data-ttu-id="5d7e1-111">您可以使用資料批註來指出屬性是必要的。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-111">You can use Data Annotations to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="5d7e1-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="5d7e1-112">Fluent API</span></span>

<span data-ttu-id="5d7e1-113">您可以使用流暢的 API 來表示需要屬性。</span><span class="sxs-lookup"><span data-stu-id="5d7e1-113">You can use the Fluent API to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

