---
title: 可測試性和 Entity Framework 4.0-EF6
description: 可測試性和 Entity Framework 4。0
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: e1ded772bfee4f1870a891eaa525b33da8e9d3bc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618360"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="6b4c0-103">可測試性和 Entity Framework 4。0</span><span class="sxs-lookup"><span data-stu-id="6b4c0-103">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="6b4c0-104">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="6b4c0-104">Scott Allen</span></span>

<span data-ttu-id="6b4c0-105">發佈日期：2010 年 5 月</span><span class="sxs-lookup"><span data-stu-id="6b4c0-105">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="6b4c0-106">簡介</span><span class="sxs-lookup"><span data-stu-id="6b4c0-106">Introduction</span></span>

<span data-ttu-id="6b4c0-107">本白皮書將說明並示範如何使用 ADO.NET Entity Framework 4.0 和 Visual Studio 2010 撰寫可測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-107">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="6b4c0-108">這份檔不會嘗試將焦點放在特定的測試方法，像是測試導向設計 (TDD) 或行為導向設計 (BDD) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-108">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="6b4c0-109">本文將著重于如何撰寫使用 ADO.NET Entity Framework 的程式碼，但仍能以自動化的方式輕鬆地隔離和測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-109">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="6b4c0-110">我們將探討常見的設計模式，以便在資料存取案例中進行測試，並瞭解如何在使用架構時套用這些模式。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-110">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="6b4c0-111">我們也將探討架構的特定功能，以瞭解這些功能如何在可測試的程式碼中運作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-111">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="6b4c0-112">什麼是可測試的程式碼？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-112">What Is Testable Code?</span></span>

<span data-ttu-id="6b4c0-113">使用自動化單元測試來驗證軟體的能力，可提供許多值得的優點。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-113">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="6b4c0-114">每個人都知道良好的測試將會減少應用程式中的軟體瑕疵，並提高應用程式的品質，但是有了單元測試之後，就不只是找出錯誤。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-114">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="6b4c0-115">良好的單元測試套件可讓開發小組節省時間，並保有其所建立之軟體的控制權。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-115">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="6b4c0-116">小組可以對現有程式碼進行變更、重構、重新設計及重建軟體，以符合新的需求，並將新的元件新增至應用程式，同時知道測試套件可以驗證應用程式的行為。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-116">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="6b4c0-117">單元測試是快速意見反應週期的一部分，可在複雜性增加時加速變更並保留軟體的可維護性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-117">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="6b4c0-118">不過，單元測試有價格。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-118">Unit testing comes with a price, however.</span></span> <span data-ttu-id="6b4c0-119">小組必須投入時間來建立和維護單元測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-119">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="6b4c0-120">建立這些測試所需的工作量，與基礎軟體的可 **測試** 性直接相關。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-120">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="6b4c0-121">測試軟體有多簡單？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-121">How easy is the software to test?</span></span> <span data-ttu-id="6b4c0-122">設計具有可測試性之軟體的團隊，會比使用未測試軟體的團隊更快速地建立有效的測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-122">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="6b4c0-123">Microsoft 設計了 ADO.NET Entity Framework 4.0 (EF4) ，並牢記可測試性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-123">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="6b4c0-124">這並不表示開發人員會針對架構程式碼本身撰寫單元測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-124">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="6b4c0-125">相反地，EF4 的可測試性目標可讓您輕鬆地建立以架構為基礎的可測試程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-125">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="6b4c0-126">在查看特定範例之前，最好先瞭解可測試程式碼的品質。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-126">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="6b4c0-127">可測試程式碼的品質</span><span class="sxs-lookup"><span data-stu-id="6b4c0-127">The Qualities of Testable Code</span></span>

<span data-ttu-id="6b4c0-128">易於測試的程式碼一律會顯示至少兩個特性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-128">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="6b4c0-129">首先，可測試的程式碼很容易 **觀察**到。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-129">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="6b4c0-130">由於有一組輸入，因此應該很容易觀察程式碼的輸出。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-130">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="6b4c0-131">例如，測試下列方法很容易，因為方法會直接傳回計算的結果。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-131">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="6b4c0-132">如果方法將計算的值寫入網路通訊端、資料庫資料表或類似下列程式碼的檔案，則測試方法會很困難。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-132">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="6b4c0-133">測試必須執行額外的工作來取得值。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-133">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="6b4c0-134">其次，可測試的程式碼很容易 **隔離**。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-134">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="6b4c0-135">讓我們使用下列虛擬程式碼作為測試程式碼的不良範例。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-135">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

<span data-ttu-id="6b4c0-136">方法很容易觀察，我們可以傳入保險原則，並確認傳回值符合預期的結果。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-136">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="6b4c0-137">不過，若要測試方法，我們必須以正確的架構安裝資料庫，並在方法嘗試傳送電子郵件時設定 SMTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-137">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="6b4c0-138">單元測試只會想要驗證方法內的計算邏輯，但是測試可能會失敗，因為電子郵件伺服器已離線，或資料庫伺服器已移動。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-138">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="6b4c0-139">這兩個失敗與測試要驗證的行為無關。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-139">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="6b4c0-140">此行為難以隔離。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-140">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="6b4c0-141">致力於撰寫可測試程式碼的軟體發展人員，通常致力於在所撰寫的程式碼中維護關注點分離。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-141">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="6b4c0-142">上述方法應該專注于商務計算，並將資料庫和電子郵件執行詳細資料委派給其他元件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-142">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="6b4c0-143">Robert c. 以單一責任原則來呼叫這項工作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-143">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="6b4c0-144">物件應封裝單一且較窄的責任，例如計算原則的值。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-144">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="6b4c0-145">所有其他的資料庫和通知工作都應該是其他物件的責任。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-145">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="6b4c0-146">以這種方式撰寫的程式碼比較容易隔離，因為它著重于單一工作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-146">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="6b4c0-147">在 .NET 中，我們需要遵循單一責任原則並達成隔離的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-147">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="6b4c0-148">我們可以使用介面定義，並強制程式碼使用介面抽象，而不是具象類型。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-148">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="6b4c0-149">本檔稍後會看到類似上述錯誤範例的方法如何 *使用類似它們* 與資料庫溝通的介面。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-149">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="6b4c0-150">不過，在測試階段，我們可以替代未與資料庫通訊的虛擬實作為，但會將資料保存在記憶體中。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-150">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="6b4c0-151">這個虛擬實作為隔離程式碼，與資料存取程式碼或資料庫設定中不相關的問題。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-151">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="6b4c0-152">另外還有其他優點需要隔離。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-152">There are additional benefits to isolation.</span></span> <span data-ttu-id="6b4c0-153">最後一個方法中的商業計算應該只需要幾毫秒的時間來執行，但測試本身可能會執行數秒，作為網路周圍的程式碼躍點，並與各種伺服器交談。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-153">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="6b4c0-154">單元測試應該會快速執行，以加速小型變更。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-154">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="6b4c0-155">單元測試應該也是可重複的，而且不會失敗，因為與測試無關的元件有問題。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-155">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="6b4c0-156">撰寫容易觀察及隔離的程式碼，可讓開發人員更輕鬆地撰寫程式碼的測試、花較少的時間等待測試執行，而且更重要的是，花較少的時間追蹤不存在的 bug。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-156">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="6b4c0-157">希望您可以知道測試的優點，並瞭解測試程式碼所展現的品質。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-157">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="6b4c0-158">我們即將說明如何撰寫可與 EF4 搭配運作的程式碼，以將資料儲存到資料庫中，同時還能觀察並容易隔離，但首先我們要將焦點縮小，以討論資料存取的可測試設計。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-158">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="6b4c0-159">資料持續性的設計模式</span><span class="sxs-lookup"><span data-stu-id="6b4c0-159">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="6b4c0-160">稍早所示的兩個不良範例都具有太多責任。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-160">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="6b4c0-161">第一個不良範例必須執行計算 *並* 寫入至檔案。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-161">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="6b4c0-162">第二個不良範例必須從資料庫讀取資料 *，並* 執行商務計算 *並* 傳送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-162">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="6b4c0-163">藉由設計較小的方法來分隔考慮並將責任委派給其他元件，您將會在撰寫可測試的程式碼時產生絕佳的進展</span><span class="sxs-lookup"><span data-stu-id="6b4c0-163">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="6b4c0-164">其目標是要藉由從小型和焦點的抽象概念組成動作來建立功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-164">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="6b4c0-165">當資料持續性的時候，我們所要尋找的小型和焦點抽象概念很常見，它們已記載為設計模式。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-165">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="6b4c0-166">Fowler 企業應用程式架構的書籍模式，是在列印時描述這些模式的第一項工作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-166">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="6b4c0-167">我們將在下列各節中提供這些模式的簡短說明，我們將示範這些 ADO.NET Entity Framework 如何實行這些模式並搭配使用。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-167">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="6b4c0-168">存放庫模式</span><span class="sxs-lookup"><span data-stu-id="6b4c0-168">The Repository Pattern</span></span>

