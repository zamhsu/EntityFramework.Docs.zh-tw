---
title: 記錄和攔截的總覽-EF Core
description: EF Core 的記錄、事件、攔截器和診斷總覽
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: d85a506167661523bf70b62d3a075a6248180d11
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635675"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="aaebe-103">記錄和攔截的總覽</span><span class="sxs-lookup"><span data-stu-id="aaebe-103">Overview of Logging and Interception</span></span>

<span data-ttu-id="aaebe-104">Entity Framework Core (EF Core) 包含數個產生記錄、回應事件和取得診斷的機制。</span><span class="sxs-lookup"><span data-stu-id="aaebe-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="aaebe-105">這些都是針對不同的情況而量身訂做的，因此請務必為手邊的工作選取最佳的機制，即使有多個機制可以運作也是一樣。</span><span class="sxs-lookup"><span data-stu-id="aaebe-105">Each of these is tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="aaebe-106">例如，資料庫攔截器可以用來記錄 SQL，但是這種方式較適合用來記錄的其中一種機制來處理。</span><span class="sxs-lookup"><span data-stu-id="aaebe-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the mechanisms tailored to logging.</span></span> <span data-ttu-id="aaebe-107">此頁面提供每個機制的總覽，並說明每個機制的使用時機。</span><span class="sxs-lookup"><span data-stu-id="aaebe-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="quick-reference"></a><span data-ttu-id="aaebe-108">快速參考</span><span class="sxs-lookup"><span data-stu-id="aaebe-108">Quick reference</span></span>

<span data-ttu-id="aaebe-109">下表提供此處所述機制之間差異的快速參考。</span><span class="sxs-lookup"><span data-stu-id="aaebe-109">The table below provides a quick reference for the differences between the mechanisms described here.</span></span>

| <span data-ttu-id="aaebe-110">機制</span><span class="sxs-lookup"><span data-stu-id="aaebe-110">Mechanism</span></span> |  <span data-ttu-id="aaebe-111">Async</span><span class="sxs-lookup"><span data-stu-id="aaebe-111">Async</span></span> | <span data-ttu-id="aaebe-112">影響範圍</span><span class="sxs-lookup"><span data-stu-id="aaebe-112">Scope</span></span> | <span data-ttu-id="aaebe-113">已登錄</span><span class="sxs-lookup"><span data-stu-id="aaebe-113">Registered</span></span> | <span data-ttu-id="aaebe-114">預定用途</span><span class="sxs-lookup"><span data-stu-id="aaebe-114">Intended use</span></span>
|:----------|--------|-------|------------|-------------
| <span data-ttu-id="aaebe-115">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="aaebe-115">Simple Logging</span></span> | <span data-ttu-id="aaebe-116">否</span><span class="sxs-lookup"><span data-stu-id="aaebe-116">No</span></span> | <span data-ttu-id="aaebe-117">每個內容</span><span class="sxs-lookup"><span data-stu-id="aaebe-117">Per context</span></span> | <span data-ttu-id="aaebe-118">內容設定</span><span class="sxs-lookup"><span data-stu-id="aaebe-118">Context configuration</span></span> | <span data-ttu-id="aaebe-119">開發階段記錄</span><span class="sxs-lookup"><span data-stu-id="aaebe-119">Development-time logging</span></span>
| <span data-ttu-id="aaebe-120">Microsoft.Extensions.Logging</span><span class="sxs-lookup"><span data-stu-id="aaebe-120">Microsoft.Extensions.Logging</span></span> | <span data-ttu-id="aaebe-121">否</span><span class="sxs-lookup"><span data-stu-id="aaebe-121">No</span></span> | <span data-ttu-id="aaebe-122">每個內容 \*</span><span class="sxs-lookup"><span data-stu-id="aaebe-122">Per context\*</span></span> | <span data-ttu-id="aaebe-123">D.I.</span><span class="sxs-lookup"><span data-stu-id="aaebe-123">D.I.</span></span> <span data-ttu-id="aaebe-124">或內容設定</span><span class="sxs-lookup"><span data-stu-id="aaebe-124">or context configuration</span></span> | <span data-ttu-id="aaebe-125">生產記錄</span><span class="sxs-lookup"><span data-stu-id="aaebe-125">Production logging</span></span>
| <span data-ttu-id="aaebe-126">事件</span><span class="sxs-lookup"><span data-stu-id="aaebe-126">Events</span></span> | <span data-ttu-id="aaebe-127">否</span><span class="sxs-lookup"><span data-stu-id="aaebe-127">No</span></span> | <span data-ttu-id="aaebe-128">每個內容</span><span class="sxs-lookup"><span data-stu-id="aaebe-128">Per context</span></span> | <span data-ttu-id="aaebe-129">任何時間</span><span class="sxs-lookup"><span data-stu-id="aaebe-129">Any time</span></span> | <span data-ttu-id="aaebe-130">回應 EF 事件</span><span class="sxs-lookup"><span data-stu-id="aaebe-130">Reacting to EF events</span></span>
| <span data-ttu-id="aaebe-131">攔截器</span><span class="sxs-lookup"><span data-stu-id="aaebe-131">Interceptors</span></span> | <span data-ttu-id="aaebe-132">是</span><span class="sxs-lookup"><span data-stu-id="aaebe-132">Yes</span></span> | <span data-ttu-id="aaebe-133">每個內容</span><span class="sxs-lookup"><span data-stu-id="aaebe-133">Per context</span></span> | <span data-ttu-id="aaebe-134">內容設定</span><span class="sxs-lookup"><span data-stu-id="aaebe-134">Context configuration</span></span> | <span data-ttu-id="aaebe-135">操作 EF 作業</span><span class="sxs-lookup"><span data-stu-id="aaebe-135">Manipulating EF operations</span></span>
| <span data-ttu-id="aaebe-136">診斷接聽程式</span><span class="sxs-lookup"><span data-stu-id="aaebe-136">Diagnostics listeners</span></span> | <span data-ttu-id="aaebe-137">否</span><span class="sxs-lookup"><span data-stu-id="aaebe-137">No</span></span> | <span data-ttu-id="aaebe-138">Process</span><span class="sxs-lookup"><span data-stu-id="aaebe-138">Process</span></span> | <span data-ttu-id="aaebe-139">全域</span><span class="sxs-lookup"><span data-stu-id="aaebe-139">Globally</span></span> | <span data-ttu-id="aaebe-140">應用程式診斷</span><span class="sxs-lookup"><span data-stu-id="aaebe-140">Application diagnostics</span></span>

