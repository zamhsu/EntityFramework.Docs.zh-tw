---
title: 撰寫資料庫提供者-EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 662c7e386bbdf0ff1e4e5051349d6a5c56a3df7b
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538463"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="891ac-102">撰寫資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="891ac-102">Writing a Database Provider</span></span>

<span data-ttu-id="891ac-103">如需有關撰寫 Entity Framework Core 資料庫提供者的詳細資訊，請參閱，以[Arthur Vickers](https://github.com/ajcvickers)[撰寫 EF Core 提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)。</span><span class="sxs-lookup"><span data-stu-id="891ac-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="891ac-104">這些文章在 EF Core 1.1 之後尚未更新，而且自該時間以來已經有重大變更。</span><span class="sxs-lookup"><span data-stu-id="891ac-104">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="891ac-105">[問題 681](https://github.com/dotnet/EntityFramework.Docs/issues/681)正在追蹤此檔的更新。</span><span class="sxs-lookup"><span data-stu-id="891ac-105">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="891ac-106">EF Core 程式碼基底是開放原始碼，包含數個可做為參考的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="891ac-106">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="891ac-107">您可以在<https://github.com/aspnet/EntityFrameworkCore>找到原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="891ac-107">You can find the source code at <https://github.com/aspnet/EntityFrameworkCore>.</span></span> <span data-ttu-id="891ac-108">查看常用協力廠商提供者的程式碼，例如[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)和[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)，可能也會很有説明。</span><span class="sxs-lookup"><span data-stu-id="891ac-108">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="891ac-109">特別是，這些專案會進行設定，以擴充並執行我們在 NuGet 上發行的功能測試。</span><span class="sxs-lookup"><span data-stu-id="891ac-109">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="891ac-110">強烈建議使用這種設定。</span><span class="sxs-lookup"><span data-stu-id="891ac-110">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="891ac-111">保持最新的提供者變更</span><span class="sxs-lookup"><span data-stu-id="891ac-111">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="891ac-112">從2.1 版後的工作開始，我們已建立可能需要提供者程式碼中對應變更的[變更記錄](provider-log.md)檔。</span><span class="sxs-lookup"><span data-stu-id="891ac-112">Starting with work after the 2.1 release, we have created a [log of changes](provider-log.md) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="891ac-113">這是為了在更新現有提供者以使用新版本的 EF Core 時提供協助。</span><span class="sxs-lookup"><span data-stu-id="891ac-113">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="891ac-114">在2.1 之前，我們在 GitHub [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)問題[`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)和提取要求上使用了和標籤，以取得類似的目的。</span><span class="sxs-lookup"><span data-stu-id="891ac-114">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="891ac-115">我們會 continiue 在問題上使用這些 lables，以指出給定版本中的工作專案可能也需要在提供者中完成工作。</span><span class="sxs-lookup"><span data-stu-id="891ac-115">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="891ac-116">`providers-beware`標籤通常表示工作專案的執行可能會中斷提供者，而`providers-fyi`標籤通常表示提供者不會中斷，但程式碼可能需要變更，例如啟用新功能。</span><span class="sxs-lookup"><span data-stu-id="891ac-116">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="891ac-117">協力廠商提供者的建議命名</span><span class="sxs-lookup"><span data-stu-id="891ac-117">Suggested naming of third party providers</span></span>

<span data-ttu-id="891ac-118">我們建議使用 NuGet 套件的下列命名。</span><span class="sxs-lookup"><span data-stu-id="891ac-118">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="891ac-119">這與 EF Core 小組所提供的封裝名稱一致。</span><span class="sxs-lookup"><span data-stu-id="891ac-119">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="891ac-120">例如：</span><span class="sxs-lookup"><span data-stu-id="891ac-120">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
