---
title: UWP 使用者入門 - 新資料庫 - EF Core
author: rowanmiller
ms.date: 08/08/2018
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: c243ef2a1940af9bf4f4b32f17acfcce7f972862
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996906"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a>在通用 Windows 平台 (UWP) 上使用 EF Core 搭配新資料庫的使用者入門

在此教學課程中，您將建置通用 Windows 平台 (UWP) 應用程式，該應用程式將使用 Entity Framework Core 對本機 SQLite 資料庫執行基本資料存取。

[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP)。

## <a name="prerequisites"></a>必要條件

* [Windows 10 Fall Creators Update (10.0; Build 16299) 或更新版本](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7 版或更新版本，搭配**通用 Windows 平台開發**工作負載。

* [.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/core)或更新版本。

## <a name="create-a-model-project"></a>建立模型專案

> [!IMPORTANT]
> 由於 .NET Core 工具與 UWP 專案互動方式的限制，模型需要放置在非 UWP 專案中，才能在套件管理員主控台 (PMC) 中執行移轉命令

* 開啟 Visual Studio

* [檔案] > [新增] > [專案]

* 從左側功能表選取 [已安裝] > [Visual C#] > [.NET Standard]。

* 選取 [類別庫 (.NET Standard)] 範本。

* 將專案命名為 *Blogging.Model*。

* 將方案命名為 *Blogging*。

* 按一下 [確定]。

## <a name="install-entity-framework-core"></a>安裝 Entity Framework Core

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 此教學課程使用 SQLite。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* **[工具] -> [NuGet 套件管理員] -> [套件管理員主控台]**。

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Sqlite`

您在本教學課程稍後將會使用一些 Entity Framework Core 工具來維護該資料庫。 因此，也請安裝工具套件。

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

## <a name="create-the-model"></a>建立模型

您現已可定義組成模型的內容與實體類別。

* 刪除 *Class1.cs*。

* 使用下列程式碼建立 *Model.cs*：

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a>建立新 UWP 專案

* 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選擇 [新增] > [新增專案]。

* 從左側功能表中，選取 [已安裝] > [Visual C#] > [Windows 通用]。

* 選取 [空白應用程式 (通用 Windows)] 專案範本。

* 將專案命名為 *Blogging.UWP*，然後按一下 [確定]

* 至少將目標與最低版本設定為 **Windows 10 Fall Creators Update (10.0; build 16299.0)**。

## <a name="create-the-initial-migration"></a>建立初始移轉

現在您已經有模型，請設定應用程式以在第一次執行時建立資料庫。 自此節中，您將會建立初始移轉。 在下一節中，您將會新增會在應用程式啟動時套用此移轉的程式碼。

移轉工具需要非 UWP 起始專案，因此請先建議該專案。

* 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選擇 [新增] > [新增專案]。

* 從左側功能表選取 [已安裝] > [Visual C#] > [.NET Core]。

* 選取 [主控台應用程式 (.NET Core)] 專案範本。

* 將專案命名為 *Blogging.Migrations.Startup*，然後按一下 [確定]。

* 新增從 *Blogging.Migrations.Startup* 專案到 *Blogging.Model* 專案的專案參考。

現在您可以建立初始移轉。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 選取 *Blogging.Model* 專案做為 [預設專案]。

* 在 [方案總管] 中，將 *Blogging.Migrations.Startup* 專案設定為啟始專案。

* 執行 `Add-Migration InitialCreate`。

  此命令會建立移轉的支架，以針對您的模型建立一組初始資料表。

## <a name="create-the-database-on-app-startup"></a>在應用程式啟動時建立資料庫

因為您要在執行應用程式的裝置上建立資料庫，請新增程式碼，在應用程式啟動時將任何擱置中的移轉套用至本機資料庫。 第一次執行應用程式時，這會負責建立本機資料庫。

* 新增從 *Blogging.UWP* 專案到 *Blogging.Model* 專案的專案參考。

* 開啟 *App.xaml.cs*。

* 加入醒目提示的程式碼，以套用任何擱置中的移轉。

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/App.xaml.cs?highlight=1-2,26-29)]

> [!TIP]  
> 若您變更模型，請使用 `Add-Migration` 命令來建立新移轉的支架，以將相對應的變更套用到資料庫。 應用程式啟動時，會將任何擱置中的移轉套用至每個裝置上的本機資料庫。
>
>EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。

## <a name="use-the-model"></a>使用模型

您現已可使用模型來執行資料存取。

* 開啟 *MainPage.xaml*。

* 加入下列醒目提示的頁面載入處理常式和 UI 內容

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml?highlight=9,11-23)]

現在加入程式碼，以連接 UI 與資料庫

* 開啟 *MainPage.xaml.cs*。

* 從下列清單中加入醒目提示的程式碼：

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml.cs?highlight=1,31-49)]

您現在可以執行應用程式來查看運作狀況。

* 在 [方案總管] 中，以滑鼠右鍵按一下 [Blogging.UWP] 專案，然後選取 [部署]。

* 將 *Blogging.UWP* 設定為起始專案。

* [偵錯] > [啟動但不偵錯]

  應用程式會建置並執行。

* 輸入 URL 並按一下 [加入] 按鈕

  ![影像](_static/create.png)

  ![影像](_static/list.png)

  大功告成！ 您現在有一個執行 Entity Framework Core 的簡單 UWP 應用程式了。

## <a name="next-steps"></a>後續步驟

針對搭配 UWP 使用 EF Core 時應該知道的相容性與效能資訊，請參閱 [EF Core 提供的 .NET 實作支援](../../platforms/index.md#universal-windows-platform)。

請參閱此文件中的其他文章，以深入了解 Entity Framework Core 的功能。