<span data-ttu-id="6b4c0-169">Fowler 會指出「存放庫」會使用類似集合的介面來協調網域和資料對應層，以存取網域物件」。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-169">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="6b4c0-170">存放庫模式的目標是要將程式碼與資料存取的 minutiae 隔離，而且我們看到先前的隔離是可測試性的必要特性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-170">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="6b4c0-171">隔離的關鍵是存放庫如何使用類似集合的介面來公開物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-171">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="6b4c0-172">您所撰寫用來使用存放庫的邏輯，並不知道存放庫將如何具體化您要求的物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-172">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="6b4c0-173">存放庫可能會與資料庫通訊，也可能只是從記憶體中的集合傳回物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-173">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="6b4c0-174">您所有的程式碼都必須知道，存放庫似乎會維護集合，而且您可以從集合中取出、新增和刪除物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-174">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="6b4c0-175">在現有的 .NET 應用程式中，實體存放庫通常會繼承自泛型介面，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6b4c0-175">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="6b4c0-176">當我們提供 EF4 的執行時，我們會對介面定義進行一些變更，但基本概念仍維持不變。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-176">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="6b4c0-177">程式碼可以使用實作為此介面的具體存放庫，依其主鍵值抓取實體、根據述詞的評估取得實體的集合，或單純取出所有可用的實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-177">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="6b4c0-178">程式碼也可以透過存放庫介面新增和移除實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-178">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="6b4c0-179">由於有員工物件的 IRepository，程式碼可以執行下列作業。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-179">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="6b4c0-180">由於程式碼使用的是 (IRepository 員工) 的介面，因此我們可以為程式碼提供不同的介面實現。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-180">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="6b4c0-181">其中一個實作為 EF4，並將物件保存到 Microsoft SQL Server 資料庫中。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-181">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="6b4c0-182">我們在) 測試期間所使用的不同實 (，可能會受到員工物件的記憶體中清單所支援。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-182">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="6b4c0-183">介面將有助於在程式碼中達成隔離。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-183">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="6b4c0-184">請注意，IRepository &lt; T &gt; 介面不會公開儲存作業。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-184">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="6b4c0-185">如何更新現有的物件？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-185">How do we update existing objects?</span></span> <span data-ttu-id="6b4c0-186">您可能會遇到包含儲存作業的 IRepository 定義，而這些儲存機制的執行必須立即將物件保存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-186">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="6b4c0-187">不過，在許多應用程式中，我們不想要個別保存物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-187">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="6b4c0-188">相反地，我們想要將物件從不同的存放庫帶入生命，將這些物件修改為商務活動的一部分，然後在單一、不可部分完成的作業中保存所有物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-188">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="6b4c0-189">幸運的是，有一個模式可以允許這種類型的行為。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-189">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="6b4c0-190">工作單位模式</span><span class="sxs-lookup"><span data-stu-id="6b4c0-190">The Unit of Work Pattern</span></span>

<span data-ttu-id="6b4c0-191">Fowler 指出工作單位會「維護受商務交易影響的物件清單，並協調寫出變更和並行問題的解決」。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-191">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="6b4c0-192">從存放庫追蹤我們所提供之物件的變更，以及當我們告知工作單位認可變更時，我們對物件所做的任何變更，都是負責追蹤的工作單位。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-192">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="6b4c0-193">工作單位也必須負責採用我們新增至所有存放庫的新物件，並將物件插入資料庫，也會管理刪除。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-193">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="6b4c0-194">如果您曾經使用過 ADO.NET 資料集完成任何工作，就已經熟悉工作單位模式。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-194">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="6b4c0-195">ADO.NET 資料集能夠追蹤更新、刪除和插入 DataRow 物件，並可在 TableAdapter 的協助下 (，) 協調對資料庫所做的所有變更。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-195">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="6b4c0-196">不過，資料集物件會建立基礎資料庫之中斷連接子集的模型。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-196">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="6b4c0-197">工作單位模式表現相同的行為，但使用的是與資料存取程式碼和不知道資料庫隔離的商務物件和網域物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-197">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="6b4c0-198">在 .NET 程式碼中建立工作單位模型的抽象概念可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="6b4c0-198">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="6b4c0-199">藉由公開工作單位的存放庫參考，我們可以確保單一工作單位物件能夠追蹤在商務交易期間具體化的所有實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-199">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="6b4c0-200">實際工作單位的 Commit 方法的執行方式，就是將記憶體中的變更與資料庫協調。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-200">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="6b4c0-201">有了 IUnitOfWork 參考，程式碼可以變更從一個或多個存放庫取出的商務物件，並使用不可部分完成的認可作業來儲存所有變更。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-201">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="6b4c0-202">延遲負載模式</span><span class="sxs-lookup"><span data-stu-id="6b4c0-202">The Lazy Load Pattern</span></span>

