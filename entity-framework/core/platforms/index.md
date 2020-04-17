---
title: 支援的 .NET 實作 - EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 2738645596a325a1a43fd5b2164e370dd8c5893e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434119"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="8466d-102">EF Core 支援的 .NET 實作</span><span class="sxs-lookup"><span data-stu-id="8466d-102">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="8466d-103">我們希望 EF Core 可供開發人員用於所有新式 .NET 實作，我們仍正朝著此目標努力。</span><span class="sxs-lookup"><span data-stu-id="8466d-103">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="8466d-104">當 EF Core 對 .NET Core 的支援皆已涵蓋在自動化測試內，且已知有許多應用程式能夠正常加以使用時，Mono、Xamarin 及 UWP 仍存在某些問題。</span><span class="sxs-lookup"><span data-stu-id="8466d-104">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="8466d-105">總覽</span><span class="sxs-lookup"><span data-stu-id="8466d-105">Overview</span></span>

<span data-ttu-id="8466d-106">下表提供每個 .NET 實作的指導：</span><span class="sxs-lookup"><span data-stu-id="8466d-106">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="8466d-107">EF Core</span><span class="sxs-lookup"><span data-stu-id="8466d-107">EF Core</span></span>                       | <span data-ttu-id="8466d-108">2.1 及 3.1</span><span class="sxs-lookup"><span data-stu-id="8466d-108">2.1 and 3.1</span></span> |
|:------------------------------|:------------|
| <span data-ttu-id="8466d-109">.NET Standard</span><span class="sxs-lookup"><span data-stu-id="8466d-109">.NET Standard</span></span>                 | <span data-ttu-id="8466d-110">2.0</span><span class="sxs-lookup"><span data-stu-id="8466d-110">2.0</span></span>         |
| <span data-ttu-id="8466d-111">.NET Core</span><span class="sxs-lookup"><span data-stu-id="8466d-111">.NET Core</span></span>                     | <span data-ttu-id="8466d-112">2.0</span><span class="sxs-lookup"><span data-stu-id="8466d-112">2.0</span></span>         |
| <span data-ttu-id="8466d-113">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="8466d-113">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="8466d-114">4.7.2</span><span class="sxs-lookup"><span data-stu-id="8466d-114">4.7.2</span></span>       |
| <span data-ttu-id="8466d-115">Mono</span><span class="sxs-lookup"><span data-stu-id="8466d-115">Mono</span></span>                          | <span data-ttu-id="8466d-116">5.4</span><span class="sxs-lookup"><span data-stu-id="8466d-116">5.4</span></span>         |
| <span data-ttu-id="8466d-117">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="8466d-117">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="8466d-118">10.14</span><span class="sxs-lookup"><span data-stu-id="8466d-118">10.14</span></span>       |
| <span data-ttu-id="8466d-119">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="8466d-119">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="8466d-120">8.0</span><span class="sxs-lookup"><span data-stu-id="8466d-120">8.0</span></span>         |
| <span data-ttu-id="8466d-121">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="8466d-121">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="8466d-122">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="8466d-122">10.0.16299</span></span>  |
| <span data-ttu-id="8466d-123">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="8466d-123">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="8466d-124">2018.1</span><span class="sxs-lookup"><span data-stu-id="8466d-124">2018.1</span></span>      |

