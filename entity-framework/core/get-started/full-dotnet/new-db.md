---
title: .NET Framework 快速入門 - 新資料庫 - EF Core
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: 66d9011a5978fc3d8253a963bdb9df27848e9ff9
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997583"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a>在 .NET Framework 上使用 EF Core 搭配新資料庫的使用者入門

在本教學課程中，您將會建置主控台應用程式，而其使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本的資料存取。 您會使用移轉從模型建立資料庫。

[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb)。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2017 15.7 版 (或更新版本)](https://www.visualstudio.com/downloads/)

## <a name="create-a-new-project"></a>建立新專案

* 開啟 Visual Studio 2017

* [檔案] > [新增] > [專案...]

* 從左側功能表中，選取 **[已安裝] > [Visual C#] > [Windows Desktop]**

* 選取 [主控台應用程式 (.NET Framework)] 專案範本

* 請確定專案的目標為 **.NET Framework 4.6.1** 或更新版本

* 將專案命名為 *ConsoleApp.NewDb*，然後按一下 [確定]

## <a name="install-entity-framework"></a>安裝 Entity Framework

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本教學課程使用 SQL Server。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`

您在本教學課程稍後將會使用一些 Entity Framework 工具，來維護該資料庫。 因此，也請安裝工具套件。

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

## <a name="create-the-model"></a>建立模型

您現已可定義組成模型的內容與實體類別。

* [專案] > [新增類別...]

* 輸入 *Model.cs* 作為名稱，然後按一下 [確定]

* 以下列程式碼來取代檔案的內容

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] 

> [!TIP]  
> 在實際的應用程式中，要將每個類別置於不同的檔案中，然後將連接字串置於組態檔或環境變數中。 為了簡單起見，在本教學課程中會將所有項目都置於單一個程式碼檔案中。

## <a name="create-the-database"></a>建立資料庫

一旦有了模型之後，即可使用移轉來建立資料庫。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Add-Migration InitialCreate` 可建立移轉的結構，為模型建立一組初始資料表。

* 執行 `Update-Database` 以將新的移轉套用至資料庫。 因為您的資料庫尚未存在，因此會在套用移轉之前為您建立。

> [!TIP]  
> 若您變更了您的模型，則可使用 `Add-Migration` 命令來建立新移轉的結構，以對資料庫做出相對應的結構描述變更。 檢查完已建立結構的程式碼之後 (並進行任何必要的變更)，就可以使用 `Update-Database` 命令將變更套用至資料庫。
>
> EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。

## <a name="use-the-model"></a>使用模型

您現已可使用模型來執行資料存取。

* 開啟 *Program.cs*

* 以下列程式碼來取代檔案的內容

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)]

* [偵錯] > [啟動但不偵錯]

  您會看到有一個部落格儲存至資料庫，然後所有部落格的詳細資料皆會列印至主控台。

  ![影像](_static/output-new-db.png)

## <a name="additional-resources"></a>其他資源

* [.NET Framework 上的 EF Core (附現有資料庫)](xref:core/get-started/full-dotnet/existing-db)
* [.NET Framework 上的 EF Core (附新資料庫) - SQLite](xref:core/get-started/netcore/new-db-sqlite) - 跨平台主控台 EF 教學課程。
