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
# <a name="provider-support-for-spatial-types"></a>空間類型的提供者支援
Entity Framework 支援透過 DbGeography 或 DbGeometry 類別來使用空間資料。 這些類別會依賴 Entity Framework 提供者所提供的資料庫特定功能。 並非所有提供者都支援空間資料，而且可能會有額外的必要條件（例如安裝空間類型元件）。 以下提供有關空間類型提供者支援的詳細資訊。  

有關如何在應用程式中使用空間類型的其他資訊，可以在兩個逐步解說中找到，一個用於 Code First，另一個用於 Database First 或 Model First：  

- [Code First 中的空間資料類型](~/ef6/modeling/code-first/data-types/spatial.md)  
- [EF 設計工具中的空間資料類型](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a>支援空間類型的 EF 版本  

空間類型的支援已在 EF5 中引進。 不過，在 EF5 空間類型中，只有在應用程式以 .NET 4.5 為目標並執行時才支援。  

針對以 .NET 4 和 .NET 4.5 為目標的應用程式，支援從 EF6 空間類型開始。  

## <a name="ef-providers-that-support-spatial-types"></a>支援空間類型的 EF 提供者  

### <a name="ef5"></a>EF5  

我們知道支援空間類型之 EF5 的 Entity Framework 提供者為：  

- Microsoft SQL Server 提供者  
    - 此提供者隨附于 EF5 中。  
    - 此提供者取決於可能需要安裝的其他低層級程式庫，請參閱下面的詳細資料。  
- [適用于 Oracle 的 Devart dotConnect](https://www.devart.com/dotconnect/oracle/)  
    - 這是 Devart 的協力廠商提供者。  

如果您知道支援空間類型的 EF5 提供者，請取得連絡人，我們很樂意將其新增至此清單。  

### <a name="ef6"></a>EF6  

我們知道支援空間類型之 EF6 的 Entity Framework 提供者為：  

- Microsoft SQL Server 提供者  
    - 此提供者隨附于 EF6 中。  
    - 此提供者取決於可能需要安裝的其他低層級程式庫，請參閱下面的詳細資料。  
- [適用于 Oracle 的 Devart dotConnect](https://www.devart.com/dotconnect/oracle/)  
    - 這是 Devart 的協力廠商提供者。  

如果您知道支援空間類型的 EF6 提供者，請取得連絡人，我們很樂意將其新增至此清單。  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>具有 Microsoft SQL Server 之空間類型的必要條件  

SQL Server 空間支援取決於低層級、SQL Server 特定的類型 SqlGeography 和 SqlGeometry。 這些類型存在於 Microsoft. SqlServer 元件中，而且此元件不會隨附在 EF 中，或做為 .NET Framework 的一部分。  

安裝 Visual Studio 時，通常也會安裝 SQL Server 的版本，而這會包含安裝的 Microsoft. SqlServer. d. d. d. d。  

如果 SQL Server 未安裝在您想要使用空間類型的電腦上，或如果已從 SQL Server 安裝中排除空間類型，您就必須手動安裝它們。 您可以使用 `SQLSysClrTypes.msi` 來安裝類型，這是 Microsoft SQL Server Feature Pack 的一部分。 空間類型是 SQL Server 版本特定的，因此建議您在 Microsoft 下載中心[搜尋 "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) ，然後選取並下載與您將使用的 SQL Server 版本對應的選項。
