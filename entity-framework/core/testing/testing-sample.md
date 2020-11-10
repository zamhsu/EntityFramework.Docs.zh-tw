---
title: EF Core 測試範例-EF Core
description: 範例顯示如何測試使用 Entity Framework Core 的應用程式
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- ':::no-loc(Item):::'
- ':::no-loc(Tag):::'
- ':::no-loc(Items):::'
- ':::no-loc(Tags):::'
- ':::no-loc(items):::'
- ':::no-loc(tags):::'
ms.openlocfilehash: 9666bbde8ae9608dcebbea3ad37c51883960a942
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431491"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="e1a35-103">EF Core 測試範例</span><span class="sxs-lookup"><span data-stu-id="e1a35-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="e1a35-104">您可以在 GitHub 上找到這份檔中的程式碼，做為可執行檔 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/)。</span><span class="sxs-lookup"><span data-stu-id="e1a35-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/).</span></span>
> <span data-ttu-id="e1a35-105">請注意，其中有些測試 **預期會失敗** 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="e1a35-106">以下將說明這種情況的原因。</span><span class="sxs-lookup"><span data-stu-id="e1a35-106">The reasons for this are explained below.</span></span>

<span data-ttu-id="e1a35-107">本檔會逐步解說使用 EF Core 之測試程式碼的範例。</span><span class="sxs-lookup"><span data-stu-id="e1a35-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="e1a35-108">應用程式</span><span class="sxs-lookup"><span data-stu-id="e1a35-108">The application</span></span>

<span data-ttu-id="e1a35-109">此 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/) 包含兩個專案：</span><span class="sxs-lookup"><span data-stu-id="e1a35-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/) contains two projects:</span></span>

- <span data-ttu-id="e1a35-110">:::no-loc(Items):::WebApi：透過單一控制器[ASP.NET Core 支援的簡易 WEB API](/aspnet/core/tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="e1a35-110">:::no-loc(Items):::WebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api) with a single controller</span></span>
- <span data-ttu-id="e1a35-111">測試：用來測試控制器的 [XUnit](https://xunit.net/) 測試專案</span><span class="sxs-lookup"><span data-stu-id="e1a35-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="e1a35-112">模型和商務規則</span><span class="sxs-lookup"><span data-stu-id="e1a35-112">The model and business rules</span></span>

<span data-ttu-id="e1a35-113">支援此 API 的模型有兩個實體類型： :::no-loc(Items)::: 和 :::no-loc(Tags)::: 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-113">The model backing this API has two entity types: :::no-loc(Items)::: and :::no-loc(Tags):::.</span></span>

- <span data-ttu-id="e1a35-114">:::no-loc(Items)::: 有區分大小寫的名稱和的集合 :::no-loc(Tags)::: 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-114">:::no-loc(Items)::: have a case-sensitive name and a collection of :::no-loc(Tags):::.</span></span>
- <span data-ttu-id="e1a35-115">每個 :::no-loc(Tag)::: 都有一個標籤和一個計數，代表它已套用至的次數 :::no-loc(Item)::: 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-115">Each :::no-loc(Tag)::: has a label and a count representing the number of times it has been applied to the :::no-loc(Item):::.</span></span>
- <span data-ttu-id="e1a35-116">每個都 :::no-loc(Item)::: 只能有一個 :::no-loc(Tag)::: 具有指定標籤的。</span><span class="sxs-lookup"><span data-stu-id="e1a35-116">Each :::no-loc(Item)::: should only have one :::no-loc(Tag)::: with a given label.</span></span>
  - <span data-ttu-id="e1a35-117">如果專案的標記具有相同的標籤一次以上，則具有該標籤之現有標記的計數會遞增，而不是建立新標記。</span><span class="sxs-lookup"><span data-stu-id="e1a35-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span>
- <span data-ttu-id="e1a35-118">刪除 :::no-loc(Item)::: 會刪除所有相關聯的 :::no-loc(Tags)::: 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-118">Deleting an :::no-loc(Item)::: should delete all associated :::no-loc(Tags):::.</span></span>

#### <a name="the-no-locitem-entity-type"></a><span data-ttu-id="e1a35-119">:::no-loc(Item):::實體類型</span><span class="sxs-lookup"><span data-stu-id="e1a35-119">The :::no-loc(Item)::: entity type</span></span>

<span data-ttu-id="e1a35-120">`:::no-loc(Item):::`實體類型：</span><span class="sxs-lookup"><span data-stu-id="e1a35-120">The `:::no-loc(Item):::` entity type:</span></span>

[!code-csharp[:::no-loc(Item):::EntityType](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Item):::.cs?name=:::no-loc(Item):::EntityType)]

