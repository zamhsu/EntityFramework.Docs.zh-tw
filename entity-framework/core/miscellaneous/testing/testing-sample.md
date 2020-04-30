---
title: EF Core 測試範例-EF Core
description: 範例顯示如何測試使用 EF Core 的應用程式
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: dda7191df7646aa06aab51d8d7891bd0ba155674
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564284"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="9700e-103">EF Core 測試範例</span><span class="sxs-lookup"><span data-stu-id="9700e-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="9700e-104">本檔中的程式碼可以在 GitHub 上找到可執行檔[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)。</span><span class="sxs-lookup"><span data-stu-id="9700e-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="9700e-105">請注意，其中有些測試**應該會失敗**。</span><span class="sxs-lookup"><span data-stu-id="9700e-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="9700e-106">其原因如下所述。</span><span class="sxs-lookup"><span data-stu-id="9700e-106">The reasons for this are explained below.</span></span> 

<span data-ttu-id="9700e-107">本檔會逐步解說用來測試使用 EF Core 之程式碼的範例。</span><span class="sxs-lookup"><span data-stu-id="9700e-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="9700e-108">應用程式</span><span class="sxs-lookup"><span data-stu-id="9700e-108">The application</span></span>

<span data-ttu-id="9700e-109">此[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)包含兩個專案：</span><span class="sxs-lookup"><span data-stu-id="9700e-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="9700e-110">ItemsWebApi：一個非常簡單的 Web API，以單一控制器[ASP.NET Core 支援](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)</span><span class="sxs-lookup"><span data-stu-id="9700e-110">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="9700e-111">測試：用來測試控制器的[XUnit](https://xunit.net/)測試專案</span><span class="sxs-lookup"><span data-stu-id="9700e-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="9700e-112">模型和商務規則</span><span class="sxs-lookup"><span data-stu-id="9700e-112">The model and business rules</span></span>

<span data-ttu-id="9700e-113">支援此 API 的模型有兩個實體類型Items ： Tags和。</span><span class="sxs-lookup"><span data-stu-id="9700e-113">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="9700e-114">具有區分大小寫的名稱和的集合Tags。</span><span class="sxs-lookup"><span data-stu-id="9700e-114"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="9700e-115">每Tag個都有一個標籤和一個計數，代表已套用至的Item次數。</span><span class="sxs-lookup"><span data-stu-id="9700e-115">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="9700e-116">每Item個都只能有Tag一個具有指定標籤的。</span><span class="sxs-lookup"><span data-stu-id="9700e-116">Each Item should only have one Tag with with a given label.</span></span>
  * <span data-ttu-id="9700e-117">如果專案被標記為相同標籤一次以上，則具有該標籤之現有標記的計數會遞增，而不是建立新的標記。</span><span class="sxs-lookup"><span data-stu-id="9700e-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="9700e-118">刪除應該Item會刪除所有相關Tags聯的。</span><span class="sxs-lookup"><span data-stu-id="9700e-118">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="9700e-119">Item實體類型</span><span class="sxs-lookup"><span data-stu-id="9700e-119">The Item entity type</span></span>

<span data-ttu-id="9700e-120">`Item`實體類型：</span><span class="sxs-lookup"><span data-stu-id="9700e-120">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="9700e-121">及其在中`DbContext.OnModelCreating`的設定：</span><span class="sxs-lookup"><span data-stu-id="9700e-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="9700e-122">請注意，實體類型會限制它可以用來反映領域模型和商務規則的方式。</span><span class="sxs-lookup"><span data-stu-id="9700e-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="9700e-123">尤其是：</span><span class="sxs-lookup"><span data-stu-id="9700e-123">In particular:</span></span>
- <span data-ttu-id="9700e-124">主要金鑰會直接對應到`_id`欄位，而不會公開公開</span><span class="sxs-lookup"><span data-stu-id="9700e-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="9700e-125">EF 會偵測並使用私用的函式，以接受主要金鑰值和名稱。</span><span class="sxs-lookup"><span data-stu-id="9700e-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="9700e-126">`Name`屬性是唯讀的，而且只會在此函式中設定。</span><span class="sxs-lookup"><span data-stu-id="9700e-126">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="9700e-127">會公開為`IReadOnlyList<Tag>` ，以防止任意修改。</span><span class="sxs-lookup"><span data-stu-id="9700e-127"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="9700e-128">EF 藉由`Tags`比對屬性`_tags`與支援欄位，藉由比對其名稱來建立關聯。</span><span class="sxs-lookup"><span data-stu-id="9700e-128">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="9700e-129">`AddTag`方法會採用標記標籤，並執行前述的商務規則。</span><span class="sxs-lookup"><span data-stu-id="9700e-129">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="9700e-130">也就是說，只有新標籤才會加入標記。</span><span class="sxs-lookup"><span data-stu-id="9700e-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="9700e-131">否則，現有標籤上的計數會遞增。</span><span class="sxs-lookup"><span data-stu-id="9700e-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="9700e-132">`Tags`導覽屬性是針對多對一關聯性所設定</span><span class="sxs-lookup"><span data-stu-id="9700e-132">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="9700e-133">不需要從Tag到Item的導覽屬性，因此不會包含。</span><span class="sxs-lookup"><span data-stu-id="9700e-133">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="9700e-134">此外， Tag不會定義外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="9700e-134">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="9700e-135">取而代之的是，EF 會建立和管理陰影狀態的屬性。</span><span class="sxs-lookup"><span data-stu-id="9700e-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="9700e-136">Tag實體類型</span><span class="sxs-lookup"><span data-stu-id="9700e-136">The Tag entity type</span></span>

