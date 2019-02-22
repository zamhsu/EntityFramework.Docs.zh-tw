---
title: .NET Core 使用者入門 - 新資料庫 - EF Core
author: rick-anderson
ms.author: riande
description: 使用 .NET Core 搭配 Entity Framework Core 的使用者入門
ms.date: 08/03/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: a0df80a8fe96be4f8cc3177919e2b087e14cb49c
ms.sourcegitcommit: 735715f10cc8a231c213e4f055d79f0effd86570
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325323"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a>在 .NET Core 主控台應用程式上使用 EF Core 搭配新資料庫的使用者入門

在此教學課程中，您會建置 .NET Core 主控台應用程式，它會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。 您會使用移轉從模型建立資料庫。 如需使用 ASP.NET Core MVC 的 Visual Studio 版本，請參閱 [ASP.NET Core - 新資料庫](xref:core/get-started/aspnetcore/new-db)。

[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)。

## <a name="prerequisites"></a>必要條件

* [.NET Core 2.1 SDK](https://www.microsoft.com/net/core)

## <a name="create-a-new-project"></a>建立新專案

* 建立新的主控台專案：

  ``` Console
  dotnet new console -o ConsoleApp.SQLite
  ```
## <a name="change-the-current-directory"></a>變更目前的目錄

在後續步驟中，我們需要對應用程式發出 `dotnet` 命令。

* 我們會將目前的目錄變更為應用程式目錄，如下所示：

  ``` Console
  cd ConsoleApp.SQLite/
  ```
## <a name="install-entity-framework-core"></a>安裝 Entity Framework Core

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本逐步解說使用 SQLite。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* 安裝 Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.Design

  ```Console
  dotnet add package Microsoft.EntityFrameworkCore.Sqlite
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

* 執行 `dotnet restore` 以安裝新的套件。

## <a name="create-the-model"></a>建立模型

定義組成模型的內容和實體類別。

* 建立具有下列內容的新 *Model.cs* 檔案。

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

提示：在實際的應用程式中，您會將每個類別放在不同的檔案中，然後將連接字串放在設定檔或環境變數中。 為了簡化教學課程，所有項目會統一放在一個檔案中。

## <a name="create-the-database"></a>建立資料庫

一旦有模型後，您就可以使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。

* 執行 `dotnet ef migrations add InitialCreate` 以建立移轉的結構，並針對模型建立一組初始的資料表。
* 執行 `dotnet ef database update` 以將新的移轉套用至資料庫。 此命令會建立資料庫，然後才套用移轉。

*blogging.db* SQLite DB 位於專案目錄中。

## <a name="use-the-model"></a>使用模型

* 開啟 *Program.cs* 並使用下列程式碼來取代內容：

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* 從主控台測試應用程式。 請參閱 [Visual Studio 注意事項](#vs) 以透過 Visual Studio 執行應用程式。

  `dotnet run`

  有一個部落格儲存至資料庫，且所有部落格的詳細資料會在主控台中顯示。

  ```Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a>變更模型：

- 若要變更模型，您可以使用 `dotnet ef migrations add` 命令來建立新的[移轉](xref:core/managing-schemas/migrations/index)支架。 一旦檢查支架程式碼 (並進行任何必要的變更)，您就可以使用 `dotnet ef database update` 命令將結構描述變更套用到資料庫。
- EF Core 在資料庫中使用 `__EFMigrationsHistory` 資料表，以追蹤已套用到資料庫的移轉。
- SQLite 資料庫引擎不支援大部分其他關聯式資料庫所支援的特定結構描述變更。 例如，不支援 `DropColumn` 作業。 EF Core 移轉將會產生這些作業的程式碼。 但若您嘗試將它們套用到資料庫或產生指令碼，EF Core 會擲回例外狀況。 請參閱 [SQLite 限制](../../providers/sqlite/limitations.md)。 針對新的開發，當模型變更時，請考慮捨棄資料庫並建立一個新的資料庫，而不是使用移轉。

<a name="vs"></a>
### <a name="run-from-visual-studio"></a>透過 Visual Studio 來執行

若要透過 Visual Studio 執行此範例，您必須將工作目錄手動設定為專案的根目錄。 如果您未設定工作目錄，就會擲回下列 `Microsoft.Data.Sqlite.SqliteException`：`SQLite Error 1: 'no such table: Blogs'`。

若要設定工作目錄：

* 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [屬性]。
* 選取左窗格中的 [偵錯] 索引標籤。
* 將**工作目錄**設定為專案目錄。
* 儲存變更。

## <a name="additional-resources"></a>其他資源

* [教學課程：在 ASP.NET Core 上使用 EF Core 搭配新資料庫 (使用 SQLite) 的使用者入門](xref:core/get-started/aspnetcore/new-db)
* [教學課程：開始使用 ASP.NET Core 中的 Razor Pages](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [教學課程：ASP.NET Core 中的 Razor Pages 與 Entity Framework Core](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
