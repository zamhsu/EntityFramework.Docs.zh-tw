---
title: 從 EF6 移植到 EF 核心 - 移植基於 EDMX 的型號 - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: f0bb06dc687aaa774981d97daadc55f00fbd527e
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78416920"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="31ac6-102">將基於 EF6 EDMX 的型號移植到 EF 核心</span><span class="sxs-lookup"><span data-stu-id="31ac6-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="31ac6-103">EF Core 不支援模型的 EDMX 檔案格式。</span><span class="sxs-lookup"><span data-stu-id="31ac6-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="31ac6-104">移植這些模型的最佳選擇是從應用程式的資料庫中生成一個新的基於代碼的模型。</span><span class="sxs-lookup"><span data-stu-id="31ac6-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="31ac6-105">安裝 EF 核心 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="31ac6-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="31ac6-106">安裝 `Microsoft.EntityFrameworkCore.Tools` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="31ac6-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="31ac6-107">重新產生模型</span><span class="sxs-lookup"><span data-stu-id="31ac6-107">Regenerate the model</span></span>

<span data-ttu-id="31ac6-108">現在,您可以使用反向工程功能基於現有資料庫創建模型。</span><span class="sxs-lookup"><span data-stu-id="31ac6-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="31ac6-109">在包管理器控制台中運行以下命令(工具 +> NuGet 包管理員 [>包管理器控制台)。</span><span class="sxs-lookup"><span data-stu-id="31ac6-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="31ac6-110">有關設置表子集的命令選項等,請參閱[包管理器主控台(可視化工作室)。](../../core/miscellaneous/cli/powershell.md)</span><span class="sxs-lookup"><span data-stu-id="31ac6-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="31ac6-111">例如,下面是在 SQL Server LocalDB 實例上從部落格資料庫設置模型的命令。</span><span class="sxs-lookup"><span data-stu-id="31ac6-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="31ac6-112">移除 EF6 型號</span><span class="sxs-lookup"><span data-stu-id="31ac6-112">Remove EF6 model</span></span>

<span data-ttu-id="31ac6-113">現在,您將從應用程式中刪除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="31ac6-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="31ac6-114">保留 EF6 NuGet 套件 (EntityFramework) 安裝是可以的,因為 EF Core 和 EF6 可以並行用於同一應用程式。</span><span class="sxs-lookup"><span data-stu-id="31ac6-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="31ac6-115">但是,如果您不打算在應用程式的任何區域使用 EF6,則卸載包將有助於在需要注意的代碼片段上出現編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="31ac6-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="31ac6-116">更新代碼</span><span class="sxs-lookup"><span data-stu-id="31ac6-116">Update your code</span></span>

<span data-ttu-id="31ac6-117">此時,需要解決編譯錯誤並查看代碼,以查看 EF6 和 EF Core 之間的行為更改是否會影響您。</span><span class="sxs-lookup"><span data-stu-id="31ac6-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="31ac6-118">測試連接埠</span><span class="sxs-lookup"><span data-stu-id="31ac6-118">Test the port</span></span>

<span data-ttu-id="31ac6-119">僅僅因為您的應用程式編譯,並不意味著它已成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="31ac6-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="31ac6-120">您需要測試應用程式的所有區域,以確保沒有任何行為更改對應用程式造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="31ac6-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
