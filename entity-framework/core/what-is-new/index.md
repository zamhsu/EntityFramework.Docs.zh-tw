---
title: EF Core 版本和計劃
author: ajcvickers
ms.date: 01/14/2020
ms.assetid: C21F89EE-FB08-4ED9-A2A0-76CB7656E6E4
uid: core/what-is-new/index
ms.openlocfilehash: 8d74c24021fd62c5c5d944eaf3973b344fdb1e9c
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124401"
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

## <a name="ef-core-50"></a>EF Core 5.0

EF Core 發行與 [.NET Core 推出排程](https://github.com/dotnet/core/blob/master/roadmap.md) \(英文\) 一致。 下一個已計劃穩定版本是 **EF Core 5.0**，排程於 2020 年 11 月推出。

[EF Core 5.0 的概略計劃](ef-core-5.0/plan.md)已依照記載的[發行計劃程序](release-planning.md)建立。

您對計劃的意見反應很重要。 若要指出問題的重要性，最佳方式是在 GitHub 上針對該問題投票 (大拇指)。 此資料接著會進入下一個版本的計劃程序。

### <a name="get-it-now"></a>立即取得！

EF Core 5.0 套件**現已**作為[每日組建](https://github.com/aspnet/AspNetCore/blob/master/docs/DailyBuilds.md) \(英文\) 提供。 

使用每日組建是找出問題並儘早提供意見反應的絕佳方式。 我們越快取得這類意見反應，就越可能在下一版正式發行之前處理它。 我們針對每個平台的每個組建執行超過 55,000 個測試，讓每日組建處於良好狀態。

預覽套件會在今年稍後送出到 NuGet。
