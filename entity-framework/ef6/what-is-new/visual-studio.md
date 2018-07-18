---
title: Visual Studio 版本-EF6
author: divega
ms.date: 2018-07-05
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
caps.latest.revision: 3
ms.openlocfilehash: 7bd08a46b1d6acc5a565952e834f01546a5262c8
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120224"
---
# <a name="visual-studio-releases"></a>Visual Studio 版本

我們建議一律使用最新版本的 Visual Studio，因為它包含適用於.NET、 NuGet 和 Entity Framework 的最新的工具。
事實上，各種範例和逐步解說在 Entity Framework 文件假設您使用的 Visual Studio 的最新版本。

它為可能的不過若要使用較舊版本的 Visual Studio 與不同版本的 Entity Framework，只要您考慮到帳戶的一些差異：

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15.7年及更新版本

- 此版本的 Visual Studio 包含最新版的 Entity Framework 工具和 EF 6.2 執行階段，而且不需要其他設定步驟。
請參閱[What's New](~/ef6/what-is-new/index.md)如需詳細資訊，這些版本上。
- 加入新的專案使用的 EF 工具中的 Entity Framework 會自動加入 EF 6.2 NuGet 套件。
您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。
- 根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 LocalDB 執行個體呼叫 MSSQLLocalDB。
您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\MSSQLLocalDB"。
請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 到 Visual Studio 2017 15.6

- 這些版本的 Visual Studio 包括 Entity Framework 工具和執行階段 6.1.3。
請參閱[過去釋放](~/ef6/what-is-new/past-releases.md#ef-613)如需詳細資訊，這些版本上。
- 加入新的專案使用的 EF 工具中的 Entity Framework 會自動將新增 EF 6.1.3 NuGet 套件。
您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。
- 根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 LocalDB 執行個體呼叫 MSSQLLocalDB。
您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\MSSQLLocalDB"。
請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- 此版本的 Visual Studio 包含和舊版本的 Entity Framework 工具和執行階段。
建議您升級至 Entity Framework Tools 6.1.3，使用[安裝程式](https://www.microsoft.com/en-us/download/details.aspx?id=40762)提供 Microsoft 下載中心取得。
請參閱[過去釋放](~/ef6/what-is-new/past-releases.md#ef-613)如需詳細資訊，這些版本上。
- 加入新的專案使用的已升級的 EF 工具中的 Entity Framework 會自動將新增 EF 6.1.3 NuGet 套件。
您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。
- 根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 LocalDB 執行個體呼叫 MSSQLLocalDB。
您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\MSSQLLocalDB"。
請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- 此版本的 Visual Studio 包含和舊版本的 Entity Framework 工具和執行階段。
建議您升級至 Entity Framework Tools 6.1.3，使用[安裝程式](https://www.microsoft.com/en-us/download/details.aspx?id=40762)提供 Microsoft 下載中心取得。
請參閱[過去釋放](~/ef6/what-is-new/past-releases.md#ef-613)如需詳細資訊，這些版本上。
- 加入新的專案使用的已升級的 EF 工具中的 Entity Framework 會自動將新增 EF 6.1.3 NuGet 套件。
您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。
- 根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是呼叫 v11.0 的 LocalDB 執行個體。
您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\v11.0"。
請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- 使用此版本的 Visual Studio 可用的 Entity Framework Tools 的版本與 Entity Framework 6 執行階段不相容，而且無法升級。
- 根據預設，Entity Framework 工具會將 Entity Framework 4.0，加入您的專案。
若要建立應用程式使用 EF 的任何較新版本，就必須先安裝[NuGet 套件管理員延伸模組](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)。
- 根據預設，EF 工具的版本中的所有程式碼產生根據 EntityObject 和 Entity Framework 4。
建議您改用程式碼產生是依據 DbContext 和 Entity Framework 5，藉由安裝 DbContext 的程式碼產生範本，如[C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)或是[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)。
- 一旦您已安裝的 NuGet 套件管理員延伸模組，您可以手動安裝或線上升級至任何可用的 EF NuGet 套件並使用 EF6 Code First，不需要設計工具。
- 根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 SQL Server Express 名為 SQLEXPRESS。
您應該使用的連接字串的 [伺服器] 區段是"。\\SQLEXPRESS"。
請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。
