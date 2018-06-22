---
title: 替代索引鍵 （唯一條件約束）-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
ms.technology: entity-framework-core
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 1b7e2bef6ede95f8c27211ba00dcc6b97cccde9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052788"
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="33aa0-102">替代索引鍵 （唯一條件約束）</span><span class="sxs-lookup"><span data-stu-id="33aa0-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="33aa0-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="33aa0-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="33aa0-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="33aa0-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="33aa0-105">針對每個模型中的替代索引鍵引進 unique 條件約束。</span><span class="sxs-lookup"><span data-stu-id="33aa0-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="33aa0-106">慣例</span><span class="sxs-lookup"><span data-stu-id="33aa0-106">Conventions</span></span>

<span data-ttu-id="33aa0-107">依照慣例，會引進其他的索引鍵的條件約束與索引將被命名為`AK_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="33aa0-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="33aa0-108">對於複合替代索引鍵`<property name>`變成屬性名稱的底線分隔清單。</span><span class="sxs-lookup"><span data-stu-id="33aa0-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="33aa0-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="33aa0-109">Data Annotations</span></span>

<span data-ttu-id="33aa0-110">Unique 條件約束不可以使用資料註解來設定。</span><span class="sxs-lookup"><span data-stu-id="33aa0-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="33aa0-111">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="33aa0-111">Fluent API</span></span>

<span data-ttu-id="33aa0-112">您可以使用 fluent 應用程式開發的應用程式開發介面來設定其他的索引鍵的索引和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="33aa0-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
