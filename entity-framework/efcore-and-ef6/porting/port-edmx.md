---
title: 從 EF6 移植到 EF Core-移植以 EDMX 為基礎的模型-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: f0bb06dc687aaa774981d97daadc55f00fbd527e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416920"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="7a83c-102">將以 EF6 .EDMX 為基礎的模型移植至 EF Core</span><span class="sxs-lookup"><span data-stu-id="7a83c-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="7a83c-103">EF Core 不支援模型的 EDMX 檔案格式。</span><span class="sxs-lookup"><span data-stu-id="7a83c-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="7a83c-104">移植這些模型的最佳選項是，從資料庫為您的應用程式產生新的以程式碼為基礎的模型。</span><span class="sxs-lookup"><span data-stu-id="7a83c-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="7a83c-105">安裝 EF Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="7a83c-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="7a83c-106">安裝 `Microsoft.EntityFrameworkCore.Tools` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="7a83c-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="7a83c-107">重新產生模型</span><span class="sxs-lookup"><span data-stu-id="7a83c-107">Regenerate the model</span></span>

<span data-ttu-id="7a83c-108">您現在可以使用反向工程功能，根據現有的資料庫建立模型。</span><span class="sxs-lookup"><span data-stu-id="7a83c-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="7a83c-109">在 [套件管理員主控台] 中執行下列命令（[工具] – > NuGet 套件管理員-> 套件管理員主控台）。</span><span class="sxs-lookup"><span data-stu-id="7a83c-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="7a83c-110">如需 scaffold 資料表子集等的命令選項，請參閱[套件管理員主控台（Visual Studio）](../../core/miscellaneous/cli/powershell.md) 。</span><span class="sxs-lookup"><span data-stu-id="7a83c-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="7a83c-111">例如，下列命令會從 SQL Server LocalDB 實例上的 [日誌] 資料庫中 scaffold 模型。</span><span class="sxs-lookup"><span data-stu-id="7a83c-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="7a83c-112">移除 EF6 模型</span><span class="sxs-lookup"><span data-stu-id="7a83c-112">Remove EF6 model</span></span>

<span data-ttu-id="7a83c-113">您現在會從應用程式中移除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="7a83c-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="7a83c-114">您可以將 EF6 NuGet 套件（EntityFramework）保持安裝，因為 EF Core 和 EF6 可並存于相同的應用程式中使用。</span><span class="sxs-lookup"><span data-stu-id="7a83c-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="7a83c-115">不過，如果您不打算在應用程式的任何區域中使用 EF6，則卸載封裝將有助於在需要注意的程式碼片段上提供編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="7a83c-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="7a83c-116">更新您的程式碼</span><span class="sxs-lookup"><span data-stu-id="7a83c-116">Update your code</span></span>

<span data-ttu-id="7a83c-117">此時，請務必解決編譯錯誤和檢查程式碼，以查看 EF6 和 EF Core 之間的行為變更是否會影響您。</span><span class="sxs-lookup"><span data-stu-id="7a83c-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="7a83c-118">測試埠</span><span class="sxs-lookup"><span data-stu-id="7a83c-118">Test the port</span></span>

<span data-ttu-id="7a83c-119">這是因為您的應用程式會進行編譯，並不表示它已成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="7a83c-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="7a83c-120">您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="7a83c-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
