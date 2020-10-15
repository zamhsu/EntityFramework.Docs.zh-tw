---
title: 從 EF6 移植至 EF Core-移植 Code-Based 模型-EF
description: 將 Entity Framework 6 以程式碼為基礎的模型應用程式移植到 Entity Framework Core 的特定資訊
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 819c3bc0eba140c023cdcd5038a4cd63c300ed4c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064220"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="cf318-103">將 EF6 Code-Based 模型移植到 EF Core</span><span class="sxs-lookup"><span data-stu-id="cf318-103">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="cf318-104">如果您已經閱讀過所有的注意事項，並已準備好進行埠，則以下是協助您開始使用的一些指導方針。</span><span class="sxs-lookup"><span data-stu-id="cf318-104">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="cf318-105">安裝 EF Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="cf318-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="cf318-106">若要使用 EF Core，請為您要使用的資料庫提供者安裝 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="cf318-106">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="cf318-107">例如，以 SQL Server 為目標時，您會安裝 `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="cf318-107">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="cf318-108">請參閱 [資料庫提供者](xref:core/providers/index) 以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="cf318-108">See [Database Providers](xref:core/providers/index) for details.</span></span>

<span data-ttu-id="cf318-109">如果您打算使用遷移，則也應該安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。</span><span class="sxs-lookup"><span data-stu-id="cf318-109">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="cf318-110">您可以將 EF6 NuGet 套件安裝 (EntityFramework) ，因為 EF Core 和 EF6 可在相同的應用程式中並存使用。</span><span class="sxs-lookup"><span data-stu-id="cf318-110">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="cf318-111">但是，如果您不打算在應用程式的任何區域中使用 EF6，則卸載套件將有助於在需要注意的程式碼片段上提供編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="cf318-111">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="cf318-112">交換命名空間</span><span class="sxs-lookup"><span data-stu-id="cf318-112">Swap namespaces</span></span>

<span data-ttu-id="cf318-113">您在 EF6 中使用的大多數 Api 都位於 `System.Data.Entity` 命名空間 (以及相關的子命名空間) 。</span><span class="sxs-lookup"><span data-stu-id="cf318-113">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="cf318-114">第一個程式碼變更是交換到 `Microsoft.EntityFrameworkCore` 命名空間。</span><span class="sxs-lookup"><span data-stu-id="cf318-114">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="cf318-115">您通常會從衍生的內容程式碼檔案開始，然後從該處完成，以解決發生的編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="cf318-115">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="cf318-116">內容設定 (連接等 ) </span><span class="sxs-lookup"><span data-stu-id="cf318-116">Context configuration (connection etc.)</span></span>

<span data-ttu-id="cf318-117">如所述， [您的應用程式可以使用 EF Core](xref:efcore-and-ef6/porting/index)，EF Core 在偵測要連接的資料庫時，會有較少的神奇之處。</span><span class="sxs-lookup"><span data-stu-id="cf318-117">As described in [Ensure EF Core Will Work for Your Application](xref:efcore-and-ef6/porting/index), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="cf318-118">您將需要覆寫 `OnConfiguring` 衍生內容上的方法，並使用資料庫提供者特定 API 來設定資料庫的連接。</span><span class="sxs-lookup"><span data-stu-id="cf318-118">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="cf318-119">大部分的 EF6 應用程式會將連接字串儲存在應用程式 `App/Web.config` 檔中。</span><span class="sxs-lookup"><span data-stu-id="cf318-119">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="cf318-120">在 EF Core 中，您會使用 API 來讀取此連接字串 `ConfigurationManager` 。</span><span class="sxs-lookup"><span data-stu-id="cf318-120">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="cf318-121">您可能需要加入架構元件的參考，才能 `System.Configuration` 使用此 API。</span><span class="sxs-lookup"><span data-stu-id="cf318-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a><span data-ttu-id="cf318-122">更新您的程式碼</span><span class="sxs-lookup"><span data-stu-id="cf318-122">Update your code</span></span>

<span data-ttu-id="cf318-123">此時，請務必解決編譯錯誤並檢查程式碼，以查看行為變更是否會影響您。</span><span class="sxs-lookup"><span data-stu-id="cf318-123">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="cf318-124">現有的遷移</span><span class="sxs-lookup"><span data-stu-id="cf318-124">Existing migrations</span></span>

<span data-ttu-id="cf318-125">將現有的 EF6 遷移移植到 EF Core 並不是一種可行的方法。</span><span class="sxs-lookup"><span data-stu-id="cf318-125">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="cf318-126">可能的話，最好是假設所有先前從 EF6 的遷移都已套用至資料庫，然後使用 EF Core 開始從該時間點遷移架構。</span><span class="sxs-lookup"><span data-stu-id="cf318-126">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="cf318-127">若要這樣做，您可以在 `Add-Migration` 模型移植到 EF Core 之後，使用命令來新增遷移。</span><span class="sxs-lookup"><span data-stu-id="cf318-127">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="cf318-128">然後，您可以從 `Up` scaffold 遷移的和方法中移除所有程式碼 `Down` 。</span><span class="sxs-lookup"><span data-stu-id="cf318-128">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="cf318-129">Scaffold 初始遷移時，後續的遷移將會與模型比較。</span><span class="sxs-lookup"><span data-stu-id="cf318-129">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="cf318-130">測試埠</span><span class="sxs-lookup"><span data-stu-id="cf318-130">Test the port</span></span>

<span data-ttu-id="cf318-131">這是因為您的應用程式會進行編譯，並不表示它已成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="cf318-131">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="cf318-132">您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="cf318-132">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