<span data-ttu-id="6b4c0-203">Fowler 使用名稱消極式載入來描述「不包含所需資料的物件，但知道如何取得」。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-203">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="6b4c0-204">透明消極式載入是撰寫可測試的商務程式碼並使用關係資料庫時，必須具備的一項重要功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-204">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="6b4c0-205">例如，請考慮下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-205">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="6b4c0-206">工時卡集合的填入方式為何？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-206">How is the TimeCards collection populated?</span></span> <span data-ttu-id="6b4c0-207">有兩個可能的答案。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-207">There are two possible answers.</span></span> <span data-ttu-id="6b4c0-208">其中一個答案是員工存放庫在要求提取員工時，會發出查詢來取得員工以及員工相關聯的時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-208">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="6b4c0-209">在關係資料庫中，這通常需要具有 JOIN 子句的查詢，而且可能會導致抓取超過應用程式需求的資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-209">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="6b4c0-210">如果應用程式永遠不需要接觸工時卡屬性，該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-210">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="6b4c0-211">第二個答案是載入工時卡屬性「依需求」。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-211">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="6b4c0-212">這項消極式載入在商務邏輯中是隱含且透明的，因為程式碼不會叫用特殊的 Api 來取出時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-212">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="6b4c0-213">這段程式碼假設在需要時，會出現時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-213">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="6b4c0-214">延遲載入通常牽涉到方法調用的執行時間攔截，因此有一些魔術。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-214">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="6b4c0-215">攔截程式碼負責與資料庫的交談，並取出時間卡資訊，同時讓商務邏輯保持商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-215">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="6b4c0-216">這種延遲的負載魔術可讓商務程式碼將本身與資料抓取作業隔離，並產生更多可測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-216">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="6b4c0-217">延遲載入的缺點是，當應用程式需要時間卡資訊時，程式碼 *將會執行* 額外的查詢。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-217">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="6b4c0-218">這對許多應用程式而言並不重要，但是針對效能敏感的應用程式或應用程式會在迴圈的每個反復專案期間，執行查詢來取出時間卡片 (問題通常稱為「N + 1 查詢」) 問題，所以消極式載入是一項拖曳。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-218">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="6b4c0-219">在這些情況下，應用程式可能會想要以最有效率的方式立即載入時間卡片資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-219">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="6b4c0-220">幸運的是，當我們移至下一節並實行這些模式時，我們將瞭解 EF4 如何支援隱含的延遲載入和有效率的積極式載入。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-220">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="6b4c0-221">使用 Entity Framework 執行模式</span><span class="sxs-lookup"><span data-stu-id="6b4c0-221">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="6b4c0-222">好消息是，我們在上一節中描述的所有設計模式都是使用 EF4 來執行的簡單方式。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-222">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="6b4c0-223">為了示範，我們將使用簡單的 ASP.NET MVC 應用程式來編輯和顯示員工及其相關聯的時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-223">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="6b4c0-224">我們將從使用下列「簡單的 CLR 物件」 (Poco) 開始。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-224">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

<span data-ttu-id="6b4c0-225">當我們探索 EF4 的不同方法和功能時，這些類別定義會稍微變更，但其目的是要盡可能將這些類別保留 (PI) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-225">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="6b4c0-226">PI 物件並不知道它所保存的*if*狀態在資料庫中的存留*程度*。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-226">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="6b4c0-227">PI 與 Poco 與可測試的軟體密切配合。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-227">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="6b4c0-228">使用 POCO 方法的物件較不受限制、更有彈性且更容易測試，因為它們可以在沒有資料庫存在的情況下運作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-228">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="6b4c0-229">有了 Poco，我們就可以在 Visual Studio 中建立實體資料模型 (EDM)  (請參閱 [圖 1]) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-229">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="6b4c0-230">我們不會使用 EDM 來為我們的實體產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-230">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="6b4c0-231">相反地，我們想要使用我們所謂手動製作的實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-231">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="6b4c0-232">我們只會使用 EDM 來產生資料庫架構，並提供 EF4 需要的中繼資料，以將物件對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-232">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="6b4c0-234">**圖1**</span><span class="sxs-lookup"><span data-stu-id="6b4c0-234">**Figure 1**</span></span>

<span data-ttu-id="6b4c0-235">注意：如果您想要先開發 EDM 模型，您可以從 EDM 產生乾淨的 POCO 程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-235">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="6b4c0-236">您可以使用資料可程式性小組所提供的 Visual Studio 2010 延伸模組來進行這項作業。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-236">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="6b4c0-237">若要下載延伸模組，請從 Visual Studio 的 [工具] 功能表啟動 [擴充管理員]，並搜尋 "POCO" 的線上範本元件庫 (請參閱 [圖 2]) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-237">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="6b4c0-238">有數個適用于 EF 的 POCO 範本。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-238">There are several POCO templates available for EF.</span></span> <span data-ttu-id="6b4c0-239">如需使用範本的詳細資訊，請參閱「 [逐步解說：適用于 Entity Framework 的 POCO 範本](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)」。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-239">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="6b4c0-241">**圖2**</span><span class="sxs-lookup"><span data-stu-id="6b4c0-241">**Figure 2**</span></span>

<span data-ttu-id="6b4c0-242">從這個 POCO 起點，我們將探討兩種可測試程式碼的不同方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-242">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="6b4c0-243">我稱之為 EF 方法的第一種方法，是利用 Entity Framework API 的抽象概念來執行工作單位和存放庫。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-243">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="6b4c0-244">在第二個方法中，我們會建立自己的自訂存放庫抽象概念，然後查看每個方法的優缺點。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-244">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="6b4c0-245">我們將從探索 EF 方法開始著手。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-245">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="6b4c0-246">EF 中心的實作為</span><span class="sxs-lookup"><span data-stu-id="6b4c0-246">An EF Centric Implementation</span></span>

<span data-ttu-id="6b4c0-247">從 ASP.NET MVC 專案考慮下列控制器動作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-247">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="6b4c0-248">此動作會抓取 Employee 物件並傳回結果，以顯示員工的詳細觀點。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-248">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="6b4c0-249">程式碼是否可測試？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-249">Is the code testable?</span></span> <span data-ttu-id="6b4c0-250">至少有兩個測試需要驗證動作的行為。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-250">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="6b4c0-251">首先，我們想要確認該動作會傳回正確的 view –簡單的測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-251">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="6b4c0-252">我們也想要撰寫測試來確認該動作會抓取正確的員工，而且我們想要執行此動作，而不需要執行程式碼來查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-252">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="6b4c0-253">請記住，我們想要隔離受測程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-253">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="6b4c0-254">隔離可確保測試不會因為資料存取程式碼或資料庫設定中的錯誤而失敗。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-254">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="6b4c0-255">如果測試失敗，我們會知道控制器邏輯中有錯誤，而不是在某些較低層級的系統元件中。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-255">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="6b4c0-256">為了達成隔離，我們需要一些抽象概念，例如我們稍早針對存放庫和工作單位提供的介面。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-256">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="6b4c0-257">請記住，儲存機制模式是設計來協調網域物件和資料對應層之間的協調。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-257">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="6b4c0-258">在此案例中，EF4 *是* 資料對應層，且已 &lt; &gt; 從 system.object 命名空間) 提供名為 IObjectSet T (的類似儲存機制抽象概念。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-258">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="6b4c0-259">介面定義看起來如下所示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-259">The interface definition looks like the following.</span></span>

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

