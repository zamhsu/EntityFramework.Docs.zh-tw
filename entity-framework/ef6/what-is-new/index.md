---
title: 新功能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 568790d9c9bb7dd2213907bef8fa090710cd3ba0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149120"
---
# <a name="whats-new-in-ef6"></a>EF6 的新功能

強烈建議您使用 Entity Framework 的最新發行版本，以確保您取得最新的功能和最高的穩定性。
不過，我們了解您可能需要使用先前的版本，或者可能想要體驗最新發行前版本中的新改善。
若要安裝特定版本的 EF，請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)。

## <a name="ef-630"></a>EF 6.3.0

EF 6.3.0 執行階段已於 2019 年 9 月發行至 NuGet。 此版本主要目標為將使用 EF 6 的現有應用程式促進移轉至 .NET Core 3.0。 社群也貢獻了一些 Bug 修正和增強功能。 有關詳細資料，請參閱每個 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中已關閉的問題。 以下是一些較需注意的事項：

- 支援 .NET Core 3.0
  - 現在，除了 .NET Framework 4.x 以外，EntityFramework 套件也針對 .NET Standard 2.1
  - 移轉命令已重寫為在程序外執行，並可使用 SDK 樣式的專案
- 支援 SQL Server HierarchyId
- 已改善與 Roslyn 和 NuGet PackageReference 的相容性
- 已新增 ef6.exe，用於從組件啟用、新增、撰寫指令碼並套用移轉。 此項目取代 migrate.exe

## <a name="past-releases"></a>過去版本

[過去版本](past-releases.md)頁面包含所有舊版 EF 與每一版所引進之主要功能的封存。
