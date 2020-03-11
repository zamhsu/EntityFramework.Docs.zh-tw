---
title: Visual Studio 版本-EF6
author: divega
ms.date: 07/05/2018
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
ms.openlocfilehash: 16bcdc6d0e7c5632d4f4c06ba285a7a666f24204
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416937"
---
# <a name="visual-studio-releases"></a><span data-ttu-id="8d221-102">Visual Studio 版本</span><span class="sxs-lookup"><span data-stu-id="8d221-102">Visual Studio Releases</span></span>

<span data-ttu-id="8d221-103">我們建議您一律使用最新版本的 Visual Studio，因為它包含適用于 .NET、NuGet 和 Entity Framework 的最新工具。</span><span class="sxs-lookup"><span data-stu-id="8d221-103">We recommend to always use the latest version of Visual Studio because it contains the latest tools for .NET, NuGet, and Entity Framework.</span></span>
<span data-ttu-id="8d221-104">事實上，跨 Entity Framework 檔的各種範例和逐步解說會假設您使用的是最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="8d221-104">In fact, the various samples and walkthroughs across the Entity Framework documentation assume that you are using a recent version of Visual Studio.</span></span>

<span data-ttu-id="8d221-105">不過，您可以使用具有不同版本 Entity Framework 的舊版 Visual Studio，只要考慮一些差異即可：</span><span class="sxs-lookup"><span data-stu-id="8d221-105">It is possible however to use older versions of Visual Studio with different versions of Entity Framework as long as you take into account some differences:</span></span>

## <a name="visual-studio-2017-157-and-newer"></a><span data-ttu-id="8d221-106">Visual Studio 2017 15.7 和更新版本</span><span class="sxs-lookup"><span data-stu-id="8d221-106">Visual Studio 2017 15.7 and newer</span></span>

