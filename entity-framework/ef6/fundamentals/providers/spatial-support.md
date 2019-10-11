---
title: 空間類型的提供者支援-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 863f1b4551bd62160915eba90fee7ba6c49c169c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181600"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="3c155-102">空間類型的提供者支援</span><span class="sxs-lookup"><span data-stu-id="3c155-102">Provider Support for Spatial Types</span></span>
<span data-ttu-id="3c155-103">Entity Framework 支援透過 DbGeography 或 DbGeometry 類別來使用空間資料。</span><span class="sxs-lookup"><span data-stu-id="3c155-103">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="3c155-104">這些類別會依賴 Entity Framework 提供者所提供的資料庫特定功能。</span><span class="sxs-lookup"><span data-stu-id="3c155-104">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="3c155-105">並非所有提供者都支援空間資料，而且可能會有額外的必要條件（例如安裝空間類型元件）。</span><span class="sxs-lookup"><span data-stu-id="3c155-105">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="3c155-106">以下提供有關空間類型提供者支援的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="3c155-106">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="3c155-107">有關如何在應用程式中使用空間類型的其他資訊，可以在兩個逐步解說中找到，一個用於 Code First，另一個用於 Database First 或 Model First：</span><span class="sxs-lookup"><span data-stu-id="3c155-107">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="3c155-108">Code First 中的空間資料類型</span><span class="sxs-lookup"><span data-stu-id="3c155-108">Spatial Data Types in Code First</span></span>](~/ef6/modeling/code-first/data-types/spatial.md)  
- [<span data-ttu-id="3c155-109">EF 設計工具中的空間資料類型</span><span class="sxs-lookup"><span data-stu-id="3c155-109">Spatial Data Types in EF Designer</span></span>](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="3c155-110">支援空間類型的 EF 版本</span><span class="sxs-lookup"><span data-stu-id="3c155-110">EF releases that support spatial types</span></span>  

<span data-ttu-id="3c155-111">空間類型的支援已在 EF5 中引進。</span><span class="sxs-lookup"><span data-stu-id="3c155-111">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="3c155-112">不過，在 EF5 空間類型中，只有在應用程式以 .NET 4.5 為目標並執行時才支援。</span><span class="sxs-lookup"><span data-stu-id="3c155-112">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="3c155-113">針對以 .NET 4 和 .NET 4.5 為目標的應用程式，支援從 EF6 空間類型開始。</span><span class="sxs-lookup"><span data-stu-id="3c155-113">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="3c155-114">支援空間類型的 EF 提供者</span><span class="sxs-lookup"><span data-stu-id="3c155-114">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="3c155-115">EF5</span><span class="sxs-lookup"><span data-stu-id="3c155-115">EF5</span></span>  

<span data-ttu-id="3c155-116">我們知道支援空間類型之 EF5 的 Entity Framework 提供者為：</span><span class="sxs-lookup"><span data-stu-id="3c155-116">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="3c155-117">Microsoft SQL Server 提供者</span><span class="sxs-lookup"><span data-stu-id="3c155-117">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="3c155-118">此提供者隨附于 EF5 中。</span><span class="sxs-lookup"><span data-stu-id="3c155-118">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="3c155-119">此提供者取決於可能需要安裝的其他低層級程式庫，請參閱下面的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="3c155-119">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="3c155-120">適用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="3c155-120">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="3c155-121">這是 Devart 的協力廠商提供者。</span><span class="sxs-lookup"><span data-stu-id="3c155-121">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="3c155-122">如果您知道支援空間類型的 EF5 提供者，請取得連絡人，我們很樂意將其新增至此清單。</span><span class="sxs-lookup"><span data-stu-id="3c155-122">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="3c155-123">EF6</span><span class="sxs-lookup"><span data-stu-id="3c155-123">EF6</span></span>  

<span data-ttu-id="3c155-124">我們知道支援空間類型之 EF6 的 Entity Framework 提供者為：</span><span class="sxs-lookup"><span data-stu-id="3c155-124">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="3c155-125">Microsoft SQL Server 提供者</span><span class="sxs-lookup"><span data-stu-id="3c155-125">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="3c155-126">此提供者隨附于 EF6 中。</span><span class="sxs-lookup"><span data-stu-id="3c155-126">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="3c155-127">此提供者取決於可能需要安裝的其他低層級程式庫，請參閱下面的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="3c155-127">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="3c155-128">適用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="3c155-128">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="3c155-129">這是 Devart 的協力廠商提供者。</span><span class="sxs-lookup"><span data-stu-id="3c155-129">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="3c155-130">如果您知道支援空間類型的 EF6 提供者，請取得連絡人，我們很樂意將其新增至此清單。</span><span class="sxs-lookup"><span data-stu-id="3c155-130">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="3c155-131">具有 Microsoft SQL Server 之空間類型的必要條件</span><span class="sxs-lookup"><span data-stu-id="3c155-131">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="3c155-132">SQL Server 空間支援取決於低層級、SQL Server 特定的類型 SqlGeography 和 SqlGeometry。</span><span class="sxs-lookup"><span data-stu-id="3c155-132">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="3c155-133">這些類型存在於 Microsoft. SqlServer 元件中，而且此元件不會隨附在 EF 中，或做為 .NET Framework 的一部分。</span><span class="sxs-lookup"><span data-stu-id="3c155-133">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="3c155-134">安裝 Visual Studio 時，通常也會安裝 SQL Server 的版本，而這會包含安裝的 Microsoft. SqlServer. d. d. d. d。</span><span class="sxs-lookup"><span data-stu-id="3c155-134">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="3c155-135">如果 SQL Server 未安裝在您想要使用空間類型的電腦上，或如果已從 SQL Server 安裝中排除空間類型，您就必須手動安裝它們。</span><span class="sxs-lookup"><span data-stu-id="3c155-135">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="3c155-136">您可以使用 `SQLSysClrTypes.msi` 來安裝類型，這是 Microsoft SQL Server Feature Pack 的一部分。</span><span class="sxs-lookup"><span data-stu-id="3c155-136">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="3c155-137">空間類型是 SQL Server 版本特定的，因此建議您在 Microsoft 下載中心[搜尋 "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) ，然後選取並下載與您將使用的 SQL Server 版本對應的選項。</span><span class="sxs-lookup"><span data-stu-id="3c155-137">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>
