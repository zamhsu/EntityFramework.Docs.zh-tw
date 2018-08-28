---
title: 從 EF6 移植到 EF Core -移植程式碼為基礎的模型
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 2484b681d71ae8711b1b3a59bc274a0b2e403294
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997045"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="440bc-102">移植到 EF Core EF6 程式碼式模型</span><span class="sxs-lookup"><span data-stu-id="440bc-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="440bc-103">如果您已經讀過所有必須留意的問題，而且您已準備好連接埠，以下是一些指導方針可協助您開始著手。</span><span class="sxs-lookup"><span data-stu-id="440bc-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="440bc-104">安裝 EF Core NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="440bc-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="440bc-105">若要使用 EF Core，您可以安裝 NuGet 封裝您想要使用的資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="440bc-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="440bc-106">比方說，當目標 SQL Server，就會安裝`Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="440bc-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="440bc-107">請參閱[資料庫提供者](../../core/providers/index.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="440bc-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="440bc-108">如果您打算使用移轉，則您也應該安裝`Microsoft.EntityFrameworkCore.Tools`封裝。</span><span class="sxs-lookup"><span data-stu-id="440bc-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="440bc-109">它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF Core 和 EF6 可以使用-並存相同的應用程式中。</span><span class="sxs-lookup"><span data-stu-id="440bc-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="440bc-110">不過，如果您不想要使用 EF6 應用程式的任何區域中，然後解除安裝封裝有助於讓需要注意的程式碼片段上的編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="440bc-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="440bc-111">交換命名空間</span><span class="sxs-lookup"><span data-stu-id="440bc-111">Swap namespaces</span></span>

<span data-ttu-id="440bc-112">您在 EF6 中使用的大部分 Api 位於`System.Data.Entity`命名空間 （及相關的子命名空間）。</span><span class="sxs-lookup"><span data-stu-id="440bc-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="440bc-113">第一次的程式碼變更是要切換至`Microsoft.EntityFrameworkCore`命名空間。</span><span class="sxs-lookup"><span data-stu-id="440bc-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="440bc-114">您會通常開始您的衍生的內容的程式碼檔案，然後算出從該處，解決發生的編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="440bc-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="440bc-115">內容組態 （連線等）。</span><span class="sxs-lookup"><span data-stu-id="440bc-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="440bc-116">中所述[確保 EF Core 會用於您的應用程式](ensure-requirements.md)，EF Core 有較少神奇偵測連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="440bc-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="440bc-117">您必須覆寫`OnConfiguring`方法在您的衍生的內容，並使用資料庫提供者特定 API 來設定資料庫的連接。</span><span class="sxs-lookup"><span data-stu-id="440bc-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="440bc-118">大部分的 EF6 應用程式的應用程式中儲存連接字串`App/Web.config`檔案。</span><span class="sxs-lookup"><span data-stu-id="440bc-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="440bc-119">在 EF Core 您讀取此連接字串使用`ConfigurationManager`應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="440bc-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="440bc-120">您可能需要將參考加入`System.Configuration`framework 組件，若要能夠使用此 API。</span><span class="sxs-lookup"><span data-stu-id="440bc-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="440bc-121">更新您的程式碼</span><span class="sxs-lookup"><span data-stu-id="440bc-121">Update your code</span></span>

<span data-ttu-id="440bc-122">到目前為止，它是解決編譯錯誤，並檢閱查看如果的行為變更會影響您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="440bc-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="440bc-123">現有的移轉</span><span class="sxs-lookup"><span data-stu-id="440bc-123">Existing migrations</span></span>

<span data-ttu-id="440bc-124">此外並非真正 EF Core EF6 移轉現有的可行方式。</span><span class="sxs-lookup"><span data-stu-id="440bc-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="440bc-125">可能的話，最好假設 EF6 從所有先前移轉已經套用到資料庫並再開始移轉結構描述從該點使用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="440bc-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="440bc-126">若要這樣做，您可以使用`Add-Migration`命令來新增移轉後的模型移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="440bc-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="440bc-127">然後，您就會移除所有的程式碼，從`Up`和`Down`的 scaffold 移轉方法。</span><span class="sxs-lookup"><span data-stu-id="440bc-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="440bc-128">後續的移轉將會比較模型時已包含 scaffold 的該初始移轉。</span><span class="sxs-lookup"><span data-stu-id="440bc-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="440bc-129">測試連接埠</span><span class="sxs-lookup"><span data-stu-id="440bc-129">Test the port</span></span>

<span data-ttu-id="440bc-130">只在您的應用程式會編譯，因為並不表示它成功匯出至 EF Core。</span><span class="sxs-lookup"><span data-stu-id="440bc-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="440bc-131">您必須測試您的應用程式，以確保沒有任何行為變更造成不良影響您的應用程式的所有區域。</span><span class="sxs-lookup"><span data-stu-id="440bc-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
