---
title: 必要/選用的屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 564d9e62e2ed4f1a52b569630ed4994529e31dc1
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929806"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="53f90-102">必要和選擇性的屬性</span><span class="sxs-lookup"><span data-stu-id="53f90-102">Required and Optional Properties</span></span>

<span data-ttu-id="53f90-103">屬性會被視為選擇性是否有效，才能包含`null`。</span><span class="sxs-lookup"><span data-stu-id="53f90-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="53f90-104">如果`null`不是有效的值指派給屬性，則它會被視為是必要的屬性。</span><span class="sxs-lookup"><span data-stu-id="53f90-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

## <a name="conventions"></a><span data-ttu-id="53f90-105">慣例</span><span class="sxs-lookup"><span data-stu-id="53f90-105">Conventions</span></span>

<span data-ttu-id="53f90-106">依照慣例，其 CLR 型別可以包含 null 的屬性會設定為選擇性 (`string`， `int?`， `byte[]`，依此類推。)。</span><span class="sxs-lookup"><span data-stu-id="53f90-106">By convention, a property whose CLR type can contain null will be configured as optional (`string`, `int?`, `byte[]`, etc.).</span></span> <span data-ttu-id="53f90-107">將設定其 CLR 類型不能包含 null 的屬性視需要而定 (`int`， `decimal`， `bool`，依此類推。)。</span><span class="sxs-lookup"><span data-stu-id="53f90-107">Properties whose CLR type cannot contain null will be configured as required (`int`, `decimal`, `bool`, etc.).</span></span>

> [!NOTE]  
> <span data-ttu-id="53f90-108">CLR 類型不能包含 null 的屬性無法設定為選擇性。</span><span class="sxs-lookup"><span data-stu-id="53f90-108">A property whose CLR type cannot contain null cannot be configured as optional.</span></span> <span data-ttu-id="53f90-109">屬性會一律視為 Entity Framework 所需。</span><span class="sxs-lookup"><span data-stu-id="53f90-109">The property will always be considered required by Entity Framework.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="53f90-110">資料註釋</span><span class="sxs-lookup"><span data-stu-id="53f90-110">Data Annotations</span></span>

<span data-ttu-id="53f90-111">您可以使用資料註解表示是必要屬性。</span><span class="sxs-lookup"><span data-stu-id="53f90-111">You can use Data Annotations to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="53f90-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="53f90-112">Fluent API</span></span>

<span data-ttu-id="53f90-113">您可以使用 Fluent API，以表示所需的屬性。</span><span class="sxs-lookup"><span data-stu-id="53f90-113">You can use the Fluent API to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

