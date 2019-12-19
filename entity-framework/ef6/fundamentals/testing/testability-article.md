---
title: 可測試性和 Entity Framework 4.0-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 28ec5446ce9faf98fb8fff141832236d70b29daf
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181581"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="8f34b-102">可測試性和 Entity Framework 4。0</span><span class="sxs-lookup"><span data-stu-id="8f34b-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="8f34b-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="8f34b-103">Scott Allen</span></span>

<span data-ttu-id="8f34b-104">發佈日期：2010 年 5 月</span><span class="sxs-lookup"><span data-stu-id="8f34b-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="8f34b-105">簡介</span><span class="sxs-lookup"><span data-stu-id="8f34b-105">Introduction</span></span>

<span data-ttu-id="8f34b-106">本白皮書將說明並示範如何使用 ADO.NET Entity Framework 4.0 和 Visual Studio 2010 撰寫可測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="8f34b-107">本文不會嘗試將焦點放在特定的測試方法，例如測試導向設計（TDD）或行為導向設計（BDD）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="8f34b-108">相反地，本文將著重于如何撰寫使用 ADO.NET Entity Framework 的程式碼，但仍可讓您以自動化方式輕鬆隔離和測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="8f34b-109">我們將探討常見的設計模式，以便在資料存取案例中進行測試，並瞭解如何在使用架構時套用這些模式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="8f34b-110">我們也會探討架構的特定功能，以瞭解這些功能在可測試的程式碼中的使用方式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="8f34b-111">什麼是可測試的程式碼？</span><span class="sxs-lookup"><span data-stu-id="8f34b-111">What Is Testable Code?</span></span>

<span data-ttu-id="8f34b-112">使用自動化單元測試來驗證軟體片段的功能，可提供許多值得的好處。</span><span class="sxs-lookup"><span data-stu-id="8f34b-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="8f34b-113">每個人都知道，良好的測試將會減少應用程式中的軟體缺失，並提升應用程式的品質</span><span class="sxs-lookup"><span data-stu-id="8f34b-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="8f34b-114">良好的單元測試套件可讓開發小組節省時間，並保有其所建立之軟體的控制權。</span><span class="sxs-lookup"><span data-stu-id="8f34b-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="8f34b-115">小組可以對現有程式碼進行變更、重構、重新設計和重建軟體，以符合新的需求，並在應用程式中加入新的元件，同時知道測試套件可以驗證應用程式的行為。</span><span class="sxs-lookup"><span data-stu-id="8f34b-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="8f34b-116">單元測試是快速意見反應迴圈的一部分，可在複雜度增加時，協助變更並保留軟體的可維護性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="8f34b-117">不過，單元測試是以價格提供。</span><span class="sxs-lookup"><span data-stu-id="8f34b-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="8f34b-118">小組必須投入時間來建立及維護單元測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="8f34b-119">建立這些測試所需的工作量與基礎軟體的可**測試**性直接相關。</span><span class="sxs-lookup"><span data-stu-id="8f34b-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="8f34b-120">要測試的軟體有多簡單？</span><span class="sxs-lookup"><span data-stu-id="8f34b-120">How easy is the software to test?</span></span> <span data-ttu-id="8f34b-121">設計具有可測試性之軟體的小組，會比使用未測試軟體的小組更快速地建立有效的測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="8f34b-122">Microsoft 設計了 ADO.NET Entity Framework 4.0 （EF4），並記住可測試性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="8f34b-123">這並不表示開發人員會針對架構程式碼本身撰寫單元測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="8f34b-124">相反地，EF4 的可測試性目標讓您能夠輕鬆地建立以架構為基礎的可測試程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="8f34b-125">在查看特定範例之前，值得了解可測試程式碼的品質。</span><span class="sxs-lookup"><span data-stu-id="8f34b-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="8f34b-126">可測試程式碼的品質</span><span class="sxs-lookup"><span data-stu-id="8f34b-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="8f34b-127">容易測試的程式碼一律會展示至少兩個特性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="8f34b-128">首先，可測試的程式碼很容易**觀察**到。</span><span class="sxs-lookup"><span data-stu-id="8f34b-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="8f34b-129">假設有一組輸入，應該很容易觀察到程式碼的輸出。</span><span class="sxs-lookup"><span data-stu-id="8f34b-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="8f34b-130">例如，測試下列方法很簡單，因為方法會直接傳回計算的結果。</span><span class="sxs-lookup"><span data-stu-id="8f34b-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="8f34b-131">如果方法將計算的值寫入網路通訊端、資料庫資料表或檔案（如下列程式碼），則測試方法會很棘手。</span><span class="sxs-lookup"><span data-stu-id="8f34b-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="8f34b-132">測試必須執行額外的工作來取得值。</span><span class="sxs-lookup"><span data-stu-id="8f34b-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="8f34b-133">其次，可測試的程式碼很容易**隔離**。</span><span class="sxs-lookup"><span data-stu-id="8f34b-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="8f34b-134">讓我們使用下列虛擬程式碼，作為可測試程式碼的不良範例。</span><span class="sxs-lookup"><span data-stu-id="8f34b-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="8f34b-135">方法很容易觀察到-我們可以傳入保險原則，並確認傳回值符合預期的結果。</span><span class="sxs-lookup"><span data-stu-id="8f34b-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="8f34b-136">不過，若要測試方法，我們必須使用正確的架構來安裝資料庫，並設定 SMTP 伺服器，以防方法嘗試傳送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="8f34b-137">單元測試只會想要驗證方法內的計算邏輯，但是測試可能會失敗，因為電子郵件伺服器已離線，或資料庫伺服器已移動。</span><span class="sxs-lookup"><span data-stu-id="8f34b-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="8f34b-138">這兩個失敗都與測試想要驗證的行為無關。</span><span class="sxs-lookup"><span data-stu-id="8f34b-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="8f34b-139">這種行為很容易隔離。</span><span class="sxs-lookup"><span data-stu-id="8f34b-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="8f34b-140">致力於撰寫可測試程式碼的軟體發展人員，通常會努力在其所撰寫的程式碼中維護顧慮的分離。</span><span class="sxs-lookup"><span data-stu-id="8f34b-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="8f34b-141">上述方法應專注于商務計算，並將資料庫和電子郵件的執行詳細資料委派給其他元件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="8f34b-142">Robert c. 會以單一責任原則來呼叫這一項。</span><span class="sxs-lookup"><span data-stu-id="8f34b-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="8f34b-143">物件應封裝單一、窄的責任，例如計算原則的值。</span><span class="sxs-lookup"><span data-stu-id="8f34b-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="8f34b-144">所有其他資料庫和通知工作都必須負責其他物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="8f34b-145">以這種方式撰寫的程式碼較容易隔離，因為它著重于單一工作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="8f34b-146">在 .NET 中，我們有我們需要遵循單一責任原則並達成隔離的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="8f34b-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="8f34b-147">我們可以使用介面定義，並強制程式碼使用介面抽象，而不是具象類型。</span><span class="sxs-lookup"><span data-stu-id="8f34b-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="8f34b-148">在本文稍後，我們將瞭解如何使用類似上述的錯誤範例，來處理*看起來*像是與資料庫交談的介面。</span><span class="sxs-lookup"><span data-stu-id="8f34b-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="8f34b-149">不過在測試時，我們可以替代不會與資料庫通訊的虛擬執行，而是將資料保留在記憶體中。</span><span class="sxs-lookup"><span data-stu-id="8f34b-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="8f34b-150">此虛擬執行會將程式碼與資料存取程式碼或資料庫設定中不相關的問題隔離。</span><span class="sxs-lookup"><span data-stu-id="8f34b-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="8f34b-151">隔離還有其他好處。</span><span class="sxs-lookup"><span data-stu-id="8f34b-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="8f34b-152">最後一種方法中的商業計算應該只需要幾毫秒的時間來執行，但測試本身可能會執行數秒，因為網路上的程式碼躍點，並與不同的伺服器交談。</span><span class="sxs-lookup"><span data-stu-id="8f34b-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="8f34b-153">單元測試應該會快速執行，以加速進行小型變更。</span><span class="sxs-lookup"><span data-stu-id="8f34b-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="8f34b-154">單元測試也應該是可重複且不會失敗，因為與測試無關的元件發生問題。</span><span class="sxs-lookup"><span data-stu-id="8f34b-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="8f34b-155">撰寫容易觀察和隔離的程式碼，可讓開發人員更輕鬆地撰寫程式碼測試、縮短等待測試執行的時間，更重要的是，花更少時間追蹤不存在的 bug。</span><span class="sxs-lookup"><span data-stu-id="8f34b-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="8f34b-156">希望您可以感謝測試的優點，並瞭解可測試的程式碼所展現的品質。</span><span class="sxs-lookup"><span data-stu-id="8f34b-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="8f34b-157">我們即將說明如何撰寫可與 EF4 搭配使用的程式碼，以將資料儲存至資料庫，同時可觀察和輕鬆隔離，但首先我們會縮小焦點，以討論資料存取的可測試設計。</span><span class="sxs-lookup"><span data-stu-id="8f34b-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="8f34b-158">資料持續性的設計模式</span><span class="sxs-lookup"><span data-stu-id="8f34b-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="8f34b-159">上述兩個錯誤範例都有太多責任。</span><span class="sxs-lookup"><span data-stu-id="8f34b-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="8f34b-160">第一個不良範例必須執行計算*並*寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="8f34b-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="8f34b-161">第二個不良範例必須從資料庫讀取資料 *，並*執行商務計算*並*傳送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="8f34b-162">藉由設計較小的方法來區分疑慮，並將責任委派給其他元件，您將會對撰寫可測試的程式碼做出絕佳的努力</span><span class="sxs-lookup"><span data-stu-id="8f34b-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="8f34b-163">其目標是透過從小型和焦點的抽象概念來撰寫動作，以建立功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="8f34b-164">有了資料持續性時，我們所尋找的小型和焦點抽象概念會被記錄為設計模式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="8f34b-165">Fowler 的企業應用程式架構書籍模式，是在列印中描述這些模式的第一項工作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="8f34b-166">我們會先在下列各節中提供這些模式的簡短描述，再示範這些 ADO.NET Entity Framework 如何執行並使用這些模式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="8f34b-167">存放庫模式</span><span class="sxs-lookup"><span data-stu-id="8f34b-167">The Repository Pattern</span></span>

