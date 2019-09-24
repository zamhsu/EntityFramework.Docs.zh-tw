---
title: 替代索引鍵（唯一條件約束）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7afcb804aeeccbd5e07c228a8fd9850ca00a2919
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197611"
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="a9e7d-102">替代索引鍵 (唯一條件約束)</span><span class="sxs-lookup"><span data-stu-id="a9e7d-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="a9e7d-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="a9e7d-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="a9e7d-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="a9e7d-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="a9e7d-105">模型中的每個替代索引鍵都會引進 unique 條件約束。</span><span class="sxs-lookup"><span data-stu-id="a9e7d-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="a9e7d-106">慣例</span><span class="sxs-lookup"><span data-stu-id="a9e7d-106">Conventions</span></span>

<span data-ttu-id="a9e7d-107">依照慣例，針對替代金鑰引進的索引和條件約束將會命名`AK_<type name>_<property name>`為。</span><span class="sxs-lookup"><span data-stu-id="a9e7d-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="a9e7d-108">對於複合的替代`<property name>`索引鍵，會變成以底線分隔的屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="a9e7d-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a9e7d-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="a9e7d-109">Data Annotations</span></span>

<span data-ttu-id="a9e7d-110">不能使用資料批註來設定唯一的條件約束。</span><span class="sxs-lookup"><span data-stu-id="a9e7d-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a9e7d-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a9e7d-111">Fluent API</span></span>

<span data-ttu-id="a9e7d-112">您可以使用流暢的 API 來設定替代金鑰的索引和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="a9e7d-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
