---
title: EF Core 版本和計劃
description: EF Core 目前版本及未來版本的排程/規劃詳細資料
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/index
ms.openlocfilehash: 18d8055840b5a38dc62d20e7e18c440b7360c9e4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128325"
---
# <a name="ef-core-releases-and-planning"></a>EF Core 版本和計劃

## <a name="stable-releases"></a>穩定版本

| 版本 | 目標架構 | 支援期限 | 連結
|:--------|------------------|-----------------|------
| [EF Core 5.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2.1 | 2022年2月中旬 | [公告](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-ef-core-5-0/) / [中斷性變更](xref:core/what-is-new/ef-core-5.0/breaking-changes) \(英文\)
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.10) | .NET Standard 2.0 | 2022 年 12 月 3 日 (LTS) | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/) \(英文\)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2.1 | 2020 年 3 月 3 日到期 | [公告](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [中斷性變更](xref:core/what-is-new/ef-core-3.x/breaking-changes) \(英文\)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2.0 | 2019 年 12 月 23 日過期 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/) \(英文\)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 2021 年 8 月 21 日 (LTS) | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/) \(英文\)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2.0 | 2018 年 10 月 1 日過期 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/) \(英文\)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | 2019 年 6 月 27 日過期 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/) \(英文\)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | 2019 年 6 月 27 日過期 | [公告](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/) \(英文\)

如需每個 EF Core 版本所支援特定平台的相關資訊，請參閱[支援的平台](xref:core/miscellaneous/platforms)。

如需支援到期與長期支援 (LTS) 版本的資訊，請參閱 [.NET 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文\)。

## <a name="guidance-on-updating-to-new-releases"></a>更新至新版本的指導方針

* 會針對支援的版本進行安全性與其他嚴重錯誤修補。 請一律使用給定版本的最新修補程式。 例如，針對 EF Core 2.1，請使用 2.1. x 來取得最高可用的 ' x '。
* 主要版本更新 (例如，從 EF Core 2 到 EF Core 3) 通常會有中斷性變更。 跨主要版本更新時，建議您進行徹底測試。 如需處理中斷性變更的指導方針，請使用上述中斷性變更連結。
* 次要版本更新通常不包含中斷性變更。 不過，仍然建議進行徹底測試，因為新功能可能會引進迴歸。

## <a name="release-planning-and-schedules"></a>發行規劃和排程

EF Core 發行與 [.NET Core 推出排程](https://github.com/dotnet/core/blob/master/roadmap.md) \(英文\) 一致。

修補程式版本通常為每月發送，但有很長的前置時間。
我們正在努力改善此情況。

如需我們如何決定每次發行所發送內容的詳細資訊，請參閱[發行規劃流程](xref:core/what-is-new/release-planning)。
我們一般不會像規劃下個主要或次要版本那麼詳細。

## <a name="ef-core-60"></a>EF Core 6。0

下一個規劃的穩定版本是 **EF Core 6.0**，排程于 **2021 年11月**。

遵循記載的[發行規劃](xref:core/what-is-new/release-planning)程式，建立了[EF Core 6.0 的高階計畫](xref:core/what-is-new/ef-core-6.0/plan)。

您對計劃的意見反應很重要。
指出問題重要性的最佳方式是在 GitHub 上針對該問題發起投票 (點讚 👍)。
此資料接著會進入下一個版本的計劃程序。

### <a name="get-it-now"></a>立即取得

EF Core 6.0 套件 **現已提供**

* [每日組建](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md) (英文) 的形式提供
  * 所有最新的功能與 Bug 修正。 通常非常穩定;75000 + 針對每個組建執行的測試。

此外，當我們進行時，經常會將預覽推送至 NuGet。 請注意，每日組建背後的預覽延隔，但經過測試，可搭配對應的 ASP.NET Core 和 .NET Core 預覽。

使用預覽版或每日組建，是發現問題的最佳方法，並能因此而儘早提供意見反應。
我們越快取得這類意見反應，就越可能在下一版正式發行之前處理它。