<span data-ttu-id="8466d-125"><sup>(1)</sup> 請參閱下方的 [.NET Framework](#net-framework) 一節。</span><span class="sxs-lookup"><span data-stu-id="8466d-125"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="8466d-126"><sup>(2)</sup> Xamarin 有一些問題與已知限制，可能會使某些使用 EF Core 開發的應用程式無法正常運作。</span><span class="sxs-lookup"><span data-stu-id="8466d-126"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="8466d-127">請查看[待處理的問題](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)清單，以了解因應措施。</span><span class="sxs-lookup"><span data-stu-id="8466d-127">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="8466d-128"><sup>(3)</sup> 建議使用 EF Core 2.0.1 及更新版本。</span><span class="sxs-lookup"><span data-stu-id="8466d-128"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="8466d-129">安裝 [.NET Core UWP 6.x 套件](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/)。</span><span class="sxs-lookup"><span data-stu-id="8466d-129">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="8466d-130">請參閱本文的[通用 Windows 平台](#universal-windows-platform)一節。</span><span class="sxs-lookup"><span data-stu-id="8466d-130">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="8466d-131"><sup>(4)</sup> Unity 目前存在問題和已知限制。</span><span class="sxs-lookup"><span data-stu-id="8466d-131"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="8466d-132">請查看[待處理的問題](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity)清單。</span><span class="sxs-lookup"><span data-stu-id="8466d-132">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="8466d-133">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="8466d-133">.NET Framework</span></span>

<span data-ttu-id="8466d-134">以 .NET Framework 為目標的應用程式可能必須變更為使用 .NET Standard 程式庫：</span><span class="sxs-lookup"><span data-stu-id="8466d-134">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="8466d-135">編輯專案檔，並確定下列項目出現在初始屬性群組中：</span><span class="sxs-lookup"><span data-stu-id="8466d-135">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="8466d-136">針對測試專案，也請確定下列項目存在：</span><span class="sxs-lookup"><span data-stu-id="8466d-136">For test projects, also make sure the following entry is present:</span></span>

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="8466d-137">若您想要使用較舊版本的 Visual Studio，請務必[將 NuGet 用戶端升級至 3.6.0 版](https://www.nuget.org/downloads)，以與 .NET Standard 2.0 程式庫搭配使用。</span><span class="sxs-lookup"><span data-stu-id="8466d-137">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="8466d-138">如果可能，我們也建議從 NuGetpackages.config 移轉至 PackageReference。</span><span class="sxs-lookup"><span data-stu-id="8466d-138">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="8466d-139">請將下列屬性新增至您的專案檔：</span><span class="sxs-lookup"><span data-stu-id="8466d-139">Add the following property to your project file:</span></span>

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="8466d-140">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="8466d-140">Universal Windows Platform</span></span>

<span data-ttu-id="8466d-141">舊版 EF Core 與 .NET UWP 有許多相容性問題，其中以 .NET Native 工具鏈編譯的應用程式問題最多。</span><span class="sxs-lookup"><span data-stu-id="8466d-141">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="8466d-142">新版 UWP 新增了 .NET Standard 2.0 的支援且包含 .NET Native 2.0，這能夠修正上述提到的大多數相容性問題。</span><span class="sxs-lookup"><span data-stu-id="8466d-142">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="8466d-143">EF Core 2.0.1 已採用 UWP 進行更全面的測試，但測試並非自動進行。</span><span class="sxs-lookup"><span data-stu-id="8466d-143">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="8466d-144">在 UWP 上使用 EF Core 時：</span><span class="sxs-lookup"><span data-stu-id="8466d-144">When using EF Core on UWP:</span></span>

* <span data-ttu-id="8466d-145">若要最佳化查詢效能，請避免在 LINQ 查詢中使用匿名型別。</span><span class="sxs-lookup"><span data-stu-id="8466d-145">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="8466d-146">若要將 UWP 應用程式部署至應用程式市集，應用程式需要使用 .NET Native 來編譯。</span><span class="sxs-lookup"><span data-stu-id="8466d-146">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="8466d-147">使用匿名型別的查詢在 .NET Native 上會有較差的效能。</span><span class="sxs-lookup"><span data-stu-id="8466d-147">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="8466d-148">若要最佳化 `SaveChanges()` 效能，請使用 [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) 並在您的實體類型中實作 [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx)、[INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) 與 [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8466d-148">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="8466d-149">回報問題</span><span class="sxs-lookup"><span data-stu-id="8466d-149">Report issues</span></span>

<span data-ttu-id="8466d-150">如有任何組合無法如預期運作，建議您在 [EF Core 問題追蹤器](https://github.com/aspnet/entityframeworkcore/issues/new)上建立新問題。</span><span class="sxs-lookup"><span data-stu-id="8466d-150">For any combination that doesn't work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/aspnet/entityframeworkcore/issues/new).</span></span> <span data-ttu-id="8466d-151">若為 Xamarin 相關的問題，請使用 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) 或 [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new)的問題追蹤器。</span><span class="sxs-lookup"><span data-stu-id="8466d-151">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>
