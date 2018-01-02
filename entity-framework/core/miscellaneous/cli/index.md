---
title: "命令列參考 - EF Core"
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 076e9251850ba10df323cd25922aa8b95b3a5491
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="entity-framework-core-tools"></a>Entity Framework Core 工具
===========================
Entity Framework Core 工具可在 EF Core 應用程式開發期間協助您。 它們主要用來將資料庫結構描述反向工程以支援 DbContext 和實體類型，以及管理「移轉」。

[EF Core 套件管理員主控台 (PMC) 工具][1]提供 Visual Studio 內的更好經驗。 使用 NuGet 的[套件管理員主控台][2]來執行它們。 這些工具會使用 .NET Framework 和 .NET Core 專案。

[EF Core .NET 命令列工具][3]是 [.NET Core 命令列介面 (CLI) 工具][4]的延伸模組，而後者跨平台，而且可以在 Visual Studio 外部執行。 這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。

這兩個工具都會公開相同的功能。 如果您要在 Visual Studio 中進行開發，則建議使用 PMC 工具，因為它們提供更加整合的體驗。

<a name="frameworks"></a>架構
----------
這些工具還支援目標設為 .NET Framework 或 .NET Core 的專案。

如果您專案的目標設為另一個架構 (例如，通用 Windows 或 Xamarin)，則建議建立所支援架構的不同 .NET Standard 專案和跨目標專案。

若要將 .NET Core 設為跨目標 (例如，以滑鼠右鍵按一下專案，然後選取 [編輯 \*.csproj]。 更新 `TargetFramework` 屬性，如下所示  (請注意，屬性名稱會變成複數)。

``` xml
<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>
```

如果您要使用 .NET Standard 類別庫，則您的啟始專案目標設為 .NET Framework 或 .NET Core 時不需要跨目標。

<a name="startup-and-target-projects"></a>啟始和目標專案
---------------------------
只要您叫用命令，就會包含兩個專案：目標專案和啟始專案。

目標專案就是新增 (在某些情況下會移除) 任何檔案的位置。

啟始專案是工具在執行您的專案程式碼時所模擬的專案。

目標專案和啟始專案可以相同。


  [1]: powershell.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: dotnet.md
  [4]: https://docs.microsoft.com/dotnet/core/tools/