<span data-ttu-id="9700e-137">`Tag`實體類型：</span><span class="sxs-lookup"><span data-stu-id="9700e-137">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="9700e-138">及其在中`DbContext.OnModelCreating`的設定：</span><span class="sxs-lookup"><span data-stu-id="9700e-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="9700e-139">類似于Item， Tag會隱藏其主要金鑰，並`Label`讓屬性成為唯讀。</span><span class="sxs-lookup"><span data-stu-id="9700e-139">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="9700e-140">ItemsController</span><span class="sxs-lookup"><span data-stu-id="9700e-140">The ItemsController</span></span>

<span data-ttu-id="9700e-141">Web API 控制器非常基本。</span><span class="sxs-lookup"><span data-stu-id="9700e-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="9700e-142">它會透過`DbContext`函式插入，從相依性插入容器取得：</span><span class="sxs-lookup"><span data-stu-id="9700e-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="9700e-143">它有方法可以取得Items Item具有指定名稱的所有或：</span><span class="sxs-lookup"><span data-stu-id="9700e-143">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="9700e-144">它有一個方法可以加入新Item的：</span><span class="sxs-lookup"><span data-stu-id="9700e-144">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="9700e-145">使用標籤標記的Item方法：</span><span class="sxs-lookup"><span data-stu-id="9700e-145">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="9700e-146">以及刪除Item和所有相關聯Tags的方法：</span><span class="sxs-lookup"><span data-stu-id="9700e-146">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="9700e-147">大部分的驗證和錯誤處理都已移除，以減少雜亂的情況。</span><span class="sxs-lookup"><span data-stu-id="9700e-147">Most validation and error handling has been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="9700e-148">測試</span><span class="sxs-lookup"><span data-stu-id="9700e-148">The Tests</span></span>

<span data-ttu-id="9700e-149">這些測試會組織成以多個資料庫提供者設定來執行：</span><span class="sxs-lookup"><span data-stu-id="9700e-149">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="9700e-150">SQL Server 提供者，這是應用程式所使用的提供者</span><span class="sxs-lookup"><span data-stu-id="9700e-150">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="9700e-151">SQLite 提供者</span><span class="sxs-lookup"><span data-stu-id="9700e-151">The SQLite provider</span></span>
* <span data-ttu-id="9700e-152">使用記憶體內部 SQLite 資料庫的 SQLite 提供者</span><span class="sxs-lookup"><span data-stu-id="9700e-152">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="9700e-153">EF 記憶體內部資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="9700e-153">The EF in-memory database provider</span></span>

<span data-ttu-id="9700e-154">這是藉由將所有測試放在基類中，然後從這個類別繼承，以測試每個提供者。</span><span class="sxs-lookup"><span data-stu-id="9700e-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="9700e-155">如果您不是使用 LocalDB，就必須變更 SQL Server 連接字串。</span><span class="sxs-lookup"><span data-stu-id="9700e-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="9700e-156">如需使用 SQLite 進行記憶體內部測試的指引，請參閱[使用 sqlite 進行測試](xref:core/miscellaneous/testing/sqlite)。</span><span class="sxs-lookup"><span data-stu-id="9700e-156">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="9700e-157">下列兩項測試預期會失敗：</span><span class="sxs-lookup"><span data-stu-id="9700e-157">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="9700e-158">`Can_remove_item_and_all_associated_tags`搭配 EF 記憶體內部資料庫提供者執行時</span><span class="sxs-lookup"><span data-stu-id="9700e-158">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="9700e-159">`Can_add_item_differing_only_by_case`與 SQL Server 提供者一起執行時</span><span class="sxs-lookup"><span data-stu-id="9700e-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="9700e-160">下面將詳細說明這一點。</span><span class="sxs-lookup"><span data-stu-id="9700e-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="9700e-161">設定和植入資料庫</span><span class="sxs-lookup"><span data-stu-id="9700e-161">Setting up and seeding the database</span></span>

