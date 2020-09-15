---
title: Visual Studio 版本-EF6
description: Visual Studio 版本和 Entity Framework 6
author: divega
ms.date: 07/05/2018
uid: ef6/what-is-new/visual-studio
ms.openlocfilehash: 90982c49f47c8967cf3a3ed91fa51a392d94636f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073626"
---
# <a name="visual-studio-releases"></a><span data-ttu-id="11a79-103">Visual Studio 版本</span><span class="sxs-lookup"><span data-stu-id="11a79-103">Visual Studio Releases</span></span>

<span data-ttu-id="11a79-104">建議您一律使用最新版的 Visual Studio，因為它包含適用于 .NET、NuGet 和 Entity Framework 的最新工具。</span><span class="sxs-lookup"><span data-stu-id="11a79-104">We recommend to always use the latest version of Visual Studio because it contains the latest tools for .NET, NuGet, and Entity Framework.</span></span>
<span data-ttu-id="11a79-105">事實上，跨 Entity Framework 檔的各種範例和逐步解說都假設您使用的是最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="11a79-105">In fact, the various samples and walkthroughs across the Entity Framework documentation assume that you are using a recent version of Visual Studio.</span></span>

<span data-ttu-id="11a79-106">不過，您可以使用舊版 Visual Studio 搭配不同版本的 Entity Framework，只要考慮一些差異即可：</span><span class="sxs-lookup"><span data-stu-id="11a79-106">It is possible however to use older versions of Visual Studio with different versions of Entity Framework as long as you take into account some differences:</span></span>

## <a name="visual-studio-2017-157-and-newer"></a><span data-ttu-id="11a79-107">Visual Studio 2017 15.7 和更新版本</span><span class="sxs-lookup"><span data-stu-id="11a79-107">Visual Studio 2017 15.7 and newer</span></span>

