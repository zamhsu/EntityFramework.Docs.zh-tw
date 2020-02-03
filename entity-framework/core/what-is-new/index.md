---
title: EF Core 版本和計劃
author: ajcvickers
ms.date: 01/29/2020
ms.assetid: C21F89EE-FB08-4ED9-A2A0-76CB7656E6E4
uid: core/what-is-new/index
ms.openlocfilehash: 0704b624a50d0736fefbb5633e9e7e657499a703
ms.sourcegitcommit: b3cf5d2e3cb170b9916795d1d8c88678269639b1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2020
ms.locfileid: "76888069"
---
# <a name="ef-core-releases-and-planning"></a>EF Core 版本和計劃

## <a name="stable-releases"></a>穩定版本

| 版本 | 目標 Framework | 支援直到 | 連結
|:--------|------------------|-----------------|------
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.1) | .NET Standard 2.0 | 2022 年 12 月 3 日 (LTS) | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/) \(英文\)
| [EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.1) \(英文\) | .NET Standard 2.1 | 2020 年 3 月 3 日 | [公告](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [中斷性變更](ef-core-3.0/breaking-changes.md) \(英文\)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ \(英文\) | .NET Standard 2.0 | 2019 年 12 月 23 日過期 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/) \(英文\)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 2021 年 8 月 21 日 (LTS) | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/) \(英文\)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ \(英文\) | .NET Standard 2.0 | 2018 年 10 月 1 日過期 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/) \(英文\)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ \(英文\) | .NET Standard 1.3 | 2019 年 6 月 27 日過期 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/) \(英文\)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ \(英文\) | .NET Standard 1.3 | 2019 年 6 月 27 日過期 | [公告](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/) \(英文\)

如需每個 EF Core 版本所支援特定平台的相關資訊，請參閱[支援的平台](../platforms/index.md)。

如需支援到期與長期支援 (LTS) 版本的資訊，請參閱 [.NET 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文\)。

## <a name="guidance-on-updating-to-new-releases"></a>更新至新版本的指導方針

* 會針對支援的版本進行安全性與其他嚴重錯誤修補。 請一律使用給定版本的最新修補程式。 例如，針對 EF Core 2.1，請使用 2.1.14。
* 主要版本更新 (例如，從 EF Core 2 到 EF Core 3) 通常會有中斷性變更。 跨主要版本更新時，建議您進行徹底測試。 如需處理中斷性變更的指導方針，請使用上述中斷性變更連結。
* 次要版本更新通常不包含中斷性變更。 不過，仍然建議進行徹底測試，因為新功能可能會引進迴歸。

## <a name="release-planning-and-schedules"></a>發行規劃和排程

EF Core 發行與 [.NET Core 推出排程](https://github.com/dotnet/core/blob/master/roadmap.md) \(英文\) 一致。

修補程式版本通常為每月發送，但有很長的前置時間。
我們正在努力改善此情況。

如需我們如何決定每次發行所發送內容的詳細資訊，請參閱[發行規劃流程](release-planning.md)。
我們一般不會像規劃下個主要或次要版本那麼詳細。

## <a name="ef-core-50"></a>EF Core 5.0

下一個已計劃穩定版本是 **EF Core 5.0**，排程於 2020 年 11 月推出。

[EF Core 5.0 的概略計劃](ef-core-5.0/plan.md)已依照記載的[發行計劃程序](release-planning.md)建立。

您對計劃的意見反應很重要。
指出問題重要性的最佳方式是在 GitHub 上針對該問題發起投票 (點讚 👍)。
此資料接著會進入下一個版本的計劃程序。

### <a name="get-it-now"></a>立即取得！

EF Core 5.0 套件**現已**作為[每日組建](https://github.com/aspnet/AspNetCore/blob/master/docs/DailyBuilds.md) \(英文\) 提供。 

使用每日組建是找出問題並儘早提供意見反應的絕佳方式。
我們越快取得這類意見反應，就越可能在下一版正式發行之前處理它。
我們為每個組建在每個平台上執行超過 56,000 次測試，努力讓每日組建保持良好狀態。

預覽套件會在今年稍後送出到 NuGet。
