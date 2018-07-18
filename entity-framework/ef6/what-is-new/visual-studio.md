---
title: Visual Studio 版本-EF6
author: divega
ms.date: 2018-07-05
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
caps.latest.revision: 3
ms.openlocfilehash: 7bd08a46b1d6acc5a565952e834f01546a5262c8
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120224"
---
# <a name="visual-studio-releases"></a><span data-ttu-id="68b8d-102">Visual Studio 版本</span><span class="sxs-lookup"><span data-stu-id="68b8d-102">Visual Studio Releases</span></span>

<span data-ttu-id="68b8d-103">我們建議一律使用最新版本的 Visual Studio，因為它包含適用於.NET、 NuGet 和 Entity Framework 的最新的工具。</span><span class="sxs-lookup"><span data-stu-id="68b8d-103">We recommend to always use the latest version of Visual Studio because it contains the latest tools for .NET, NuGet, and Entity Framework.</span></span>
<span data-ttu-id="68b8d-104">事實上，各種範例和逐步解說在 Entity Framework 文件假設您使用的 Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="68b8d-104">In fact, the various samples and walkthroughs across the Entity Framework documentation assume that you are using a recent version of Visual Studio.</span></span>

<span data-ttu-id="68b8d-105">它為可能的不過若要使用較舊版本的 Visual Studio 與不同版本的 Entity Framework，只要您考慮到帳戶的一些差異：</span><span class="sxs-lookup"><span data-stu-id="68b8d-105">It is possible however to use older versions of Visual Studio with different versions of Entity Framework as long as you take into account some differences:</span></span>

## <a name="visual-studio-2017-157-and-newer"></a><span data-ttu-id="68b8d-106">Visual Studio 2017 15.7年及更新版本</span><span class="sxs-lookup"><span data-stu-id="68b8d-106">Visual Studio 2017 15.7 and newer</span></span>

- <span data-ttu-id="68b8d-107">此版本的 Visual Studio 包含最新版的 Entity Framework 工具和 EF 6.2 執行階段，而且不需要其他設定步驟。</span><span class="sxs-lookup"><span data-stu-id="68b8d-107">This version of Visual Studio includes the latest release of Entity Framework tools and the EF 6.2 runtime, and does not require additional setup steps.</span></span>
<span data-ttu-id="68b8d-108">請參閱[What's New](~/ef6/what-is-new/index.md)如需詳細資訊，這些版本上。</span><span class="sxs-lookup"><span data-stu-id="68b8d-108">See [What's New](~/ef6/what-is-new/index.md) for more details on these releases.</span></span>
- <span data-ttu-id="68b8d-109">加入新的專案使用的 EF 工具中的 Entity Framework 會自動加入 EF 6.2 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-109">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.2 NuGet package.</span></span>
<span data-ttu-id="68b8d-110">您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-110">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="68b8d-111">根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 LocalDB 執行個體呼叫 MSSQLLocalDB。</span><span class="sxs-lookup"><span data-stu-id="68b8d-111">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="68b8d-112">您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="68b8d-112">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="68b8d-113">請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。</span><span class="sxs-lookup"><span data-stu-id="68b8d-113">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a><span data-ttu-id="68b8d-114">Visual Studio 2015 到 Visual Studio 2017 15.6</span><span class="sxs-lookup"><span data-stu-id="68b8d-114">Visual Studio 2015 to Visual Studio 2017 15.6</span></span>