<span data-ttu-id="9700e-162">XUnit 就像大部分的測試架構一樣，會為每個測試回合建立新的測試類別實例。</span><span class="sxs-lookup"><span data-stu-id="9700e-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="9700e-163">此外，XUnit 也不會平行執行指定測試類別內的測試。</span><span class="sxs-lookup"><span data-stu-id="9700e-163">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="9700e-164">這表示我們可以在測試的函式中設定和設定資料庫，而且它會在每個測試的已知狀態中。</span><span class="sxs-lookup"><span data-stu-id="9700e-164">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="9700e-165">這個範例會針對每個測試重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="9700e-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="9700e-166">這適用于 SQLite 和 EF 記憶體內部資料庫測試，但可能牽涉到與其他資料庫系統的嚴重負擔，包括 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="9700e-166">This works well for SQLite and EF in-memory database testing, but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="9700e-167">[跨測試共用資料庫](xref:core/miscellaneous/testing/sharing-databases)涵蓋了降低此額外負荷的方法。</span><span class="sxs-lookup"><span data-stu-id="9700e-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="9700e-168">執行每項測試時：</span><span class="sxs-lookup"><span data-stu-id="9700e-168">When each test is run:</span></span>
* <span data-ttu-id="9700e-169">已針對使用中的提供者設定 DbCoNtextOptions，並傳遞至基類的函式</span><span class="sxs-lookup"><span data-stu-id="9700e-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="9700e-170">這些選項會儲存在屬性中，並在整個測試中用於建立 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="9700e-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="9700e-171">呼叫種子方法來建立和植入資料庫</span><span class="sxs-lookup"><span data-stu-id="9700e-171">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="9700e-172">Seed 方法藉由刪除並重新建立資料庫，來確保其已清除</span><span class="sxs-lookup"><span data-stu-id="9700e-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="9700e-173">某些知名的測試實體會建立並儲存至資料庫</span><span class="sxs-lookup"><span data-stu-id="9700e-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="9700e-174">然後，每個具體的測試類別都會繼承自這個。</span><span class="sxs-lookup"><span data-stu-id="9700e-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="9700e-175">例如：</span><span class="sxs-lookup"><span data-stu-id="9700e-175">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="9700e-176">測試結構</span><span class="sxs-lookup"><span data-stu-id="9700e-176">Test structure</span></span>

<span data-ttu-id="9700e-177">即使應用程式使用相依性插入，測試也不會。</span><span class="sxs-lookup"><span data-stu-id="9700e-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="9700e-178">在這裡使用相依性插入是可行的，但它所需的額外程式碼卻沒有太大的價值。</span><span class="sxs-lookup"><span data-stu-id="9700e-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="9700e-179">相反地，會使用`new`建立 DbCoNtext，然後直接將它當做相依性傳遞至控制器。</span><span class="sxs-lookup"><span data-stu-id="9700e-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="9700e-180">然後每個測試都會在控制器上執行受測的方法，並判斷提示結果是否符合預期。</span><span class="sxs-lookup"><span data-stu-id="9700e-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="9700e-181">例如：</span><span class="sxs-lookup"><span data-stu-id="9700e-181">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="9700e-182">請注意，會使用不同的 DbCoNtext 實例來植入資料庫，並執行測試。</span><span class="sxs-lookup"><span data-stu-id="9700e-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="9700e-183">這可確保在植入時，測試不會使用（或往返）內容所追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="9700e-183">This ensures that test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="9700e-184">它也更符合 web 應用程式和服務中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="9700e-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="9700e-185">變動資料庫的測試會在測試中建立第二個 DbCoNtext 實例，原因類似。</span><span class="sxs-lookup"><span data-stu-id="9700e-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="9700e-186">也就是說，建立新的、乾淨的內容，然後從資料庫中讀取，以確保變更真的儲存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="9700e-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes really were saved to the database.</span></span> <span data-ttu-id="9700e-187">例如：</span><span class="sxs-lookup"><span data-stu-id="9700e-187">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="9700e-188">另外兩個相關的測試涵蓋了加入標記的商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="9700e-188">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="9700e-189">使用不同資料庫提供者的問題</span><span class="sxs-lookup"><span data-stu-id="9700e-189">Issues using different database providers</span></span>