<span data-ttu-id="8f34b-168">Fowler 會指出存放庫在網域和資料對應層之間，使用類似集合的介面來存取網域物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="8f34b-169">存放庫模式的目標是要隔離資料存取 minutiae 中的程式碼，而且如我們所見，先前的隔離是可測試性的必要特性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="8f34b-170">隔離的關鍵在於，存放庫如何使用類似集合的介面來公開物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="8f34b-171">您撰寫來使用儲存機制的邏輯，並不知道存放庫如何具體化您要求的物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="8f34b-172">存放庫可能會與資料庫交談，或者它可能只會從記憶體中的集合傳回物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="8f34b-173">您的所有程式碼都必須知道，存放庫會出現來維護集合，而您可以從集合中取出、加入和刪除物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="8f34b-174">在現有的 .NET 應用程式中，實體存放庫通常會繼承自泛型介面，如下所示：</span><span class="sxs-lookup"><span data-stu-id="8f34b-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="8f34b-175">當我們提供 EF4 的執行時，我們會對介面定義進行一些變更，但基本概念會維持不變。</span><span class="sxs-lookup"><span data-stu-id="8f34b-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="8f34b-176">程式碼可以使用實作為此介面的具體存放庫，依其主要索引鍵值抓取實體、根據述詞的評估來抓取實體集合，或只抓取所有可用的實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="8f34b-177">程式碼也可以透過存放庫介面新增和移除實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="8f34b-178">在指定 Employee 物件的 IRepository 時，程式碼可以執行下列作業。</span><span class="sxs-lookup"><span data-stu-id="8f34b-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="8f34b-179">因為程式碼使用的是介面（Employee 的 IRepository），所以我們可以為程式碼提供不同的介面執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="8f34b-180">其中一個執行方式是將物件 EF4 並保存到 Microsoft SQL Server 資料庫中，藉以進行支援。</span><span class="sxs-lookup"><span data-stu-id="8f34b-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="8f34b-181">不同的執行（在測試期間所使用的）可能會受到員工物件的記憶體中清單所支援。</span><span class="sxs-lookup"><span data-stu-id="8f34b-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="8f34b-182">介面將有助於在程式碼中達成隔離。</span><span class="sxs-lookup"><span data-stu-id="8f34b-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="8f34b-183">請注意，IRepository&lt;T&gt; 介面不會公開儲存作業。</span><span class="sxs-lookup"><span data-stu-id="8f34b-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="8f34b-184">如何更新現有的物件？</span><span class="sxs-lookup"><span data-stu-id="8f34b-184">How do we update existing objects?</span></span> <span data-ttu-id="8f34b-185">您可能會遇到包含儲存作業的 IRepository 定義，而且這些存放庫的執行必須立即將物件保存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="8f34b-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="8f34b-186">不過，在許多應用程式中，我們不想要個別保存物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="8f34b-187">相反地，我們想要將物件從不同的存放庫帶入現實，將這些物件修改為商務活動的一部分，然後將所有物件保存為單一不可部分完成作業的一部分。</span><span class="sxs-lookup"><span data-stu-id="8f34b-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="8f34b-188">幸運的是，有一個模式可以允許這種行為。</span><span class="sxs-lookup"><span data-stu-id="8f34b-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="8f34b-189">工作單位模式</span><span class="sxs-lookup"><span data-stu-id="8f34b-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="8f34b-190">Fowler 指出「工作單位」將會「維護受商務交易影響的物件清單」，並協調寫出變更和並行處理問題的解決方式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="8f34b-191">追蹤存放庫中的物件所做的變更，並保存我們對物件所做的任何變更（當我們告訴工作單位認可變更時），這是工作單位的責任。</span><span class="sxs-lookup"><span data-stu-id="8f34b-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="8f34b-192">將新物件新增至所有存放庫並將物件插入資料庫，也會負責管理刪除作業的工作單位。</span><span class="sxs-lookup"><span data-stu-id="8f34b-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="8f34b-193">如果您曾經完成過 ADO.NET 資料集的任何工作，則您已經熟悉工作單位模式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="8f34b-194">ADO.NET 資料集能夠追蹤更新、刪除和插入 DataRow 物件，而且可以（透過 TableAdapter 的協助）協調資料庫的所有變更。</span><span class="sxs-lookup"><span data-stu-id="8f34b-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="8f34b-195">不過，DataSet 物件會將基礎資料庫的已中斷連接子集模型化。</span><span class="sxs-lookup"><span data-stu-id="8f34b-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="8f34b-196">工作單位模式會展現相同的行為，但會使用與資料存取程式碼隔離並不知道資料庫的商務物件和網域物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="8f34b-197">在 .NET 程式碼中建立工作單位模型的抽象概念可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="8f34b-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="8f34b-198">藉由公開工作單位的存放庫參考，我們可以確保單一工作單位物件能夠追蹤在商務交易期間具體化的所有實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="8f34b-199">執行實際工作單元的 Commit 方法，就是將記憶體中的變更與資料庫進行協調的所有神奇之處。</span><span class="sxs-lookup"><span data-stu-id="8f34b-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="8f34b-200">假設有 IUnitOfWork 參考，程式碼可以對從一或多個存放庫中抓取的商務物件進行變更，並使用不可部分完成的認可作業來儲存所有變更。</span><span class="sxs-lookup"><span data-stu-id="8f34b-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="8f34b-201">延遲負載模式</span><span class="sxs-lookup"><span data-stu-id="8f34b-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="8f34b-202">Fowler 會使用「延遲負載」來描述「不包含您需要的所有資料，但知道如何取得」的物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="8f34b-203">透明的消極式載入是撰寫可測試的商務程式碼並使用關係資料庫時，必須具備的重要功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="8f34b-204">例如，請考慮下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="8f34b-205">如何填入工時卡集合？</span><span class="sxs-lookup"><span data-stu-id="8f34b-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="8f34b-206">有兩個可能的答案。</span><span class="sxs-lookup"><span data-stu-id="8f34b-206">There are two possible answers.</span></span> <span data-ttu-id="8f34b-207">其中一個答案是員工存放庫在要求提取員工時，會發出查詢，同時將員工與員工相關聯的時間卡片資訊一起取得。</span><span class="sxs-lookup"><span data-stu-id="8f34b-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="8f34b-208">在關係資料庫中，這通常需要含有聯結子句的查詢，而且可能會導致抓取比應用程式需要更多的資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="8f34b-209">如果應用程式永遠不需要接觸工時卡屬性，該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="8f34b-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="8f34b-210">第二個答案是依需求載入工時卡屬性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="8f34b-211">這種消極式載入對商務邏輯而言是隱含且透明的，因為程式碼不會叫用特殊的 Api 來取出時間卡片資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="8f34b-212">這段程式碼會假設有需要的時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="8f34b-213">消極式載入牽涉到一些神奇之處，通常牽涉到執行時間攔截方法調用。</span><span class="sxs-lookup"><span data-stu-id="8f34b-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="8f34b-214">攔截程式碼負責與資料庫交談並抓取時間卡資訊，同時讓商務邏輯維持商務邏輯的自由。</span><span class="sxs-lookup"><span data-stu-id="8f34b-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="8f34b-215">這項延遲負載魔術可讓商務程式碼將本身與資料抓取作業隔離，並導致更具測試性的程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="8f34b-216">消極式載入的缺點是，當應用程式需要時間卡資訊時，該程式*代碼會執行*額外的查詢。</span><span class="sxs-lookup"><span data-stu-id="8f34b-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="8f34b-217">這不是許多應用程式的顧慮，但是對於效能敏感的應用程式或應用程式會在多個員工物件上迴圈，並在迴圈的每次反覆運算期間執行查詢來取出時間卡（問題通常稱為 N + 1查詢問題），消極式載入是一個拖曳。</span><span class="sxs-lookup"><span data-stu-id="8f34b-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="8f34b-218">在這些情況下，應用程式可能會想要以最有效率的方式立即載入時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="8f34b-219">幸好，我們將在進入下一節並執行這些模式時，看到 EF4 如何同時支援隱含的延遲載入和有效率的積極式載入。</span><span class="sxs-lookup"><span data-stu-id="8f34b-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="8f34b-220">使用 Entity Framework 來執行模式</span><span class="sxs-lookup"><span data-stu-id="8f34b-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="8f34b-221">好消息是，我們在上一節所描述的所有設計模式，都是使用 EF4 來執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="8f34b-222">為了示範，我們將使用簡單的 ASP.NET MVC 應用程式來編輯和顯示員工及其相關聯的時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="8f34b-223">我們會先使用下列「單純的舊版 CLR 物件」（Poco）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="8f34b-224">當我們探索 EF4 的不同方法和功能時，這些類別定義會稍有變更，但其目的是要盡可能將這些類別保持為非持續性（PI）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="8f34b-225">PI 物件不知道它所保存的狀態*是否*存在於*資料庫中。*</span><span class="sxs-lookup"><span data-stu-id="8f34b-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="8f34b-226">PI 和 Poco 會與可測試的軟體搭配使用。</span><span class="sxs-lookup"><span data-stu-id="8f34b-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="8f34b-227">使用 POCO 方法的物件較不受限制、更有彈性且更容易測試，因為它們可以在沒有資料庫存在的情況下運作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="8f34b-228">Poco 就緒之後，我們就可以在 Visual Studio 中建立實體資料模型（EDM）（請參閱 [圖 1]）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="8f34b-229">我們不會使用 EDM 為我們的實體產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="8f34b-230">相反地，我們想要使用我們以手動方式所謂的實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="8f34b-231">我們只會使用 EDM 來產生我們的資料庫架構，並提供中繼資料 EF4 需要將物件對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="8f34b-233">**圖1**</span><span class="sxs-lookup"><span data-stu-id="8f34b-233">**Figure 1**</span></span>

