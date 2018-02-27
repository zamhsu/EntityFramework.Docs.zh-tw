---
title: ".NET Core 使用者入門 - 新資料庫 - EF Core"
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
description: "使用 .NET Core 搭配 Entity Framework Core 的使用者入門"
keywords: .NET Core, Entity Framework Core, VS Code, Visual Studio Code, Mac, Linux
ms.date: 04/05/2017
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
ms.technology: entity-framework-core
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: 3becf75e7a513a3aa18c3c2daf628b65327365b0
ms.sourcegitcommit: 0858f157b806f4a881b94ddbeecf1ece1d53e1e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a>在 .NET Core 主控台應用程式上使用 EF Core 搭配新資料庫的使用者入門

在本逐步解說中，您將建置 .NET Core 主控台應用程式，該應用程式將使用 Entity Framework Core 對 SQLite 資料庫執行基本資料存取。 您將使用移轉，以便從您的模型來建立資料庫。 如需使用 ASP.NET Core MVC 的 Visual Studio 版本，請參閱 [ASP.NET Core - 新資料庫](xref:core/get-started/aspnetcore/new-db)。

> [!NOTE]  
> [.NET Core SDK](https://www.microsoft.com/net/download/core) 已不再支援 `project.json` 或 Visual Studio 2015。 建議您[從 project.json 移轉至 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。 如果您使用 Visual Studio，建議您移轉至 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) \(英文\)。

## <a name="prerequisites"></a>必要條件

若要完成此逐步解說，必須符合下列必要條件：
* 支援 .NET Core 的作業系統。
* [.NET Core SDK](https://www.microsoft.com/net/core) 2.0 (不過，指示也可用來建立使用先前版的應用程式，且幾乎不必修改)。

## <a name="create-a-new-project"></a>建立新專案

* 為您的專案建立新的 `ConsoleApp.SQLite` 資料夾，並使用 `dotnet` 命令將 .NET Core 應用程式填入該資料夾。

``` Console
mkdir ConsoleApp.SQLite
cd ConsoleApp.SQLite/
dotnet new console
```

## <a name="install-entity-framework-core"></a>安裝 Entity Framework Core

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本逐步解說使用 SQLite。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* 安裝 Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.Design

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

* 手動編輯 `ConsoleApp.SQLite.csproj`，以將 DotNetCliToolReference 加入 Microsoft.EntityFrameworkCore.Tools.DotNet：

  ``` xml
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  </ItemGroup>
  ```

`ConsoleApp.SQLite.csproj` 現在應該會包含下列程式碼：

[!code[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/ConsoleApp.SQLite.csproj)]

 注意：上列所使用的版本號碼，為發行時正確的版本號碼。

*  執行 `dotnet restore` 以安裝新的套件。

## <a name="create-the-model"></a>建立模型

定義組成模型的內容和實體類別。

* 建立具有下列內容的新 *Model.cs* 檔案。

[!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

秘訣：在實際的應用程式中，您會將每個類別放在個別的檔案中，然後將連接字串放在組態檔中。 為了簡化教學課程，我們會將所有項目放在一個檔案中。

## <a name="create-the-database"></a>建立資料庫

一旦有模型之後，就可以使用[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)來建立資料庫。

* 執行 `dotnet ef migrations add InitialCreate` 以建立移轉的結構，並針對模型建立一組初始的資料表。
* 執行 `dotnet ef database update` 以將新的移轉套用至資料庫。 此命令會建立資料庫，然後才套用移轉。

> [!NOTE]  
> 在 SQLite 中使用相對路徑時，路徑會相對於應用程式的主要組件。 在此範例中，主要的二進位檔是 `bin/Debug/netcoreapp2.0/ConsoleApp.SQLite.dll`，因此 SQLite 資料庫將位於 `bin/Debug/netcoreapp2.0/blogging.db` 中。

## <a name="use-your-model"></a>使用您的模型

* 開啟 *Program.cs* 並使用下列程式碼來取代內容：

 [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* 測試應用程式：

 `dotnet run`

 有一個部落格儲存至資料庫，且所有部落格的詳細資料會在主控台中顯示。

  ``` Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a>變更模型：

- 如果您變更了您的模型，則可以使用 `dotnet ef migrations add` 命令來建立新[移轉](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)的結構，以對資料庫做出相對應的結構描述變更。 檢查完已建立結構的程式碼之後 (並進行任何必要的變更)，就可以使用 `dotnet ef database update` 命令將變更套用至資料庫。
- EF 在資料庫中使用 `__EFMigrationsHistory` 資料表，以確認已經套用至資料庫的移轉。
- 由於 SQLite 中的限制，SQLite 不支援所有移轉 (結構描述變更)。 請參閱 [SQLite 限制](../../providers/sqlite/limitations.md)。 針對新的開發，當模型變更時，請考慮捨棄資料庫並建立一個新的資料庫，而不是使用移轉。

## <a name="additional-resources"></a>其他資源

* [.NET Core - 使用 SQLite 的新資料庫](xref:core/get-started/netcore/new-db-sqlite) - 跨平台的主控台 EF 教學課程。
* [Mac 或 Linux 上的 ASP.NET Core MVC 簡介](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [使用 Visual Studio 的 ASP.NET Core MVC 簡介](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [使用 Visual Studio 的 ASP.NET Core 與 Entity Framework Core 的使用者入門](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