<span data-ttu-id="aaebe-141">\* 通常是透過相依性 `Microsoft.Extensions.Logging` 插入來設定每個應用程式，但在 EF 層級，您可以視需要使用不同的記錄器 _來_ 設定每個內容。</span><span class="sxs-lookup"><span data-stu-id="aaebe-141">\*Typically `Microsoft.Extensions.Logging` is configured per-application via dependency injection However, at the EF level, each context _can_ be configured with a different logger if needed.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="aaebe-142">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="aaebe-142">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="aaebe-143">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="aaebe-143">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="aaebe-144">在設定 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> [DbCoNtext 實例](xref:core/dbcontext-configuration/index)時，可以使用任何類型的應用程式存取 EF Core 記錄。</span><span class="sxs-lookup"><span data-stu-id="aaebe-144">EF Core logs can be accessed from any type of application through the use of <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="aaebe-145">這項設定通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="aaebe-145">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="aaebe-146">例如：</span><span class="sxs-lookup"><span data-stu-id="aaebe-146">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="aaebe-147">此概念類似于 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6。</span><span class="sxs-lookup"><span data-stu-id="aaebe-147">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="aaebe-148">如需詳細資訊，請參閱 [簡單記錄](xref:core/logging-events-diagnostics/simple-logging) 。</span><span class="sxs-lookup"><span data-stu-id="aaebe-148">See [Simple Logging](xref:core/logging-events-diagnostics/simple-logging) for more information.</span></span>

## <a name="microsoftextensionslogging"></a><span data-ttu-id="aaebe-149">Microsoft.Extensions.Logging</span><span class="sxs-lookup"><span data-stu-id="aaebe-149">Microsoft.Extensions.Logging</span></span>

<span data-ttu-id="aaebe-150">針對許多常見的記錄系統，記錄是具有外掛程式提供者的可[延伸](/dotnet/core/extensions/logging)記錄機制。</span><span class="sxs-lookup"><span data-stu-id="aaebe-150">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="aaebe-151">EF Core 與完全整合 `Microsoft.Extensions.Logging` ，並且預設會針對 ASP.NET Core 應用程式使用這種形式的記錄。</span><span class="sxs-lookup"><span data-stu-id="aaebe-151">EF Core fully integrates with `Microsoft.Extensions.Logging` and this form of logging is used by default for ASP.NET Core applications.</span></span>

<span data-ttu-id="aaebe-152">如需詳細資訊，請參閱 [使用 EF Core 中的登入](xref:core/logging-events-diagnostics/extensions-logging) 。</span><span class="sxs-lookup"><span data-stu-id="aaebe-152">See [Using Microsoft.Extensions.Logging in EF Core](xref:core/logging-events-diagnostics/extensions-logging) for more information.</span></span>

