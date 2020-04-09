---
title: 從 EF6 移植到 EF 核心 - 移植基於代碼的模型 - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78419634"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="bf7fd-102">將基於 EF6 的模型移植到 EF 核心</span><span class="sxs-lookup"><span data-stu-id="bf7fd-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="bf7fd-103">如果您已經閱讀了所有注意事項,並且已準備好移植,那麼這裡有一些説明您入門的指南。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="bf7fd-104">安裝 EF 核心 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="bf7fd-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="bf7fd-105">要使用 EF Core,請為要使用的資料庫提供程式安裝 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="bf7fd-106">例如, 在定位 SQL Server 時,`Microsoft.EntityFrameworkCore.SqlServer`將安裝 。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="bf7fd-107">有關詳細資訊[,請參考資料庫提供者](../../core/providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="bf7fd-108">如果您計劃使用遷移,則還應安裝該`Microsoft.EntityFrameworkCore.Tools`包。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="bf7fd-109">保留 EF6 NuGet 套件 (EntityFramework) 安裝是可以的,因為 EF Core 和 EF6 可以並行用於同一應用程式。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="bf7fd-110">但是,如果您不打算在應用程式的任何區域使用 EF6,則卸載包將有助於在需要注意的代碼片段上出現編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="bf7fd-111">交換命名空間</span><span class="sxs-lookup"><span data-stu-id="bf7fd-111">Swap namespaces</span></span>

<span data-ttu-id="bf7fd-112">在 EF6 中使用的大多數`System.Data.Entity`API 都在命名空間(和相關子命名空間)中。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="bf7fd-113">第一個代碼更改是交換到`Microsoft.EntityFrameworkCore`命名空間。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="bf7fd-114">您通常從派生的上下文代碼檔開始,然後從那裡工作,解決髮生編譯錯誤時的錯誤。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="bf7fd-115">內容設定(連線等)</span><span class="sxs-lookup"><span data-stu-id="bf7fd-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="bf7fd-116">正如[《確保EF核心適用於您的應用程式](ensure-requirements.md)》中所述,EF Core 在檢測要連接到的資料庫方面沒有那麼神奇。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="bf7fd-117">您需要重寫派生上下文中`OnConfiguring`的方法,並使用資料庫提供程式特定的 API 來設置到資料庫的連接。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="bf7fd-118">大多數 EF6 應用程式將連接字串儲存在`App/Web.config`應用程式 檔案中。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="bf7fd-119">在 EF Core`ConfigurationManager`中,使用 API 讀取此連接字串。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="bf7fd-120">您可能需要添加對框架程式集的`System.Configuration`引用才能使用此 API。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="bf7fd-121">更新代碼</span><span class="sxs-lookup"><span data-stu-id="bf7fd-121">Update your code</span></span>

<span data-ttu-id="bf7fd-122">此時,需要解決編譯錯誤並查看代碼,以查看行為更改是否會影響您。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="bf7fd-123">現有移轉</span><span class="sxs-lookup"><span data-stu-id="bf7fd-123">Existing migrations</span></span>

<span data-ttu-id="bf7fd-124">實際上沒有將現有 EF6 遷移移植到 EF Core 的可行方法。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="bf7fd-125">如果可能,最好假設以前從 EF6 的所有遷移都已應用於資料庫,然後使用 EF Core 從該點開始遷移架構。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="bf7fd-126">為此,在模型移植到`Add-Migration`EF Core 後,可以使用 命令添加遷移。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="bf7fd-127">然後,您將從`Up`基架遷移和`Down`方法中刪除所有代碼。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="bf7fd-128">後續遷移將與初始遷移被基架時模型進行比較。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="bf7fd-129">測試連接埠</span><span class="sxs-lookup"><span data-stu-id="bf7fd-129">Test the port</span></span>

<span data-ttu-id="bf7fd-130">僅僅因為您的應用程式編譯,並不意味著它已成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="bf7fd-131">您需要測試應用程式的所有區域,以確保沒有任何行為更改對應用程式造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="bf7fd-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
