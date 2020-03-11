---
title: Visual Studio 版本-EF6
author: divega
ms.date: 07/05/2018
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
ms.openlocfilehash: 16bcdc6d0e7c5632d4f4c06ba285a7a666f24204
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416937"
---
# <a name="visual-studio-releases"></a>Visual Studio 版本

我們建議您一律使用最新版本的 Visual Studio，因為它包含適用于 .NET、NuGet 和 Entity Framework 的最新工具。
事實上，跨 Entity Framework 檔的各種範例和逐步解說會假設您使用的是最新版本的 Visual Studio。

不過，您可以使用具有不同版本 Entity Framework 的舊版 Visual Studio，只要考慮一些差異即可：

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15.7 和更新版本

- 這個版本的 Visual Studio 包含最新版本的 Entity Framework 工具和 EF 6.2 執行時間，而且不需要額外的設定步驟。
如需這些版本的詳細資訊，請參閱[新功能](~/ef6/what-is-new/index.md)。
- 使用 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 6.2 NuGet 套件。
您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。
[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\MSSQLLocalDB]。
在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 至 Visual Studio 2017 15。6

- 這些版本的 Visual Studio 包括 Entity Framework 工具和執行時間之 ef6.1.3。
如需這些版本的詳細資訊，請參閱[過去的版本](~/ef6/what-is-new/past-releases.md#ef-613)。
- 使用 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 之 ef6.1.3 NuGet 套件。
您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。
[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\MSSQLLocalDB]。
在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- 這個版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。
建議您使用 Microsoft 下載中心提供[的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762)，升級至 Entity Framework Tools 之 ef6.1.3。
如需這些版本的詳細資訊，請參閱[過去的版本](~/ef6/what-is-new/past-releases.md#ef-613)。
- 使用已升級的 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 之 ef6.1.3 NuGet 套件。
您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。
[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\MSSQLLocalDB]。
在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- 這個版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。
建議您使用 Microsoft 下載中心提供[的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762)，升級至 Entity Framework Tools 之 ef6.1.3。
如需這些版本的詳細資訊，請參閱[過去的版本](~/ef6/what-is-new/past-releases.md#ef-613)。
- 使用已升級的 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 之 ef6.1.3 NuGet 套件。
您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。
- 根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為11.0 的 LocalDB 實例。
[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\11.0]。
在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- 這個版本的 Visual Studio 提供的 Entity Framework Tools 版本與 Entity Framework 6 執行時間不相容，因此無法升級。
- 根據預設，Entity Framework 工具會將 Entity Framework 4.0 新增至您的專案。
若要使用任何較新版本的 EF 來建立應用程式，您必須先安裝[NuGet 套件管理員延伸](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)模組。
- 根據預設，EF tools 版本中的所有程式碼產生都是以 EntityObject 和 Entity Framework 4 為基礎。
我們建議您安裝[C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)或[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)的 DbCoNtext 程式碼產生範本，以根據 DbCoNtext 和 Entity Framework 5 來切換程式碼產生。
- 安裝 NuGet 套件管理員延伸模組之後，您可以手動安裝或升級至任何線上可用的 EF NuGet 套件，並使用 EF6 搭配 Code First，這不需要設計工具。
- 根據預設，此版本 Visual Studio 提供的 SQL Server 實例會 SQL Server Express 名為 SQLEXPRESS。
[連接字串] 的 [伺服器] 區段應使用 []。\\SQLEXPRESS」。
在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。
