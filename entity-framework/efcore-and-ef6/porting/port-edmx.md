---
title: 從 EF6 移植到 EF Core-移植 EDMX 為基礎的模型
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: a1c978e141f47a39fff59eb5fc417a63afd37b04
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71148991"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="17671-102">移植到 EF Core EF6 EDMX 型模型</span><span class="sxs-lookup"><span data-stu-id="17671-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="17671-103">EF Core 不支援模型的 EDMX 檔案格式。</span><span class="sxs-lookup"><span data-stu-id="17671-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="17671-104">移植這些模型的最佳選項是，從資料庫為您的應用程式產生新的以程式碼為基礎的模型。</span><span class="sxs-lookup"><span data-stu-id="17671-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="17671-105">安裝 EF Core NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="17671-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="17671-106">安裝 `Microsoft.EntityFrameworkCore.Tools` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="17671-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="17671-107">重新產生模型</span><span class="sxs-lookup"><span data-stu-id="17671-107">Regenerate the model</span></span>

<span data-ttu-id="17671-108">您現在可以使用反向工程功能，根據現有的資料庫建立模型。</span><span class="sxs-lookup"><span data-stu-id="17671-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="17671-109">在 [套件管理員主控台] 中執行下列命令（[工具] – > NuGet 套件管理員-> 套件管理員主控台）。</span><span class="sxs-lookup"><span data-stu-id="17671-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="17671-110">如需 scaffold 資料表子集等的命令選項，請參閱[套件管理員主控台（Visual Studio）](../../core/miscellaneous/cli/powershell.md) 。</span><span class="sxs-lookup"><span data-stu-id="17671-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="17671-111">例如，下列命令會從 SQL Server LocalDB 實例上的 [日誌] 資料庫中 scaffold 模型。</span><span class="sxs-lookup"><span data-stu-id="17671-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="17671-112">移除 EF6 模型</span><span class="sxs-lookup"><span data-stu-id="17671-112">Remove EF6 model</span></span>

<span data-ttu-id="17671-113">您現在會從應用程式中移除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="17671-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="17671-114">它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF Core 和 EF6 可以使用-並存相同的應用程式中。</span><span class="sxs-lookup"><span data-stu-id="17671-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="17671-115">不過，如果您不打算在應用程式的任何區域中使用 EF6，則卸載封裝將有助於在需要注意的程式碼片段上提供編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="17671-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="17671-116">更新您的程式碼</span><span class="sxs-lookup"><span data-stu-id="17671-116">Update your code</span></span>

<span data-ttu-id="17671-117">此時，它是定址的編譯錯誤，並檢閱看 EF6 和 EF Core 之間的行為變更會影響您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="17671-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="17671-118">測試埠</span><span class="sxs-lookup"><span data-stu-id="17671-118">Test the port</span></span>

<span data-ttu-id="17671-119">只在您的應用程式會編譯，因為並不表示它成功匯出至 EF Core。</span><span class="sxs-lookup"><span data-stu-id="17671-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="17671-120">您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="17671-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
