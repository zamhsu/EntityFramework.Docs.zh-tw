---
title: 從 EF6 移植到 EF Core-移植 EDMX 為基礎的模型
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: c999d2114c76ee3a7615ae897b42ee3376cff14e
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="04e8a-102">移植到 EF Core EF6 EDMX 型模型</span><span class="sxs-lookup"><span data-stu-id="04e8a-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="04e8a-103">EF 核心不支援模型的 EDMX 檔案格式。</span><span class="sxs-lookup"><span data-stu-id="04e8a-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="04e8a-104">最佳的選項，這些模型中，連接埠是從應用程式資料庫中產生新的程式碼為基礎的模型。</span><span class="sxs-lookup"><span data-stu-id="04e8a-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="04e8a-105">安裝 EF Core NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="04e8a-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="04e8a-106">安裝`Microsoft.EntityFrameworkCore.Tools`NuGet 封裝。</span><span class="sxs-lookup"><span data-stu-id="04e8a-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="04e8a-107">重新產生模型</span><span class="sxs-lookup"><span data-stu-id="04e8a-107">Regenerate the model</span></span>

<span data-ttu-id="04e8a-108">您現在可以使用反向工程功能來建立模型，根據您現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="04e8a-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="04e8a-109">Package Manager Console 中，執行下列命令 (工具 –> NuGet 套件管理員 –> Package Manager Console)。</span><span class="sxs-lookup"><span data-stu-id="04e8a-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="04e8a-110">請參閱[Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) scaffold 等資料表子集的命令選項。</span><span class="sxs-lookup"><span data-stu-id="04e8a-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="04e8a-111">例如，以下是 scaffold 模型，以從您的 SQL Server LocalDB 執行個體上的部落格資料庫命令。</span><span class="sxs-lookup"><span data-stu-id="04e8a-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="04e8a-112">移除 EF6 模型</span><span class="sxs-lookup"><span data-stu-id="04e8a-112">Remove EF6 model</span></span>

<span data-ttu-id="04e8a-113">您現在會從您的應用程式移除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="04e8a-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="04e8a-114">它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF 核心和 EF6 可以使用-並存相同的應用程式中。</span><span class="sxs-lookup"><span data-stu-id="04e8a-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="04e8a-115">不過，如果您不打算使用 EF6 應用程式的任何區域中，然後解除安裝封裝有助於讓上需要注意的程式碼片段的編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="04e8a-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="04e8a-116">更新您的程式碼</span><span class="sxs-lookup"><span data-stu-id="04e8a-116">Update your code</span></span>

<span data-ttu-id="04e8a-117">此時，它是定址的編譯錯誤，並檢閱看 EF6 和 EF 核心之間的行為變更會影響您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="04e8a-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="04e8a-118">測試連接埠</span><span class="sxs-lookup"><span data-stu-id="04e8a-118">Test the port</span></span>

<span data-ttu-id="04e8a-119">只在您的應用程式會編譯，因為並不表示它成功匯出至 EF 核心。</span><span class="sxs-lookup"><span data-stu-id="04e8a-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="04e8a-120">您必須測試您的應用程式，以確保沒有任何行為變更已經造成不利影響您的應用程式的所有區域。</span><span class="sxs-lookup"><span data-stu-id="04e8a-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>

> [!TIP]
> <span data-ttu-id="04e8a-121">請參閱[開始使用 ASP.NET Core 與現有的資料庫在 EF 核心](xref:core/get-started/aspnetcore/existing-db)對於如何使用現有的資料庫，其他參考</span><span class="sxs-lookup"><span data-stu-id="04e8a-121">See [Getting Started with EF Core on ASP.NET Core with an Existing Database](xref:core/get-started/aspnetcore/existing-db) for an additional reference on how to work with an existing database,</span></span> 
