---
title: 支援空間類型的提供者-EF6
description: Entity Framework 6 中的空間類型提供者支援
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: d6214ec503af8ea02b586cfddfd958ec7b47e42f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070441"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="66c00-103">空間類型的提供者支援</span><span class="sxs-lookup"><span data-stu-id="66c00-103">Provider Support for Spatial Types</span></span>
<span data-ttu-id="66c00-104">Entity Framework 支援透過 DbGeography 或 DbGeometry 類別處理空間資料。</span><span class="sxs-lookup"><span data-stu-id="66c00-104">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="66c00-105">這些類別依賴 Entity Framework 提供者所提供的資料庫特定功能。</span><span class="sxs-lookup"><span data-stu-id="66c00-105">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="66c00-106">並非所有提供者都支援空間資料，而且可能有其他必要條件，例如安裝空間類型元件。</span><span class="sxs-lookup"><span data-stu-id="66c00-106">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="66c00-107">以下提供有關空間類型提供者支援的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="66c00-107">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="66c00-108">有關如何在應用程式中使用空間類型的其他資訊，可以在兩個逐步解說中找到，一個用於 Code First，另一個用於 Database First 或 Model First：</span><span class="sxs-lookup"><span data-stu-id="66c00-108">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="66c00-109">Code First 中的空間資料類型</span><span class="sxs-lookup"><span data-stu-id="66c00-109">Spatial Data Types in Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)  
- [<span data-ttu-id="66c00-110">EF 設計工具中的空間資料類型</span><span class="sxs-lookup"><span data-stu-id="66c00-110">Spatial Data Types in EF Designer</span></span>](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="66c00-111">支援空間類型的 EF 版本</span><span class="sxs-lookup"><span data-stu-id="66c00-111">EF releases that support spatial types</span></span>  

<span data-ttu-id="66c00-112">EF5 中引進了對空間類型的支援。</span><span class="sxs-lookup"><span data-stu-id="66c00-112">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="66c00-113">不過，只有當應用程式是以 .NET 4.5 為目標並執行時，才支援 EF5 空間類型。</span><span class="sxs-lookup"><span data-stu-id="66c00-113">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="66c00-114">以 .NET 4 和 .NET 4.5 為目標的應用程式支援以 EF6 空間類型開始。</span><span class="sxs-lookup"><span data-stu-id="66c00-114">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="66c00-115">支援空間類型的 EF 提供者</span><span class="sxs-lookup"><span data-stu-id="66c00-115">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="66c00-116">EF5</span><span class="sxs-lookup"><span data-stu-id="66c00-116">EF5</span></span>  

<span data-ttu-id="66c00-117">我們注意到支援空間類型的 EF5 Entity Framework 提供者為：</span><span class="sxs-lookup"><span data-stu-id="66c00-117">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="66c00-118">Microsoft SQL Server 提供者</span><span class="sxs-lookup"><span data-stu-id="66c00-118">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="66c00-119">此提供者隨附于 EF5 的一部分。</span><span class="sxs-lookup"><span data-stu-id="66c00-119">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="66c00-120">此提供者相依于可能需要安裝的一些較低層級程式庫，如需詳細資料，請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="66c00-120">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="66c00-121">適用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="66c00-121">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="66c00-122">這是來自 Devart 的協力廠商提供者。</span><span class="sxs-lookup"><span data-stu-id="66c00-122">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="66c00-123">如果您知道支援空間類型的 EF5 提供者，請聯繫，我們很樂意將其新增至此清單。</span><span class="sxs-lookup"><span data-stu-id="66c00-123">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="66c00-124">EF6</span><span class="sxs-lookup"><span data-stu-id="66c00-124">EF6</span></span>  

<span data-ttu-id="66c00-125">我們注意到支援空間類型的 EF6 Entity Framework 提供者為：</span><span class="sxs-lookup"><span data-stu-id="66c00-125">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="66c00-126">Microsoft SQL Server 提供者</span><span class="sxs-lookup"><span data-stu-id="66c00-126">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="66c00-127">此提供者隨附于 EF6 的一部分。</span><span class="sxs-lookup"><span data-stu-id="66c00-127">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="66c00-128">此提供者相依于可能需要安裝的一些較低層級程式庫，如需詳細資料，請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="66c00-128">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="66c00-129">適用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="66c00-129">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="66c00-130">這是來自 Devart 的協力廠商提供者。</span><span class="sxs-lookup"><span data-stu-id="66c00-130">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="66c00-131">如果您知道支援空間類型的 EF6 提供者，請聯繫，我們很樂意將其新增至此清單。</span><span class="sxs-lookup"><span data-stu-id="66c00-131">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="66c00-132">具有 Microsoft SQL Server 的空間類型必要條件</span><span class="sxs-lookup"><span data-stu-id="66c00-132">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="66c00-133">SQL Server 空間支援取決於低層級的 SQL Server 特定類型 SqlGeography 和 SqlGeometry。</span><span class="sxs-lookup"><span data-stu-id="66c00-133">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="66c00-134">這些類型會存留在 Microsoft.SqlServer.Types.dll 元件中，而此元件不會隨附于 EF 或 .NET Framework 的一部分。</span><span class="sxs-lookup"><span data-stu-id="66c00-134">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="66c00-135">安裝 Visual Studio 時，通常也會安裝 SQL Server 的版本，這將包含安裝 Microsoft.SqlServer.Types.dll。</span><span class="sxs-lookup"><span data-stu-id="66c00-135">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="66c00-136">如果 SQL Server 未安裝在您要使用空間類型的電腦上，或空間類型已從 SQL Server 安裝中排除，則您必須手動安裝它們。</span><span class="sxs-lookup"><span data-stu-id="66c00-136">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="66c00-137">您可以使用來安裝類型 `SQLSysClrTypes.msi` ，這是 Microsoft SQL Server Feature Pack 的一部分。</span><span class="sxs-lookup"><span data-stu-id="66c00-137">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="66c00-138">空間類型 SQL Server 版本專屬，因此我們建議您在 Microsoft 下載中心 [搜尋「SQL Server Feature Pack](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) 」，然後選取並下載與您將使用之 SQL Server 版本對應的選項。</span><span class="sxs-lookup"><span data-stu-id="66c00-138">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>