<span data-ttu-id="8f34b-234">注意：如果您想要先開發 EDM 模型，可以從 EDM 產生乾淨的 POCO 程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="8f34b-235">您可以使用資料可程式性小組所提供的 Visual Studio 2010 延伸模組來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="8f34b-236">若要下載擴充功能，請從 Visual Studio 中的 [工具] 功能表啟動 [擴充管理員]，並搜尋「POCO」範本的線上元件庫（請參閱 [圖 2]）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="8f34b-237">有數個適用于 EF 的 POCO 範本。</span><span class="sxs-lookup"><span data-stu-id="8f34b-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="8f34b-238">如需使用範本的詳細資訊，請參閱 [「逐步解說：適用于 Entity Framework 的 POCO 範本」。](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)</span><span class="sxs-lookup"><span data-stu-id="8f34b-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="8f34b-240">**圖2**</span><span class="sxs-lookup"><span data-stu-id="8f34b-240">**Figure 2**</span></span>

<span data-ttu-id="8f34b-241">從這個 POCO 開始，我們將探討兩種不同的可測試程式碼方法。</span><span class="sxs-lookup"><span data-stu-id="8f34b-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="8f34b-242">第一種方法是呼叫 EF 方法，因為它會利用來自 Entity Framework API 的抽象概念來執行工作和存放庫的單位。</span><span class="sxs-lookup"><span data-stu-id="8f34b-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="8f34b-243">在第二個方法中，我們將建立自己的自訂存放庫抽象概念，然後查看每個方法的優缺點。</span><span class="sxs-lookup"><span data-stu-id="8f34b-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="8f34b-244">我們將從探索 EF 方法開始。</span><span class="sxs-lookup"><span data-stu-id="8f34b-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="8f34b-245">EF 中心的執行</span><span class="sxs-lookup"><span data-stu-id="8f34b-245">An EF Centric Implementation</span></span>

<span data-ttu-id="8f34b-246">請考慮 ASP.NET MVC 專案中的下列控制器動作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="8f34b-247">動作會抓取 Employee 物件，並傳回結果以顯示員工的詳細觀點。</span><span class="sxs-lookup"><span data-stu-id="8f34b-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="8f34b-248">程式碼是否可測試？</span><span class="sxs-lookup"><span data-stu-id="8f34b-248">Is the code testable?</span></span> <span data-ttu-id="8f34b-249">至少有兩個測試需要驗證動作的行為。</span><span class="sxs-lookup"><span data-stu-id="8f34b-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="8f34b-250">首先，我們想要確認動作會傳回正確的觀點–這是一個簡單的測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="8f34b-251">我們也想要撰寫測試來確認動作會抓取正確的員工，而我們想要這麼做，而不執行程式碼來查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="8f34b-252">請記住，我們想要隔離受測程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="8f34b-253">隔離會確保測試不會因為資料存取程式碼或資料庫設定中的錯誤而失敗。</span><span class="sxs-lookup"><span data-stu-id="8f34b-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="8f34b-254">如果測試失敗，我們會知道控制器邏輯中有錯誤，而不是在較低層級的系統元件中。</span><span class="sxs-lookup"><span data-stu-id="8f34b-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="8f34b-255">為了達到隔離，我們需要一些抽象概念，像是我們稍早針對存放庫和工作單位所提供的介面。</span><span class="sxs-lookup"><span data-stu-id="8f34b-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="8f34b-256">請記住，存放庫模式是設計來在網域物件和資料對應層之間進行協調。</span><span class="sxs-lookup"><span data-stu-id="8f34b-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="8f34b-257">在此案例中，EF4*是*資料對應層，而且已經提供名為 IObjectSet&lt;t&gt; 的類似存放庫抽象概念（從 system.web 命名空間）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="8f34b-258">介面定義看起來如下所示。</span><span class="sxs-lookup"><span data-stu-id="8f34b-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="8f34b-259">IObjectSet&lt;T&gt; 符合存放庫的需求，因為它類似物件的集合（透過 IEnumerable&lt;T&gt;），並提供從模擬的集合中新增和移除物件的方法。</span><span class="sxs-lookup"><span data-stu-id="8f34b-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="8f34b-260">Attach 和 Detach 方法會公開 EF4 API 的其他功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="8f34b-261">若要使用 IObjectSet&lt;T&gt; 作為存放庫的介面，我們需要一個工作單位抽象來將存放庫系結在一起。</span><span class="sxs-lookup"><span data-stu-id="8f34b-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="8f34b-262">此介面的一個具體執行會與 SQL Server 交談，而且很容易就能使用 EF4 的 ObjectCoNtext 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="8f34b-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="8f34b-263">ObjectCoNtext 類別是 EF4 API 中的實際工作單位。</span><span class="sxs-lookup"><span data-stu-id="8f34b-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="8f34b-264">將 IObjectSet&lt;T&gt; 的生活，就像叫用 ObjectCoNtext 物件的 Createobjectset<tentity> 方法一樣簡單。</span><span class="sxs-lookup"><span data-stu-id="8f34b-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="8f34b-265">在幕後，架構將會使用我們在 EDM 中提供的中繼資料來產生具體的 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="8f34b-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8f34b-266">我們會繼續傳回 IObjectSet&lt;T&gt; 介面，因為它有助於保留用戶端程式代碼中的可測試性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="8f34b-267">這個具體的執行功能在生產環境中很有用，但我們需要專注于如何使用我們的 IUnitOfWork 抽象概念來加速測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="8f34b-268">測試加倍</span><span class="sxs-lookup"><span data-stu-id="8f34b-268">The Test Doubles</span></span>

