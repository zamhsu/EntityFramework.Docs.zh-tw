---
title: 替代索引鍵 （唯一條件約束）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7ec58ee31aac79e15329dc8542f37fd117772fbe
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994187"
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="bf3da-102">替代索引鍵 （唯一條件約束）</span><span class="sxs-lookup"><span data-stu-id="bf3da-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="bf3da-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="bf3da-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="bf3da-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="bf3da-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="bf3da-105">針對每個模型中的替代索引鍵引進 unique 條件約束。</span><span class="sxs-lookup"><span data-stu-id="bf3da-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="bf3da-106">慣例</span><span class="sxs-lookup"><span data-stu-id="bf3da-106">Conventions</span></span>

<span data-ttu-id="bf3da-107">依照慣例，導入的替代索引鍵的條件約束與索引將會命名為`AK_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="bf3da-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="bf3da-108">對於複合的替代索引鍵`<property name>`會變成屬性名稱的底線分隔清單。</span><span class="sxs-lookup"><span data-stu-id="bf3da-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="bf3da-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="bf3da-109">Data Annotations</span></span>

<span data-ttu-id="bf3da-110">Unique 條件約束不可以使用資料註解來設定。</span><span class="sxs-lookup"><span data-stu-id="bf3da-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="bf3da-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="bf3da-111">Fluent API</span></span>

<span data-ttu-id="bf3da-112">您可以使用 Fluent API 來設定其他的索引鍵的索引和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="bf3da-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
