---
title: ASP.NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 25e5a683acf4bbed0b978cc6a80f1b50a0b64ca1
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319175"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a>在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門

在本教學課程中，您將會建置 ASP.NET Core MVC 應用程式，而其使用 Entity Framework Core 執行基本的資料存取。 本教學課程會使用移轉以從資料模型建立資料庫。

若要遵循本教學課程，您可以在 Windows 上使用 Visual Studio 2017 或在 Windows、macOS 或 Linux 上使用 .NET Core CLI。

在 GitHub 上檢視此文章的範例：
* [搭配使用 Visual Studio 2017 與 SQL Server](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* [搭配使用 .NET Core CLI 與 SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite)。

## <a name="prerequisites"></a>必要條件

安裝下列軟體：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2017 15.7 版或更新版本](https://www.visualstudio.com/downloads/)以及下列工作負載：
  * **ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)
  * **.NET Core 跨平台開發** (位在 [其他工具組] 下)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。

---

## <a name="create-a-new-project"></a>建立新專案

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 開啟 Visual Studio 2017
* [檔案] > [新增] > [專案]
* 從左側功能表選取 [已安裝] > [Visual C#] > [.NET Core]。
* 選取 [ASP.NET Core Web 應用程式]。
* 輸入 **EFGetStarted.AspNetCore.NewDb** 作為名稱，然後按一下 [確定]。
* 在 [新增 ASP.NET Core Web 應用程式] 對話方塊中：
  * 確認下拉式清單中已選取 **NET Core** 和 **ASP.NET Core 2.1** 選項
  * 選取 [Web 應用程式 (模型-檢視-控制器)] 專案範本
  * 確認 [驗證] 已設為 [無任何驗證]
  * 按一下 [確定] 

警告：如果您針對 [驗證] 使用 [個別使用者帳戶] 而不是 [無]，則系統會將 Entity Framework Core 模型新增至 `Models\IdentityModel.cs` 中的專案。 您可利用在本教學課程中所學到的技術，選擇新增第二個模型，或是擴充此現有模型以包含您的實體類別。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 請執行下列命令來建立 MVC 專案：

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* 變更為專案目錄。 針對新專案執行您輸入的後續命令。

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a>安裝 Entity Framework Core

若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在本教學課程中，因為使用 SQL Server，所以無需安裝提供者套件。 [Microsoft.AspnetCore.App 中繼套件](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)包含 SQL Server 提供者套件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

本教學課程會使用 SQLite，因為它可以在 .NET Core 支援的所有平台上執行。

* 執行下列命令以安裝 SQLite 提供者：

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a>建立模型

定義組成模型的內容類別與實體類別。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 以滑鼠右鍵按一下 [Models] 資料夾，然後選取 [新增] > [類別]。
* 輸入 **Model.cs** 作為名稱，然後按一下 [確定]。
* 以下列程式碼取代檔案的內容：

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 在 **Models** 資料夾中，用下列程式碼建立 **Model.cs**：

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

生產環境應用程式通常會將每個類別放在不同的檔案中。 為了簡單起見，本教學課程會將這些類別置於同一個檔案中。

## <a name="register-the-context-with-dependency-injection"></a>使用相依性插入來註冊內容

若要為 MVC 控制器提供 `BloggingContext`，請在 `Startup.cs` 中將其註冊為服務。

服務 (例如 `BloggingContext`) 均會在應用程式啟動期間以[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)註冊，使其能自動獲提供透過建構函式參數與屬性取用服務 (例如 MVC 控制器) 的元件。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **Startup.cs** 中，新增下列 `using` 陳述式：

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* 將下列反白顯示的程式碼新增至 `ConfigureServices` 方法：

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 在 **Startup.cs** 中，新增下列 `using` 陳述式：

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* 將下列反白顯示的程式碼新增至 `ConfigureServices` 方法：

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

生產環境應用程式一般會將連接字串放在設定檔或環境變數中。 為了簡單起見，本教學課程將其定義在程式碼中。 如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。

## <a name="create-the-database"></a>建立資料庫

下列步驟會使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]
* 執行下列命令：

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  如果您收到 `The term 'add-migration' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。

  `Add-Migration` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。 `Update-Database` 命令會建立資料庫，並對資料庫套用新的移轉。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 執行下列命令：

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  `migrations` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。 `database update` 命令會建立資料庫，並對資料庫套用新的移轉。

---

## <a name="create-a-controller"></a>建立控制器

為 `Blog` 實體設定控制器與檢視的結構。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]。
* 選取 [使用 Entity Framework 執行檢視的 MVC 控制器]，然後按一下 [新增]。
* 將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]。
* 按一下 [加入] 。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 執行下列命令：

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  `tool install` 和 `add package` 命令會安裝工具，該工具可以建立控制器和檢視的 Scaffolding。 `restore` 命令可確保下載所有專案的套件，而 `aspnet-codegenerator` 命令會執行 Scaffolding。
---

Scaffolding 引擎會建立下列檔案：

* 控制器 (*Controllers/BlogsController.cs*)
* [建立]、[刪除]、[詳細資料]、[編輯] 和 [索引] 頁面的 Razor 檢視 (_Views/Blogs/*.cshtml_)

## <a name="run-the-application"></a>執行應用程式

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **偵錯** > **啟動但不偵錯**

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet run
```
---

* 巡覽至 `/Blogs`

* 使用 [建立新項目] 連結來建立一些部落格項目。

  ![建立頁面](_static/create.png)

* 測試 [詳細資料]、[編輯] 和 [刪除] 連結。

  ![索引頁面](_static/index-new-db.png)

## <a name="additional-resources"></a>其他資源

* [教學課程：在 ASP.NET Core 上使用 EF Core 搭配新資料庫的使用者入門](xref:core/get-started/netcore/new-db-sqlite)
* [教學課程：開始使用 ASP.NET Core 中的 Razor Pages](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [教學課程：ASP.NET Core 中的 Razor Pages 與 Entity Framework Core](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
