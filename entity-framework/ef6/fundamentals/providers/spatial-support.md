---
title: 空間類型-EF6 的提供者支援
author: divega
ms.date: 2016-10-23
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 07eeecb5f5e3e3eab8548c4c7c0ed55c5ffb4f31
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998283"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="657b8-102">空間類型的提供者支援</span><span class="sxs-lookup"><span data-stu-id="657b8-102">Provider Support for Spatial Types</span></span>
<span data-ttu-id="657b8-103">Entity Framework 支援透過 DbGeography 或 DbGeometry 類別的空間資料使用。</span><span class="sxs-lookup"><span data-stu-id="657b8-103">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="657b8-104">這些類別會依賴資料庫特有的 Entity Framework 提供者所提供的功能。</span><span class="sxs-lookup"><span data-stu-id="657b8-104">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="657b8-105">並非所有提供者支援空間資料及執行可能會有額外的先決條件，例如空間類型組件的安裝。</span><span class="sxs-lookup"><span data-stu-id="657b8-105">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="657b8-106">以下提供有關提供者支援空間類型的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="657b8-106">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="657b8-107">在兩個逐步解說，一個用於 Code First，另一個則用於 Database First 或 Model First，就可以找到有關如何使用應用程式中的空間類型的其他資訊：</span><span class="sxs-lookup"><span data-stu-id="657b8-107">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="657b8-108">空間資料類型的程式碼在第一次</span><span class="sxs-lookup"><span data-stu-id="657b8-108">Spatial Data Types in Code First</span></span>](~/ef6/modeling/code-first/data-types/spatial.md)  
- [<span data-ttu-id="657b8-109">在 EF 設計工具中的空間資料類型</span><span class="sxs-lookup"><span data-stu-id="657b8-109">Spatial Data Types in EF Designer</span></span>](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="657b8-110">支援空間類型的 EF 版本</span><span class="sxs-lookup"><span data-stu-id="657b8-110">EF releases that support spatial types</span></span>  

<span data-ttu-id="657b8-111">EF5 中引進了空間類型的支援。</span><span class="sxs-lookup"><span data-stu-id="657b8-111">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="657b8-112">不過，在 EF5 空間類型時，才支援應用程式為目標，並在.NET 4.5 上執行。</span><span class="sxs-lookup"><span data-stu-id="657b8-112">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="657b8-113">從的 EF6 空間類型支援以.NET 4 和.NET 4.5 為目標的應用程式。</span><span class="sxs-lookup"><span data-stu-id="657b8-113">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="657b8-114">EF 支援空間類型的提供者</span><span class="sxs-lookup"><span data-stu-id="657b8-114">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="657b8-115">EF5</span><span class="sxs-lookup"><span data-stu-id="657b8-115">EF5</span></span>  

<span data-ttu-id="657b8-116">我們已了解，支援空間類型的 EF5 Entity Framework 提供者：</span><span class="sxs-lookup"><span data-stu-id="657b8-116">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="657b8-117">Microsoft SQL Server 提供者</span><span class="sxs-lookup"><span data-stu-id="657b8-117">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="657b8-118">此提供者被隨附於 EF5。</span><span class="sxs-lookup"><span data-stu-id="657b8-118">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="657b8-119">此提供者取決於安裝可能需要一些其他低階程式庫 — 如需詳細資訊，請參閱下面的。</span><span class="sxs-lookup"><span data-stu-id="657b8-119">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="657b8-120">Devart dotConnect for Oracle</span><span class="sxs-lookup"><span data-stu-id="657b8-120">Devart dotConnect for Oracle</span></span>](http://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="657b8-121">這是 Devart 的協力廠商提供者。</span><span class="sxs-lookup"><span data-stu-id="657b8-121">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="657b8-122">如果您知道 EF5 提供者支援空間類型然後請聯繫，我們會很樂意與將它新增至這份清單。</span><span class="sxs-lookup"><span data-stu-id="657b8-122">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="657b8-123">EF6</span><span class="sxs-lookup"><span data-stu-id="657b8-123">EF6</span></span>  

<span data-ttu-id="657b8-124">我們已了解，支援空間類型的 EF6 Entity Framework 提供者：</span><span class="sxs-lookup"><span data-stu-id="657b8-124">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="657b8-125">Microsoft SQL Server 提供者</span><span class="sxs-lookup"><span data-stu-id="657b8-125">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="657b8-126">此提供者被隨附於 EF6。</span><span class="sxs-lookup"><span data-stu-id="657b8-126">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="657b8-127">此提供者取決於安裝可能需要一些其他低階程式庫 — 如需詳細資訊，請參閱下面的。</span><span class="sxs-lookup"><span data-stu-id="657b8-127">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="657b8-128">Devart dotConnect for Oracle</span><span class="sxs-lookup"><span data-stu-id="657b8-128">Devart dotConnect for Oracle</span></span>](http://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="657b8-129">這是 Devart 的協力廠商提供者。</span><span class="sxs-lookup"><span data-stu-id="657b8-129">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="657b8-130">如果您知道 EF6 提供者支援空間類型然後請聯繫，我們會很樂意與將它新增至這份清單。</span><span class="sxs-lookup"><span data-stu-id="657b8-130">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="657b8-131">使用 Microsoft SQL Server 空間類型的必要條件</span><span class="sxs-lookup"><span data-stu-id="657b8-131">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="657b8-132">SQL Server 空間的支援取決於低層級，SQL Server 特定型別 SqlGeography 和 SqlGeometry。</span><span class="sxs-lookup"><span data-stu-id="657b8-132">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="657b8-133">這些型別存在於 Microsoft.SqlServer.Types.dll 組件，且這個組件不隨附 EF 的一部分，或做為.NET Framework 的一部分。</span><span class="sxs-lookup"><span data-stu-id="657b8-133">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="657b8-134">安裝 Visual Studio 時通常也會安裝 SQL Server 的版本，且這會包含將 microsoft.sqlserver.types.dll 安裝。</span><span class="sxs-lookup"><span data-stu-id="657b8-134">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="657b8-135">如果您要使用空間的類型，在電腦上未安裝 SQL Server，或從 SQL Server 安裝中排除空間型別，則您必須手動進行安裝。</span><span class="sxs-lookup"><span data-stu-id="657b8-135">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="657b8-136">類型可以使用安裝`SQLSysClrTypes.msi`，這是 Microsoft SQL Server Feature Pack 的一部分。</span><span class="sxs-lookup"><span data-stu-id="657b8-136">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="657b8-137">空間類型是 SQL Server 特定版本，因此我們建議[搜尋 「 SQL Server 功能套件 」](https://www.microsoft.com/en-us/search/result.aspx?q=sql+server+feature+pack)在 Microsoft 下載中心取得，然後選取，然後下載對應的選項，您將使用的 SQL Server 的版本。</span><span class="sxs-lookup"><span data-stu-id="657b8-137">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/en-us/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>