<span data-ttu-id="9700e-190">使用與生產應用程式中使用的不同資料庫系統進行測試，可能會導致問題。</span><span class="sxs-lookup"><span data-stu-id="9700e-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="9700e-191">這會在[測試使用 EF Core 之程式碼](xref:core/miscellaneous/testing/index)的概念層級中討論。</span><span class="sxs-lookup"><span data-stu-id="9700e-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="9700e-192">下列各節涵蓋此範例中的測試所示範的兩個這類問題的範例。</span><span class="sxs-lookup"><span data-stu-id="9700e-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-application-is-broken"></a><span data-ttu-id="9700e-193">應用程式中斷時的測試階段</span><span class="sxs-lookup"><span data-stu-id="9700e-193">Test passes when application is broken</span></span>

<span data-ttu-id="9700e-194">我們的應用程式有一個需求，就是「Items有區分大小寫的名稱和集合Tags。」</span><span class="sxs-lookup"><span data-stu-id="9700e-194">One of the requirements for our application is that, "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="9700e-195">這非常容易測試：</span><span class="sxs-lookup"><span data-stu-id="9700e-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="9700e-196">針對 EF 記憶體內部資料庫執行這項測試，表示一切正常。</span><span class="sxs-lookup"><span data-stu-id="9700e-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="9700e-197">使用 SQLite 時，所有專案仍然看起來沒問題。</span><span class="sxs-lookup"><span data-stu-id="9700e-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="9700e-198">但是，在針對 SQL Server 執行時，測試會失敗！</span><span class="sxs-lookup"><span data-stu-id="9700e-198">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="9700e-199">這是因為 EF 記憶體內部資料庫和 SQLite 資料庫預設都會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9700e-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="9700e-200">另一方面，SQL Server 則不區分大小寫！</span><span class="sxs-lookup"><span data-stu-id="9700e-200">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="9700e-201">根據設計，EF Core 不會變更這些行為，因為強制變更區分大小寫可能會對效能造成重大影響。</span><span class="sxs-lookup"><span data-stu-id="9700e-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have big performance impact.</span></span>

<span data-ttu-id="9700e-202">一旦我們知道這是一個問題，我們就可以修正應用程式，並在測試中進行補償。</span><span class="sxs-lookup"><span data-stu-id="9700e-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="9700e-203">不過，這裡的重點是，如果只使用 EF 記憶體內部資料庫或 SQLite 提供者進行測試，就可能會遺漏此 bug。</span><span class="sxs-lookup"><span data-stu-id="9700e-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-application-is-correct"></a><span data-ttu-id="9700e-204">當應用程式正確時，測試會失敗</span><span class="sxs-lookup"><span data-stu-id="9700e-204">Test fails when application is correct</span></span> 

<span data-ttu-id="9700e-205">我們的應用程式有另一個需求，就是「刪除Item應該刪除所有相關Tags聯的」。</span><span class="sxs-lookup"><span data-stu-id="9700e-205">Another of the requirements for our application is that, "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="9700e-206">同樣地，很容易測試：</span><span class="sxs-lookup"><span data-stu-id="9700e-206">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="9700e-207">這項測試會在 SQL Server 和 SQLite 上傳遞，但會因 EF 記憶體內部資料庫而失敗！</span><span class="sxs-lookup"><span data-stu-id="9700e-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="9700e-208">在此情況下，應用程式會正常運作，因為 SQL Server 支援[cascade 刪除](xref:core/saving/cascade-delete)。</span><span class="sxs-lookup"><span data-stu-id="9700e-208">In this case the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="9700e-209">SQLite 也支援「串聯刪除」，如同大部分的關係資料庫，因此在 SQLite 上測試此功能會運作。</span><span class="sxs-lookup"><span data-stu-id="9700e-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="9700e-210">另一方面，EF 記憶體內部資料庫不[支援串聯刪除](https://github.com/dotnet/efcore/issues/3924)。</span><span class="sxs-lookup"><span data-stu-id="9700e-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="9700e-211">這表示應用程式的這個部分無法使用 EF 記憶體內部資料庫提供者進行測試。</span><span class="sxs-lookup"><span data-stu-id="9700e-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
