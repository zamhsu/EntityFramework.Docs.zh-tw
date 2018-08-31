---
title: 撰寫資料庫提供者的 EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: c7130b0d104cd26584d298da98eb3e7080ee7f3c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993662"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="1012b-102">撰寫資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="1012b-102">Writing a Database Provider</span></span>

<span data-ttu-id="1012b-103">撰寫 Entity Framework Core 資料庫提供者的相關資訊，請參閱[因此您想要撰寫的 EF Core 提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)由[Arthur Vickers](https://github.com/ajcvickers)。</span><span class="sxs-lookup"><span data-stu-id="1012b-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="1012b-104">這些文章有尚未更新從 EF Core 1.1，而且已有重大變更自那時起[問題 681](https://github.com/aspnet/EntityFramework.Docs/issues/681)正在追蹤更新這份文件。</span><span class="sxs-lookup"><span data-stu-id="1012b-104">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time [Issue 681](https://github.com/aspnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="1012b-105">EF Core 程式碼基底是開放原始碼，並包含數個可做為參考資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="1012b-105">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="1012b-106">您可以找到原始程式碼https://github.com/aspnet/EntityFrameworkCore。</span><span class="sxs-lookup"><span data-stu-id="1012b-106">You can find the source code at https://github.com/aspnet/EntityFrameworkCore.</span></span> <span data-ttu-id="1012b-107">它也可能有助於查看程式碼常用的協力廠商提供者，例如[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)， [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)，並[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)。</span><span class="sxs-lookup"><span data-stu-id="1012b-107">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="1012b-108">特別是，這些專案已設定，以從擴充，並在 NuGet 上執行我們所發行的功能測試。</span><span class="sxs-lookup"><span data-stu-id="1012b-108">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="1012b-109">強烈建議這種安裝程式。</span><span class="sxs-lookup"><span data-stu-id="1012b-109">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="1012b-110">保持最新提供者變更</span><span class="sxs-lookup"><span data-stu-id="1012b-110">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="1012b-111">在 2.1 版之後，以啟動工作，我們建立了[之變更的記錄](provider-log.md)，可能需要在提供者程式碼中對應的變更。</span><span class="sxs-lookup"><span data-stu-id="1012b-111">Starting with work after the 2.1 release, we have created a [log of changes](provider-log.md) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="1012b-112">這被為了協助更新現有的提供者時，才能使用 EF Core 的新版本。</span><span class="sxs-lookup"><span data-stu-id="1012b-112">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="1012b-113">在 2.1 之前, 我們會使用[ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)並[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)我們的 GitHub 問題和類似的目的的提取要求中的標籤。</span><span class="sxs-lookup"><span data-stu-id="1012b-113">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="1012b-114">我們將 continiue 問題上使用這些標籤，來指出在指定的版本中的工作項目可能也需要在提供者中的工作。</span><span class="sxs-lookup"><span data-stu-id="1012b-114">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="1012b-115">A`providers-beware`標籤通常表示工作項目的實作可能會中斷提供者，雖然`providers-fyi`標籤通常會表示提供者並不會中斷，但程式碼可能需要變更，例如，若要啟用新功能。</span><span class="sxs-lookup"><span data-stu-id="1012b-115">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="1012b-116">建議的命名的協力廠商提供者</span><span class="sxs-lookup"><span data-stu-id="1012b-116">Suggested naming of third party providers</span></span>

<span data-ttu-id="1012b-117">我們建議使用 NuGet 套件的下列命名。</span><span class="sxs-lookup"><span data-stu-id="1012b-117">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="1012b-118">這是與 EF Core 小組所傳遞的封裝的名稱一致。</span><span class="sxs-lookup"><span data-stu-id="1012b-118">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="1012b-119">例如: </span><span class="sxs-lookup"><span data-stu-id="1012b-119">For example:</span></span>
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