<span data-ttu-id="8f34b-269">為了隔離控制器動作，我們必須能夠在實際工作單位（由 ObjectCoNtext 支援）和測試 double 或「假」工作單位（執行記憶體中的作業）之間切換。</span><span class="sxs-lookup"><span data-stu-id="8f34b-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="8f34b-270">執行此類型切換的常見方法是不要讓 MVC 控制器具現化工作單位，而是改以函式參數的形式將工作單位傳遞至控制器。</span><span class="sxs-lookup"><span data-stu-id="8f34b-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="8f34b-271">上述程式碼是相依性插入的範例。</span><span class="sxs-lookup"><span data-stu-id="8f34b-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="8f34b-272">我們不允許控制器建立其相依性（工作單位），但會將相依性插入控制器中。</span><span class="sxs-lookup"><span data-stu-id="8f34b-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="8f34b-273">在 MVC 專案中，通常會搭配使用自訂控制器 factory 與反轉控制（IoC）容器，將相依性插入自動化。</span><span class="sxs-lookup"><span data-stu-id="8f34b-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="8f34b-274">這些主題已超出本文的範圍，但您可以遵循本文結尾的參考來閱讀更多。</span><span class="sxs-lookup"><span data-stu-id="8f34b-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="8f34b-275">可用於測試的假工作單位實作為可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="8f34b-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="8f34b-276">請注意，假的工作單位會公開一個已認可的屬性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="8f34b-277">將功能新增至可加速測試的假類別，有時會很有用。</span><span class="sxs-lookup"><span data-stu-id="8f34b-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="8f34b-278">在此情況下，您可以藉由檢查已認可的屬性，輕鬆觀察程式碼是否認可工作單位。</span><span class="sxs-lookup"><span data-stu-id="8f34b-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="8f34b-279">我們也需要假的 IObjectSet&lt;T&gt;，將員工和工時卡物件保存在記憶體中。</span><span class="sxs-lookup"><span data-stu-id="8f34b-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="8f34b-280">我們可以使用泛型來提供單一的執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="8f34b-281">這項測試會將大部分的工作都委派給基礎 HashSet&lt;T&gt; 物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="8f34b-282">請注意，IObjectSet&lt;T&gt; 需要強制使用 T 做為類別（參考型別）的泛型條件約束，同時也會強制我們執行 IQueryable&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="8f34b-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="8f34b-283">使用標準 LINQ 運算子 AsQueryable，可以輕鬆地將記憶體中的集合顯示為 IQueryable&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="8f34b-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="8f34b-284">測試</span><span class="sxs-lookup"><span data-stu-id="8f34b-284">The Tests</span></span>

<span data-ttu-id="8f34b-285">傳統單元測試會使用單一測試類別來保存單一 MVC 控制器中所有動作的所有測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="8f34b-286">我們可以使用我們所建立的記憶體中 fakes，撰寫這些測試或任何類型的單元測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="8f34b-287">不過，在本文中，我們將避免整合型測試類別方法，並改為將我們的測試分組，以專注于特定的功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="8f34b-288">  例如，「建立新員工」可能是我們想要測試的功能，因此我們將使用單一測試類別來驗證負責建立新員工的單一控制器動作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="8f34b-289">所有這些精細的測試類別都需要一些常見的安裝程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="8f34b-290">例如，我們一律需要建立記憶體內部儲存機制和假的工作單位。</span><span class="sxs-lookup"><span data-stu-id="8f34b-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="8f34b-291">我們也需要員工控制器的實例，並插入假的工作單位。</span><span class="sxs-lookup"><span data-stu-id="8f34b-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="8f34b-292">我們會使用基類，在測試類別之間共用這個通用的安裝程式碼。</span><span class="sxs-lookup"><span data-stu-id="8f34b-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="8f34b-293">我們在基類中使用的「媽媽物件」是用來建立測試資料的一種常見模式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="8f34b-294">媽媽物件包含 factory 方法，可具現化測試實體以在多個測試裝置上使用。</span><span class="sxs-lookup"><span data-stu-id="8f34b-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="8f34b-295">我們可以使用 EmployeeControllerTestBase 做為一些測試裝置的基類（請參閱 [圖 3]）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="8f34b-296">每個測試裝置都會測試特定的控制器動作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="8f34b-297">例如，一個測試裝置將著重在測試 HTTP GET 要求期間所使用的建立動作（以顯示用來建立員工的視圖），而不同的裝置將著重于 HTTP POST 要求中所使用的建立動作（以取得所提交的資訊建立員工的使用者）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="8f34b-298">每個衍生類別只負責其特定內容中所需的設定，並提供驗證其特定測試內容之結果所需的判斷提示。</span><span class="sxs-lookup"><span data-stu-id="8f34b-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="8f34b-300">**圖3**</span><span class="sxs-lookup"><span data-stu-id="8f34b-300">**Figure 3**</span></span>

<span data-ttu-id="8f34b-301">在這裡提供的命名慣例和測試樣式不需要用於可測試的程式碼，它只是一種方法。</span><span class="sxs-lookup"><span data-stu-id="8f34b-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="8f34b-302">[圖 4] 顯示 Visual Studio 2010 的 Jet 大腦 Resharper 測試執行器外掛程式中執行的測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="8f34b-304">**圖4**</span><span class="sxs-lookup"><span data-stu-id="8f34b-304">**Figure 4**</span></span>

<span data-ttu-id="8f34b-305">有了用來處理共用安裝程式碼的基類，每個控制器動作的單元測試都很小，而且容易撰寫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="8f34b-306">測試會快速執行（因為我們正在執行記憶體中的作業），而且不應該因為不相關的基礎結構或環境問題而失敗（因為我們已隔離受測試的單元）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="8f34b-307">在這些測試中，基底類別會執行大部分的設定工作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="8f34b-308">請記住，基類的處理常式會建立記憶體內部存放庫、假的工作單位，以及 EmployeeController 類別的實例。</span><span class="sxs-lookup"><span data-stu-id="8f34b-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="8f34b-309">測試類別衍生自這個基類，並著重于測試 Create 方法的細節。</span><span class="sxs-lookup"><span data-stu-id="8f34b-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="8f34b-310">在此情況下，細節會向下包括至您將在任何單元測試程式中看到的「排列」、「act」和「判斷提示」步驟：</span><span class="sxs-lookup"><span data-stu-id="8f34b-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="8f34b-311">建立 newEmployee 物件以模擬傳入的資料。</span><span class="sxs-lookup"><span data-stu-id="8f34b-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="8f34b-312">叫用 EmployeeController 的 Create 動作，並傳入 newEmployee。</span><span class="sxs-lookup"><span data-stu-id="8f34b-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="8f34b-313">確認 [建立] 動作會產生預期的結果（員工出現在存放庫中）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="8f34b-314">我們所建立的內容可讓我們測試任何 EmployeeController 動作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="8f34b-315">例如，當我們針對員工控制器的 [索引] 動作撰寫測試時，我們可以從測試基類繼承，為我們的測試建立相同的基本設定。</span><span class="sxs-lookup"><span data-stu-id="8f34b-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="8f34b-316">同樣地，基底類別會建立記憶體內部儲存機制、假的工作單位，以及 EmployeeController 的實例。</span><span class="sxs-lookup"><span data-stu-id="8f34b-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="8f34b-317">索引動作的測試只需要專注于叫用索引動作，並測試動作傳回的模型品質。</span><span class="sxs-lookup"><span data-stu-id="8f34b-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="8f34b-318">我們使用記憶體內部 fakes 所建立的測試，是針對測試軟體的*狀態*。</span><span class="sxs-lookup"><span data-stu-id="8f34b-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="8f34b-319">例如，當測試建立動作時，我們想要在建立動作執行後檢查儲存機制的狀態–存放庫是否持有新員工？</span><span class="sxs-lookup"><span data-stu-id="8f34b-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="8f34b-320">稍後我們將探討以互動為基礎的測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="8f34b-321">以互動為基礎的測試會詢問受測程式碼是否已叫用物件上的適當方法，並傳遞正確的參數。</span><span class="sxs-lookup"><span data-stu-id="8f34b-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="8f34b-322">我們現在要繼續探討另一個設計模式–消極式載入。</span><span class="sxs-lookup"><span data-stu-id="8f34b-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="8f34b-323">積極式載入和消極式載入</span><span class="sxs-lookup"><span data-stu-id="8f34b-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="8f34b-324">在 ASP.NET MVC web 應用程式的某個時間點，我們可能會想要顯示員工的資訊，並包含員工的相關聯卡片。</span><span class="sxs-lookup"><span data-stu-id="8f34b-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="8f34b-325">例如，我們可能會有一個 [時間卡摘要] 顯示，顯示員工名稱和系統中的總卡片數。</span><span class="sxs-lookup"><span data-stu-id="8f34b-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="8f34b-326">有數種方法可以執行這項功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="8f34b-327">Projection</span><span class="sxs-lookup"><span data-stu-id="8f34b-327">Projection</span></span>

