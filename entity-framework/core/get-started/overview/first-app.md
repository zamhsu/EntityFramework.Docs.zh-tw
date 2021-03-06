---
title: 使用者入門 - EF Core
description: Entity Framework Core 的使用者入門教學課程
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/overview/first-app
ms.openlocfilehash: 560a6f293e32f4e1f75c13367291c4e31e83b157
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024338"
---
# <a name="getting-started-with-ef-core"></a>開始使用 EF Core

在此教學課程中，您會建置 .NET Core 主控台應用程式，它會使用 Entity Framework Core 對 SQLite 資料庫執行資料存取。

若要遵循本教學課程，您可以在 Windows 上使用 Visual Studio 或在 Windows、macOS 或 Linux 上使用 .NET Core CLI。

[在 GitHub 上檢視此文章的範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted)。

## <a name="prerequisites"></a>必要條件

安裝下列軟體：

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* [.NET Core SDK](https://www.microsoft.com/net/download/core)。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16.3 版或更新版本](https://www.visualstudio.com/downloads/)與此工作負載：
  * **.NET Core 跨平台開發** (位在 [其他工具組]  下)

---

## <a name="create-a-new-project"></a>建立新專案

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 開啟 Visual Studio
* 按一下 [建立新專案] 
* 選取 [主控台應用程式 (.NET Core)]  以及 [C#]  標籤，並按一下 [下一步] 
* 輸入 **EFGetStarted** 作為名稱，然後按一下 [建立] 

---

## <a name="install-entity-framework-core"></a>安裝 Entity Framework Core

若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。 本教學課程會使用 SQLite，因為它可以在 .NET Core 支援的所有平台上執行。 如需可用的提供者清單，請參閱[資料庫提供者](xref:core/providers/index)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [工具] > [NuGet 套件管理員] > [套件管理員主控台] 
* 執行下列命令：

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

提示：您也可以用右鍵按一下專案並選取 [管理 NuGet 套件]  來安裝套件

---

## <a name="create-the-model"></a>建立模型

定義組成模型的內容類別與實體類別。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 在專案目錄中，使用下列程式碼建立 **Model.cs**

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 以滑鼠右鍵按一下專案，然後選取 [新增] > [類別] 
* 輸入 **Model.cs** 作為名稱，然後按一下 [新增] 
* 以下列程式碼來取代檔案的內容

---

[!code-csharp[Main](../../../../samples/core/GetStarted/Model.cs)]

EF Core 也可以對現有資料庫中的模型進行[反向工程](xref:core/managing-schemas/scaffolding)。

提示：為了清晰易懂，此應用程式刻意保持簡潔。 [連接字串](xref:core/miscellaneous/connection-strings)不應儲存在實際執行應用程式的程式碼中。 您也可以將每個 C# 類別分割到其自有檔案。

## <a name="create-the-database"></a>建立資料庫

下列步驟會使用[移轉](xref:core/managing-schemas/migrations/index)來建立資料庫。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 執行下列命令：

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  這會安裝 [dotnet ef](xref:core/cli/dotnet) 以及在專案上執行命令所需的設計套件。 `migrations` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。 `database update` 命令會建立資料庫，並對資料庫套用新的移轉。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **套件管理員主控台 (PMC)** 中執行下列命令

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  這會安裝[適用於 EF Core 的 PMC 工具](xref:core/cli/powershell)。 `Add-Migration` 命令會建立移轉的 Scaffolding，以針對模型建立一組初始資料表。 `Update-Database` 命令會建立資料庫，並對資料庫套用新的移轉。

---

## <a name="create-read-update--delete"></a>建立、讀取、更新及刪除

* 開啟 *Program.cs* 並使用下列程式碼來取代內容：

  [!code-csharp[Main](../../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>執行應用程式

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[偵錯] > [啟動但不偵錯] 

---

## <a name="next-steps"></a>後續步驟

* 遵循 [ASP.NET Core 教學課程](/aspnet/core/data/ef-rp/intro)以在 Web 應用程式中使用 EF Core
* 深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [設定模型](xref:core/modeling/index)以指定如[必要](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大長度](xref:core/modeling/entity-properties#maximum-length)之類的內容
* 變更模型後，請使用[移轉](xref:core/managing-schemas/migrations/index)來更新資料庫結構描述
