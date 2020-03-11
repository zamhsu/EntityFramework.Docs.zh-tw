---
title: 從 EF6 移植到 EF Core-移植以程式碼為基礎的模型-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419634"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="29c8d-102">將以 EF6 程式碼為基礎的模型移植至 EF Core</span><span class="sxs-lookup"><span data-stu-id="29c8d-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="29c8d-103">如果您已閱讀所有注意事項，而且您已準備好進行通訊，以下是一些可協助您開始使用的指導方針。</span><span class="sxs-lookup"><span data-stu-id="29c8d-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="29c8d-104">安裝 EF Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="29c8d-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="29c8d-105">若要使用 EF Core，請安裝您想要使用之資料庫提供者的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="29c8d-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="29c8d-106">例如，以 SQL Server 為目標時，您會安裝 `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="29c8d-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="29c8d-107">如需詳細資訊，請參閱[資料庫提供者](../../core/providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="29c8d-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="29c8d-108">如果您打算使用遷移，則也應該安裝 `Microsoft.EntityFrameworkCore.Tools` 套件。</span><span class="sxs-lookup"><span data-stu-id="29c8d-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="29c8d-109">您可以將 EF6 NuGet 套件（EntityFramework）保持安裝，因為 EF Core 和 EF6 可並存于相同的應用程式中使用。</span><span class="sxs-lookup"><span data-stu-id="29c8d-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="29c8d-110">不過，如果您不打算在應用程式的任何區域中使用 EF6，則卸載封裝將有助於在需要注意的程式碼片段上提供編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="29c8d-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="29c8d-111">交換命名空間</span><span class="sxs-lookup"><span data-stu-id="29c8d-111">Swap namespaces</span></span>

<span data-ttu-id="29c8d-112">您在 EF6 中使用的大部分 Api 都位於 `System.Data.Entity` 命名空間（和相關的子命名空間）中。</span><span class="sxs-lookup"><span data-stu-id="29c8d-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="29c8d-113">第一個程式碼變更是要交換至 `Microsoft.EntityFrameworkCore` 命名空間。</span><span class="sxs-lookup"><span data-stu-id="29c8d-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="29c8d-114">您通常會從衍生的內容程式碼檔案開始，然後從該處著手，以解決發生的編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="29c8d-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="29c8d-115">內容設定（連接等）</span><span class="sxs-lookup"><span data-stu-id="29c8d-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="29c8d-116">如[確保 EF Core 適用于您的應用程式](ensure-requirements.md)，EF Core 在偵測資料庫以進行連接時，會比較不神奇。</span><span class="sxs-lookup"><span data-stu-id="29c8d-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="29c8d-117">您必須覆寫衍生內容上的 `OnConfiguring` 方法，並使用資料庫提供者特定的 API 來設定資料庫的連接。</span><span class="sxs-lookup"><span data-stu-id="29c8d-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="29c8d-118">大部分的 EF6 應用程式會將連接字串儲存在應用程式 `App/Web.config` 檔中。</span><span class="sxs-lookup"><span data-stu-id="29c8d-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="29c8d-119">在 EF Core 中，您會使用 `ConfigurationManager` API 來讀取此連接字串。</span><span class="sxs-lookup"><span data-stu-id="29c8d-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="29c8d-120">您可能需要加入 `System.Configuration` framework 元件的參考，才能夠使用此 API。</span><span class="sxs-lookup"><span data-stu-id="29c8d-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
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

## <a name="update-your-code"></a><span data-ttu-id="29c8d-121">更新您的程式碼</span><span class="sxs-lookup"><span data-stu-id="29c8d-121">Update your code</span></span>

<span data-ttu-id="29c8d-122">此時，請務必解決編譯錯誤和檢查程式碼，以查看行為變更是否會影響您。</span><span class="sxs-lookup"><span data-stu-id="29c8d-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="29c8d-123">現有的遷移</span><span class="sxs-lookup"><span data-stu-id="29c8d-123">Existing migrations</span></span>

<span data-ttu-id="29c8d-124">沒有任何可行的方法可以將現有的 EF6 遷移移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="29c8d-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="29c8d-125">可能的話，最好假設先前從 EF6 進行的所有遷移都已套用至資料庫，然後使用 EF Core 開始從該點遷移架構。</span><span class="sxs-lookup"><span data-stu-id="29c8d-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="29c8d-126">若要這麼做，您可以使用 `Add-Migration` 命令，在模型移植至 EF Core 後新增遷移。</span><span class="sxs-lookup"><span data-stu-id="29c8d-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="29c8d-127">接著，您會從 scaffold 遷移的 `Up` 和 `Down` 方法中移除所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="29c8d-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="29c8d-128">當 scaffold 初始遷移時，後續的遷移會與模型進行比較。</span><span class="sxs-lookup"><span data-stu-id="29c8d-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="29c8d-129">測試埠</span><span class="sxs-lookup"><span data-stu-id="29c8d-129">Test the port</span></span>

<span data-ttu-id="29c8d-130">這是因為您的應用程式會進行編譯，並不表示它已成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="29c8d-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="29c8d-131">您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="29c8d-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
