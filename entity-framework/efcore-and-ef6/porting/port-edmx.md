---
title: 從 EF6 移植至 EF Core-移植 EDMX-Based 模型-EF
description: 將 Entity Framework 6 以 EDMX 為基礎的模型應用程式移植到 Entity Framework Core 的特定資訊
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 9c1b308318c9bf0325ece0b60ace646becc8de39
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429217"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="c3937-103">將 EF6 EDMX-Based 模型移植到 EF Core</span><span class="sxs-lookup"><span data-stu-id="c3937-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="c3937-104">EF Core 不支援模型的 EDMX 檔案格式。</span><span class="sxs-lookup"><span data-stu-id="c3937-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="c3937-105">移植這些模型的最佳選項，是為您的應用程式從資料庫產生新的以程式碼為基礎的模型。</span><span class="sxs-lookup"><span data-stu-id="c3937-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="c3937-106">安裝 EF Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="c3937-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="c3937-107">安裝 `Microsoft.EntityFrameworkCore.Tools` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="c3937-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="c3937-108">重新產生模型</span><span class="sxs-lookup"><span data-stu-id="c3937-108">Regenerate the model</span></span>

<span data-ttu-id="c3937-109">您現在可以使用反向工程功能，根據您現有的資料庫建立模型。</span><span class="sxs-lookup"><span data-stu-id="c3937-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="c3937-110">在封裝管理員主控台中執行下列命令 (工具-> NuGet 封裝管理員– > 封裝管理員主控台) 。</span><span class="sxs-lookup"><span data-stu-id="c3937-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="c3937-111">請參閱 [封裝管理員主控台 (Visual Studio) ](xref:core/cli/powershell) 的命令選項，以 scaffold 資料表的子集等等。</span><span class="sxs-lookup"><span data-stu-id="c3937-111">See [Package Manager Console (Visual Studio)](xref:core/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="c3937-112">例如，以下命令可讓您從 SQL Server LocalDB 實例上的 [日誌] 資料庫 scaffold 模型。</span><span class="sxs-lookup"><span data-stu-id="c3937-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="c3937-113">移除 EF6 模型</span><span class="sxs-lookup"><span data-stu-id="c3937-113">Remove EF6 model</span></span>

<span data-ttu-id="c3937-114">您現在可以從應用程式中移除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="c3937-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="c3937-115">您可以將 EF6 NuGet 套件安裝 (EntityFramework) ，因為 EF Core 和 EF6 可在相同的應用程式中並存使用。</span><span class="sxs-lookup"><span data-stu-id="c3937-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="c3937-116">但是，如果您不打算在應用程式的任何區域中使用 EF6，則卸載套件將有助於在需要注意的程式碼片段上提供編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="c3937-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="c3937-117">更新您的程式碼</span><span class="sxs-lookup"><span data-stu-id="c3937-117">Update your code</span></span>

<span data-ttu-id="c3937-118">此時，請務必解決編譯錯誤並檢查程式碼，以查看 EF6 與 EF Core 之間的行為變更是否會影響您。</span><span class="sxs-lookup"><span data-stu-id="c3937-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="c3937-119">測試埠</span><span class="sxs-lookup"><span data-stu-id="c3937-119">Test the port</span></span>

<span data-ttu-id="c3937-120">這是因為您的應用程式會進行編譯，並不表示它已成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="c3937-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="c3937-121">您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="c3937-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