<span data-ttu-id="8f34b-328">建立摘要的一個簡單方法是，針對我們想要在視圖中顯示的資訊來建立專用的模型。</span><span class="sxs-lookup"><span data-stu-id="8f34b-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="8f34b-329">在此案例中，此模型看起來可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="8f34b-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="8f34b-330">請注意，EmployeeSummaryViewModel 不是實體，換句話說，這不是我們想要保存在資料庫中的內容。</span><span class="sxs-lookup"><span data-stu-id="8f34b-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="8f34b-331">我們只會使用這個類別，以強型別方式將資料隨機放入視圖中。</span><span class="sxs-lookup"><span data-stu-id="8f34b-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="8f34b-332">視圖模型類似于資料傳輸物件（DTO），因為它不包含任何行為（沒有方法）–只有屬性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="8f34b-333">屬性會保存我們需要移動的資料。</span><span class="sxs-lookup"><span data-stu-id="8f34b-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="8f34b-334">使用 LINQ 的標準投射運算子（Select 運算子）來具現化此視圖模型是很容易的。</span><span class="sxs-lookup"><span data-stu-id="8f34b-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="8f34b-335">上述程式碼有兩個值得注意的功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-335">There are two notable features to the above code.</span></span> <span data-ttu-id="8f34b-336">首先–程式碼很容易測試，因為它仍然很容易觀察和隔離。</span><span class="sxs-lookup"><span data-stu-id="8f34b-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="8f34b-337">Select 運算子的運作方式與我們的記憶體內部 fakes 相同，因為它是針對實際工作單元。</span><span class="sxs-lookup"><span data-stu-id="8f34b-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="8f34b-338">第二個值得注意的功能是程式碼如何讓 EF4 產生單一、有效率的查詢，將員工和時間卡資訊組合在一起。</span><span class="sxs-lookup"><span data-stu-id="8f34b-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="8f34b-339">我們已將員工資訊和時間卡資訊載入至相同的物件，而不需使用任何特殊 Api。</span><span class="sxs-lookup"><span data-stu-id="8f34b-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="8f34b-340">程式碼只會使用適用于記憶體內部資料來源和遠端資料源的標準 LINQ 運算子，來表示其所需的資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="8f34b-341">EF4 能夠將 LINQ 查詢和 C\# 編譯器所產生的運算式樹狀架構，轉譯成單一且有效率的 T-SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="8f34b-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="8f34b-342">有時候，我們不想要使用視圖模型或 DTO 物件，而是使用實際實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="8f34b-343">當我們知道我們需要員工*和*員工的時間卡時，我們可以立即以不顯眼且有效率的方式來載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="8f34b-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="8f34b-344">明確的積極式載入</span><span class="sxs-lookup"><span data-stu-id="8f34b-344">Explicit Eager Loading</span></span>

<span data-ttu-id="8f34b-345">當我們想要立即載入相關的實體資訊時，我們需要一些商務邏輯（或在此案例中為控制器動作邏輯）的機制，以表達其對存放庫的期望。</span><span class="sxs-lookup"><span data-stu-id="8f34b-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="8f34b-346">EF4 ObjectQuery&lt;T&gt; 類別會定義 Include 方法，以指定要在查詢期間取得的相關物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="8f34b-347">請記住，EF4 ObjectCoNtext 會透過衍生自 ObjectQuery&lt;T&gt;的具體 ObjectSet&lt;T&gt; 類別來公開實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="8f34b-348">  如果我們使用 ObjectSet&lt;T&gt; 控制器動作中的參考，我們可以撰寫下列程式碼來指定每個員工的即時卡片資訊載入。</span><span class="sxs-lookup"><span data-stu-id="8f34b-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="8f34b-349">不過，因為我們想要讓程式碼保持可測試性，所以我們不會公開 ObjectSet&lt;T&gt; 從真實的工作單位類別之外。</span><span class="sxs-lookup"><span data-stu-id="8f34b-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="8f34b-350">相反地，我們依賴 IObjectSet&lt;T&gt; 介面較容易假，但是 IObjectSet&lt;T&gt; 不會定義 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="8f34b-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="8f34b-351">LINQ 的優點是我們可以建立自己的 Include 運算子。</span><span class="sxs-lookup"><span data-stu-id="8f34b-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="8f34b-352">請注意，此 Include 運算子定義為 IQueryable&lt;T&gt; 的擴充方法，而不是 IObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="8f34b-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8f34b-353">這讓我們能夠使用具有更廣泛可能類型的方法，包括 IQueryable&lt;T&gt;、IObjectSet&lt;T&gt;、ObjectQuery&lt;T&gt;和 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="8f34b-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8f34b-354">在事件中，基礎序列不是正版 EF4 ObjectQuery&lt;T&gt;，因此不會有任何傷害，而且 Include 運算子不會有任何作用。</span><span class="sxs-lookup"><span data-stu-id="8f34b-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="8f34b-355">如果基礎序列*是*ObjectQuery&lt;t&gt; （或衍生自 ObjectQuery&lt;t&gt;），則 EF4 會看到我們對其他資料的需求，並制訂適當的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="8f34b-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="8f34b-356">有了這個新的操作員，我們就可以從存放庫明確要求立即載入的時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="8f34b-357">針對實際的 ObjectCoNtext 執行時，程式碼會產生下列單一查詢。</span><span class="sxs-lookup"><span data-stu-id="8f34b-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="8f34b-358">查詢會從資料庫收集足夠的資訊，以具體化 employee 物件並完全填入其工時卡屬性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="8f34b-359">最棒的是，動作方法內的程式碼仍可進行完整測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="8f34b-360">我們不需要為 fakes 提供任何額外的功能來支援 Include 運算子。</span><span class="sxs-lookup"><span data-stu-id="8f34b-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="8f34b-361">壞消息是我們必須在程式碼內使用 Include 運算子，我們想要保持非持續性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="8f34b-362">這是您在建立可測試的程式碼時，需要評估之取捨類型的主要範例。</span><span class="sxs-lookup"><span data-stu-id="8f34b-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="8f34b-363">有時候，您需要讓持續性考慮在存放庫抽象以外流失，以符合效能目標。</span><span class="sxs-lookup"><span data-stu-id="8f34b-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="8f34b-364">積極式載入的替代方法是消極式載入。</span><span class="sxs-lookup"><span data-stu-id="8f34b-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="8f34b-365">消極式載入表示我們*不*需要我們的商務程式碼，就能明確宣告相關資料的需求。</span><span class="sxs-lookup"><span data-stu-id="8f34b-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="8f34b-366">相反地，我們會在應用程式中使用我們的實體，如果需要額外的資料，Entity Framework 會視需要載入資料。</span><span class="sxs-lookup"><span data-stu-id="8f34b-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="8f34b-367">消極式載入</span><span class="sxs-lookup"><span data-stu-id="8f34b-367">Lazy Loading</span></span>

<span data-ttu-id="8f34b-368">很容易就能想像一下，我們不知道什麼是商務邏輯所需的資料。</span><span class="sxs-lookup"><span data-stu-id="8f34b-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="8f34b-369">我們可能知道邏輯需要 employee 物件，但我們可能會分支到不同的執行路徑，其中有些路徑需要員工的時間卡資訊，有些則不會。</span><span class="sxs-lookup"><span data-stu-id="8f34b-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="8f34b-370">這類案例非常適合用於隱含的消極式載入，因為資料神奇地出現在必要的基礎上。</span><span class="sxs-lookup"><span data-stu-id="8f34b-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="8f34b-371">消極式載入（也稱為延後載入）會將一些需求放在實體物件上。</span><span class="sxs-lookup"><span data-stu-id="8f34b-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="8f34b-372">具有真正持續性無知的 Poco 不會面對持續性層的任何需求，但真正的持續性無知實際上不可能達成。</span><span class="sxs-lookup"><span data-stu-id="8f34b-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="8f34b-373">  相反地，我們會以相對角度來測量持續性無知。</span><span class="sxs-lookup"><span data-stu-id="8f34b-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="8f34b-374">如果我們需要繼承自持續性導向基類，或使用特定集合來達到 Poco 中的消極式載入，則會很遺憾。</span><span class="sxs-lookup"><span data-stu-id="8f34b-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="8f34b-375">幸好，EF4 有一個較不具侵入性的解決方案。</span><span class="sxs-lookup"><span data-stu-id="8f34b-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="8f34b-376">幾乎無法偵測</span><span class="sxs-lookup"><span data-stu-id="8f34b-376">Virtually Undetectable</span></span>