<span data-ttu-id="e1a35-121">以及其在中的設定 `DbContext.OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="e1a35-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[Configure:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Items):::Context.cs?name=Configure:::no-loc(Item):::)]

<span data-ttu-id="e1a35-122">請注意，實體類型會限制可用來反映領域模型和商務規則的方式。</span><span class="sxs-lookup"><span data-stu-id="e1a35-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="e1a35-123">尤其是：</span><span class="sxs-lookup"><span data-stu-id="e1a35-123">In particular:</span></span>

- <span data-ttu-id="e1a35-124">主要金鑰會直接對應到 `_id` 欄位，而不會公開</span><span class="sxs-lookup"><span data-stu-id="e1a35-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="e1a35-125">EF 會偵測並使用私用的函式，以接受主要金鑰值和名稱。</span><span class="sxs-lookup"><span data-stu-id="e1a35-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="e1a35-126">`Name`屬性是唯讀的，而且只會在函式中設定。</span><span class="sxs-lookup"><span data-stu-id="e1a35-126">The `Name` property is read-only and set only in the constructor.</span></span>
- <span data-ttu-id="e1a35-127">:::no-loc(Tags)::: 會公開為 `IReadOnlyList<:::no-loc(Tag):::>` ，以防止任意修改。</span><span class="sxs-lookup"><span data-stu-id="e1a35-127">:::no-loc(Tags)::: are exposed as a `IReadOnlyList<:::no-loc(Tag):::>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="e1a35-128">EF 藉由比對 `:::no-loc(Tags):::` 屬性與支援欄位的名稱，來建立其關聯 `_:::no-loc(tags):::` 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-128">EF associates the `:::no-loc(Tags):::` property with the `_:::no-loc(tags):::` backing field by matching their names.</span></span>
  - <span data-ttu-id="e1a35-129">`Add:::no-loc(Tag):::`方法會採用標記標籤，並執行上述的商務規則。</span><span class="sxs-lookup"><span data-stu-id="e1a35-129">The `Add:::no-loc(Tag):::` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="e1a35-130">也就是說，只會為新標籤新增標記。</span><span class="sxs-lookup"><span data-stu-id="e1a35-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="e1a35-131">否則，現有標籤上的計數會遞增。</span><span class="sxs-lookup"><span data-stu-id="e1a35-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="e1a35-132">已 `:::no-loc(Tags):::` 針對多對一關聯性設定導覽屬性</span><span class="sxs-lookup"><span data-stu-id="e1a35-132">The `:::no-loc(Tags):::` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="e1a35-133">不需要從到的導覽屬性 :::no-loc(Tag)::: :::no-loc(Item)::: ，因此不包含它。</span><span class="sxs-lookup"><span data-stu-id="e1a35-133">There is no need for a navigation property from :::no-loc(Tag)::: to :::no-loc(Item):::, so it is not included.</span></span>
  - <span data-ttu-id="e1a35-134">此外，也不 :::no-loc(Tag)::: 會定義外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="e1a35-134">Also, :::no-loc(Tag)::: does not define a foreign key property.</span></span>
    <span data-ttu-id="e1a35-135">相反地，EF 會建立和管理陰影狀態的屬性。</span><span class="sxs-lookup"><span data-stu-id="e1a35-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-no-loctag-entity-type"></a><span data-ttu-id="e1a35-136">:::no-loc(Tag):::實體類型</span><span class="sxs-lookup"><span data-stu-id="e1a35-136">The :::no-loc(Tag)::: entity type</span></span>

