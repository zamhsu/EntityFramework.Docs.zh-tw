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
# <a name="provider-support-for-spatial-types"></a>空間類型的提供者支援
Entity Framework 支援透過 DbGeography 或 DbGeometry 類別處理空間資料。 這些類別依賴 Entity Framework 提供者所提供的資料庫特定功能。 並非所有提供者都支援空間資料，而且可能有其他必要條件，例如安裝空間類型元件。 以下提供有關空間類型提供者支援的詳細資訊。  

有關如何在應用程式中使用空間類型的其他資訊，可以在兩個逐步解說中找到，一個用於 Code First，另一個用於 Database First 或 Model First：  

- [Code First 中的空間資料類型](xref:ef6/modeling/code-first/data-types/spatial)  
- [EF 設計工具中的空間資料類型](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a>支援空間類型的 EF 版本  

EF5 中引進了對空間類型的支援。 不過，只有當應用程式是以 .NET 4.5 為目標並執行時，才支援 EF5 空間類型。  

以 .NET 4 和 .NET 4.5 為目標的應用程式支援以 EF6 空間類型開始。  

## <a name="ef-providers-that-support-spatial-types"></a>支援空間類型的 EF 提供者  

### <a name="ef5"></a>EF5  

我們注意到支援空間類型的 EF5 Entity Framework 提供者為：  

- Microsoft SQL Server 提供者  
    - 此提供者隨附于 EF5 的一部分。  
    - 此提供者相依于可能需要安裝的一些較低層級程式庫，如需詳細資料，請參閱下文。  
- [適用于 Oracle 的 Devart dotConnect](https://www.devart.com/dotconnect/oracle/)  
    - 這是來自 Devart 的協力廠商提供者。  

如果您知道支援空間類型的 EF5 提供者，請聯繫，我們很樂意將其新增至此清單。  

### <a name="ef6"></a>EF6  

我們注意到支援空間類型的 EF6 Entity Framework 提供者為：  

- Microsoft SQL Server 提供者  
    - 此提供者隨附于 EF6 的一部分。  
    - 此提供者相依于可能需要安裝的一些較低層級程式庫，如需詳細資料，請參閱下文。  
- [適用于 Oracle 的 Devart dotConnect](https://www.devart.com/dotconnect/oracle/)  
    - 這是來自 Devart 的協力廠商提供者。  

如果您知道支援空間類型的 EF6 提供者，請聯繫，我們很樂意將其新增至此清單。  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>具有 Microsoft SQL Server 的空間類型必要條件  

SQL Server 空間支援取決於低層級的 SQL Server 特定類型 SqlGeography 和 SqlGeometry。 這些類型會存留在 Microsoft.SqlServer.Types.dll 元件中，而此元件不會隨附于 EF 或 .NET Framework 的一部分。  

安裝 Visual Studio 時，通常也會安裝 SQL Server 的版本，這將包含安裝 Microsoft.SqlServer.Types.dll。  

如果 SQL Server 未安裝在您要使用空間類型的電腦上，或空間類型已從 SQL Server 安裝中排除，則您必須手動安裝它們。 您可以使用來安裝類型 `SQLSysClrTypes.msi` ，這是 Microsoft SQL Server Feature Pack 的一部分。 空間類型 SQL Server 版本專屬，因此我們建議您在 Microsoft 下載中心 [搜尋「SQL Server Feature Pack](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) 」，然後選取並下載與您將使用之 SQL Server 版本對應的選項。
