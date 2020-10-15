---
title: 記錄和攔截的總覽-EF Core
description: EF Core 的記錄、事件、攔截器和診斷總覽
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066596"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="0f628-103">記錄和攔截的總覽</span><span class="sxs-lookup"><span data-stu-id="0f628-103">Overview of logging and interception</span></span>

<span data-ttu-id="0f628-104">Entity Framework Core (EF Core) 包含數個產生記錄、回應事件和取得診斷的機制。</span><span class="sxs-lookup"><span data-stu-id="0f628-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="0f628-105">這些都是針對不同的情況而量身訂做的，因此請務必為手邊的工作選取最佳的機制，即使有多個機制可以運作也是一樣。</span><span class="sxs-lookup"><span data-stu-id="0f628-105">Each of these are tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="0f628-106">例如，資料庫攔截器可以用來記錄 SQL，但是這種方式較適合用來處理其中一個記錄特定機制。</span><span class="sxs-lookup"><span data-stu-id="0f628-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the logging-specific mechanisms.</span></span> <span data-ttu-id="0f628-107">此頁面提供每個機制的總覽，並說明每個機制的使用時機。</span><span class="sxs-lookup"><span data-stu-id="0f628-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="0f628-108">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="0f628-108">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="0f628-109">這項功能已在 EF Core 5.0 中新增。</span><span class="sxs-lookup"><span data-stu-id="0f628-109">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="0f628-110">您可以使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)從任何類型的應用程式存取 EF Core 記錄</span><span class="sxs-lookup"><span data-stu-id="0f628-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="0f628-111">設定 [DbCoNtext 實例](xref:core/miscellaneous/configuring-dbcontext)時。</span><span class="sxs-lookup"><span data-stu-id="0f628-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="0f628-112">這項設定通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="0f628-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0f628-113">例如︰</span><span class="sxs-lookup"><span data-stu-id="0f628-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="0f628-114">此概念類似于 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6。</span><span class="sxs-lookup"><span data-stu-id="0f628-114">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="0f628-115">如需詳細資訊，請參閱 [簡單記錄](xref:core/miscellaneous/events/simple-logging) 。</span><span class="sxs-lookup"><span data-stu-id="0f628-115">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>