- <span data-ttu-id="68b8d-115">這些版本的 Visual Studio 包括 Entity Framework 工具和執行階段 6.1.3。</span><span class="sxs-lookup"><span data-stu-id="68b8d-115">These versions of Visual Studio include Entity Framework tools and runtime 6.1.3.</span></span>
<span data-ttu-id="68b8d-116">請參閱[過去釋放](~/ef6/what-is-new/past-releases.md#ef-613)如需詳細資訊，這些版本上。</span><span class="sxs-lookup"><span data-stu-id="68b8d-116">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="68b8d-117">加入新的專案使用的 EF 工具中的 Entity Framework 會自動將新增 EF 6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-117">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="68b8d-118">您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-118">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="68b8d-119">根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 LocalDB 執行個體呼叫 MSSQLLocalDB。</span><span class="sxs-lookup"><span data-stu-id="68b8d-119">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="68b8d-120">您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="68b8d-120">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="68b8d-121">請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。</span><span class="sxs-lookup"><span data-stu-id="68b8d-121">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2013"></a><span data-ttu-id="68b8d-122">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="68b8d-122">Visual Studio 2013</span></span>
- <span data-ttu-id="68b8d-123">此版本的 Visual Studio 包含和舊版本的 Entity Framework 工具和執行階段。</span><span class="sxs-lookup"><span data-stu-id="68b8d-123">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="68b8d-124">建議您升級至 Entity Framework Tools 6.1.3，使用[安裝程式](https://www.microsoft.com/en-us/download/details.aspx?id=40762)提供 Microsoft 下載中心取得。</span><span class="sxs-lookup"><span data-stu-id="68b8d-124">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/en-us/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="68b8d-125">請參閱[過去釋放](~/ef6/what-is-new/past-releases.md#ef-613)如需詳細資訊，這些版本上。</span><span class="sxs-lookup"><span data-stu-id="68b8d-125">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="68b8d-126">加入新的專案使用的已升級的 EF 工具中的 Entity Framework 會自動將新增 EF 6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-126">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="68b8d-127">您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-127">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="68b8d-128">根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 LocalDB 執行個體呼叫 MSSQLLocalDB。</span><span class="sxs-lookup"><span data-stu-id="68b8d-128">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="68b8d-129">您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="68b8d-129">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="68b8d-130">請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。</span><span class="sxs-lookup"><span data-stu-id="68b8d-130">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2012"></a><span data-ttu-id="68b8d-131">Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="68b8d-131">Visual Studio 2012</span></span>

- <span data-ttu-id="68b8d-132">此版本的 Visual Studio 包含和舊版本的 Entity Framework 工具和執行階段。</span><span class="sxs-lookup"><span data-stu-id="68b8d-132">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="68b8d-133">建議您升級至 Entity Framework Tools 6.1.3，使用[安裝程式](https://www.microsoft.com/en-us/download/details.aspx?id=40762)提供 Microsoft 下載中心取得。</span><span class="sxs-lookup"><span data-stu-id="68b8d-133">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/en-us/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="68b8d-134">請參閱[過去釋放](~/ef6/what-is-new/past-releases.md#ef-613)如需詳細資訊，這些版本上。</span><span class="sxs-lookup"><span data-stu-id="68b8d-134">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="68b8d-135">加入新的專案使用的已升級的 EF 工具中的 Entity Framework 會自動將新增 EF 6.1.3 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-135">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="68b8d-136">您可以手動安裝，或線上升級至任何可用的 EF NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="68b8d-136">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="68b8d-137">根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是呼叫 v11.0 的 LocalDB 執行個體。</span><span class="sxs-lookup"><span data-stu-id="68b8d-137">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called v11.0.</span></span>
<span data-ttu-id="68b8d-138">您應該使用的連接字串的 [伺服器] 區段是"(localdb)\\v11.0"。</span><span class="sxs-lookup"><span data-stu-id="68b8d-138">The server section of connection string you should use is "(localdb)\\v11.0".</span></span>
<span data-ttu-id="68b8d-139">請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。</span><span class="sxs-lookup"><span data-stu-id="68b8d-139">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2010"></a><span data-ttu-id="68b8d-140">Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="68b8d-140">Visual Studio 2010</span></span>

- <span data-ttu-id="68b8d-141">使用此版本的 Visual Studio 可用的 Entity Framework Tools 的版本與 Entity Framework 6 執行階段不相容，而且無法升級。</span><span class="sxs-lookup"><span data-stu-id="68b8d-141">The version of Entity Framework Tools available with this version of Visual Studio is not compatible with the Entity Framework 6 runtime and cannot be upgraded.</span></span>
- <span data-ttu-id="68b8d-142">根據預設，Entity Framework 工具會將 Entity Framework 4.0，加入您的專案。</span><span class="sxs-lookup"><span data-stu-id="68b8d-142">By default, the Entity Framework tools will add Entity Framework 4.0 to your projects.</span></span>
<span data-ttu-id="68b8d-143">若要建立應用程式使用 EF 的任何較新版本，就必須先安裝[NuGet 套件管理員延伸模組](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)。</span><span class="sxs-lookup"><span data-stu-id="68b8d-143">In order to create applications using any newer versions of EF, you will first need to install the [NuGet Package Manager extension](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager).</span></span>
- <span data-ttu-id="68b8d-144">根據預設，EF 工具的版本中的所有程式碼產生根據 EntityObject 和 Entity Framework 4。</span><span class="sxs-lookup"><span data-stu-id="68b8d-144">By default, all code generation in the version of EF tools is based on EntityObject and Entity Framework 4.</span></span>
<span data-ttu-id="68b8d-145">建議您改用程式碼產生是依據 DbContext 和 Entity Framework 5，藉由安裝 DbContext 的程式碼產生範本，如[C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)或是[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)。</span><span class="sxs-lookup"><span data-stu-id="68b8d-145">We recommend that you switch the code generation to be based on DbContext and Entity Framework 5, by installing the DbContext code generation templates for [C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) or [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET).</span></span>
- <span data-ttu-id="68b8d-146">一旦您已安裝的 NuGet 套件管理員延伸模組，您可以手動安裝或線上升級至任何可用的 EF NuGet 套件並使用 EF6 Code First，不需要設計工具。</span><span class="sxs-lookup"><span data-stu-id="68b8d-146">Once you have installed the NuGet Package Manager extensions, you can manually install or upgrade to any EF NuGet package available online and use EF6 with Code First, which does not require a designer.</span></span>
- <span data-ttu-id="68b8d-147">根據預設，適用於此版本的 Visual Studio 的 SQL Server 執行個體是 SQL Server Express 名為 SQLEXPRESS。</span><span class="sxs-lookup"><span data-stu-id="68b8d-147">By default, the SQL Server instance available with this version of Visual Studio is SQL Server Express named SQLEXPRESS.</span></span>
<span data-ttu-id="68b8d-148">您應該使用的連接字串的 [伺服器] 區段是"。\\SQLEXPRESS"。</span><span class="sxs-lookup"><span data-stu-id="68b8d-148">The server section of connection string you should use is ".\\SQLEXPRESS".</span></span>
<span data-ttu-id="68b8d-149">請務必使用逐字字串前面會加上`@`字元或雙反斜線"\\\\"C# 程式碼中指定的連接字串時。</span><span class="sxs-lookup"><span data-stu-id="68b8d-149">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>
