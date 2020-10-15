---
title: 明確載入相關資料-EF Core
description: 使用 Entity Framework Core 明確載入相關資料
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 0cfc4b5304d81d5a58497857474804946d3f85d7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065715"
---
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="40a4a-103">明確載入相關資料</span><span class="sxs-lookup"><span data-stu-id="40a4a-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="40a4a-104">明確式載入</span><span class="sxs-lookup"><span data-stu-id="40a4a-104">Explicit loading</span></span>

<span data-ttu-id="40a4a-105">您可以透過 `DbContext.Entry(...)` API 來明確地載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="40a4a-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#Eager)]

<span data-ttu-id="40a4a-106">您也可以透過執行會傳回相關實體的個別查詢，來明確地載入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="40a4a-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="40a4a-107">如果已啟用變更追蹤，則當查詢具體化實體時，EF Core 會自動將新載入之實體的導覽屬性設定為參考已載入的任何實體，並將已載入之實體的導覽屬性設定為參考新載入的實體。</span><span class="sxs-lookup"><span data-stu-id="40a4a-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="40a4a-108">查詢相關實體</span><span class="sxs-lookup"><span data-stu-id="40a4a-108">Querying related entities</span></span>

<span data-ttu-id="40a4a-109">您也可以取得表示導覽屬性內容的 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="40a4a-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="40a4a-110">它可讓您在查詢上套用其他運算子。</span><span class="sxs-lookup"><span data-stu-id="40a4a-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="40a4a-111">例如，在相關實體上套用匯總運算子，而不將其載入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="40a4a-111">For example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryAggregate)]

<span data-ttu-id="40a4a-112">您也可以篩選要將哪些相關實體載入至記憶體。</span><span class="sxs-lookup"><span data-stu-id="40a4a-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryFiltered)]
