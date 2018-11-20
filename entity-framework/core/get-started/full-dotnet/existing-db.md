---
title: .NET Framework 快速入門 - 現有的資料庫 - EF Core
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: 1b90c491a3b2025da750a3266ff45d9d92bb1d0d
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688611"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a>在 .NET Framework 上使用 EF Core 搭配現有資料庫的使用者入門

在本教學課程中，您將會建置主控台應用程式，而其使用 Entity Framework 對 Microsoft SQL Server 資料庫執行基本的資料存取。 您會對現有的資料庫進行還原工程，以建立 Entity Framework 模型。

[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb)。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2017 15.7 版 (或更新版本)](https://www.visualstudio.com/downloads/)

## <a name="create-blogging-database"></a>建立部落格資料庫

本教學課程使用 LocalDb 執行個體上的**部落格**資料庫，作為現有的資料庫。 如果您在另一個教學課程中已建立了**部落格**資料庫，則可跳過這些步驟。

* 開啟 Visual Studio

* [工具] > [連線到資料庫...]

* 選取 [Microsoft SQL Server]，並按一下 [繼續]

* 輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**

* 輸入 **master** 作為**資料庫名稱**，並按一下 [確定]

* master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下

* 以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]

* 將以下列出的指令碼，複製到查詢編輯器中

* 以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>建立新專案

* 開啟 Visual Studio 2017

* [檔案] > [新增] > [專案...]

* 從左側功能表中，選取 **[已安裝] > [Visual C#] > [Windows Desktop]**

* 選取 [主控台應用程式 (.NET Framework)] 專案範本

* 請確定專案的目標為 **.NET Framework 4.6.1** 或更新版本

* 將專案命名為 *ConsoleApp.ExistingDb*，然後按一下 [確定]

## <a name="install-entity-framework"></a>安裝 Entity Framework

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本教學課程使用 SQL Server。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`

在下一個步驟中，您會用到一些 Entity Framework Tools 對資料庫進行還原工程。 因此，也請安裝工具套件。

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

## <a name="reverse-engineer-the-model"></a>對模型進行還原工程

現在就可以根據您現有的資料庫來建立 EF 模型。

* [工具] –> [NuGet 套件管理員] –> [套件管理員主控台]

* 執行下列命令，以便從現有的資料庫來建立模型

  ``` powershell
  Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
  ```

> [!TIP]  
> 您可以將 `-Tables` 引數新增至上述命令，以指定要為哪些資料表產生實體。 例如，`-Tables Blog,Post`。

還原工程處理序會根據現有資料庫的結構描述，建立實體類別 (`Blog` 和 `Post`) 與衍生的內容 (`BloggingContext`)。

實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。 以下為 `Blog` 與 `Post` 實體類別：

 [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Post.cs)]

> [!TIP]  
> 若要啟用延遲載入，可以設定瀏覽屬性 `virtual` (Blog.Post 與 Post.Blog)。

該內容代表具備資料庫的工作階段。 其備有多種您可用於查詢及儲存實體類別執行個體的方法。

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)]

## <a name="use-the-model"></a>使用模型

您現已可使用模型來執行資料存取。

* 開啟 *Program.cs*

* 以下列程式碼來取代檔案的內容

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] 

* 偵錯 > 啟動但不偵錯

  您會看到有一個部落格儲存至資料庫，然後所有部落格的詳細資料皆會列印至主控台。

  ![影像](_static/output-existing-db.png)

## <a name="next-steps"></a>後續步驟

如需如何建立內容和實體類別的詳細資訊，請參閱下列文章：
* [反向工程](xref:core/managing-schemas/scaffolding)
* [Entity Framework Core 工具參考 - .NET CLI](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [Entity Framework Core 工具參考 - 套件管理員主控台](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)