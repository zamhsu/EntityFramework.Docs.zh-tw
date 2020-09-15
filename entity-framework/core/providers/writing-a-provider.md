---
title: 撰寫資料庫提供者-EF Core
description: 撰寫新的 Entity Framework Core 提供者的資訊
author: anmiller
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: b6054696711eb4bf865841428f58ca41791676f9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071208"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="fe85d-103">撰寫資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="fe85d-103">Writing a Database Provider</span></span>

<span data-ttu-id="fe85d-104">如需撰寫 Entity Framework Core 資料庫提供者的詳細資訊，請參閱，您想要透過[Arthur Vickers](https://github.com/ajcvickers)[來撰寫 EF Core 提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)。</span><span class="sxs-lookup"><span data-stu-id="fe85d-104">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="fe85d-105">這些貼文自 EF Core 1.1 以來尚未更新，並且自這段時間起已經有重大變更。</span><span class="sxs-lookup"><span data-stu-id="fe85d-105">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="fe85d-106">[問題 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) 正在追蹤此檔的更新。</span><span class="sxs-lookup"><span data-stu-id="fe85d-106">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="fe85d-107">EF Core 程式碼基底是開放原始碼，並且包含數個可當做參考使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="fe85d-107">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="fe85d-108">您可以在中找到原始程式碼 <https://github.com/aspnet/EntityFrameworkCore> 。</span><span class="sxs-lookup"><span data-stu-id="fe85d-108">You can find the source code at <https://github.com/aspnet/EntityFrameworkCore>.</span></span> <span data-ttu-id="fe85d-109">查看常用協力廠商提供者（例如 [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)和 [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)）的程式碼可能也會很有説明。</span><span class="sxs-lookup"><span data-stu-id="fe85d-109">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="fe85d-110">尤其是，這些專案會設定為從 NuGet 上發佈的功能測試進行擴充和執行。</span><span class="sxs-lookup"><span data-stu-id="fe85d-110">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="fe85d-111">強烈建議使用這種設定。</span><span class="sxs-lookup"><span data-stu-id="fe85d-111">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="fe85d-112">透過提供者變更保持最新狀態</span><span class="sxs-lookup"><span data-stu-id="fe85d-112">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="fe85d-113">從2.1 版之後的工作開始，我們已建立可能需要在提供者程式碼中對應變更的 [變更記錄](xref:core/providers/provider-log) 檔。</span><span class="sxs-lookup"><span data-stu-id="fe85d-113">Starting with work after the 2.1 release, we have created a [log of changes](xref:core/providers/provider-log) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="fe85d-114">這是為了在更新現有的提供者以使用新版 EF Core 時提供協助。</span><span class="sxs-lookup"><span data-stu-id="fe85d-114">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="fe85d-115">在2.1 之前，我們 [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) 在 GitHub 問題上使用和標籤，並以類似的目的提取要求。</span><span class="sxs-lookup"><span data-stu-id="fe85d-115">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="fe85d-116">我們將 continiue 使用這些 lables 來解決問題，以指出指定版本中的哪些工作專案可能也需要在提供者中完成工作。</span><span class="sxs-lookup"><span data-stu-id="fe85d-116">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="fe85d-117">`providers-beware`標籤通常表示工作專案的執行可能會中斷提供者，而 `providers-fyi` 標籤通常表示提供者不會中斷，但是程式碼可能需要變更，例如啟用新的功能。</span><span class="sxs-lookup"><span data-stu-id="fe85d-117">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="fe85d-118">協力廠商提供者的建議命名</span><span class="sxs-lookup"><span data-stu-id="fe85d-118">Suggested naming of third party providers</span></span>

<span data-ttu-id="fe85d-119">建議您針對 NuGet 套件使用下列命名。</span><span class="sxs-lookup"><span data-stu-id="fe85d-119">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="fe85d-120">這與 EF Core 小組提供的封裝名稱一致。</span><span class="sxs-lookup"><span data-stu-id="fe85d-120">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="fe85d-121">例如：</span><span class="sxs-lookup"><span data-stu-id="fe85d-121">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
