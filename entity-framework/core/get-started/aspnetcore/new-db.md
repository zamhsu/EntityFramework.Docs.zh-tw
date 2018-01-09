---
title: "ASP.NET Core 使用者入門 - 新資料庫 - EF Core"
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 7e7ecaff29e9830bf3bcf742e6a5d54e1ced24de
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a>在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門

在本逐步解說中，您將建置 ASP.NET Core MVC 應用程式，該應用程式將使用 Entity Framework Core 執行基本的資料存取。 您將使用移轉，以便從您的 EF Core 模型來建立資料庫。 如需 Entity Framework Core 的教學課程，請參閱[其他資源](#additional-resources)。

本教學課程需要：
* [Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) 與這些工作負載：
  * **ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)
  * **.NET Core 跨平台開發** (位在 [其他工具組] 下)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb) \(英文\)。

## <a name="create-a-new-project-in-visual-studio-2017"></a>在 Visual Studio 2017 中建立新專案

* [檔案] > [新增] > [專案]
* 從左側功能表選取 [已安裝] > [範本] > [Visual C#] > [.NET Core]。
* 選取 [ASP.NET Core Web 應用程式]。
* 輸入 **EFGetStarted.AspNetCore.NewDb** 作為名稱，然後按一下 [確定]。
* 在 [新增 ASP.NET Core Web 應用程式] 對話方塊中：
  * 確認下拉式清單中已選取 [.NET Core] 和 [ASP.NET Core 2.0] 選項
  * 選取 [Web 應用程式 (模型-檢視-控制器)] 專案範本
  * 確認 [驗證] 已設為 [無驗證]
  * 按一下 [確定] 

警告：如果您針對 [驗證] 使用 [個別使用者帳戶] 而不是 [無]，則會將 Entity Framework Core 模型新增至您專案中的 `Models\IdentityModel.cs`。 您可以利用在本逐步解說中學到的技術，選擇新增第二個模型，或是擴充此現有模型以包含您的實體類別。

## <a name="install-entity-framework-core"></a>安裝 Entity Framework Core

針對您要作為目標的 EF Core 資料庫提供者來安裝套件。 本逐步解說會使用 SQL Server。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`

我們將使用一些 Entity Framework Core 工具，以便從您的 EF Core 模型來建立資料庫。 因此，我們也將安裝工具套件：

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

我們將使用一些 ASP.NET Core Scaffolding 工具，於稍後建立控制器與檢視。 因此，我們也將安裝此設計套件：

* 執行 `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`

## <a name="create-the-model"></a>建立模型

定義組成模型的內容和實體類別：

* 以滑鼠右鍵按一下 [Models] 資料夾，然後選取 [新增] > [類別]。
* 輸入 **Model.cs** 作為名稱，然後按一下 [確定]。
* 以下列程式碼取代檔案的內容：

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

注意：在實際的應用程式中，您通常會將模型中的每個類別放在不同的檔案中。 為了簡單起見，我們在此教學課程中將所有類別放在同一個檔案中。

## <a name="register-your-context-with-dependency-injection"></a>使用相依性插入來註冊內容

服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)來註冊。 接著，會透過建構函式參數或屬性，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。

為了讓 MVC 控制器能使用 `BloggingContext`，我們要將它註冊為服務。

* 開啟 **Startup.cs**
* 加入下列 `using` 陳述式：

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

加入 `AddDbContext` 方法以將它註冊為服務：

* 將下列程式碼加入 `ConfigureServices` 方法：

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

注意：實際的應用程式通常會將連接字串放入到設定檔中。 為了簡單起見，我們在程式碼中加以定義。 如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。

## <a name="create-your-database"></a>建立您的資料庫

一旦有模型之後，就可以使用[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)來建立資料庫。

* 開啟 PMC：

  [工具] –> [NuGet 套件管理員] –> [套件管理員主控台]
* 執行 `Add-Migration InitialCreate` 來建立移轉的結構，以針對您的模型建立一組初始的資料表。 如果您收到 `The term 'add-migration' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。
* 執行 `Update-Database` 以將新的移轉套用至資料庫。 此命令會建立資料庫，然後才套用移轉。

## <a name="create-a-controller"></a>建立控制器

在專案中啟用 Scaffolding：

* 在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。
* 選取 [基本相依性]，然後按一下 [新增]。
* 您可以忽略或刪除 *ScaffoldingReadMe.txt* 檔案。

啟用 Scaffolding 之後，就可以建立 `Blog` 實體的控制器結構。

* 在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。
* 選取 [使用 Entity Framework 執行檢視的 MVC 控制器] 並按一下 [確定]。
* 將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]。
* 按一下 [加入] 。


## <a name="run-the-application"></a>執行應用程式

按 F5 來執行和測試應用程式。

* 巡覽至 `/Blogs`
* 使用建立連結來建立一些部落格項目。 測試詳細資料和刪除連結。

![image](_static/create.png)

![image](_static/index-new-db.png)

## <a name="additional-resources"></a>其他資源

* [EF - 使用 SQLite 的新資料庫](xref:core/get-started/netcore/new-db-sqlite) - 跨平台的主控台 EF 教學課程。
* [Mac 或 Linux 上的 ASP.NET Core MVC 簡介](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [使用 Visual Studio 的 ASP.NET Core MVC 簡介](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [使用 Visual Studio 的 ASP.NET Core 與 Entity Framework Core 的使用者入門](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
