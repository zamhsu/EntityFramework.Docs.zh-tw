---
title: 支援的 .NET 實作 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 08/30/2017
ms.technology: entity-framework-core
uid: core/platforms/index
ms.openlocfilehash: 790628c407cc4374fee4ebde8201783955afdcc3
ms.sourcegitcommit: fd50ac53b93a03825dcbb42ed2e7ca95ca858d5f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900326"
---
# <a name="net-implementations-supported-by-ef-core"></a>EF Core 支援的 .NET 實作

我們想要 EF Core 在您可撰寫 .NET 程式碼的任何位置都可以使用，因此仍然朝該目標努力中。 當 EF Core 對 .NET Core 及 .NET Framework 的支援皆已涵蓋在自動化測試內，且已知有許多應用程式能夠正常加以使用時，Mono、Xamarin 及 UWP 仍存在問題。

下表提供每個 .NET 實作的指導：

| .NET 實作                                                                                                  | 狀態                                                             | EF Core 1.x 需求                                                                                | EF Core 2.x 需求 <sup>(1)</sup>                                                                 |
|:---------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------|
| **.NET Core** ([ASP.NET Core](../get-started/aspnetcore/index.md)、[主控台](../get-started/netcore/index.md) 等等) | 完整支援與建議。                                    | [.NET Core SDK 1.x](https://www.microsoft.com/net/core/)                                                | [.NET Core SDK 2.x](https://www.microsoft.com/net/core/)                                                |
| **.NET Framework** (WinForms、WPF、ASP.NET、[主控台](../get-started/full-dotnet/index.md) 等等)                    | 完整支援與建議。 也可以使用 EF6 <sup>(2)</sup> | .NET Framework 4.5.1                                                                                    | .NET Framework 4.6.1                                                                                    |
| **Mono 和 Xamarin**                                                                                                   | 進行中 <sup>(3)</sup>                                         | Mono 4.6 <br/> Xamarin.iOS 10 <br/> Xamarin.Mac 3 <br/> Xamarin.Android 7                               | Mono 5.4 <br/> Xamarin.iOS 10.14 <br/> Xamarin.Mac 3.8 <br/> Xamarin.Android 7.5                        |
| [**通用 Windows 平台**](../get-started/uwp/index.md)                                                        | 建議 EF Core 2.0.1 <sup>(4)</sup>                           | [.NET Core UWP 5.x 套件](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/) | [.NET Core UWP 6.x 套件](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/) |

<sup>(1)</sup> EF Core 2.0 將其設為目標，因此需要支援 [.NET Standard 2.0](https://docs.microsoft.com/dotnet/standard/net-standard) 的 .NET 實作。

<sup>(2)</sup> 請參閱[比對 EF Core 與 EF6](../../efcore-and-ef6/index.md) 來選擇正確的技術。

<sup>(3)</sup> Xamarin 有一些問題與已知限制，可能會使某些使用 EF Core 2.0 開發的應用程式無法正常運作。 請查看[待處理的問題](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)清單，以了解因應措施。

<sup>(4)</sup> 舊版 EF Core 與 .NET UWP 有許多相容性問題，其中以 .NET Native 工具鏈編譯的應用程式問題最多。 新版 UWP 新增了 .NET Standard 2.0 的支援且包含 .NET Native 2.0，這能夠修正上述提到的大多數相容性問題。 EF Core 2.0.1 已採用 UWP 進行更全面的測試，但測試並非自動進行。

如有任何組合無法如預期運作，我們鼓勵您在 [EF Core 問題追蹤器](https://github.com/aspnet/entityframeworkcore/issues/new)建立新的問題。 若為 Xamarin 相關的問題，請使用 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) 或 [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new)的問題追蹤器。
