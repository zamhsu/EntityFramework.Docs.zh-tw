---
title: UWP 使用者入門 - 新資料庫 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.topic: get-started-article
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
ms.technology: entity-framework-core
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: f743ff5392d1f30283a13d2e7fb8029be88387aa
ms.sourcegitcommit: 96324e58c02b97277395ed43173bf13ac80d2012
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
ms.locfileid: "26049831"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a>在通用 Windows 平台 (UWP) 上使用 EF Core 搭配新資料庫的使用者入門

> [!NOTE]
> 本教學課程使用 EF Core 2.0.1 (連同 ASP.NET Core 和 .NET Core SDK 2.0.3 一起發行)。 EF Core 2.0.0 缺少良好 UWP 體驗所需的某些重要錯誤 (bug) 修正。

在本逐步解說中，您將建置通用 Windows 平台 (UWP) 應用程式，該應用程式將使用 Entity Framework 對本機 SQLite 資料庫執行基本資料存取。

> [!IMPORTANT]
> **請考慮避免在 UWP 上的 LINQ 查詢中使用匿名型別**。 若要將 UWP 應用程式部署至應用程式市集，您的應用程式需要使用 .NET Native 來編譯。 使用匿名型別的查詢在 .NET Native 上會有較差的效能。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP/UWP.SQLite) \(英文\)。

## <a name="prerequisites"></a>必要條件

若要完成此逐步解說，必須要有下列項目：

* [Windows 10 Fall Creators Update](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10) (10.0.16299.0)

* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更新版本。

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.4 版或更新版本，包含**通用 Windows 平台開發**工作負載。

## <a name="create-a-new-model-project"></a>建立新模型專案

> [!WARNING]
> 由於 .NET Core 工具與 UWP 專案互動方式的限制，模型需要放置在非 UWP 專案中，才能在套件管理員主控台中執行移轉命令

* 開啟 Visual Studio

* [檔案] > [新增] > [專案]...

* 從左側功能表選取 [範本] > [Visual C#]

* 選取 [類別庫 (.NET Standard)] 專案範本

* 提供專案名稱，然後按一下 [確定]

## <a name="install-entity-framework"></a>安裝 Entity Framework

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本逐步解說使用 SQLite。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Sqlite`

稍後在本逐步解說中，我們將使用部分 Entity Framework 工具來維護資料庫。 因此，我們也將安裝工具套件。

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

* 編輯 .csproj 檔案，並以 `<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>` 取代 `<TargetFramework>netstandard2.0</TargetFramework>`

## <a name="create-your-model"></a>建立您的模型

現在就來定義組成模型的內容和實體類別。

* [專案] > [加入類別]...

* 輸入 *Model.cs* 作為名稱，然後按一下 [確定]

* 以下列程式碼來取代檔案的內容

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a>建立新 UWP 專案

* 開啟 Visual Studio

* [檔案] > [新增] > [專案]...

* 從左側功能表選取 [範本] > [Visual C#] > [Windows 通用]

* 選取 [空白應用程式 (通用 Windows)] 專案範本

* 提供專案名稱，然後按一下 [確定]

* 將目標和最小版本至少設定為 `Windows 10 Fall Creators Update (10.0; build 16299.0)`

## <a name="create-your-database"></a>建立您的資料庫

一旦有模型之後，就可以使用移轉來建立資料庫。

* [工具] –> [NuGet 套件管理員] –> [套件管理員主控台]

* 選取模型專案作為預設專案，並將它設定為啟始專案

* 執行 `Add-Migration MyFirstMigration` 來建立移轉的結構，以針對您的模型建立一組初始的資料表。

因為我們要在執行應用程式的裝置上建立資料庫，所以我們將加入一些程式碼，在應用程式啟動時將任何擱置中的移轉套用至本機資料庫。 第一次執行應用程式時，這會負責為我們建立本機資料庫。

* 在 [方案總管] 中，以滑鼠右鍵按一下 [App.xaml]，然後選取 [檢視程式碼]

* 將醒目提示的 using 加入檔案的開頭

* 加入醒目提示的程式碼，以套用擱置中的移轉

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/App.xaml.cs?highlight=1,25-28)]

> [!TIP]  
> 如果日後要對您的模型進行變更，則可以使用 `Add-Migration` 命令來建立新移轉的結構，以將相對應的變更套用至資料庫。 應用程式啟動時，會將任何擱置中的移轉套用至每個裝置上的本機資料庫。
>
>EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。

## <a name="use-your-model"></a>使用您的模型

您現在可以使用您的模型來執行資料存取。

* 開啟 *MainPage.xaml*

* 加入下列醒目提示的頁面載入處理常式和 UI 內容

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml?highlight=9,11-23)]

現在我們將加入程式碼，以連接 UI 與資料庫

* 在 [方案總管] 中，以滑鼠右鍵按一下 [MainPage.xaml]，然後選取 [檢視程式碼]

* 從下列清單中加入醒目提示的程式碼

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml.cs?highlight=30-48)]

您現在可以執行應用程式來查看運作狀況。

* 偵錯 > 啟動但不偵錯

* 系統將會建置並啟動應用程式

* 輸入 URL 並按一下 [加入] 按鈕

![image](_static/create.png)

![image](_static/list.png)

## <a name="next-steps"></a>後續步驟

> [!TIP]
> 在您的實體類型中實作 [`INotifyPropertyChanged`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx)、[`INotifyPropertyChanging`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanging.aspx)、[`INotifyCollectionChanged`](https://msdn.microsoft.com/en-us/library/system.collections.specialized.inotifycollectionchanged.aspx) 和使用 `ChangeTrackingStrategy.ChangingAndChangedNotifications`，可以改善 `SaveChanges()` 的效能。

大功告成！ 您現在有一個執行 Entity Framework 的簡單 UWP 應用程式了。

請參閱此文件中的其他文章，以深入了解 Entity Framework 的功能。
