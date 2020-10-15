---
title: Visual Studio 版本-EF6
description: Visual Studio 版本和 Entity Framework 6
author: ajcvickers
ms.date: 07/05/2018
uid: ef6/what-is-new/visual-studio
ms.openlocfilehash: ba6d8255f1cb5393b2f0e3e404f4fd2d1f1621c4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064233"
---
# <a name="visual-studio-releases"></a>Visual Studio 版本

建議您一律使用最新版的 Visual Studio，因為它包含適用于 .NET、NuGet 和 Entity Framework 的最新工具。
事實上，跨 Entity Framework 檔的各種範例和逐步解說都假設您使用的是最新版本的 Visual Studio。

不過，您可以使用舊版 Visual Studio 搭配不同版本的 Entity Framework，只要考慮一些差異即可：

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15.7 和更新版本

- 此版本的 Visual Studio 包含最新版本的 Entity Framework 工具和 EF 6.2 執行時間，而且不需要額外的設定步驟。
如需這些版本的詳細資訊，請參閱 [新功能](xref:ef6/what-is-new/index) 。
- 使用 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.2 NuGet 套件。
您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。
您應使用之連接字串的 [伺服器] 區段為 " (localdb) \\ MSSQLLocalDB"。
`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 至 Visual Studio 2017 15。6

- 這些版本的 Visual Studio 包括 Entity Framework 工具和執行時間6.1.3。
如需這些版本的詳細資訊，請參閱 [過去的版本](xref:ef6/what-is-new/past-releases#ef-613) 。
- 使用 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.1.3 NuGet 套件。
您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。
您應使用之連接字串的 [伺服器] 區段為 " (localdb) \\ MSSQLLocalDB"。
`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- 此版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。
建議您使用 Microsoft 下載中心提供 [的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762) ，升級到 Entity Framework Tools 6.1.3。
如需這些版本的詳細資訊，請參閱 [過去的版本](xref:ef6/what-is-new/past-releases#ef-613) 。
- 使用已升級的 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.1.3 NuGet 套件。
您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。
您應使用之連接字串的 [伺服器] 區段為 " (localdb) \\ MSSQLLocalDB"。
`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- 此版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。
建議您使用 Microsoft 下載中心提供 [的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762) ，升級到 Entity Framework Tools 6.1.3。
如需這些版本的詳細資訊，請參閱 [過去的版本](xref:ef6/what-is-new/past-releases#ef-613) 。
- 使用已升級的 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.1.3 NuGet 套件。
您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 v 11.0 的 LocalDB 實例。
您應使用之連接字串的 [伺服器] 區段是 " (localdb) \\ v 11.0"。
`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- 此版本的 Visual Studio 可用 Entity Framework Tools 版本與 Entity Framework 6 執行時間不相容，因此無法升級。
- 根據預設，Entity Framework 工具會將 Entity Framework 4.0 加入至您的專案。
為了使用任何較新版本的 EF 建立應用程式，您必須先安裝 [NuGet 封裝管理員擴充](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)功能。
- 根據預設，EF tools 版本中的所有程式碼產生都會以 EntityObject 和 Entity Framework 4 為基礎。
建議您藉由安裝適用于 [c #](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) 或 [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)的 DbCoNtext 程式碼產生範本，將程式碼產生切換為以 DbCoNtext 和 Entity Framework 5 為基礎。
- 安裝 NuGet 封裝管理員擴充功能之後，您可以手動安裝或升級至任何線上可用的 EF NuGet 套件，並使用 EF6 搭配 Code First，而不需要設計工具。
- 根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例 SQL Server Express 名為 SQLEXPRESS。
您應使用之連接字串的 [伺服器] 區段為 "。 \\SQLEXPRESS」。
`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。