- <span data-ttu-id="11a79-108">此版本的 Visual Studio 包含最新版本的 Entity Framework 工具和 EF 6.2 執行時間，而且不需要額外的設定步驟。</span><span class="sxs-lookup"><span data-stu-id="11a79-108">This version of Visual Studio includes the latest release of Entity Framework tools and the EF 6.2 runtime, and does not require additional setup steps.</span></span>
<span data-ttu-id="11a79-109">如需這些版本的詳細資訊，請參閱 [新功能](xref:ef6/what-is-new/index) 。</span><span class="sxs-lookup"><span data-stu-id="11a79-109">See [What's New](xref:ef6/what-is-new/index) for more details on these releases.</span></span>
- <span data-ttu-id="11a79-110">使用 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.2 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-110">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.2 NuGet package.</span></span>
<span data-ttu-id="11a79-111">您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-111">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="11a79-112">根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="11a79-112">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="11a79-113">您應使用之連接字串的 [伺服器] 區段為 " (localdb) \\ MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="11a79-113">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="11a79-114">`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="11a79-114">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a><span data-ttu-id="11a79-115">Visual Studio 2015 至 Visual Studio 2017 15。6</span><span class="sxs-lookup"><span data-stu-id="11a79-115">Visual Studio 2015 to Visual Studio 2017 15.6</span></span>

- <span data-ttu-id="11a79-116">這些版本的 Visual Studio 包括 Entity Framework 工具和執行時間6.1.3。</span><span class="sxs-lookup"><span data-stu-id="11a79-116">These versions of Visual Studio include Entity Framework tools and runtime 6.1.3.</span></span>
<span data-ttu-id="11a79-117">如需這些版本的詳細資訊，請參閱 [過去的版本](xref:ef6/what-is-new/past-releases#ef-613) 。</span><span class="sxs-lookup"><span data-stu-id="11a79-117">See [Past Releases](xref:ef6/what-is-new/past-releases#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="11a79-118">使用 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-118">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="11a79-119">您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-119">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="11a79-120">根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="11a79-120">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="11a79-121">您應使用之連接字串的 [伺服器] 區段為 " (localdb) \\ MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="11a79-121">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="11a79-122">`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="11a79-122">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2013"></a><span data-ttu-id="11a79-123">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="11a79-123">Visual Studio 2013</span></span>
- <span data-ttu-id="11a79-124">此版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。</span><span class="sxs-lookup"><span data-stu-id="11a79-124">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="11a79-125">建議您使用 Microsoft 下載中心提供 [的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762) ，升級到 Entity Framework Tools 6.1.3。</span><span class="sxs-lookup"><span data-stu-id="11a79-125">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="11a79-126">如需這些版本的詳細資訊，請參閱 [過去的版本](xref:ef6/what-is-new/past-releases#ef-613) 。</span><span class="sxs-lookup"><span data-stu-id="11a79-126">See [Past Releases](xref:ef6/what-is-new/past-releases#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="11a79-127">使用已升級的 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-127">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="11a79-128">您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-128">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="11a79-129">根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 MSSQLLocalDB 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="11a79-129">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="11a79-130">您應使用之連接字串的 [伺服器] 區段為 " (localdb) \\ MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="11a79-130">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="11a79-131">`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="11a79-131">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2012"></a><span data-ttu-id="11a79-132">Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="11a79-132">Visual Studio 2012</span></span>

- <span data-ttu-id="11a79-133">此版本的 Visual Studio 包含和舊版的 Entity Framework 工具和執行時間。</span><span class="sxs-lookup"><span data-stu-id="11a79-133">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="11a79-134">建議您使用 Microsoft 下載中心提供 [的安裝程式](https://www.microsoft.com/download/details.aspx?id=40762) ，升級到 Entity Framework Tools 6.1.3。</span><span class="sxs-lookup"><span data-stu-id="11a79-134">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="11a79-135">如需這些版本的詳細資訊，請參閱 [過去的版本](xref:ef6/what-is-new/past-releases#ef-613) 。</span><span class="sxs-lookup"><span data-stu-id="11a79-135">See [Past Releases](xref:ef6/what-is-new/past-releases#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="11a79-136">使用已升級的 EF tools 將 Entity Framework 新增至新專案，將會自動新增 EF 6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-136">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="11a79-137">您可以手動安裝或升級至任何可在線上使用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="11a79-137">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="11a79-138">根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例是一個稱為 v 11.0 的 LocalDB 實例。</span><span class="sxs-lookup"><span data-stu-id="11a79-138">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called v11.0.</span></span>
<span data-ttu-id="11a79-139">您應使用之連接字串的 [伺服器] 區段是 " (localdb) \\ v 11.0"。</span><span class="sxs-lookup"><span data-stu-id="11a79-139">The server section of connection string you should use is "(localdb)\\v11.0".</span></span>
<span data-ttu-id="11a79-140">`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="11a79-140">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2010"></a><span data-ttu-id="11a79-141">Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="11a79-141">Visual Studio 2010</span></span>

- <span data-ttu-id="11a79-142">此版本的 Visual Studio 可用 Entity Framework Tools 版本與 Entity Framework 6 執行時間不相容，因此無法升級。</span><span class="sxs-lookup"><span data-stu-id="11a79-142">The version of Entity Framework Tools available with this version of Visual Studio is not compatible with the Entity Framework 6 runtime and cannot be upgraded.</span></span>
- <span data-ttu-id="11a79-143">根據預設，Entity Framework 工具會將 Entity Framework 4.0 加入至您的專案。</span><span class="sxs-lookup"><span data-stu-id="11a79-143">By default, the Entity Framework tools will add Entity Framework 4.0 to your projects.</span></span>
<span data-ttu-id="11a79-144">為了使用任何較新版本的 EF 建立應用程式，您必須先安裝 [NuGet 封裝管理員擴充](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)功能。</span><span class="sxs-lookup"><span data-stu-id="11a79-144">In order to create applications using any newer versions of EF, you will first need to install the [NuGet Package Manager extension](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager).</span></span>
- <span data-ttu-id="11a79-145">根據預設，EF tools 版本中的所有程式碼產生都會以 EntityObject 和 Entity Framework 4 為基礎。</span><span class="sxs-lookup"><span data-stu-id="11a79-145">By default, all code generation in the version of EF tools is based on EntityObject and Entity Framework 4.</span></span>
<span data-ttu-id="11a79-146">建議您藉由安裝適用于 [c #](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) 或 [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)的 DbCoNtext 程式碼產生範本，將程式碼產生切換為以 DbCoNtext 和 Entity Framework 5 為基礎。</span><span class="sxs-lookup"><span data-stu-id="11a79-146">We recommend that you switch the code generation to be based on DbContext and Entity Framework 5, by installing the DbContext code generation templates for [C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) or [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET).</span></span>
- <span data-ttu-id="11a79-147">安裝 NuGet 封裝管理員擴充功能之後，您可以手動安裝或升級至任何線上可用的 EF NuGet 套件，並使用 EF6 搭配 Code First，而不需要設計工具。</span><span class="sxs-lookup"><span data-stu-id="11a79-147">Once you have installed the NuGet Package Manager extensions, you can manually install or upgrade to any EF NuGet package available online and use EF6 with Code First, which does not require a designer.</span></span>
- <span data-ttu-id="11a79-148">根據預設，這個版本的 Visual Studio 可用的 SQL Server 實例 SQL Server Express 名為 SQLEXPRESS。</span><span class="sxs-lookup"><span data-stu-id="11a79-148">By default, the SQL Server instance available with this version of Visual Studio is SQL Server Express named SQLEXPRESS.</span></span>
<span data-ttu-id="11a79-149">您應使用之連接字串的 [伺服器] 區段為 "。 \\SQLEXPRESS」。</span><span class="sxs-lookup"><span data-stu-id="11a79-149">The server section of connection string you should use is ".\\SQLEXPRESS".</span></span>
<span data-ttu-id="11a79-150">`@` \\ \\ 在 c # 程式碼中指定連接字串時，請記得使用前面加上或雙反斜線 "" 的逐字字串。</span><span class="sxs-lookup"><span data-stu-id="11a79-150">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>
