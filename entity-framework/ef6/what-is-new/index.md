---
title: 新功能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: 9daae787d0cec0ca536413e6263bb363ba76ff2c
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812143"
---
# <a name="whats-new-in-ef6"></a>EF6 的新功能

強烈建議您使用 Entity Framework 的最新發行版本，以確保您取得最新的功能和最高的穩定性。
不過，我們了解您可能需要使用先前的版本，或者可能想要體驗最新發行前版本中的新改善。
若要安裝特定版本的 EF，請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)。

## <a name="ef-630"></a>EF 6.3.0

EF 6.3.0 執行階段已於 2019 年 9 月發行至 NuGet。 此版本主要目標為將使用 EF 6 的現有應用程式促進移轉至 .NET Core 3.0。 社群也貢獻了一些 Bug 修正和增強功能。 有關詳細資料，請參閱每個 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中已關閉的問題。 以下是一些較需注意的事項：

- 支援 .NET Core 3.0
  - 現在除了 .NET Framework 4.x 以外，EntityFramework 套件也以 .NET Standard 2.1 為目標。
  - 這表示 EF 6.3 可以跨平台，在 Windows 以外的其他作業系統上獲得支援，例如 Linux 和 macOS。
  - 移轉命令已經過重寫，改為在處理序外執行，並使用 SDK 樣式的專案。
- 對 SQL Server HierarchyId 的支援。
- 已改善與 Roslyn 和 NuGet PackageReference 的相容性。
- 已新增 `ef6.exe` 公用程式，用於從組件啟用、新增、撰寫指令碼並套用移轉。 這取代了 `migrate.exe`。

### <a name="ef-designer-support"></a>EF 設計工具支援

目前不支援直接在 .NET Core 或 .NET Standard 專案上使用 EF 設計工具。 

您可以透過新增 EDMX 檔案與實體的產生類別和 DbContext 作為連結的檔案至相同方案中的 .NET Core 3.0 或 .NET Standard 2.1 專案來作為因應措施。

在專案檔中，連結的檔案看起來像下面這樣：

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

請注意，EDMX 檔案是使用 EntityDeploy 建置動作連結的。 這是特殊 MSBuild 工作 (現在包括在 EF 6.3 套件中)，它會負責新增 EF 模組到目標組件作為內嵌資源 (或將它複製為輸出資料夾中的檔案，視 EDMX 中的中繼資料成品處理設定而定)。 如需有關如何設定的詳細資訊，請參閱我們的 [EDMX .NET Core 範例](https://aka.ms/EdmxDotNetCoreSample)。

## <a name="past-releases"></a>過去版本

[過去版本](past-releases.md)頁面包含所有舊版 EF 與每一版所引進之主要功能的封存。