<span data-ttu-id="8f34b-377">使用 POCO 物件時，EF4 可以動態產生實體的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="8f34b-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="8f34b-378">這些 proxy 會以不可見的方式包裝具體化 Poco，並藉由攔截每個屬性 get 和 set 作業來執行其他工作，以提供額外的服務。</span><span class="sxs-lookup"><span data-stu-id="8f34b-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="8f34b-379">其中一項服務就是我們要尋找的消極式載入功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="8f34b-380">另一個服務是一種有效率的變更追蹤機制，可以在程式變更實體的屬性值時記錄。</span><span class="sxs-lookup"><span data-stu-id="8f34b-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="8f34b-381">在 SaveChanges 方法期間，ObjectCoNtext 會使用變更清單來保存任何已修改的實體，並使用 UPDATE 命令。</span><span class="sxs-lookup"><span data-stu-id="8f34b-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="8f34b-382">不過，若要讓這些 proxy 運作，它們需要一個方法來連結實體的屬性 get 和 set 作業，而 proxy 會藉由覆寫虛擬成員來達成此目標。</span><span class="sxs-lookup"><span data-stu-id="8f34b-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="8f34b-383">因此，如果我們想要進行隱含的消極式載入和有效率的變更追蹤，我們必須回到 POCO 類別定義，並將屬性標示為 virtual。</span><span class="sxs-lookup"><span data-stu-id="8f34b-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="8f34b-384">我們仍然可以說，Employee 實體大部分的持續性都是未知的。</span><span class="sxs-lookup"><span data-stu-id="8f34b-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="8f34b-385">唯一的需求是使用虛擬成員，而這不會影響程式碼的可測試性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="8f34b-386">我們不需要衍生自任何特殊基類，或甚至使用消極式載入專用的特殊集合。</span><span class="sxs-lookup"><span data-stu-id="8f34b-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="8f34b-387">如程式碼所示，任何執行 ICollection&lt;T&gt; 的類別都可以用來保存相關的實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="8f34b-388">此外，我們還需要在工作單元中進行一項次要變更。</span><span class="sxs-lookup"><span data-stu-id="8f34b-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="8f34b-389">當直接使用 ObjectCoNtext 物件時，消極式載入會預設為*關閉*。</span><span class="sxs-lookup"><span data-stu-id="8f34b-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="8f34b-390">我們可以在 CoNtextoptions.negotiate 屬性上設定一個屬性來啟用延後載入，而且我們可以在實際工作單元內設定此屬性（如果我們想要在所有位置啟用消極式載入）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="8f34b-391">啟用隱含的消極式載入時，應用程式程式碼可以使用員工和員工的相關聯的時間卡，同時知道不知道 EF 載入額外資料所需的工作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="8f34b-392">消極式載入可讓應用程式的程式碼更容易撰寫，而且使用 proxy 的神奇，程式碼就能保持完整的可測試性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="8f34b-393">工作單位的記憶體內部 fakes 可以在測試期間視需要預先載入具有相關聯資料的假實體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="8f34b-394">此時，我們會從使用 IObjectSet&lt;T&gt; 建立存放庫，並查看抽象來隱藏持續性架構的所有符號。</span><span class="sxs-lookup"><span data-stu-id="8f34b-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="8f34b-395">自訂存放庫</span><span class="sxs-lookup"><span data-stu-id="8f34b-395">Custom Repositories</span></span>

<span data-ttu-id="8f34b-396">當我們在本文中第一次提供工作單位設計模式時，我們提供了一些範例程式碼，說明工作單位的外觀。</span><span class="sxs-lookup"><span data-stu-id="8f34b-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="8f34b-397">讓我們使用我們使用的員工和員工的時間卡案例，來重新展示這一原始的想法。</span><span class="sxs-lookup"><span data-stu-id="8f34b-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="8f34b-398">此工作單位和我們在上一節中建立的工作單位之間的主要差異在於，此工作單位不會使用 EF4 架構中的任何抽象概念（沒有 IObjectSet&lt;T&gt;）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="8f34b-399">IObjectSet&lt;T&gt; 以存放庫介面的形式運作良好，但它公開的 API 可能無法完全符合我們的應用程式需求。</span><span class="sxs-lookup"><span data-stu-id="8f34b-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="8f34b-400">在即將推出的方法中，我們將使用自訂 IRepository&lt;T&gt; 抽象來代表存放庫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="8f34b-401">許多開發人員遵循測試導向設計、行為導向設計，以及網域導向的方法設計，偏好 IRepository&lt;T&gt; 方法的原因有好幾個。</span><span class="sxs-lookup"><span data-stu-id="8f34b-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="8f34b-402">首先，IRepository&lt;T&gt; 介面代表「反毀」層。</span><span class="sxs-lookup"><span data-stu-id="8f34b-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="8f34b-403">如 Eric Evans 在他的網域導向設計書籍中所述，反毀層會讓您的網域程式碼遠離基礎結構 Api，例如持續性 API。</span><span class="sxs-lookup"><span data-stu-id="8f34b-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="8f34b-404">其次，開發人員可以將方法建立到存放庫中，以符合應用程式的確切需求（在撰寫測試時發現）。</span><span class="sxs-lookup"><span data-stu-id="8f34b-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="8f34b-405">例如，我們可能經常需要使用識別碼值來尋找單一實體，因此我們可以將 FindById 方法新增至存放庫介面。</span><span class="sxs-lookup"><span data-stu-id="8f34b-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="8f34b-406">  我們的 IRepository&lt;T&gt; 定義看起來會像下面這樣。</span><span class="sxs-lookup"><span data-stu-id="8f34b-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="8f34b-407">請注意，我們會回頭使用 IQueryable&lt;T&gt; 介面來公開實體集合。</span><span class="sxs-lookup"><span data-stu-id="8f34b-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="8f34b-408">IQueryable&lt;T&gt; 可讓 LINQ 運算式樹狀結構流入 EF4 提供者，並為提供者提供查詢的整體觀點。</span><span class="sxs-lookup"><span data-stu-id="8f34b-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="8f34b-409">第二個選項是傳回 IEnumerable&lt;T&gt;，這表示 EF4 LINQ 提供者只會看到存放庫內建的運算式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="8f34b-410">在儲存機制外部完成的任何分組、排序和投射，都不會在傳送至資料庫的 SQL 命令中構成，因而可能會造成效能降低。</span><span class="sxs-lookup"><span data-stu-id="8f34b-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="8f34b-411">另一方面，只傳回 IEnumerable&lt;T&gt; 結果的存放庫，並不會對您造成新的 SQL 命令感到驚訝。</span><span class="sxs-lookup"><span data-stu-id="8f34b-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="8f34b-412">這兩種方法都能運作，而且這兩種方法都保持可測試狀態。</span><span class="sxs-lookup"><span data-stu-id="8f34b-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="8f34b-413">使用泛型和 EF4 ObjectCoNtext API，提供 IRepository&lt;T&gt; 介面的單一實作為簡單明瞭。</span><span class="sxs-lookup"><span data-stu-id="8f34b-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="8f34b-414">因為用戶端必須叫用方法來取得實體，所以 IRepository&lt;T&gt; 方法可讓我們進一步控制查詢。</span><span class="sxs-lookup"><span data-stu-id="8f34b-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="8f34b-415">在方法內，我們可以提供額外的檢查和 LINQ 運算子，以強制執行應用程式條件約束。</span><span class="sxs-lookup"><span data-stu-id="8f34b-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="8f34b-416">請注意，介面有兩個泛型型別參數的條件約束。</span><span class="sxs-lookup"><span data-stu-id="8f34b-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="8f34b-417">第一個條件約束是 ObjectSet&lt;T&gt;所需的類別缺點污點，而第二個條件約束會強制我們的實體執行 IEntity –為應用程式建立的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="8f34b-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="8f34b-418">IEntity 介面會強制實體具有可讀取的 Id 屬性，然後我們可以在 FindById 方法中使用這個屬性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="8f34b-419">IEntity 是以下列程式碼定義。</span><span class="sxs-lookup"><span data-stu-id="8f34b-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="8f34b-420">IEntity 可視為持續性無知的小違規，因為我們的實體需要實作為此介面。</span><span class="sxs-lookup"><span data-stu-id="8f34b-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="8f34b-421">記住持續性無知是關於取捨，而許多 FindById 功能將會超過介面加諸的條件約束。</span><span class="sxs-lookup"><span data-stu-id="8f34b-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="8f34b-422">介面不會影響可測試性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="8f34b-423">將即時 IRepository 具現化&lt;T&gt; 需要 EF4 ObjectCoNtext，因此，具體工作單位的執行方式應該管理具現化。</span><span class="sxs-lookup"><span data-stu-id="8f34b-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="8f34b-424">使用自訂存放庫</span><span class="sxs-lookup"><span data-stu-id="8f34b-424">Using the Custom Repository</span></span>

