---
title: 函數對應-Azure Cosmos DB 提供者-EF Core
description: Azure Cosmos DB EF Core 提供者的函式對應
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543584"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="7a520-103">Azure Cosmos DB EF Core 提供者的函式對應</span><span class="sxs-lookup"><span data-stu-id="7a520-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="7a520-104">此頁面會顯示使用 Azure Cosmos DB 提供者時，哪些 .NET 成員會轉譯成哪些 SQL 函數。</span><span class="sxs-lookup"><span data-stu-id="7a520-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="7a520-105">.NET</span><span class="sxs-lookup"><span data-stu-id="7a520-105">.NET</span></span>                          | <span data-ttu-id="7a520-106">SQL</span><span class="sxs-lookup"><span data-stu-id="7a520-106">SQL</span></span>                              | <span data-ttu-id="7a520-107">加入</span><span class="sxs-lookup"><span data-stu-id="7a520-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="7a520-108">收集。包含 (專案) </span><span class="sxs-lookup"><span data-stu-id="7a520-108">collection.Contains(item)</span></span>     | <span data-ttu-id="7a520-109">@item 在 @collection</span><span class="sxs-lookup"><span data-stu-id="7a520-109">@item IN @collection</span></span>
<span data-ttu-id="7a520-110">英 孚。函數。隨機 () </span><span class="sxs-lookup"><span data-stu-id="7a520-110">EF.Functions.Random()</span></span>         | <span data-ttu-id="7a520-111">RAND () </span><span class="sxs-lookup"><span data-stu-id="7a520-111">RAND()</span></span>                           | <span data-ttu-id="7a520-112">EF Core 6.0</span><span class="sxs-lookup"><span data-stu-id="7a520-112">EF Core 6.0</span></span>
<span data-ttu-id="7a520-113">stringValue 包含 (值) </span><span class="sxs-lookup"><span data-stu-id="7a520-113">stringValue.Contains(value)</span></span>   | <span data-ttu-id="7a520-114">包含 (@stringValue 、 @value) </span><span class="sxs-lookup"><span data-stu-id="7a520-114">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="7a520-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="7a520-115">EF Core 5.0</span></span>
<span data-ttu-id="7a520-116">stringValue. EndsWith (值) </span><span class="sxs-lookup"><span data-stu-id="7a520-116">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="7a520-117">ENDSWITH (@stringValue ， @value) </span><span class="sxs-lookup"><span data-stu-id="7a520-117">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="7a520-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="7a520-118">EF Core 5.0</span></span>
<span data-ttu-id="7a520-119">stringValue. FirstOrDefault () </span><span class="sxs-lookup"><span data-stu-id="7a520-119">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="7a520-120">左 (@stringValue ，1) </span><span class="sxs-lookup"><span data-stu-id="7a520-120">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="7a520-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="7a520-121">EF Core 5.0</span></span>
<span data-ttu-id="7a520-122">stringValue. LastOrDefault () </span><span class="sxs-lookup"><span data-stu-id="7a520-122">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="7a520-123">RIGHT (@stringValue ，1) </span><span class="sxs-lookup"><span data-stu-id="7a520-123">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="7a520-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="7a520-124">EF Core 5.0</span></span>
<span data-ttu-id="7a520-125">stringValue. StartsWith (值) </span><span class="sxs-lookup"><span data-stu-id="7a520-125">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="7a520-126">STARTSWITH (@stringValue ， @value) </span><span class="sxs-lookup"><span data-stu-id="7a520-126">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="7a520-127">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="7a520-127">EF Core 5.0</span></span>
