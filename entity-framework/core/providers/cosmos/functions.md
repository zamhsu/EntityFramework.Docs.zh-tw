---
title: 函數對應-Azure Cosmos DB 提供者-EF Core
description: Azure Cosmos DB EF Core 提供者的函式對應
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066591"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="8b0bc-103">Azure Cosmos DB EF Core 提供者的函式對應</span><span class="sxs-lookup"><span data-stu-id="8b0bc-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="8b0bc-104">此頁面會顯示使用 Azure Cosmos DB 提供者時，哪些 .NET 成員會轉譯成哪些 SQL 函數。</span><span class="sxs-lookup"><span data-stu-id="8b0bc-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="8b0bc-105">.NET</span><span class="sxs-lookup"><span data-stu-id="8b0bc-105">.NET</span></span>                          | <span data-ttu-id="8b0bc-106">SQL</span><span class="sxs-lookup"><span data-stu-id="8b0bc-106">SQL</span></span>                              | <span data-ttu-id="8b0bc-107">加入</span><span class="sxs-lookup"><span data-stu-id="8b0bc-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="8b0bc-108">收集。包含 (專案) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-108">collection.Contains(item)</span></span>     | <span data-ttu-id="8b0bc-109">@item 在 @collection</span><span class="sxs-lookup"><span data-stu-id="8b0bc-109">@item IN @collection</span></span>
<span data-ttu-id="8b0bc-110">stringValue 包含 (值) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-110">stringValue.Contains(value)</span></span>   | <span data-ttu-id="8b0bc-111">包含 (@stringValue 、 @value) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-111">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="8b0bc-112">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="8b0bc-112">EF Core 5.0</span></span>
<span data-ttu-id="8b0bc-113">stringValue. EndsWith (值) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-113">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="8b0bc-114">ENDSWITH (@stringValue ， @value) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-114">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="8b0bc-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="8b0bc-115">EF Core 5.0</span></span>
<span data-ttu-id="8b0bc-116">stringValue. FirstOrDefault ( # A1</span><span class="sxs-lookup"><span data-stu-id="8b0bc-116">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="8b0bc-117">左 (@stringValue ，1) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-117">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="8b0bc-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="8b0bc-118">EF Core 5.0</span></span>
<span data-ttu-id="8b0bc-119">stringValue. LastOrDefault ( # A1</span><span class="sxs-lookup"><span data-stu-id="8b0bc-119">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="8b0bc-120">RIGHT (@stringValue ，1) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-120">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="8b0bc-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="8b0bc-121">EF Core 5.0</span></span>
<span data-ttu-id="8b0bc-122">stringValue. StartsWith (值) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-122">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="8b0bc-123">STARTSWITH (@stringValue ， @value) </span><span class="sxs-lookup"><span data-stu-id="8b0bc-123">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="8b0bc-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="8b0bc-124">EF Core 5.0</span></span>