- <span data-ttu-id="8d221-107">這個版本的 Visual Studio 包含最新版本的 Entity Framework 工具和 EF 6.2 執行時間，而且不需要額外的設定步驟。</span><span class="sxs-lookup"><span data-stu-id="8d221-107">This version of Visual Studio includes the latest release of Entity Framework tools and the EF 6.2 runtime, and does not require additional setup steps.</span></span>
<span data-ttu-id="8d221-108">如需這些版本的詳細資訊，請參閱[新功能](~/ef6/what-is-new/index.md)。</span><span class="sxs-lookup"><span data-stu-id="8d221-108">See [What's New](~/ef6/what-is-new/index.md) for more details on these releases.</span></span>
- <span data-ttu-id="8d221-109">使用 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 6.2 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-109">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.2 NuGet package.</span></span>
<span data-ttu-id="8d221-110">您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-110">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="8d221-111">根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="8d221-111">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="8d221-112">[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\MSSQLLocalDB]。</span><span class="sxs-lookup"><span data-stu-id="8d221-112">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="8d221-113">在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="8d221-113">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a><span data-ttu-id="8d221-114">Visual Studio 2015 至 Visual Studio 2017 15。6</span><span class="sxs-lookup"><span data-stu-id="8d221-114">Visual Studio 2015 to Visual Studio 2017 15.6</span></span>

- <span data-ttu-id="8d221-115">這些版本的 Visual Studio 包括 Entity Framework 工具和執行時間之 ef6.1.3。</span><span class="sxs-lookup"><span data-stu-id="8d221-115">These versions of Visual Studio include Entity Framework tools and runtime 6.1.3.</span></span>
<span data-ttu-id="8d221-116">如需這些版本的詳細資訊，請參閱[過去的版本](~/ef6/what-is-new/past-releases.md#ef-613)。</span><span class="sxs-lookup"><span data-stu-id="8d221-116">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="8d221-117">使用 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 之 ef6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-117">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="8d221-118">您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-118">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="8d221-119">根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="8d221-119">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="8d221-120">[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\MSSQLLocalDB]。</span><span class="sxs-lookup"><span data-stu-id="8d221-120">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="8d221-121">在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="8d221-121">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2013"></a><span data-ttu-id="8d221-122">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="8d221-122">Visual Studio 2013</span></span>
- <span data-ttu-id="8d221-123">這個版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。</span><span class="sxs-lookup"><span data-stu-id="8d221-123">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="8d221-124">建議您使用 Microsoft 下載中心提供[的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762)，升級至 Entity Framework Tools 之 ef6.1.3。</span><span class="sxs-lookup"><span data-stu-id="8d221-124">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="8d221-125">如需這些版本的詳細資訊，請參閱[過去的版本](~/ef6/what-is-new/past-releases.md#ef-613)。</span><span class="sxs-lookup"><span data-stu-id="8d221-125">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="8d221-126">使用已升級的 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 之 ef6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-126">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="8d221-127">您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-127">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="8d221-128">根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="8d221-128">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="8d221-129">[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\MSSQLLocalDB]。</span><span class="sxs-lookup"><span data-stu-id="8d221-129">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="8d221-130">在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="8d221-130">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2012"></a><span data-ttu-id="8d221-131">Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="8d221-131">Visual Studio 2012</span></span>

- <span data-ttu-id="8d221-132">這個版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。</span><span class="sxs-lookup"><span data-stu-id="8d221-132">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="8d221-133">建議您使用 Microsoft 下載中心提供[的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762)，升級至 Entity Framework Tools 之 ef6.1.3。</span><span class="sxs-lookup"><span data-stu-id="8d221-133">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="8d221-134">如需這些版本的詳細資訊，請參閱[過去的版本](~/ef6/what-is-new/past-releases.md#ef-613)。</span><span class="sxs-lookup"><span data-stu-id="8d221-134">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="8d221-135">使用已升級的 EF 工具將 Entity Framework 新增至新的專案，將會自動新增 EF 之 ef6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-135">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="8d221-136">您可以手動安裝或升級至任何線上可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8d221-136">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="8d221-137">根據預設，這個版本的 Visual Studio 提供的 SQL Server 實例是一個稱為11.0 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="8d221-137">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called v11.0.</span></span>
<span data-ttu-id="8d221-138">[連接字串] 的 [伺服器] 區段應使用 [（localdb）\\11.0]。</span><span class="sxs-lookup"><span data-stu-id="8d221-138">The server section of connection string you should use is "(localdb)\\v11.0".</span></span>
<span data-ttu-id="8d221-139">在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="8d221-139">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2010"></a><span data-ttu-id="8d221-140">Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="8d221-140">Visual Studio 2010</span></span>

- <span data-ttu-id="8d221-141">這個版本的 Visual Studio 提供的 Entity Framework Tools 版本與 Entity Framework 6 執行時間不相容，因此無法升級。</span><span class="sxs-lookup"><span data-stu-id="8d221-141">The version of Entity Framework Tools available with this version of Visual Studio is not compatible with the Entity Framework 6 runtime and cannot be upgraded.</span></span>
- <span data-ttu-id="8d221-142">根據預設，Entity Framework 工具會將 Entity Framework 4.0 新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="8d221-142">By default, the Entity Framework tools will add Entity Framework 4.0 to your projects.</span></span>
<span data-ttu-id="8d221-143">若要使用任何較新版本的 EF 來建立應用程式，您必須先安裝[NuGet 套件管理員延伸](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)模組。</span><span class="sxs-lookup"><span data-stu-id="8d221-143">In order to create applications using any newer versions of EF, you will first need to install the [NuGet Package Manager extension](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager).</span></span>
- <span data-ttu-id="8d221-144">根據預設，EF tools 版本中的所有程式碼產生都是以 EntityObject 和 Entity Framework 4 為基礎。</span><span class="sxs-lookup"><span data-stu-id="8d221-144">By default, all code generation in the version of EF tools is based on EntityObject and Entity Framework 4.</span></span>
<span data-ttu-id="8d221-145">我們建議您安裝[C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)或[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)的 DbCoNtext 程式碼產生範本，以根據 DbCoNtext 和 Entity Framework 5 來切換程式碼產生。</span><span class="sxs-lookup"><span data-stu-id="8d221-145">We recommend that you switch the code generation to be based on DbContext and Entity Framework 5, by installing the DbContext code generation templates for [C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) or [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET).</span></span>
- <span data-ttu-id="8d221-146">安裝 NuGet 套件管理員延伸模組之後，您可以手動安裝或升級至任何線上可用的 EF NuGet 套件，並使用 EF6 搭配 Code First，這不需要設計工具。</span><span class="sxs-lookup"><span data-stu-id="8d221-146">Once you have installed the NuGet Package Manager extensions, you can manually install or upgrade to any EF NuGet package available online and use EF6 with Code First, which does not require a designer.</span></span>
- <span data-ttu-id="8d221-147">根據預設，此版本 Visual Studio 提供的 SQL Server 實例會 SQL Server Express 名為 SQLEXPRESS。</span><span class="sxs-lookup"><span data-stu-id="8d221-147">By default, the SQL Server instance available with this version of Visual Studio is SQL Server Express named SQLEXPRESS.</span></span>
<span data-ttu-id="8d221-148">[連接字串] 的 [伺服器] 區段應使用 []。\\SQLEXPRESS」。</span><span class="sxs-lookup"><span data-stu-id="8d221-148">The server section of connection string you should use is ".\\SQLEXPRESS".</span></span>
<span data-ttu-id="8d221-149">在程式碼中C#指定連接字串時，請記得使用前面加上 `@` 或雙斜線 "\\\\" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="8d221-149">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>
