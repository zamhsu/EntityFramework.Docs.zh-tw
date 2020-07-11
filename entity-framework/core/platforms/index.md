---
title: 支援的 .NET 實作 - EF Core
author: bricelam
ms.date: 06/26/2020
uid: core/platforms/index
ms.openlocfilehash: f7e7a4f7993061aa46c1f9c648169158f7a35193
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238043"
---
# <a name="net-implementations-supported-by-ef-core"></a>EF Core 支援的 .NET 實作

我們希望 EF Core 可供開發人員用於所有新式 .NET 實作，我們仍正朝著此目標努力。 當 EF Core 對 .NET Core 的支援皆已涵蓋在自動化測試內，且已知有許多應用程式能夠正常加以使用時，Mono、Xamarin 及 UWP 仍存在某些問題。

## <a name="overview"></a>總覽

下表提供每個 .NET 實作的指導：

| EF Core                       | 2.1 及 3.1 | 5.0             |
|:------------------------------|:------------|:----------------|
| .NET Standard                 | 2.0         | 2.1             |
| .NET Core                     | 2.0         | 3.0             |
| .NET Framework<sup>(1)</sup>  | 4.7.2       | (不支援) |
| Mono                          | 5.4         | 6.4             |
| Xamarin.iOS<sup>(2)</sup>     | 10.14       | 12.16           |
| <sup> (2) </sup>的 Xamarin. Mac     | 3.8         | 5.16            |
| Xamarin.Android<sup>(2)</sup> | 8.0         | 10.0            |
| UWP<sup>(3)</sup>             | 10.0.16299  | TBD             |
| Unity<sup>(4)</sup>           | 2018.1      | TBD             |

<sup>(1)</sup> 請參閱下方的 [.NET Framework](#net-framework) 一節。

<sup>(2)</sup> Xamarin 有一些問題與已知限制，可能會使某些使用 EF Core 開發的應用程式無法正常運作。 請查看[待處理的問題](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)清單，以了解因應措施。

<sup>(3)</sup> 建議使用 EF Core 2.0.1 及更新版本。 安裝 [.NET Core UWP 6.x 套件](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/)。 請參閱本文的[通用 Windows 平台](#universal-windows-platform)一節。

<sup>(4)</sup> Unity 目前存在問題和已知限制。 請查看[待處理的問題](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity)清單。

## <a name="net-framework"></a>.NET Framework

以 .NET Framework 為目標的應用程式可能必須變更為使用 .NET Standard 程式庫：

編輯專案檔，並確定下列項目出現在初始屬性群組中：

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

針對測試專案，也請確定下列項目存在：

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

若您想要使用較舊版本的 Visual Studio，請務必[將 NuGet 用戶端升級至 3.6.0 版](https://www.nuget.org/downloads)，以與 .NET Standard 2.0 程式庫搭配使用。

如果可能，我們也建議從 NuGetpackages.config 移轉至 PackageReference。 請將下列屬性新增至您的專案檔：

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>通用 Windows 平台

舊版 EF Core 與 .NET UWP 有許多相容性問題，其中以 .NET Native 工具鏈編譯的應用程式問題最多。 新版 UWP 新增了 .NET Standard 2.0 的支援且包含 .NET Native 2.0，這能夠修正上述提到的大多數相容性問題。 EF Core 2.0.1 已採用 UWP 進行更全面的測試，但測試並非自動進行。

在 UWP 上使用 EF Core 時：

* 若要最佳化查詢效能，請避免在 LINQ 查詢中使用匿名型別。 若要將 UWP 應用程式部署至應用程式市集，應用程式需要使用 .NET Native 來編譯。 使用匿名型別的查詢在 .NET Native 上會有較差的效能。

* 若要最佳化 `SaveChanges()` 效能，請使用 [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) 並在您的實體類型中實作 [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx)、[INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) 與 [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx)。

## <a name="report-issues"></a>回報問題

如有任何組合無法如預期運作，建議您在 [EF Core 問題追蹤器](https://github.com/aspnet/entityframeworkcore/issues/new)上建立新問題。 若為 Xamarin 相關的問題，請使用 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) 或 [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new)的問題追蹤器。