<span data-ttu-id="8f34b-425">使用我們的自訂存放庫與使用以 IObjectSet&lt;T&gt;為基礎的儲存機制並沒有顯著差異。</span><span class="sxs-lookup"><span data-stu-id="8f34b-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8f34b-426">我們不會直接將 LINQ 運算子套用至屬性，而是必須先叫用其中一個存放庫的方法，以抓取 IQueryable&lt;T&gt; 參考。</span><span class="sxs-lookup"><span data-stu-id="8f34b-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="8f34b-427">請注意，我們先前實施的自訂 Include 運算子不需要變更即可正常執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="8f34b-428">存放庫的 FindById 方法會從嘗試取得單一實體的動作中移除重複的邏輯。</span><span class="sxs-lookup"><span data-stu-id="8f34b-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="8f34b-429">我們已檢查過的兩種方法的可測試性並沒有顯著的差異。</span><span class="sxs-lookup"><span data-stu-id="8f34b-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="8f34b-430">我們可以藉由建立 HashSet&lt;Employee&gt; 支援的具象類別，來提供 IRepository&lt;T&gt; 的假實現，就像我們在最後一節中所做的一樣。</span><span class="sxs-lookup"><span data-stu-id="8f34b-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="8f34b-431">不過，有些開發人員偏好使用 mock 物件和模擬物件架構，而不是建立 fakes。</span><span class="sxs-lookup"><span data-stu-id="8f34b-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="8f34b-432">在下一節中，我們將探討如何使用模擬來測試我們的執行，並討論模擬與 fakes 之間的差異。</span><span class="sxs-lookup"><span data-stu-id="8f34b-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="8f34b-433">使用模擬進行測試</span><span class="sxs-lookup"><span data-stu-id="8f34b-433">Testing with Mocks</span></span>

<span data-ttu-id="8f34b-434">有不同的方法可以建立 Fowler 會呼叫「測試雙精度」的方式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="8f34b-435">測試雙精度浮點數（例如電影 stunt 雙精度浮點數）是您在測試期間，針對實際的生產物件所建立的「獨立式」物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="8f34b-436">我們所建立的記憶體內部存放庫會針對與 SQL Server 交談的存放庫進行測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="8f34b-437">我們已瞭解如何在單元測試期間使用這些測試雙精度浮點數來隔離程式碼，並讓測試快速執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="8f34b-438">我們所建立的測試加倍，有實際的工作執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="8f34b-439">在幕後，每一個都會儲存物件的具象集合，而且當我們在測試期間操作儲存機制時，會在此集合中新增和移除物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="8f34b-440">有些開發人員想要以這種方式來建立測試加倍，包括實際的程式碼和工作的執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="8f34b-441">  這些測試加倍是我們所謂的*fakes*。</span><span class="sxs-lookup"><span data-stu-id="8f34b-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="8f34b-442">他們有工作中的執行，但它們並不足以供生產環境使用。</span><span class="sxs-lookup"><span data-stu-id="8f34b-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="8f34b-443">假存放庫實際上並不會寫入資料庫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="8f34b-444">假的 SMTP 伺服器實際上不會透過網路傳送電子郵件訊息。</span><span class="sxs-lookup"><span data-stu-id="8f34b-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="8f34b-445">模擬與 Fakes</span><span class="sxs-lookup"><span data-stu-id="8f34b-445">Mocks versus Fakes</span></span>

<span data-ttu-id="8f34b-446">還有另一種類型的測試 double，稱為*mock*。</span><span class="sxs-lookup"><span data-stu-id="8f34b-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="8f34b-447">雖然 fakes 有運作中的執行，但模擬不會有任何實作為。</span><span class="sxs-lookup"><span data-stu-id="8f34b-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="8f34b-448">在模擬物件架構的協助之下，我們會在執行時間中建立這些 mock 物件，並將它們當做測試加倍。</span><span class="sxs-lookup"><span data-stu-id="8f34b-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="8f34b-449">在本節中，我們會使用開放原始碼模擬 framework Moq。</span><span class="sxs-lookup"><span data-stu-id="8f34b-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="8f34b-450">以下是使用 Moq 以動態方式為員工存放庫建立測試 double 的簡單範例。</span><span class="sxs-lookup"><span data-stu-id="8f34b-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="8f34b-451">我們會向 Moq 尋求 IRepository&lt;員工&gt; 實行，並以動態方式建立一個。</span><span class="sxs-lookup"><span data-stu-id="8f34b-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="8f34b-452">藉由存取 Mock&lt;T&gt; 物件的 Object 屬性，我們可以&lt;員工&gt; 取得執行 IRepository 的物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="8f34b-453">這是我們可以傳入控制器的內建物件，而且不知道這是測試雙重或真正的存放庫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="8f34b-454">我們可以叫用物件上的方法，就像我們會在實際執行的物件上叫用方法一樣。</span><span class="sxs-lookup"><span data-stu-id="8f34b-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="8f34b-455">當我們叫用 Add 方法時，您必須想知道模擬儲存機制會執行的動作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="8f34b-456">因為 mock 物件後面沒有任何執行，所以 Add 不會執行任何操作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="8f34b-457">在幕後不會有具體的集合，像是我們所撰寫的 fakes，所以會捨棄員工。</span><span class="sxs-lookup"><span data-stu-id="8f34b-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="8f34b-458">FindById 的傳回值呢？</span><span class="sxs-lookup"><span data-stu-id="8f34b-458">What about the return value of FindById?</span></span> <span data-ttu-id="8f34b-459">在此情況下，mock 物件會執行唯一可以做的事，這會傳回預設值。</span><span class="sxs-lookup"><span data-stu-id="8f34b-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="8f34b-460">因為我們傳回的是參考型別（Employee），所以傳回值為 null 值。</span><span class="sxs-lookup"><span data-stu-id="8f34b-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="8f34b-461">模擬可能聽起來不萬能;不過，我們還沒有談到模擬的兩個功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="8f34b-462">首先，Moq 架構會記錄在 mock 物件上發出的所有呼叫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="8f34b-463">稍後在程式碼中，如果有人叫用 Add 方法，或者任何人叫用了 FindById 方法，就可以詢問 Moq。</span><span class="sxs-lookup"><span data-stu-id="8f34b-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="8f34b-464">我們稍後會看到如何在測試中使用此「黑色箱」錄製功能。</span><span class="sxs-lookup"><span data-stu-id="8f34b-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="8f34b-465">第二個很棒的功能是我們可以如何使用 Moq 來程式設計具有*預期*的模擬物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="8f34b-466">預期會告訴 mock 物件如何回應任何指定的互動。</span><span class="sxs-lookup"><span data-stu-id="8f34b-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="8f34b-467">例如，我們可以在我們的 mock 中編寫預期的程式，並在有人叫用 FindById 時，告訴它傳回 employee 物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="8f34b-468">Moq 架構會使用安裝程式 API 和 lambda 運算式來設計這些預期。</span><span class="sxs-lookup"><span data-stu-id="8f34b-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="8f34b-469">在此範例中，我們會要求 Moq 以動態方式建立存放庫，然後在預期的情況下為存放庫進行程式設計。</span><span class="sxs-lookup"><span data-stu-id="8f34b-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="8f34b-470">當有人叫用傳遞值為5的 FindById 方法時，預期會告訴 mock 物件傳回識別碼值為5的新員工物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="8f34b-471">這種測試會通過，而且我們不需要為假的 IRepository&lt;T&gt;建立完整的執行。</span><span class="sxs-lookup"><span data-stu-id="8f34b-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="8f34b-472">讓我們重新流覽稍早撰寫的測試，並將其修改為使用模擬，而不是 fakes。</span><span class="sxs-lookup"><span data-stu-id="8f34b-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="8f34b-473">就像之前一樣，我們將使用基類來設定所有控制器測試所需的基礎結構的通用部分。</span><span class="sxs-lookup"><span data-stu-id="8f34b-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="8f34b-474">安裝程式碼最常維持不變。</span><span class="sxs-lookup"><span data-stu-id="8f34b-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="8f34b-475">我們會使用 Moq 來建立模擬物件，而不是使用 fakes。</span><span class="sxs-lookup"><span data-stu-id="8f34b-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="8f34b-476">當程式碼叫用 employee 屬性時，基底類別會針對模擬工作單位進行排列，以傳回 mock 存放庫。</span><span class="sxs-lookup"><span data-stu-id="8f34b-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="8f34b-477">Mock 設定的其餘部分將會發生在每個特定案例專用的測試裝置內。</span><span class="sxs-lookup"><span data-stu-id="8f34b-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="8f34b-478">例如，「索引」動作的測試裝置會設定模擬儲存機制，以在動作叫用模擬儲存機制的 FindAll 方法時，傳回員工清單。</span><span class="sxs-lookup"><span data-stu-id="8f34b-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="8f34b-479">除了預期外，我們的測試看起來會像之前的測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="8f34b-480">不過，透過模擬架構的錄音功能，我們可以從不同角度來進行測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="8f34b-481">我們將在下一節探討這個新的觀點。</span><span class="sxs-lookup"><span data-stu-id="8f34b-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="8f34b-482">狀態與互動測試</span><span class="sxs-lookup"><span data-stu-id="8f34b-482">State versus Interaction Testing</span></span>

