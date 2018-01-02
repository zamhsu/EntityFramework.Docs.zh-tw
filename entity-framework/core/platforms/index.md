---
title: "支援的 .NET 實作 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 08/30/2017
ms.technology: entity-framework-core
uid: core/platforms/index
ms.openlocfilehash: 6b6ea9ca833810169d0d850f3bea8776b761c007
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="net-implementations-supported-by-ef-core"></a>EF Core 支援的 .NET 實作

我們想要 EF Core 在您可撰寫 .NET 程式碼的任何位置都可以使用，因此仍然朝該目標努力中。 下表提供我們要啟用 EF Core 之每個 .NET 實作的指引。

EF Core 2.0 的目標設為 [.NET Standard 2.0](https://docs.microsoft.com/dotnet/standard/net-standard)，因此需要支援它的 .NET 實作。

| .NET 實作 | 狀態 | 1.x 需要 | 2.x 需要
|-|-|-|-
| **.NET Core** ([ASP.NET Core](../get-started/aspnetcore/index.md)、[主控台](../get-started/netcore/index.md) 等等) | **完全支援和建議**：由自動化測試和許多已知可成功使用它的應用程式所涵蓋。 | [.NET Core SDK 1.x](https://www.microsoft.com/net/core/) | [.NET Core SDK 2.x](https://www.microsoft.com/net/core/)
| **.NET Framework** (WinForms、WPF、ASP.NET、[主控台](../get-started/full-dotnet/index.md) 等等) | **完全支援和建議**：由自動化測試和許多已知可成功使用它的應用程式所涵蓋。 此平台也提供 EF 6 (請參閱[比較 EF Core 與 EF6](../../efcore-and-ef6/index.md)來選擇正確的技術)。 | .NET Framework 4.5.1 | .NET Framework 4.6.1
| **Mono 和 Xamarin** | **進行中 - 可能發生問題**：EF Core 小組和客戶已執行一些測試。 早期採用者已回報一些成功，但[已發生問題](https://github.com/aspnet/entityframework/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)，而其他成功則可能會在持續測試時發現。 特別的是，Xamarin.iOS 有一些限制可能會讓使用 EF Core 2.0 所開發的一些應用程式無法正常運作。 | Mono 4.6 <br/> Xamarin.iOS 10 <br/> Xamarin.Mac 3 <br/> Xamarin.Android 7 | Mono 5.4 <br/> Xamarin.iOS 10.14 <br/> Xamarin.Mac 3.8 <br/> Xamarin.Android 7.5
| [**通用 Windows 平台**](../get-started/uwp/index.md) | **進行中 - 可能發生問題**：EF Core 小組和客戶已執行一些測試。 使用 .NET Native 工具鏈編譯時已回報[問題](https://github.com/aspnet/entityframework/issues?utf8=%E2%9C%93&q=is%3Aopen%20is%3Aissue%20label%3Aarea-uwp%20)，而此工具鏈一般是在發行組建期間使用，而且是部署至 Windows 市集的必要項目 (如果您未使用 .NET Native，或只想要實驗，則有許多問題都不會影響您)。 | [最新 .NET UWP 5 套件](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/5.4.1) | [最新 .NET UWP 6 套件](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/) <sup>(1)</sup>

<sup>(1)</sup> 這版 .NET UWP 支援 .NET Standard 2.0，並且包含修正大部分先前報告之相容性問題的 .NET Native 2.0，但測試已顯示計劃在未來修補版本中解決之 EF Core 2.0 的[一些剩餘問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A2.0.1+label%3Aarea-uwp)。

針對任何未如預期運作的組合，我們鼓勵在 [EF Core 問題追蹤程式](https://github.com/aspnet/entityframeworkcore/issues/new)中建立新問題，而針對 Xamarin 相關問題，則是 [Xamarin 問題追蹤程式](https://bugzilla.xamarin.com/newbug)。