<span data-ttu-id="e1a35-137">`:::no-loc(Tag):::`實體類型：</span><span class="sxs-lookup"><span data-stu-id="e1a35-137">The `:::no-loc(Tag):::` entity type:</span></span>

[!code-csharp[:::no-loc(Tag):::EntityType](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Tag):::.cs?name=:::no-loc(Tag):::EntityType)]

<span data-ttu-id="e1a35-138">以及其在中的設定 `DbContext.OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="e1a35-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[Configure:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Items):::Context.cs?name=Configure:::no-loc(Tag):::)]

<span data-ttu-id="e1a35-139">同樣地 :::no-loc(Item)::: ， :::no-loc(Tag)::: 會隱藏其主鍵，並讓 `Label` 屬性成為唯讀。</span><span class="sxs-lookup"><span data-stu-id="e1a35-139">Similarly to :::no-loc(Item):::, :::no-loc(Tag)::: hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-no-locitemscontroller"></a><span data-ttu-id="e1a35-140">:::no-loc(Items):::控制器</span><span class="sxs-lookup"><span data-stu-id="e1a35-140">The :::no-loc(Items):::Controller</span></span>

<span data-ttu-id="e1a35-141">Web API 控制器相當基本。</span><span class="sxs-lookup"><span data-stu-id="e1a35-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="e1a35-142">它會透過函式 `DbContext` 插入，從相依性插入容器取得：</span><span class="sxs-lookup"><span data-stu-id="e1a35-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Constructor)]

<span data-ttu-id="e1a35-143">它有方法可取得所有 :::no-loc(Items)::: 或 :::no-loc(Item)::: 具有指定名稱的：</span><span class="sxs-lookup"><span data-stu-id="e1a35-143">It has methods to get all :::no-loc(Items)::: or an :::no-loc(Item)::: with a given name:</span></span>

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Get)]

<span data-ttu-id="e1a35-144">它有一種方法可以新增 :::no-loc(Item)::: ：</span><span class="sxs-lookup"><span data-stu-id="e1a35-144">It has a method to add a new :::no-loc(Item)::::</span></span>

[!code-csharp[Post:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Post:::no-loc(Item):::)]

<span data-ttu-id="e1a35-145">:::no-loc(Item):::使用標籤標記的方法：</span><span class="sxs-lookup"><span data-stu-id="e1a35-145">A method to tag an :::no-loc(Item)::: with a label:</span></span>

[!code-csharp[Post:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Post:::no-loc(Tag):::)]

<span data-ttu-id="e1a35-146">以及刪除 :::no-loc(Item)::: 和所有相關聯的方法 :::no-loc(Tags)::: ：</span><span class="sxs-lookup"><span data-stu-id="e1a35-146">And a method to delete an :::no-loc(Item)::: and all associated :::no-loc(Tags)::::</span></span>

[!code-csharp[Delete:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Delete:::no-loc(Item):::)]

<span data-ttu-id="e1a35-147">已移除大部分的驗證和錯誤處理，以減少雜亂。</span><span class="sxs-lookup"><span data-stu-id="e1a35-147">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="e1a35-148">測試</span><span class="sxs-lookup"><span data-stu-id="e1a35-148">The Tests</span></span>

<span data-ttu-id="e1a35-149">測試的組織方式是使用多個資料庫提供者設定來執行：</span><span class="sxs-lookup"><span data-stu-id="e1a35-149">The tests are organized to run with multiple database provider configurations:</span></span>

- <span data-ttu-id="e1a35-150">SQL Server 提供者，也就是應用程式所使用的提供者</span><span class="sxs-lookup"><span data-stu-id="e1a35-150">The SQL Server provider, which is the provider used by the application</span></span>
- <span data-ttu-id="e1a35-151">SQLite 提供者</span><span class="sxs-lookup"><span data-stu-id="e1a35-151">The SQLite provider</span></span>
- <span data-ttu-id="e1a35-152">使用記憶體內部 SQLite 資料庫的 SQLite 提供者</span><span class="sxs-lookup"><span data-stu-id="e1a35-152">The SQLite provider using in-memory SQLite databases</span></span>
- <span data-ttu-id="e1a35-153">EF 記憶體內部資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="e1a35-153">The EF in-memory database provider</span></span>

<span data-ttu-id="e1a35-154">做法是將所有測試放在基類中，然後繼承自這個類別，以測試每個提供者。</span><span class="sxs-lookup"><span data-stu-id="e1a35-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="e1a35-155">如果您不是使用 LocalDB，您將需要變更 SQL Server 連接字串。</span><span class="sxs-lookup"><span data-stu-id="e1a35-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>
> <span data-ttu-id="e1a35-156">請參閱使用 [sqlite 進行測試](xref:core/testing/sqlite) ，以取得使用 sqlite 進行記憶體內部測試的指引。</span><span class="sxs-lookup"><span data-stu-id="e1a35-156">See [Testing with SQLite](xref:core/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span>

<span data-ttu-id="e1a35-157">下列兩項測試預期會失敗：</span><span class="sxs-lookup"><span data-stu-id="e1a35-157">The following two tests are expected to fail:</span></span>

- <span data-ttu-id="e1a35-158">`Can_remove_item_and_all_associated_:::no-loc(tags):::` 使用 EF 記憶體內部資料庫提供者執行時</span><span class="sxs-lookup"><span data-stu-id="e1a35-158">`Can_remove_item_and_all_associated_:::no-loc(tags):::` when running with the EF in-memory database provider</span></span>
- <span data-ttu-id="e1a35-159">`Can_add_item_differing_only_by_case` 使用 SQL Server 提供者執行時</span><span class="sxs-lookup"><span data-stu-id="e1a35-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="e1a35-160">以下將詳細說明這一點。</span><span class="sxs-lookup"><span data-stu-id="e1a35-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="e1a35-161">設定和植入資料庫</span><span class="sxs-lookup"><span data-stu-id="e1a35-161">Setting up and seeding the database</span></span>

<span data-ttu-id="e1a35-162">XUnit 和大部分的測試架構一樣，將會為每個測試回合建立新的測試類別實例。</span><span class="sxs-lookup"><span data-stu-id="e1a35-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="e1a35-163">此外，XUnit 也不會平行執行指定測試類別內的測試。</span><span class="sxs-lookup"><span data-stu-id="e1a35-163">Also, XUnit will not run tests within a given test class in parallel.</span></span>
<span data-ttu-id="e1a35-164">這表示我們可以在測試的函式中設定和設定資料庫，而且每個測試的狀態都是已知狀態。</span><span class="sxs-lookup"><span data-stu-id="e1a35-164">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="e1a35-165">此範例會為每個測試重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="e1a35-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="e1a35-166">這適用于 SQLite 和 EF 記憶體內部資料庫測試，但可能涉及與其他資料庫系統（包括 SQL Server）的大量額外負荷。</span><span class="sxs-lookup"><span data-stu-id="e1a35-166">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="e1a35-167">[跨測試共用資料庫](xref:core/testing/sharing-databases)涵蓋了降低此額外負荷的方法。</span><span class="sxs-lookup"><span data-stu-id="e1a35-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/testing/sharing-databases).</span></span>

<span data-ttu-id="e1a35-168">執行每個測試時：</span><span class="sxs-lookup"><span data-stu-id="e1a35-168">When each test is run:</span></span>

- <span data-ttu-id="e1a35-169">已針對使用中的提供者設定 DbCoNtextOptions，並傳遞至基類的函式</span><span class="sxs-lookup"><span data-stu-id="e1a35-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  - <span data-ttu-id="e1a35-170">這些選項會儲存在屬性中，並在整個測試中用來建立 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="e1a35-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
- <span data-ttu-id="e1a35-171">呼叫種子方法來建立和植入資料庫</span><span class="sxs-lookup"><span data-stu-id="e1a35-171">A Seed method is called to create and seed the database</span></span>
  - <span data-ttu-id="e1a35-172">植入方法會藉由刪除並重新建立，來確保資料庫是乾淨的</span><span class="sxs-lookup"><span data-stu-id="e1a35-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  - <span data-ttu-id="e1a35-173">系統會建立一些知名的測試實體，並將其儲存至資料庫</span><span class="sxs-lookup"><span data-stu-id="e1a35-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=Seeding)]

<span data-ttu-id="e1a35-174">每個具體的測試類別都會繼承自這個類別。</span><span class="sxs-lookup"><span data-stu-id="e1a35-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="e1a35-175">例如：</span><span class="sxs-lookup"><span data-stu-id="e1a35-175">For example:</span></span>

[!code-csharp[Sqlite:::no-loc(Items):::ControllerTest](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/Sqlite:::no-loc(Items):::ControllerTest.cs?name=Sqlite:::no-loc(Items):::ControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="e1a35-176">測試結構</span><span class="sxs-lookup"><span data-stu-id="e1a35-176">Test structure</span></span>

<span data-ttu-id="e1a35-177">雖然應用程式使用相依性插入，但測試卻沒有。</span><span class="sxs-lookup"><span data-stu-id="e1a35-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="e1a35-178">您可以在這裡使用相依性插入，但所需的額外程式碼不會有太大的價值。</span><span class="sxs-lookup"><span data-stu-id="e1a35-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="e1a35-179">相反地，會使用建立 DbCoNtext，然後直接以相依性的 `new` 形式傳遞至控制器。</span><span class="sxs-lookup"><span data-stu-id="e1a35-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="e1a35-180">然後，每個測試會在控制器上執行受測試的方法，並判斷提示結果如預期般執行。</span><span class="sxs-lookup"><span data-stu-id="e1a35-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="e1a35-181">例如：</span><span class="sxs-lookup"><span data-stu-id="e1a35-181">For example:</span></span>

[!code-csharp[CanGet:::no-loc(Items):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanGet:::no-loc(Items):::)]

<span data-ttu-id="e1a35-182">請注意，會使用不同的 DbCoNtext 實例來植入資料庫，並執行測試。</span><span class="sxs-lookup"><span data-stu-id="e1a35-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span>
<span data-ttu-id="e1a35-183">這可確保測試不會使用 (，或在植入時，不使用) 內容所追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="e1a35-183">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="e1a35-184">也更符合 web 應用程式和服務中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="e1a35-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="e1a35-185">基於類似的原因，改變資料庫的測試會在測試中建立第二個 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="e1a35-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="e1a35-186">也就是說，您可以建立新的、乾淨的內容，然後從資料庫中讀取資料，以確保變更已儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e1a35-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span>
<span data-ttu-id="e1a35-187">例如：</span><span class="sxs-lookup"><span data-stu-id="e1a35-187">For example:</span></span>

[!code-csharp[CanAdd:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Item):::)]

<span data-ttu-id="e1a35-188">有兩個稍微複雜的測試涵蓋了有關加入的商務邏輯 :::no-loc(tags)::: 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-188">Two slightly more involved tests cover the business logic around adding :::no-loc(tags):::.</span></span>

[!code-csharp[CanAdd:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Tag):::)]

[!code-csharp[CanUp:::no-loc(Tag):::Count](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanUp:::no-loc(Tag):::Count)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="e1a35-189">使用不同資料庫提供者的問題</span><span class="sxs-lookup"><span data-stu-id="e1a35-189">Issues using different database providers</span></span>

<span data-ttu-id="e1a35-190">使用與生產環境應用程式不同的資料庫系統進行測試可能會導致問題。</span><span class="sxs-lookup"><span data-stu-id="e1a35-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="e1a35-191">這些會在 [使用 EF Core 的測試程式碼](xref:core/testing/index)的概念層級中討論。</span><span class="sxs-lookup"><span data-stu-id="e1a35-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/testing/index).</span></span>
<span data-ttu-id="e1a35-192">下列各節涵蓋此範例中的測試所示範之這類問題的兩個範例。</span><span class="sxs-lookup"><span data-stu-id="e1a35-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="e1a35-193">應用程式中斷時的測試階段</span><span class="sxs-lookup"><span data-stu-id="e1a35-193">Test passes when the application is broken</span></span>

<span data-ttu-id="e1a35-194">我們應用程式的其中一項需求是「 :::no-loc(Items)::: 有區分大小寫的名稱和集合。」 :::no-loc(Tags):::</span><span class="sxs-lookup"><span data-stu-id="e1a35-194">One of the requirements for our application is that ":::no-loc(Items)::: have a case-sensitive name and a collection of :::no-loc(Tags):::."</span></span>
<span data-ttu-id="e1a35-195">這相當簡單，可進行測試：</span><span class="sxs-lookup"><span data-stu-id="e1a35-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAdd:::no-loc(Item):::CaseInsensitive](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Item):::CaseInsensitive)]

<span data-ttu-id="e1a35-196">針對 EF 記憶體內部資料庫執行這項測試表示一切正常。</span><span class="sxs-lookup"><span data-stu-id="e1a35-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="e1a35-197">使用 SQLite 時，一切仍然沒問題。</span><span class="sxs-lookup"><span data-stu-id="e1a35-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="e1a35-198">但是針對 SQL Server 執行時，測試會失敗！</span><span class="sxs-lookup"><span data-stu-id="e1a35-198">But the test fails when run against SQL Server!</span></span>

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.:::no-loc(Items):::ControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="e1a35-199">這是因為 EF 記憶體內部資料庫和 SQLite 資料庫預設都有區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="e1a35-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="e1a35-200">另一方面，SQL Server 不區分大小寫！</span><span class="sxs-lookup"><span data-stu-id="e1a35-200">SQL Server, on the other hand, is case-insensitive!</span></span>

<span data-ttu-id="e1a35-201">根據設計，EF Core 不會變更這些行為，因為強制變更區分大小寫可能會對效能造成重大影響。</span><span class="sxs-lookup"><span data-stu-id="e1a35-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="e1a35-202">一旦我們知道這是問題，我們就可以修正應用程式，並在測試中進行補償。</span><span class="sxs-lookup"><span data-stu-id="e1a35-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="e1a35-203">不過，這裡的重點是，如果只使用 EF in memory 資料庫或 SQLite 提供者進行測試，就會遺漏這個 bug。</span><span class="sxs-lookup"><span data-stu-id="e1a35-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="e1a35-204">當應用程式正確時測試失敗</span><span class="sxs-lookup"><span data-stu-id="e1a35-204">Test fails when the application is correct</span></span>

<span data-ttu-id="e1a35-205">我們應用程式的另一項需求是「刪除 :::no-loc(Item)::: 所有相關聯的」 :::no-loc(Tags)::: 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-205">Another of the requirements for our application is that "deleting an :::no-loc(Item)::: should delete all associated :::no-loc(Tags):::."</span></span>
<span data-ttu-id="e1a35-206">同樣地，您也可以輕鬆地進行測試：</span><span class="sxs-lookup"><span data-stu-id="e1a35-206">Again, easy to test:</span></span>

[!code-csharp[Delete:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=Delete:::no-loc(Item):::)]

<span data-ttu-id="e1a35-207">這項測試會 SQL Server 和 SQLite 傳遞，但無法與 EF 記憶體內部資料庫一起使用！</span><span class="sxs-lookup"><span data-stu-id="e1a35-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.:::no-loc(Items):::ControllerTest.Can_remove_item_and_all_associated_:::no-loc(tags):::()
```

<span data-ttu-id="e1a35-208">在此情況下，應用程式會正常運作，因為 SQL Server 支援串聯 [刪除](xref:core/saving/cascade-delete)。</span><span class="sxs-lookup"><span data-stu-id="e1a35-208">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span>
<span data-ttu-id="e1a35-209">SQLite 也支援串聯刪除，與大部分的關係資料庫相同，因此在 SQLite 上進行測試可正常運作。</span><span class="sxs-lookup"><span data-stu-id="e1a35-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="e1a35-210">另一方面，EF 記憶體內部資料庫 [不支援串聯刪除](https://github.com/dotnet/efcore/issues/3924)。</span><span class="sxs-lookup"><span data-stu-id="e1a35-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="e1a35-211">這表示應用程式的這個部分無法使用 EF 記憶體內部資料庫提供者進行測試。</span><span class="sxs-lookup"><span data-stu-id="e1a35-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