<span data-ttu-id="8f34b-483">您可以使用不同的技術來測試具有 mock 物件的軟體。</span><span class="sxs-lookup"><span data-stu-id="8f34b-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="8f34b-484">其中一種方法是使用狀態測試，這是我們在目前為止所做的工作。</span><span class="sxs-lookup"><span data-stu-id="8f34b-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="8f34b-485">以狀態為基礎的測試會對軟體的狀態做出判斷提示。</span><span class="sxs-lookup"><span data-stu-id="8f34b-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="8f34b-486">在最後一個測試中，我們在控制器上叫用了動作方法，並對它所應建立的模型做出判斷提示。</span><span class="sxs-lookup"><span data-stu-id="8f34b-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="8f34b-487">以下是一些測試狀態的其他範例：</span><span class="sxs-lookup"><span data-stu-id="8f34b-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="8f34b-488">確認存放庫在 Create 執行之後包含新的 employee 物件。</span><span class="sxs-lookup"><span data-stu-id="8f34b-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="8f34b-489">確認模型在索引執行後保存所有員工的清單。</span><span class="sxs-lookup"><span data-stu-id="8f34b-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="8f34b-490">確認在刪除執行後，存放庫未包含指定的員工。</span><span class="sxs-lookup"><span data-stu-id="8f34b-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="8f34b-491">另一個您會看到模擬物件的方法，就是驗證*互動*。</span><span class="sxs-lookup"><span data-stu-id="8f34b-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="8f34b-492">雖然以狀態為基礎的測試會對物件的狀態進行判斷提示，但互動式測試會對物件的互動方式做出判斷提示。</span><span class="sxs-lookup"><span data-stu-id="8f34b-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="8f34b-493">例如：</span><span class="sxs-lookup"><span data-stu-id="8f34b-493">For example:</span></span>

-   <span data-ttu-id="8f34b-494">確認在建立執行時，控制器會叫用存放庫的 Add 方法。</span><span class="sxs-lookup"><span data-stu-id="8f34b-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="8f34b-495">確認控制器會在執行索引時，叫用存放庫的 FindAll 方法。</span><span class="sxs-lookup"><span data-stu-id="8f34b-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="8f34b-496">確認控制器會叫用工作單位的 Commit 方法，以在執行 [編輯] 時儲存變更。</span><span class="sxs-lookup"><span data-stu-id="8f34b-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="8f34b-497">互動測試通常需要較少的測試資料，因為我們不會在集合內刺探並驗證計數。</span><span class="sxs-lookup"><span data-stu-id="8f34b-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="8f34b-498">例如，如果我們知道詳細資料動作會使用正確的值叫用存放庫的 FindById 方法，則此動作的行為可能會正確。</span><span class="sxs-lookup"><span data-stu-id="8f34b-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="8f34b-499">我們可以驗證此行為，而不需要設定任何要從 FindById 傳回的測試資料。</span><span class="sxs-lookup"><span data-stu-id="8f34b-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="8f34b-500">上述測試裝置中唯一需要的設定是由基類提供的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="8f34b-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="8f34b-501">當我們叫用控制器動作時，Moq 會記錄與 mock 存放庫的互動。</span><span class="sxs-lookup"><span data-stu-id="8f34b-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="8f34b-502">使用 Moq 的驗證 API，我們可以在控制器叫用具有適當識別碼值的 FindById 時，詢問 Moq。</span><span class="sxs-lookup"><span data-stu-id="8f34b-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="8f34b-503">如果控制器未叫用方法，或以非預期的參數值叫用方法，則 Verify 方法會擲回例外狀況，且測試將會失敗。</span><span class="sxs-lookup"><span data-stu-id="8f34b-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="8f34b-504">以下是另一個驗證 Create 動作在目前工作單位上叫用 Commit 的範例。</span><span class="sxs-lookup"><span data-stu-id="8f34b-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="8f34b-505">互動測試有一個危險，那就是過度指定互動的趨勢。</span><span class="sxs-lookup"><span data-stu-id="8f34b-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="8f34b-506">模擬物件記錄並確認每個與模擬物件互動的能力，並不表示測試應該嘗試驗證每個互動。</span><span class="sxs-lookup"><span data-stu-id="8f34b-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="8f34b-507">某些互動是執行詳細資料，您應該只驗證滿足目前測試*所需*的互動。</span><span class="sxs-lookup"><span data-stu-id="8f34b-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="8f34b-508">模擬或 fakes 之間的選擇主要取決於您要測試的系統和您的個人（或小組）喜好設定。</span><span class="sxs-lookup"><span data-stu-id="8f34b-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="8f34b-509">模擬物件可以大幅減少執行測試加倍所需的程式碼數量，但並非所有人都有熟悉的程式設計期望和驗證互動。</span><span class="sxs-lookup"><span data-stu-id="8f34b-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="8f34b-510">結論</span><span class="sxs-lookup"><span data-stu-id="8f34b-510">Conclusions</span></span>

<span data-ttu-id="8f34b-511">在本文中，我們示範了數種方法來建立可測試的程式碼，同時使用 ADO.NET Entity Framework 來進行資料持續性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="8f34b-512">我們可以利用內建的抽象概念（例如 IObjectSet&lt;T&gt;），或建立自己的抽象概念，例如 IRepository&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="8f34b-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="8f34b-513">  在這兩種情況下，ADO.NET Entity Framework 4.0 中的 POCO 支援可讓這些抽象類別的取用者維持持續性未知且高度可測試性。</span><span class="sxs-lookup"><span data-stu-id="8f34b-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="8f34b-514">隱含消極式載入之類的其他 EF4 功能可讓商務和應用程式服務程式碼工作，而不需擔心關聯式資料存放區的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="8f34b-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="8f34b-515">最後，我們所建立的抽象概念在單元測試中很容易模擬或假，而我們可以使用這些測試來達到快速執行、高度隔離和可靠的測試。</span><span class="sxs-lookup"><span data-stu-id="8f34b-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="8f34b-516">其他資源</span><span class="sxs-lookup"><span data-stu-id="8f34b-516">Additional Resources</span></span>

-   <span data-ttu-id="8f34b-517">Robert c. 「[單一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)」</span><span class="sxs-lookup"><span data-stu-id="8f34b-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="8f34b-518">聖馬丁 Fowler，*企業應用程式架構模式*的[模式目錄](https://www.martinfowler.com/eaaCatalog/index.html)</span><span class="sxs-lookup"><span data-stu-id="8f34b-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="8f34b-519">Griffin Caprio，「相依性[插入](https://msdn.microsoft.com/magazine/cc163739.aspx)」</span><span class="sxs-lookup"><span data-stu-id="8f34b-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="8f34b-520">資料可程式性 Blog，「[逐步解說：使用 Entity Framework 4.0 的測試導向開發](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)」。</span><span class="sxs-lookup"><span data-stu-id="8f34b-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)”.</span></span>
-   <span data-ttu-id="8f34b-521">資料可程式性的 Blog，「[使用存放庫和 Entity Framework 4.0 的工作單位模式](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)」</span><span class="sxs-lookup"><span data-stu-id="8f34b-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)”</span></span>
-   <span data-ttu-id="8f34b-522">Aaron Jensen 「[簡介機器規格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」</span><span class="sxs-lookup"><span data-stu-id="8f34b-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="8f34b-523">Eric 先生，" [BDD With MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"</span><span class="sxs-lookup"><span data-stu-id="8f34b-523">Eric Lee, “ [BDD with MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)”</span></span>
-   <span data-ttu-id="8f34b-524">Eric Evans，「[網域導向設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)」</span><span class="sxs-lookup"><span data-stu-id="8f34b-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="8f34b-525">聖馬丁 Fowler，"[模擬不是存根](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="8f34b-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="8f34b-526">聖馬丁 Fowler，" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="8f34b-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="8f34b-527">Moq</span><span class="sxs-lookup"><span data-stu-id="8f34b-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="8f34b-528">自傳</span><span class="sxs-lookup"><span data-stu-id="8f34b-528">Biography</span></span>

<span data-ttu-id="8f34b-529">Scott Allen 是 Pluralsight 和創辦人 OdeToCode.com 的技術人員的成員。</span><span class="sxs-lookup"><span data-stu-id="8f34b-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="8f34b-530">在15年的商務軟體開發中，Scott 已針對從8位 embedded 裝置到可高度擴充性 ASP.NET web 應用程式的所有專案，處理解決方案。</span><span class="sxs-lookup"><span data-stu-id="8f34b-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="8f34b-531">您可以在 OdeToCode 或 Twitter 上的[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)，觸達 Scott 的 blog。</span><span class="sxs-lookup"><span data-stu-id="8f34b-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
