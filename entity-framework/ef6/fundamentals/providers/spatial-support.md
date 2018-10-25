---
title: 空間類型-EF6 的提供者支援
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 9c00e82c663daec219fe649a8d889afcc81564f7
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022271"
---
# <a name="provider-support-for-spatial-types"></a>空間類型的提供者支援
Entity Framework 支援透過 DbGeography 或 DbGeometry 類別的空間資料使用。 這些類別會依賴資料庫特有的 Entity Framework 提供者所提供的功能。 並非所有提供者支援空間資料及執行可能會有額外的先決條件，例如空間類型組件的安裝。 以下提供有關提供者支援空間類型的詳細資訊。  

在兩個逐步解說，一個用於 Code First，另一個則用於 Database First 或 Model First，就可以找到有關如何使用應用程式中的空間類型的其他資訊：  

- [空間資料類型的程式碼在第一次](~/ef6/modeling/code-first/data-types/spatial.md)  
- [在 EF 設計工具中的空間資料類型](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a>支援空間類型的 EF 版本  

EF5 中引進了空間類型的支援。 不過，在 EF5 空間類型時，才支援應用程式為目標，並在.NET 4.5 上執行。  

從的 EF6 空間類型支援以.NET 4 和.NET 4.5 為目標的應用程式。  

## <a name="ef-providers-that-support-spatial-types"></a>EF 支援空間類型的提供者  

### <a name="ef5"></a>EF5  

我們已了解，支援空間類型的 EF5 Entity Framework 提供者：  

- Microsoft SQL Server 提供者  
    - 此提供者被隨附於 EF5。  
    - 此提供者取決於安裝可能需要一些其他低階程式庫 — 如需詳細資訊，請參閱下面的。  
- [Devart dotConnect for Oracle](http://www.devart.com/dotconnect/oracle/)  
    - 這是 Devart 的協力廠商提供者。  

如果您知道 EF5 提供者支援空間類型然後請聯繫，我們會很樂意與將它新增至這份清單。  

### <a name="ef6"></a>EF6  

我們已了解，支援空間類型的 EF6 Entity Framework 提供者：  

- Microsoft SQL Server 提供者  
    - 此提供者被隨附於 EF6。  
    - 此提供者取決於安裝可能需要一些其他低階程式庫 — 如需詳細資訊，請參閱下面的。  
- [Devart dotConnect for Oracle](http://www.devart.com/dotconnect/oracle/)  
    - 這是 Devart 的協力廠商提供者。  

如果您知道 EF6 提供者支援空間類型然後請聯繫，我們會很樂意與將它新增至這份清單。  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>使用 Microsoft SQL Server 空間類型的必要條件  

SQL Server 空間的支援取決於低層級，SQL Server 特定型別 SqlGeography 和 SqlGeometry。 這些型別存在於 Microsoft.SqlServer.Types.dll 組件，且這個組件不隨附 EF 的一部分，或做為.NET Framework 的一部分。  

安裝 Visual Studio 時通常也會安裝 SQL Server 的版本，且這會包含將 microsoft.sqlserver.types.dll 安裝。  

如果您要使用空間的類型，在電腦上未安裝 SQL Server，或從 SQL Server 安裝中排除空間型別，則您必須手動進行安裝。 類型可以使用安裝`SQLSysClrTypes.msi`，這是 Microsoft SQL Server Feature Pack 的一部分。 空間類型是 SQL Server 特定版本，因此我們建議[搜尋 「 SQL Server 功能套件 」](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack)在 Microsoft 下載中心取得，然後選取，然後下載對應的選項，您將使用的 SQL Server 的版本。
