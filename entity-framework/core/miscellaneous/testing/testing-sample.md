---
title: ''
description: ''
author: ''
ms.date: ''
uid: ''
no-loc:
- Item
- Tag
- Items
- Tags
ms.openlocfilehash: ae073fc0b3a99fb9de07a3e0a42c638fe0838a5a
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672822"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="4adc0-101">EF Core 測試範例</span><span class="sxs-lookup"><span data-stu-id="4adc0-101">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="4adc0-102">本檔中的程式碼可以在 GitHub 上找到可執行檔[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)。</span><span class="sxs-lookup"><span data-stu-id="4adc0-102">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="4adc0-103">請注意，其中有些測試**應該會失敗**。</span><span class="sxs-lookup"><span data-stu-id="4adc0-103">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="4adc0-104">其原因如下所述。</span><span class="sxs-lookup"><span data-stu-id="4adc0-104">The reasons for this are explained below.</span></span> 

<span data-ttu-id="4adc0-105">本檔會逐步解說用來測試使用 EF Core 之程式碼的範例。</span><span class="sxs-lookup"><span data-stu-id="4adc0-105">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="4adc0-106">應用程式</span><span class="sxs-lookup"><span data-stu-id="4adc0-106">The application</span></span>

<span data-ttu-id="4adc0-107">此[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)包含兩個專案：</span><span class="sxs-lookup"><span data-stu-id="4adc0-107">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="4adc0-108">ItemsWebApi：一個非常簡單的 Web API，以單一控制器[ASP.NET Core 支援](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)</span><span class="sxs-lookup"><span data-stu-id="4adc0-108">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="4adc0-109">測試：用來測試控制器的[XUnit](https://xunit.net/)測試專案</span><span class="sxs-lookup"><span data-stu-id="4adc0-109">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="4adc0-110">模型和商務規則</span><span class="sxs-lookup"><span data-stu-id="4adc0-110">The model and business rules</span></span>

<span data-ttu-id="4adc0-111">支援此 API 的模型有兩個實體類型： Items 和 Tags 。</span><span class="sxs-lookup"><span data-stu-id="4adc0-111">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="4adc0-112">具有區分大小寫的名稱和的集合 Tags 。</span><span class="sxs-lookup"><span data-stu-id="4adc0-112"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="4adc0-113">每個 Tag 都有一個標籤和一個計數，代表已套用至的次數 Item 。</span><span class="sxs-lookup"><span data-stu-id="4adc0-113">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="4adc0-114">每個都 Item 只能有一個 Tag 具有指定標籤的。</span><span class="sxs-lookup"><span data-stu-id="4adc0-114">Each Item should only have one Tag with a given label.</span></span>
  * <span data-ttu-id="4adc0-115">如果專案被標記為相同標籤一次以上，則具有該標籤之現有標記的計數會遞增，而不是建立新的標記。</span><span class="sxs-lookup"><span data-stu-id="4adc0-115">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="4adc0-116">刪除 Item 應該會刪除所有相關聯的 Tags 。</span><span class="sxs-lookup"><span data-stu-id="4adc0-116">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="4adc0-117">Item實體類型</span><span class="sxs-lookup"><span data-stu-id="4adc0-117">The Item entity type</span></span>

<span data-ttu-id="4adc0-118">`Item`實體類型：</span><span class="sxs-lookup"><span data-stu-id="4adc0-118">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="4adc0-119">及其在中的設定 `DbContext.OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="4adc0-119">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="4adc0-120">請注意，實體類型會限制它可以用來反映領域模型和商務規則的方式。</span><span class="sxs-lookup"><span data-stu-id="4adc0-120">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="4adc0-121">尤其是：</span><span class="sxs-lookup"><span data-stu-id="4adc0-121">In particular:</span></span>
- <span data-ttu-id="4adc0-122">主要金鑰會直接對應到 `_id` 欄位，而不會公開公開</span><span class="sxs-lookup"><span data-stu-id="4adc0-122">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="4adc0-123">EF 會偵測並使用私用的函式，以接受主要金鑰值和名稱。</span><span class="sxs-lookup"><span data-stu-id="4adc0-123">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="4adc0-124">`Name`屬性是唯讀的，而且只會在此函式中設定。</span><span class="sxs-lookup"><span data-stu-id="4adc0-124">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="4adc0-125">會公開為 `IReadOnlyList<Tag>` ，以防止任意修改。</span><span class="sxs-lookup"><span data-stu-id="4adc0-125"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="4adc0-126">EF 藉由比對 `Tags` 屬性與支援欄位，藉由比對 `_tags` 其名稱來建立關聯。</span><span class="sxs-lookup"><span data-stu-id="4adc0-126">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="4adc0-127">`AddTag`方法會採用標記標籤，並執行前述的商務規則。</span><span class="sxs-lookup"><span data-stu-id="4adc0-127">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="4adc0-128">也就是說，只有新標籤才會加入標記。</span><span class="sxs-lookup"><span data-stu-id="4adc0-128">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="4adc0-129">否則，現有標籤上的計數會遞增。</span><span class="sxs-lookup"><span data-stu-id="4adc0-129">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="4adc0-130">`Tags`導覽屬性是針對多對一關聯性所設定</span><span class="sxs-lookup"><span data-stu-id="4adc0-130">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="4adc0-131">不需要從到的導覽屬性 Tag Item ，因此不會包含。</span><span class="sxs-lookup"><span data-stu-id="4adc0-131">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="4adc0-132">此外，不 Tag 會定義外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="4adc0-132">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="4adc0-133">取而代之的是，EF 會建立和管理陰影狀態的屬性。</span><span class="sxs-lookup"><span data-stu-id="4adc0-133">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="4adc0-134">Tag實體類型</span><span class="sxs-lookup"><span data-stu-id="4adc0-134">The Tag entity type</span></span>

<span data-ttu-id="4adc0-135">`Tag`實體類型：</span><span class="sxs-lookup"><span data-stu-id="4adc0-135">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="4adc0-136">及其在中的設定 `DbContext.OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="4adc0-136">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="4adc0-137">類似于 Item ， Tag 會隱藏其主要金鑰，並讓 `Label` 屬性成為唯讀。</span><span class="sxs-lookup"><span data-stu-id="4adc0-137">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="4adc0-138">ItemsController</span><span class="sxs-lookup"><span data-stu-id="4adc0-138">The ItemsController</span></span>

<span data-ttu-id="4adc0-139">Web API 控制器非常基本。</span><span class="sxs-lookup"><span data-stu-id="4adc0-139">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="4adc0-140">它會透過函式 `DbContext` 插入，從相依性插入容器取得：</span><span class="sxs-lookup"><span data-stu-id="4adc0-140">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="4adc0-141">它有方法可以取得 Items Item 具有指定名稱的所有或：</span><span class="sxs-lookup"><span data-stu-id="4adc0-141">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="4adc0-142">它有一個方法可以加入新的 Item ：</span><span class="sxs-lookup"><span data-stu-id="4adc0-142">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="4adc0-143">Item使用標籤標記的方法：</span><span class="sxs-lookup"><span data-stu-id="4adc0-143">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="4adc0-144">以及刪除 Item 和所有相關聯的方法 Tags ：</span><span class="sxs-lookup"><span data-stu-id="4adc0-144">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="4adc0-145">大部分的驗證和錯誤處理都已移除，以減少雜亂的情況。</span><span class="sxs-lookup"><span data-stu-id="4adc0-145">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="4adc0-146">測試</span><span class="sxs-lookup"><span data-stu-id="4adc0-146">The Tests</span></span>

<span data-ttu-id="4adc0-147">這些測試會組織成以多個資料庫提供者設定來執行：</span><span class="sxs-lookup"><span data-stu-id="4adc0-147">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="4adc0-148">SQL Server 提供者，這是應用程式所使用的提供者</span><span class="sxs-lookup"><span data-stu-id="4adc0-148">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="4adc0-149">SQLite 提供者</span><span class="sxs-lookup"><span data-stu-id="4adc0-149">The SQLite provider</span></span>
* <span data-ttu-id="4adc0-150">使用記憶體內部 SQLite 資料庫的 SQLite 提供者</span><span class="sxs-lookup"><span data-stu-id="4adc0-150">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="4adc0-151">EF 記憶體內部資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="4adc0-151">The EF in-memory database provider</span></span>

<span data-ttu-id="4adc0-152">這是藉由將所有測試放在基類中，然後從這個類別繼承，以測試每個提供者。</span><span class="sxs-lookup"><span data-stu-id="4adc0-152">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="4adc0-153">如果您不是使用 LocalDB，就必須變更 SQL Server 連接字串。</span><span class="sxs-lookup"><span data-stu-id="4adc0-153">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="4adc0-154">如需使用 SQLite 進行記憶體內部測試的指引，請參閱[使用 sqlite 進行測試](xref:core/miscellaneous/testing/sqlite)。</span><span class="sxs-lookup"><span data-stu-id="4adc0-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="4adc0-155">下列兩項測試預期會失敗：</span><span class="sxs-lookup"><span data-stu-id="4adc0-155">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="4adc0-156">`Can_remove_item_and_all_associated_tags`搭配 EF 記憶體內部資料庫提供者執行時</span><span class="sxs-lookup"><span data-stu-id="4adc0-156">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="4adc0-157">`Can_add_item_differing_only_by_case`與 SQL Server 提供者一起執行時</span><span class="sxs-lookup"><span data-stu-id="4adc0-157">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="4adc0-158">下面將詳細說明這一點。</span><span class="sxs-lookup"><span data-stu-id="4adc0-158">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="4adc0-159">設定和植入資料庫</span><span class="sxs-lookup"><span data-stu-id="4adc0-159">Setting up and seeding the database</span></span>

<span data-ttu-id="4adc0-160">XUnit 就像大部分的測試架構一樣，會為每個測試回合建立新的測試類別實例。</span><span class="sxs-lookup"><span data-stu-id="4adc0-160">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="4adc0-161">此外，XUnit 也不會平行執行指定測試類別內的測試。</span><span class="sxs-lookup"><span data-stu-id="4adc0-161">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="4adc0-162">這表示我們可以在測試的函式中設定和設定資料庫，而且它會在每個測試的已知狀態中。</span><span class="sxs-lookup"><span data-stu-id="4adc0-162">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="4adc0-163">這個範例會針對每個測試重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="4adc0-163">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="4adc0-164">這適用于 SQLite 和 EF 記憶體內部資料庫測試，但可能牽涉到與其他資料庫系統的嚴重負擔，包括 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="4adc0-164">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="4adc0-165">[跨測試共用資料庫](xref:core/miscellaneous/testing/sharing-databases)涵蓋了降低此額外負荷的方法。</span><span class="sxs-lookup"><span data-stu-id="4adc0-165">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="4adc0-166">執行每項測試時：</span><span class="sxs-lookup"><span data-stu-id="4adc0-166">When each test is run:</span></span>
* <span data-ttu-id="4adc0-167">已針對使用中的提供者設定 DbCoNtextOptions，並傳遞至基類的函式</span><span class="sxs-lookup"><span data-stu-id="4adc0-167">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="4adc0-168">這些選項會儲存在屬性中，並在整個測試中用於建立 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="4adc0-168">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="4adc0-169">呼叫種子方法來建立和植入資料庫</span><span class="sxs-lookup"><span data-stu-id="4adc0-169">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="4adc0-170">Seed 方法藉由刪除並重新建立資料庫，來確保其已清除</span><span class="sxs-lookup"><span data-stu-id="4adc0-170">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="4adc0-171">某些知名的測試實體會建立並儲存至資料庫</span><span class="sxs-lookup"><span data-stu-id="4adc0-171">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="4adc0-172">然後，每個具體的測試類別都會繼承自這個。</span><span class="sxs-lookup"><span data-stu-id="4adc0-172">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="4adc0-173">例如：</span><span class="sxs-lookup"><span data-stu-id="4adc0-173">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="4adc0-174">測試結構</span><span class="sxs-lookup"><span data-stu-id="4adc0-174">Test structure</span></span>

<span data-ttu-id="4adc0-175">即使應用程式使用相依性插入，測試也不會。</span><span class="sxs-lookup"><span data-stu-id="4adc0-175">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="4adc0-176">在這裡使用相依性插入是可行的，但它所需的額外程式碼卻沒有太大的價值。</span><span class="sxs-lookup"><span data-stu-id="4adc0-176">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="4adc0-177">相反地，會使用建立 DbCoNtext，然後直接將它當做相依性 `new` 傳遞至控制器。</span><span class="sxs-lookup"><span data-stu-id="4adc0-177">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="4adc0-178">然後每個測試都會在控制器上執行受測的方法，並判斷提示結果是否符合預期。</span><span class="sxs-lookup"><span data-stu-id="4adc0-178">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="4adc0-179">例如：</span><span class="sxs-lookup"><span data-stu-id="4adc0-179">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="4adc0-180">請注意，會使用不同的 DbCoNtext 實例來植入資料庫，並執行測試。</span><span class="sxs-lookup"><span data-stu-id="4adc0-180">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="4adc0-181">這可確保在植入時，測試不會使用（或往返）內容所追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="4adc0-181">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="4adc0-182">它也更符合 web 應用程式和服務中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="4adc0-182">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="4adc0-183">變動資料庫的測試會在測試中建立第二個 DbCoNtext 實例，原因類似。</span><span class="sxs-lookup"><span data-stu-id="4adc0-183">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="4adc0-184">也就是說，建立新的、乾淨的內容，然後從資料庫中讀取，以確保變更已儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="4adc0-184">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span> <span data-ttu-id="4adc0-185">例如：</span><span class="sxs-lookup"><span data-stu-id="4adc0-185">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="4adc0-186">另外兩個相關的測試涵蓋了加入標記的商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="4adc0-186">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="4adc0-187">使用不同資料庫提供者的問題</span><span class="sxs-lookup"><span data-stu-id="4adc0-187">Issues using different database providers</span></span>

<span data-ttu-id="4adc0-188">使用與生產應用程式中使用的不同資料庫系統進行測試，可能會導致問題。</span><span class="sxs-lookup"><span data-stu-id="4adc0-188">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="4adc0-189">這會在[測試使用 EF Core 之程式碼](xref:core/miscellaneous/testing/index)的概念層級中討論。</span><span class="sxs-lookup"><span data-stu-id="4adc0-189">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="4adc0-190">下列各節涵蓋此範例中的測試所示範的兩個這類問題的範例。</span><span class="sxs-lookup"><span data-stu-id="4adc0-190">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="4adc0-191">應用程式中斷時的測試階段</span><span class="sxs-lookup"><span data-stu-id="4adc0-191">Test passes when the application is broken</span></span>

<span data-ttu-id="4adc0-192">我們應用程式的其中一個需求，就是「 Items 有區分大小寫的名稱和集合 Tags 。」</span><span class="sxs-lookup"><span data-stu-id="4adc0-192">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="4adc0-193">這非常容易測試：</span><span class="sxs-lookup"><span data-stu-id="4adc0-193">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="4adc0-194">針對 EF 記憶體內部資料庫執行這項測試，表示一切正常。</span><span class="sxs-lookup"><span data-stu-id="4adc0-194">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="4adc0-195">使用 SQLite 時，所有專案仍然看起來沒問題。</span><span class="sxs-lookup"><span data-stu-id="4adc0-195">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="4adc0-196">但是，在針對 SQL Server 執行時，測試會失敗！</span><span class="sxs-lookup"><span data-stu-id="4adc0-196">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="4adc0-197">這是因為 EF 記憶體內部資料庫和 SQLite 資料庫預設都會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="4adc0-197">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="4adc0-198">另一方面，SQL Server 則不區分大小寫！</span><span class="sxs-lookup"><span data-stu-id="4adc0-198">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="4adc0-199">根據設計，EF Core 不會變更這些行為，因為強制變更區分大小寫可能會對效能造成重大影響。</span><span class="sxs-lookup"><span data-stu-id="4adc0-199">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="4adc0-200">一旦我們知道這是一個問題，我們就可以修正應用程式，並在測試中進行補償。</span><span class="sxs-lookup"><span data-stu-id="4adc0-200">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="4adc0-201">不過，這裡的重點是，如果只使用 EF 記憶體內部資料庫或 SQLite 提供者進行測試，就可能會遺漏此 bug。</span><span class="sxs-lookup"><span data-stu-id="4adc0-201">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="4adc0-202">當應用程式正確時，測試會失敗</span><span class="sxs-lookup"><span data-stu-id="4adc0-202">Test fails when the application is correct</span></span> 

<span data-ttu-id="4adc0-203">我們的應用程式的另一個需求是「刪除 Item 應該刪除所有相關聯的」 Tags 。</span><span class="sxs-lookup"><span data-stu-id="4adc0-203">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="4adc0-204">同樣地，很容易測試：</span><span class="sxs-lookup"><span data-stu-id="4adc0-204">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="4adc0-205">這項測試會在 SQL Server 和 SQLite 上傳遞，但會因 EF 記憶體內部資料庫而失敗！</span><span class="sxs-lookup"><span data-stu-id="4adc0-205">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="4adc0-206">在此情況下，應用程式會正常運作，因為 SQL Server 支援[cascade 刪除](xref:core/saving/cascade-delete)。</span><span class="sxs-lookup"><span data-stu-id="4adc0-206">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="4adc0-207">SQLite 也支援「串聯刪除」，如同大部分的關係資料庫，因此在 SQLite 上測試此功能會運作。</span><span class="sxs-lookup"><span data-stu-id="4adc0-207">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="4adc0-208">另一方面，EF 記憶體內部資料庫不[支援串聯刪除](https://github.com/dotnet/efcore/issues/3924)。</span><span class="sxs-lookup"><span data-stu-id="4adc0-208">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="4adc0-209">這表示應用程式的這個部分無法使用 EF 記憶體內部資料庫提供者進行測試。</span><span class="sxs-lookup"><span data-stu-id="4adc0-209">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