## <a name="events"></a><span data-ttu-id="aaebe-153">事件</span><span class="sxs-lookup"><span data-stu-id="aaebe-153">Events</span></span>

> [!NOTE]
> <span data-ttu-id="aaebe-154">EF Core 5.0 中引進了其他事件。</span><span class="sxs-lookup"><span data-stu-id="aaebe-154">Additional events were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="aaebe-155">EF Core 會在 EF Core 程式碼中發生特定情況時，公開 [.net 事件](/dotnet/standard/events/) 以作為回呼。</span><span class="sxs-lookup"><span data-stu-id="aaebe-155">EF Core exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="aaebe-156">事件比攔截器更簡單，並允許更具彈性的註冊。</span><span class="sxs-lookup"><span data-stu-id="aaebe-156">Events are simpler than interceptors and allow more flexible registration.</span></span> <span data-ttu-id="aaebe-157">不過，它們只會進行同步處理，因此無法執行非封鎖的非同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="aaebe-157">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="aaebe-158">每個 DbCoNtext 實例都會註冊事件，而且可以隨時完成此註冊。</span><span class="sxs-lookup"><span data-stu-id="aaebe-158">Events are registered per DbContext instance and this registration can be done at any time.</span></span> <span data-ttu-id="aaebe-159">您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="aaebe-159">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

<span data-ttu-id="aaebe-160">如需詳細資訊，請參閱 [EF Core 中的 .Net 事件](xref:core/logging-events-diagnostics/events) 。</span><span class="sxs-lookup"><span data-stu-id="aaebe-160">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

## <a name="interception"></a><span data-ttu-id="aaebe-161">Interception</span><span class="sxs-lookup"><span data-stu-id="aaebe-161">Interception</span></span>

> [!NOTE]
> <span data-ttu-id="aaebe-162">這項功能是在 EF Core 3.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="aaebe-162">This feature was introduced in EF Core 3.0.</span></span> <span data-ttu-id="aaebe-163">其他攔截器是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="aaebe-163">Additional interceptors were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="aaebe-164">EF Core 攔截器可以攔截、修改及/或隱藏 EF Core 作業。</span><span class="sxs-lookup"><span data-stu-id="aaebe-164">EF Core interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="aaebe-165">這包括執行命令等低層級的資料庫作業，以及較高層級的作業，例如對 SaveChanges 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="aaebe-165">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="aaebe-166">攔截器與記錄和診斷不同，因為它們允許修改或隱藏正在攔截的作業。</span><span class="sxs-lookup"><span data-stu-id="aaebe-166">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="aaebe-167">[簡易記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft Extensions。記錄](xref:core/logging-events-diagnostics/extensions-logging) 是較佳的記錄選擇。</span><span class="sxs-lookup"><span data-stu-id="aaebe-167">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="aaebe-168">當設定內容時，會針對每個 DbCoNtext 實例註冊攔截器。</span><span class="sxs-lookup"><span data-stu-id="aaebe-168">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="aaebe-169">您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="aaebe-169">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

<span data-ttu-id="aaebe-170">如需詳細資訊，請參閱 [攔截](xref:core/logging-events-diagnostics/interceptors) 。</span><span class="sxs-lookup"><span data-stu-id="aaebe-170">See [Interception](xref:core/logging-events-diagnostics/interceptors) for more information.</span></span>

## <a name="diagnostic-listeners"></a><span data-ttu-id="aaebe-171">診斷接聽程式</span><span class="sxs-lookup"><span data-stu-id="aaebe-171">Diagnostic listeners</span></span>

<span data-ttu-id="aaebe-172">診斷接聽項允許接聽目前 .NET 進程中發生的任何 EF Core 事件。</span><span class="sxs-lookup"><span data-stu-id="aaebe-172">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span>

<span data-ttu-id="aaebe-173">診斷接聽程式不適合從單一 DbCoNtext 實例取得事件。</span><span class="sxs-lookup"><span data-stu-id="aaebe-173">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="aaebe-174">EF Core 攔截器提供對每個內容註冊之相同事件的存取權。</span><span class="sxs-lookup"><span data-stu-id="aaebe-174">EF Core interceptors provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="aaebe-175">診斷接聽程式並非設計來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="aaebe-175">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="aaebe-176">[簡易記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft Extensions。記錄](xref:core/logging-events-diagnostics/extensions-logging) 是較佳的記錄選擇。</span><span class="sxs-lookup"><span data-stu-id="aaebe-176">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="aaebe-177">如需詳細資訊，請參閱 [使用 EF Core 中的診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式。</span><span class="sxs-lookup"><span data-stu-id="aaebe-177">See [Using diagnostic listeners in EF Core](xref:core/logging-events-diagnostics/diagnostic-listeners) for more information.</span></span>