<span data-ttu-id="6b4c0-260">IObjectSet &lt; T &gt; 符合存放庫的需求，因為它類似于透過 IEnumerable T)  (的物件集合 &lt; &gt; ，並提供方法來新增和移除模擬集合中的物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-260">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="6b4c0-261">附加和卸離方法會公開 EF4 API 的額外功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-261">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="6b4c0-262">若要使用 IObjectSet &lt; T &gt; 做為存放庫的介面，我們需要一個工作單位抽象單位來將存放庫系結在一起。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-262">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="6b4c0-263">此介面有一個具體的實作為 SQL Server，而且很容易就能使用 EF4 的 ObjectCoNtext 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-263">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="6b4c0-264">ObjectCoNtext 類別是 EF4 API 中的實際工作單位。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-264">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

<span data-ttu-id="6b4c0-265">將 IObjectSet 的工作變得很簡單，只要叫 &lt; &gt; 用 ObjectCoNtext 物件的 createobjectset<tentity> 方法即可。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-265">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="6b4c0-266">在幕後，架構會使用我們在 EDM 中提供的中繼資料來產生具體的 ObjectSet &lt; T &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-266">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="6b4c0-267">我們會保留傳回 IObjectSet &lt; T 介面， &gt; 因為它有助於保留用戶端程式代碼中的可測試性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-267">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="6b4c0-268">這種具體的執行在生產環境中很有用，但我們必須專注于如何使用我們的 IUnitOfWork 抽象概念來加速測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-268">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="6b4c0-269">測試雙精度浮點數</span><span class="sxs-lookup"><span data-stu-id="6b4c0-269">The Test Doubles</span></span>

<span data-ttu-id="6b4c0-270">為了找出控制器動作，我們必須能夠在 (的 ObjectCoNtext) 和測試雙或「假」工作單元之間切換， (執行記憶體中的作業) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-270">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="6b4c0-271">執行這種類型切換的常見方法是不要讓 MVC 控制器具現化工作單位，但改以函式的形式將工作單位傳至控制器中，以作為函式參數。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-271">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="6b4c0-272">上述程式碼是相依性插入的範例。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-272">The above code is an example of dependency injection.</span></span> <span data-ttu-id="6b4c0-273">我們不允許控制器 (工作單位來建立相依性) 但會將相依性插入控制器中。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-273">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="6b4c0-274">在 MVC 專案中，通常會搭配使用自訂控制器 factory 與控制反轉 (IoC) 容器來自動插入相依性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-274">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="6b4c0-275">這些主題已超出本文的討論範圍，但您可以遵循本文結尾的參考來閱讀更多內容。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-275">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="6b4c0-276">您可以用來進行測試的假工作單元，看起來可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-276">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

<span data-ttu-id="6b4c0-277">請注意，虛設的工作單位會公開認可的屬性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-277">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="6b4c0-278">將功能新增至可協助測試的假類別，有時會很有用。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-278">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="6b4c0-279">在此情況下，很容易就能觀察程式碼是否藉由檢查認可的屬性來認可工作單位。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-279">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="6b4c0-280">我們也需要假的 IObjectSet &lt; T &gt; 來保存員工和工時卡在記憶體中的物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-280">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="6b4c0-281">我們可以使用泛型提供單一的實作為。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-281">We can provide a single implementation using generics.</span></span>

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

<span data-ttu-id="6b4c0-282">這項測試會將大部分的工作委派給基礎 HashSet &lt; T &gt; 物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-282">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="6b4c0-283">請注意，IObjectSet &lt; T &gt; 需要泛型條件約束，強制 T 做為)  (參考型別的類別，而且也會強制我們執行 IQueryable &lt; T &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-283">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="6b4c0-284">&lt; &gt; 使用標準 LINQ 運算子 AsQueryable，可輕鬆地將記憶體中的集合顯示為 IQueryable T。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-284">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="6b4c0-285">測試</span><span class="sxs-lookup"><span data-stu-id="6b4c0-285">The Tests</span></span>

<span data-ttu-id="6b4c0-286">傳統單元測試會使用單一測試類別來保存單一 MVC 控制器中所有動作的所有測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-286">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="6b4c0-287">我們可以使用我們所建立的記憶體 fakes，撰寫這些測試或任何類型的單元測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-287">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="6b4c0-288">不過，在本文中，我們會避免整合型測試類別的方法，而是將測試分組以專注于特定的功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-288">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="6b4c0-289">例如，「建立新員工」可能是我們想要測試的功能，因此我們將使用單一測試類別來確認負責建立新員工的單一控制器動作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-289">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="6b4c0-290">所有這些精細的測試類別都需要一些常見的設定程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-290">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="6b4c0-291">例如，我們一律需要建立記憶體中的存放庫和假的工作單元。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-291">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="6b4c0-292">我們也需要員工控制器的實例，並插入假的工作單位。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-292">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="6b4c0-293">我們會使用基類，在測試類別之間共用這個通用的設定程式碼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-293">We’ll share this common setup code across test classes by using a base class.</span></span>

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

<span data-ttu-id="6b4c0-294">我們在基類中使用的「物件母親」是建立測試資料的一種常見模式。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-294">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="6b4c0-295">母親物件包含可具現化測試實體以便跨多個測試裝置使用的 factory 方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-295">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

<span data-ttu-id="6b4c0-296">我們可以使用 EmployeeControllerTestBase 作為許多測試裝置的基類 (請參閱 [圖 3]) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-296">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="6b4c0-297">每個測試裝置都會測試特定的控制器動作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-297">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="6b4c0-298">例如，一個測試裝置將著重于測試在 HTTP GET 要求期間使用的 [建立] 動作 (為了顯示建立員工) 的觀點，而不同的裝置會著重在 HTTP POST 要求中使用的 [建立] 動作 (以取得使用者所提交的資訊，以建立員工) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-298">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="6b4c0-299">每個衍生類別只負責其特定內容中所需的設定，以及提供驗證其特定測試內容之結果所需的判斷提示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-299">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="6b4c0-301">**圖3**</span><span class="sxs-lookup"><span data-stu-id="6b4c0-301">**Figure 3**</span></span>

<span data-ttu-id="6b4c0-302">測試程式碼不需要此處提供的命名慣例和測試樣式，只是其中一種方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-302">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="6b4c0-303">[圖 4] 顯示 Visual Studio 2010 的 Jet 大腦 Resharper 測試執行器外掛程式中執行的測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-303">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="6b4c0-305">**圖4**</span><span class="sxs-lookup"><span data-stu-id="6b4c0-305">**Figure 4**</span></span>

<span data-ttu-id="6b4c0-306">利用基類來處理共用的安裝程式碼，每個控制器動作的單元測試都很小，而且很容易撰寫。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-306">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="6b4c0-307">測試將會快速執行 (因為我們會) 執行記憶體中的作業，而且不應該因為不相關的基礎結構或環境 (問題而失敗，因為我們已將測試中的單元隔離) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-307">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

<span data-ttu-id="6b4c0-308">在這些測試中，基類會進行大部分的設定工作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-308">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="6b4c0-309">請記住，基類的函式會建立記憶體中的存放庫、假的工作單位，以及 EmployeeController 類別的實例。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-309">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="6b4c0-310">測試類別衍生自此基類，並著重于測試 Create 方法的細節。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-310">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="6b4c0-311">在此情況下，詳細資訊歸結到您將在任何單元測試程式中看到的「排列、操作和判斷提示」步驟：</span><span class="sxs-lookup"><span data-stu-id="6b4c0-311">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="6b4c0-312">建立 newEmployee 物件以模擬傳入的資料。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-312">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="6b4c0-313">叫用 EmployeeController 的 Create 動作，然後傳入 newEmployee。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-313">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="6b4c0-314">確認 [建立] 動作會產生預期的結果 (員工出現在 [存放庫]) 中。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-314">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="6b4c0-315">我們所建立的內容可讓我們測試任何 EmployeeController 動作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-315">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="6b4c0-316">例如，當我們為員工控制器的索引動作撰寫測試時，我們可以從測試基類繼承，以針對測試建立相同的基本設定。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-316">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="6b4c0-317">同樣地，基類會建立記憶體中的存放庫、假的工作單位，以及 EmployeeController 的實例。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-317">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="6b4c0-318">索引動作的測試只需要專注于叫用索引動作，並測試動作所傳回之模型的品質。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-318">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

<span data-ttu-id="6b4c0-319">我們使用記憶體內部 fakes 建立的測試，是針對測試軟體的 *狀態* 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-319">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="6b4c0-320">例如，在測試建立動作時，我們想要在建立動作執行之後檢查儲存機制的狀態–存放庫是否保有新的員工？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-320">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="6b4c0-321">稍後我們將探討以互動為基礎的測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-321">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="6b4c0-322">互動測試將會詢問受測程式碼是否在物件上叫用適當的方法，並傳遞正確的參數。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-322">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="6b4c0-323">現在，我們將移至另一個設計模式，也就是消極式載入。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-323">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="6b4c0-324">積極式載入和消極式載入</span><span class="sxs-lookup"><span data-stu-id="6b4c0-324">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="6b4c0-325">在 ASP.NET MVC web 應用程式中的某個時間點，我們可能會想要顯示員工的資訊並包含員工相關聯的時間卡。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-325">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="6b4c0-326">例如，我們可能會有一個顯示員工名稱和系統中的總時間卡總數的時間卡摘要顯示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-326">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="6b4c0-327">有幾種方法可以執行這項功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-327">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="6b4c0-328">Projection</span><span class="sxs-lookup"><span data-stu-id="6b4c0-328">Projection</span></span>

<span data-ttu-id="6b4c0-329">建立摘要的一個簡單方法是，針對我們想要顯示在視圖中的資訊，建立專用的模型。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-329">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="6b4c0-330">在此案例中，模型看起來可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-330">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="6b4c0-331">請注意，EmployeeSummaryViewModel 不是實體，換句話說，這不是我們想要保存在資料庫中的內容。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-331">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="6b4c0-332">我們只會使用這個類別，以強型別的方式將資料隨機播放到視圖中。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-332">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="6b4c0-333">視圖模型就像是 (DTO) 的資料傳輸物件，因為它不包含任何方法 () –僅限屬性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-333">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="6b4c0-334">這些屬性會保存需要移動的資料。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-334">The properties will hold the data we need to move.</span></span> <span data-ttu-id="6b4c0-335">使用 LINQ 的標準投射運算子（Select 運算子）來具現化此視圖模型很容易。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-335">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

<span data-ttu-id="6b4c0-336">上述程式碼有兩個值得注意的功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-336">There are two notable features to the above code.</span></span> <span data-ttu-id="6b4c0-337">首先–程式碼很容易測試，因為它仍然很容易觀察和隔離。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-337">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="6b4c0-338">Select 運算子的運作方式，與我們對實際工作單位執行的記憶體內部 fakes 一樣。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-338">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

<span data-ttu-id="6b4c0-339">第二個值得注意的功能是程式碼如何讓 EF4 產生單一、有效率的查詢，將員工和時間卡片資訊組合在一起。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-339">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="6b4c0-340">我們已將員工資訊和時間卡片資訊載入相同的物件中，而不需要使用任何特殊的 Api。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-340">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="6b4c0-341">此程式碼只會使用適用于記憶體內部資料來源和遠端資料源的標準 LINQ 運算子來表示所需的資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-341">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="6b4c0-342">EF4 能夠將 LINQ 查詢和 C 編譯器所產生的運算式樹狀架構，轉譯 \# 成單一且有效率的 t-SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-342">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

<span data-ttu-id="6b4c0-343">在其他情況下，我們不想使用視圖模型或 DTO 物件，而是實際實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-343">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="6b4c0-344">當我們知道需要員工 *和* 員工的時間卡時，可以立即以不顯眼且有效率的方式載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-344">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="6b4c0-345">明確的積極式載入</span><span class="sxs-lookup"><span data-stu-id="6b4c0-345">Explicit Eager Loading</span></span>

<span data-ttu-id="6b4c0-346">當我們想要立即負載相關的實體資訊時，我們需要某種機制來 (商務邏輯，而在此案例中，控制器動作邏輯) 來表達其對存放庫的期望。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-346">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="6b4c0-347">EF4 ObjectQuery &lt; T &gt; 類別會定義包含方法，以指定要在查詢期間取得的相關物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-347">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="6b4c0-348">請記住，EF4 ObjectCoNtext 會透過 &lt; &gt; 繼承自 ObjectQuery t 的具體 ObjectSet T 類別來公開實體 &lt; &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-348">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="6b4c0-349">如果我們 &lt; &gt; 在控制器動作中使用 ObjectSet T 參考，我們可以撰寫下列程式碼來指定每位員工的一次性載入的時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-349">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="6b4c0-350">不過，由於我們想要讓程式碼可供測試，因此不會 &lt; &gt; 從實際的工作單位類別外部公開 ObjectSet T。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-350">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="6b4c0-351">相反地，我們會依賴 IObjectSet &lt; T &gt; 介面，此介面較容易偽造，但 IObjectSet &lt; T 不 &gt; 會定義 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-351">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="6b4c0-352">LINQ 的優點是我們可以建立自己的 Include 運算子。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-352">The beauty of LINQ is that we can create our own Include operator.</span></span>

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

<span data-ttu-id="6b4c0-353">請注意，這個 Include 運算子定義為 IQueryable T 的擴充方法， &lt; &gt; 而不是 IObjectSet &lt; T &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-353">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="6b4c0-354">如此一來，我們就能夠搭配更廣泛的可能類型來使用方法，包括 IQueryable &lt; T &gt; 、IObjectSet &lt; T &gt; 、ObjectQuery &lt; T &gt; 和 ObjectSet &lt; T &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-354">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="6b4c0-355">當基礎序列不是正版 EF4 ObjectQuery &lt; T 時，就不 &gt; 會有任何損害，而且 Include 運算子也不會有任何作用。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-355">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="6b4c0-356">如果基礎序列 *是* ObjectQuery &lt; t &gt; (或衍生自 ObjectQuery &lt; t &gt;) ，則 EF4 將會看到我們對其他資料的需求，並制訂適當的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-356">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="6b4c0-357">有了這個新的運算子之後，我們就可以明確地向存放庫要求立即載入的時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-357">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="6b4c0-358">針對實際的 ObjectCoNtext 執行時，程式碼會產生下列單一查詢。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-358">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="6b4c0-359">此查詢會從資料庫收集足夠的資訊以具體化員工物件，並完整填入其工時卡屬性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-359">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

<span data-ttu-id="6b4c0-360">最棒的是，動作方法內的程式碼仍可完全測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-360">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="6b4c0-361">我們不需要為 fakes 提供任何額外的功能來支援 Include 運算子。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-361">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="6b4c0-362">壞消息是我們必須使用我們想要保留非持續性的程式碼內的 Include 運算子。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-362">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="6b4c0-363">這是在建立可測試的程式碼時，需要評估的取捨類型的主要範例。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-363">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="6b4c0-364">有時您需要讓保存庫抽象以外的持續性問題發生，以符合效能目標。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-364">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="6b4c0-365">積極式載入的替代方案是消極式載入。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-365">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="6b4c0-366">消極式載入表示我們 *不* 需要我們的商務程式碼來明確宣佈相關資料的需求。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-366">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="6b4c0-367">相反地，我們會在應用程式中使用我們的實體，如果需要其他資料，Entity Framework 將視需要載入資料。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-367">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="6b4c0-368">消極式載入</span><span class="sxs-lookup"><span data-stu-id="6b4c0-368">Lazy Loading</span></span>

<span data-ttu-id="6b4c0-369">很容易就能想像出一種商務邏輯所需的資料。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-369">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="6b4c0-370">我們可能知道邏輯需要員工物件，但我們可能會分支到不同的執行路徑，其中某些路徑需要來自員工的時間卡資訊，而有些則不需要。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-370">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="6b4c0-371">像這樣的案例很適合用於隱含的延遲載入，因為資料神奇地出現在需要的基礎上。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-371">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="6b4c0-372">消極式載入（也稱為延後載入）會在實體物件上放置一些需求。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-372">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="6b4c0-373">具有真正持續性無知的 Poco 不會面對持續性層的任何需求，但真正的持續性無知幾乎無法達成。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-373">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="6b4c0-374">相反地，我們會以相對的角度測量持續性無知。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-374">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="6b4c0-375">如果我們需要從持續性導向基類繼承，或使用特製化集合來達到 Poco 中的消極式載入，則會很不幸。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-375">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="6b4c0-376">幸運的是，EF4 有一個較不具侵入性的解決方案。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-376">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="6b4c0-377">幾乎無法偵測</span><span class="sxs-lookup"><span data-stu-id="6b4c0-377">Virtually Undetectable</span></span>

<span data-ttu-id="6b4c0-378">使用 POCO 物件時，EF4 可以動態產生實體的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-378">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="6b4c0-379">這些 proxy 會以不可見的方式包裝具體化 Poco，並藉由攔截每個屬性的 get 和 set 作業來執行其他工作，以提供其他服務。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-379">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="6b4c0-380">這種服務是我們要尋找的消極式載入功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-380">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="6b4c0-381">另一項服務是有效率的變更追蹤機制，可在程式變更實體的屬性值時記錄。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-381">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="6b4c0-382">在 SaveChanges 方法期間，ObjectCoNtext 會使用變更清單來保存任何已修改的實體（使用 UPDATE 命令）。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-382">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="6b4c0-383">不過，若要讓這些 proxy 能夠運作，他們需要一種方法來攔截實體上的屬性 get 和 set 作業，而 proxy 會覆寫虛擬成員以達成此目標。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-383">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="6b4c0-384">因此，如果我們想要有隱含的延遲載入和有效率的變更追蹤，我們必須回到 POCO 類別定義，並將屬性標示為虛擬。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-384">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="6b4c0-385">我們仍然可以說，Employee 實體大多是持續性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-385">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="6b4c0-386">唯一的需求是使用虛擬成員，而這不會影響程式碼的可測試性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-386">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="6b4c0-387">我們不需要從任何特殊基類衍生，或甚至使用消極式載入專用的特殊集合。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-387">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="6b4c0-388">如同程式碼所示，執行 ICollection T 的任何類別 &lt; &gt; 都可以用來保存相關的實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-388">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="6b4c0-389">此外，我們還需要在我們的工作單元內進行一項微小的變更。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-389">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="6b4c0-390">當直接使用 ObjectCoNtext 物件時，消極式載入預設為 *關閉* 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-390">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="6b4c0-391">我們可以在 CoNtextOptions 屬性上設定一個屬性來啟用延後載入，如果我們想要在所有地方啟用消極式載入，我們可以在實際的工作單元內設定這個屬性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-391">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

<span data-ttu-id="6b4c0-392">啟用隱含消極式載入後，應用程式程式碼可以使用員工和員工相關聯的時間卡，而其餘知道不知道 EF 載入額外資料所需的工作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-392">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="6b4c0-393">消極式載入可讓應用程式程式碼更容易撰寫，並以 proxy 魔術讓程式碼保持完整的可測試性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-393">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="6b4c0-394">工作單位的記憶體內部 fakes 可以在測試期間視需要預先載入具有相關聯資料的假實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-394">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="6b4c0-395">現在，我們將使用 IObjectSet T 來建立存放庫的注意力 &lt; &gt; ，並查看抽象概念來隱藏持續性架構的所有徵兆。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-395">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="6b4c0-396">自訂存放庫</span><span class="sxs-lookup"><span data-stu-id="6b4c0-396">Custom Repositories</span></span>

<span data-ttu-id="6b4c0-397">當我們在本文中第一次看到工作單位設計模式時，我們提供了一些範例程式碼，可讓工作單位看起來像這樣。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-397">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="6b4c0-398">讓我們使用我們使用的員工和員工的時間卡案例，重新展示此原始概念。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-398">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="6b4c0-399">此工作單位和我們在上一節中建立的工作單位之間的主要差異在於，此工作單元如何不使用 EF4 架構中的任何抽象 (沒有 IObjectSet &lt; T &gt;) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-399">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="6b4c0-400">IObjectSet &lt; T &gt; 適用于存放庫介面，但它所公開的 API 可能無法與應用程式的需求完全一致。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-400">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="6b4c0-401">在接下來的方法中，我們將使用自訂的 IRepository &lt; T 抽象概念來代表存放庫 &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-401">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="6b4c0-402">許多開發人員都遵循以測試為導向的設計、行為導向設計，以及領域導向的方法設計，基於許多原因而偏好使用 IRepository &lt; T &gt; 方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-402">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="6b4c0-403">首先，IRepository &lt; T &gt; 介面代表「反損毀」層。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-403">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="6b4c0-404">如同網域導向設計書籍中的 Eric Evans 所述，反損毀層可讓您的網域程式碼遠離基礎結構 Api，例如持續性 API。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-404">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="6b4c0-405">其次，開發人員可以在存放庫中建立方法，以符合應用程式 (在) 撰寫測試時所發現的確切需求。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-405">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="6b4c0-406">例如，我們可能經常需要使用識別碼值找出單一實體，因此我們可以將 FindById 方法新增至存放庫介面。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-406">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="6b4c0-407">我們 &lt; 的 IRepository T &gt; 定義看起來會如下所示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-407">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="6b4c0-408">請注意，我們會回頭使用 IQueryable &lt; T &gt; 介面來公開實體集合。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-408">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="6b4c0-409">IQueryable &lt; T &gt; 可讓 LINQ 運算式樹狀架構流入 EF4 提供者，並為提供者提供查詢的整體觀點。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-409">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="6b4c0-410">第二個選項是傳回 IEnumerable &lt; T &gt; ，這表示 EF4 LINQ 提供者將只會看到在存放庫內建立的運算式。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-410">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="6b4c0-411">在存放庫以外完成的任何群組、排序和投影都不會組成傳送至資料庫的 SQL 命令，這可能會影響效能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-411">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="6b4c0-412">另一方面，只傳回 IEnumerable T 結果的存放庫 &lt; &gt; 永遠不會對您造成新的 SQL 命令感到驚訝。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-412">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="6b4c0-413">這兩種方法都可以運作，而且這兩種方法都可供測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-413">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="6b4c0-414">您可以直接 &lt; &gt; 使用泛型和 EF4 ObjectCoNtext API 來提供 IRepository T 介面的單一執行。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-414">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

<span data-ttu-id="6b4c0-415">IRepository &lt; T &gt; 方法可讓我們對查詢有一些額外的控制權，因為用戶端必須叫用方法來取得實體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-415">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="6b4c0-416">在方法內部，我們可以提供額外的檢查和 LINQ 運算子來強制執行應用程式條件約束。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-416">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="6b4c0-417">請注意，介面在泛型型別參數上有兩個條件約束。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-417">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="6b4c0-418">第一個條件約束是 ObjectSet T 所需的類別缺點污點 &lt; &gt; ，而第二個條件約束會強制我們的實體執行 IEntity，也就是為應用程式建立的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-418">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="6b4c0-419">IEntity 介面會強制實體具有可讀取的識別碼屬性，然後我們可以在 FindById 方法中使用這個屬性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-419">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="6b4c0-420">IEntity 會使用下列程式碼來定義。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-420">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="6b4c0-421">IEntity 可能被視為不符合持續性無知，因為我們的實體需要執行這個介面。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-421">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="6b4c0-422">記住持續性無知是關於取捨，而針對許多 FindById 功能，會超過介面所加諸的限制。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-422">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="6b4c0-423">介面對可測試性沒有任何影響。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-423">The interface has no impact on testability.</span></span>

<span data-ttu-id="6b4c0-424">具現化即時 IRepository &lt; T &gt; 需要 EF4 ObjectCoNtext，因此具體的工作執行單位應該管理具現化。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-424">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a><span data-ttu-id="6b4c0-425">使用自訂存放庫</span><span class="sxs-lookup"><span data-stu-id="6b4c0-425">Using the Custom Repository</span></span>

<span data-ttu-id="6b4c0-426">使用我們的自訂存放庫，與使用以 IObjectSet T 為基礎的儲存機制並無太大差異 &lt; &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-426">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="6b4c0-427">我們必須先叫用存放庫的方法，以抓取 IQueryable T 參考，而不是直接將 LINQ 運算子套用至 &lt; 屬性 &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-427">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="6b4c0-428">請注意，我們先前實行的自訂 Include 運算子將可運作，而不會變更。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-428">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="6b4c0-429">存放庫的 FindById 方法會從嘗試取出單一實體的動作中移除重複的邏輯。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-429">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="6b4c0-430">這兩種方法的可測試性並沒有明顯的差異。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-430">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="6b4c0-431">我們可以藉 &lt; 由建立 HashSet 員工所支援的具體類別，來提供 IRepository T 的假實， &gt; &lt; &gt; 就像我們在上一節中所做的一樣。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-431">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="6b4c0-432">不過，有些開發人員偏好使用 mock 物件和模擬物件架構，而不是建立 fakes。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-432">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="6b4c0-433">我們將探討如何使用模擬來測試我們的執行，並在下一節討論模擬和 fakes 之間的差異。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-433">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="6b4c0-434">使用模擬進行測試</span><span class="sxs-lookup"><span data-stu-id="6b4c0-434">Testing with Mocks</span></span>

<span data-ttu-id="6b4c0-435">有不同的方法可以建立 Fowler 「測試雙精度」的聖馬丁。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-435">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="6b4c0-436">測試雙 (像是電影 stunt 雙) ，是您在測試期間針對真實的生產物件所建立的「獨立」物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-436">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="6b4c0-437">我們所建立的記憶體內部存放庫會針對與 SQL Server 交談的存放庫測試雙精度浮點數。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-437">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="6b4c0-438">我們已瞭解如何在單元測試期間使用這些測試雙精度浮點數，以隔離程式碼並讓測試快速執行。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-438">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="6b4c0-439">我們所建立的測試將會有實際的工作執行。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-439">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="6b4c0-440">在幕後，每個物件都會儲存物件的具體集合，而且當我們在測試期間操作存放庫時，它們會在此集合中加入和移除物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-440">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="6b4c0-441">有些開發人員想要以這種方式來建立測試，這種方式是使用真正的程式碼和運作中的。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-441">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="6b4c0-442">這些測試會加倍，我們稱之為 *fakes*。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-442">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="6b4c0-443">它們具有運作中的實作為，但其實不足以供生產環境使用。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-443">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="6b4c0-444">假的存放庫實際上不會寫入資料庫。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-444">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="6b4c0-445">假的 SMTP 伺服器實際上不會透過網路傳送電子郵件訊息。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-445">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="6b4c0-446">模擬與 Fakes</span><span class="sxs-lookup"><span data-stu-id="6b4c0-446">Mocks versus Fakes</span></span>

<span data-ttu-id="6b4c0-447">另外還有另一種類型的測試雙重稱為 *mock*。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-447">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="6b4c0-448">雖然 fakes 具有運作中的執行，但模擬沒有任何執行。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-448">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="6b4c0-449">藉由模擬物件架構的說明，我們會在執行時間時建立這些 mock 物件，並將它們作為測試雙精度浮點數。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-449">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="6b4c0-450">在本節中，我們將使用開放原始碼模擬 framework Moq。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-450">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="6b4c0-451">以下是使用 Moq 動態建立員工存放庫之測試雙的簡單範例。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-451">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="6b4c0-452">我們要求 Moq 進行 IRepository &lt; 員工的 &gt; 執行，並以動態方式建立一個。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-452">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="6b4c0-453">我們可以藉 &lt; &gt; 由存取 Mock T 物件的 object 屬性，取得執行 IRepository 員工的 &lt; 物件 &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-453">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="6b4c0-454">這是我們可以傳遞至控制器的內建物件，而且不會知道這是測試雙重或真正的儲存機制。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-454">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="6b4c0-455">我們可以在物件上叫用方法，就像我們在實際執行的物件上叫用方法一樣。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-455">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="6b4c0-456">當我們叫用 Add 方法時，您必須想知道 mock 存放庫會做什麼。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-456">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="6b4c0-457">因為 mock 物件背後沒有任何執行，所以 Add 不會執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-457">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="6b4c0-458">幕後沒有具象的集合，就像我們所撰寫的 fakes 一樣，因此會捨棄員工。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-458">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="6b4c0-459">FindById 傳回的值呢？</span><span class="sxs-lookup"><span data-stu-id="6b4c0-459">What about the return value of FindById?</span></span> <span data-ttu-id="6b4c0-460">在此情況下，mock 物件會執行唯一可執行檔動作，這會傳回預設值。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-460">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="6b4c0-461">由於我們會傳回 (員工) 的參考型別，因此傳回值為 null 值。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-461">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="6b4c0-462">模擬可能音效毫無價值;不過，我們還沒有談到模擬的兩項功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-462">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="6b4c0-463">首先，Moq 架構會記錄對 mock 物件發出的所有呼叫。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-463">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="6b4c0-464">稍後在程式碼中，我們可以詢問 Moq 是否有任何人叫用 Add 方法，或是否有任何人叫用 FindById 方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-464">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="6b4c0-465">我們稍後會看到如何在測試中使用這個「黑箱」錄製功能。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-465">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="6b4c0-466">第二個很棒的功能，就是我們可以如何使用 Moq 來對具有 *預期*的 mock 物件進行程式設計。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-466">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="6b4c0-467">預期會告知 mock 物件如何回應任何指定的互動。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-467">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="6b4c0-468">例如，我們可以在我們的 mock 中設計預期的程式，並在有人叫用 FindById 時告訴它傳回 employee 物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-468">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="6b4c0-469">Moq 架構會使用安裝程式 API 和 lambda 運算式來設計這些期望。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-469">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

<span data-ttu-id="6b4c0-470">在此範例中，我們會要求 Moq 動態建立存放庫，然後在預期的情況下為存放庫設計程式。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-470">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="6b4c0-471">預期當有人叫用傳遞值為5的 FindById 方法時，預期會告知 mock 物件傳回識別碼值為5的新員工物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-471">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="6b4c0-472">這種測試會通過，而且我們不需要建立完整的實 IRepository &lt; t &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-472">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="6b4c0-473">讓我們重新審視稍早撰寫的測試，並將其重新修改為使用模擬，而不是 fakes。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-473">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="6b4c0-474">就像之前一樣，我們將使用基類來設定所有控制器測試所需的基礎結構的通用部分。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-474">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

<span data-ttu-id="6b4c0-475">安裝程式碼大多保持不變。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-475">The setup code remains mostly the same.</span></span> <span data-ttu-id="6b4c0-476">我們不使用 fakes，而是使用 Moq 來建立模擬物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-476">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="6b4c0-477">當程式碼叫用 employee 屬性時，基底類別會排列模擬單位來傳回 mock 存放庫。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-477">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="6b4c0-478">Mock 設定的其餘部分會在每個特定案例專用的測試裝置內進行。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-478">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="6b4c0-479">例如，索引動作的測試裝置會設定模擬儲存機制，以在動作叫用 mock 儲存機制的 FindAll 方法時，傳回員工清單。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-479">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

<span data-ttu-id="6b4c0-480">除了期望外，我們的測試看起來類似于先前的測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-480">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="6b4c0-481">不過，有了 mock 架構的錄音功能，我們就可以從不同的角度進行測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-481">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="6b4c0-482">在下一節中，我們將探討這個新的觀點。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-482">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="6b4c0-483">狀態與互動測試</span><span class="sxs-lookup"><span data-stu-id="6b4c0-483">State versus Interaction Testing</span></span>

<span data-ttu-id="6b4c0-484">您可以使用不同的技巧來測試具有 mock 物件的軟體。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-484">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="6b4c0-485">其中一個方法是使用以狀態為基礎的測試，也就是我們在這份檔中的目前為止所做的。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-485">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="6b4c0-486">以狀態為基礎的測試會對軟體的狀態進行判斷提示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-486">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="6b4c0-487">在最後一項測試中，我們已在控制器上叫用動作方法，並對它應該建立的模型提出判斷提示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-487">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="6b4c0-488">以下是一些測試狀態的其他範例：</span><span class="sxs-lookup"><span data-stu-id="6b4c0-488">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="6b4c0-489">確認在建立執行之後，存放庫包含新的 employee 物件。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-489">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="6b4c0-490">確認模型保存在執行索引之後的所有員工清單。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-490">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="6b4c0-491">確認在刪除執行之後，儲存機制不包含指定的員工。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-491">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="6b4c0-492">模擬物件的另一種方法是驗證 *互動*。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-492">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="6b4c0-493">以狀態為基礎的測試會對物件的狀態進行判斷提示，以互動為基礎的測試會對物件的互動進行判斷提示。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-493">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="6b4c0-494">例如：</span><span class="sxs-lookup"><span data-stu-id="6b4c0-494">For example:</span></span>

-   <span data-ttu-id="6b4c0-495">確認控制器會在建立執行時叫用存放庫的 Add 方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-495">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="6b4c0-496">確認控制器在索引執行時叫用存放庫的 FindAll 方法。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-496">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="6b4c0-497">確認控制器會叫用工作單位的 Commit 方法，以在編輯執行時儲存變更。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-497">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="6b4c0-498">互動測試通常需要較少的測試資料，因為我們不會在集合內刺探並驗證計數。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-498">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="6b4c0-499">例如，如果我們知道詳細資料動作會以正確的值叫用存放庫的 FindById 方法，則此動作的行為可能會正確。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-499">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="6b4c0-500">我們可以驗證此行為，而不需設定任何要從 FindById 傳回的測試資料。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-500">We can verify this behavior without setting up any test data to return from FindById.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

<span data-ttu-id="6b4c0-501">上述測試裝置中唯一需要的設定是基類所提供的設定。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-501">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="6b4c0-502">當我們叫用控制器動作時，Moq 會記錄與 mock 儲存機制的互動。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-502">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="6b4c0-503">使用 Moq 的 Verify API，我們可以詢問 Moq 是否已叫用具有適當識別碼值的 FindById。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-503">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="6b4c0-504">如果控制器未叫用方法，或叫用具有未預期參數值的方法，則 Verify 方法會擲回例外狀況，而且測試將會失敗。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-504">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="6b4c0-505">以下是另一個範例，確認建立動作在目前的工作單位上叫用 Commit。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-505">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="6b4c0-506">互動測試有一個危險，那就是不能指定互動的趨勢。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-506">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="6b4c0-507">Mock 物件記錄和驗證與模擬物件之每次互動的能力，不表示測試應該嘗試驗證每個互動。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-507">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="6b4c0-508">某些互動是實作為詳細資料，您應該只驗證滿足目前測試 *所需* 的互動。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-508">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="6b4c0-509">模擬或 fakes 之間的選擇主要取決於您要測試的系統，以及您的個人 (或小組) 喜好設定。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-509">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="6b4c0-510">模擬物件可以大幅減少實作為測試雙精確度所需的程式碼數量，但不是每個人都熟悉程式設計預期以及驗證互動。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-510">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="6b4c0-511">結論</span><span class="sxs-lookup"><span data-stu-id="6b4c0-511">Conclusions</span></span>

<span data-ttu-id="6b4c0-512">在本白皮書中，我們示範了幾種方法來建立可測試的程式碼，同時使用 ADO.NET Entity Framework 來進行資料持續性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-512">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="6b4c0-513">我們可以利用內建的抽象（例如 IObjectSet &lt; t &gt; ），或建立自己的抽象概念，例如 IRepository &lt; t &gt; 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-513">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="6b4c0-514">在這兩種情況下，ADO.NET Entity Framework 4.0 中的 POCO 支援都可讓這些抽象概念的取用者維持持續性和高測試性。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-514">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="6b4c0-515">隱含消極式載入等其他 EF4 功能可讓商務和應用程式服務程式碼正常運作，而不需要擔心關聯式資料存放區的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-515">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="6b4c0-516">最後，在單元測試中，我們所建立的抽象概念很容易模擬或假，而且我們可以使用這些測試，以達到快速執行、高度隔離和可靠的測試。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-516">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="6b4c0-517">其他資源</span><span class="sxs-lookup"><span data-stu-id="6b4c0-517">Additional Resources</span></span>

-   <span data-ttu-id="6b4c0-518">Robert c. 「 [單一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)」</span><span class="sxs-lookup"><span data-stu-id="6b4c0-518">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="6b4c0-519">Fowler，*企業應用程式架構模式*的[模式目錄](https://www.martinfowler.com/eaaCatalog/index.html)</span><span class="sxs-lookup"><span data-stu-id="6b4c0-519">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="6b4c0-520">Griffin Caprio，「相依性 [插入](https://msdn.microsoft.com/magazine/cc163739.aspx)」</span><span class="sxs-lookup"><span data-stu-id="6b4c0-520">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="6b4c0-521">資料可程式性 Blog [：「逐步解說：使用 Entity Framework 4.0 的測試驅動開發](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)」。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-521">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="6b4c0-522">資料可程式性 Blog：「 [使用存放庫和 Entity Framework 4.0 的工作單位模式](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)」</span><span class="sxs-lookup"><span data-stu-id="6b4c0-522">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="6b4c0-523">Aaron Jensen，「 [電腦規格簡介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」</span><span class="sxs-lookup"><span data-stu-id="6b4c0-523">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="6b4c0-524">Eric 先生，" [BDD With MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"</span><span class="sxs-lookup"><span data-stu-id="6b4c0-524">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="6b4c0-525">Eric Evans，「 [網域導向設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)」</span><span class="sxs-lookup"><span data-stu-id="6b4c0-525">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="6b4c0-526">聖馬丁 Fowler，" [模擬非存根](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="6b4c0-526">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="6b4c0-527">聖馬丁 Fowler，" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="6b4c0-527">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="6b4c0-528">Moq</span><span class="sxs-lookup"><span data-stu-id="6b4c0-528">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="6b4c0-529">傳記</span><span class="sxs-lookup"><span data-stu-id="6b4c0-529">Biography</span></span>

<span data-ttu-id="6b4c0-530">Scott Allen 是 Pluralsight 的技術人員和 OdeToCode.com 創辦人的成員。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-530">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="6b4c0-531">在15年的商務軟體開發中，Scott 已針對從8位 embedded 裝置到可高度擴充的 ASP.NET web 應用程式的所有專案，處理解決方案。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-531">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="6b4c0-532">您可以在他的 OdeToCode 或 Twitter 的網路上，觸及 Scott [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) 。</span><span class="sxs-lookup"><span data-stu-id="6b4c0-532">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
