---
title: 命令列參考 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.openlocfilehash: 757d6562f5d3bcd4f026def02f208f5827786873
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997066"
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

若您想要使用類別程式庫，請在可行的情況下使用 .NET Core 或 .NET Framework 類別程式庫。 這會使 .NET 工具產生較少問題。 而如果您想要使用 .NET Standard 類別程式庫，則需要以 .NET Framework 或 .Net Core 為目標的啟動專案，使工具擁有具體的目標平台來上傳您的類別程式庫。 您可使用實際沒有程式碼的虛設專案作為啟動專案，此專案的目的僅是為工具提供目標。

若您的專案將另一個架構設為目標 (例如通用 Windows 或 Xamarin)，則必須建立個別的 .NET Standard 類別程式庫。 在此情況下，請遵循上述指導，並建立可供工具使用的啟動專案。

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
