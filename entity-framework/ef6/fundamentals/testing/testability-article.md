---
title: 可測試性和 Entity Framework 4.0
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
caps.latest.revision: 3
ms.openlocfilehash: 61773f8a23ff54ddb78aeeb5656c669b12f91478
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120363"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="cc224-102">可測試性和 Entity Framework 4.0</span><span class="sxs-lookup"><span data-stu-id="cc224-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="cc224-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="cc224-103">Scott Allen</span></span>

<span data-ttu-id="cc224-104">發行日期： 2010 年</span><span class="sxs-lookup"><span data-stu-id="cc224-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="cc224-105">簡介</span><span class="sxs-lookup"><span data-stu-id="cc224-105">Introduction</span></span>

<span data-ttu-id="cc224-106">這份白皮書說明並示範如何撰寫可測試的程式碼使用 ADO.NET Entity Framework 4.0 和 Visual Studio 2010。</span><span class="sxs-lookup"><span data-stu-id="cc224-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="cc224-107">這份文件不會嘗試將焦點放在特定的測試方法，例如測試導向設計 (TDD) 或行為導向設計 (BDD)。</span><span class="sxs-lookup"><span data-stu-id="cc224-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="cc224-108">改為本文將著重在如何撰寫使用 ADO.NET Entity Framework，但仍然容易隔離，並以自動化方式測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="cc224-109">我們將探討常見的設計模式，方便進行測試中的資料存取案例，並了解如何使用架構時，適用這些模式。</span><span class="sxs-lookup"><span data-stu-id="cc224-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="cc224-110">我們也將探討以查看這些功能可以在測試的程式碼中的運作方式的 framework 的特定功能。</span><span class="sxs-lookup"><span data-stu-id="cc224-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="cc224-111">什麼是可測試的程式碼？</span><span class="sxs-lookup"><span data-stu-id="cc224-111">What Is Testable Code?</span></span>

<span data-ttu-id="cc224-112">若要確認的軟體使用自動化的單元測試的能力提供許多理想的好處。</span><span class="sxs-lookup"><span data-stu-id="cc224-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="cc224-113">大家都知道，很好的測試將會減少軟體缺失，在應用程式，並增加應用程式的品質-但備妥的單元測試及遠遠超過只發現 bug 的數目。</span><span class="sxs-lookup"><span data-stu-id="cc224-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="cc224-114">優良的單元測試套件可讓開發小組節省時間，以及完全掌控他們建立的軟體。</span><span class="sxs-lookup"><span data-stu-id="cc224-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="cc224-115">小組可以變更現有的程式碼、 重構、 重新設計，並進行重建軟體，以符合新的需求，並同時了解測試套件的應用程式中加入新的元件可以確認應用程式的行為。</span><span class="sxs-lookup"><span data-stu-id="cc224-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="cc224-116">單元測試是推動變更和保留的可維護性軟體的複雜性增加時快速意見反應週期的一部分。</span><span class="sxs-lookup"><span data-stu-id="cc224-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="cc224-117">單元測試必須付出代價，不過。</span><span class="sxs-lookup"><span data-stu-id="cc224-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="cc224-118">小組必須投入的時間來建立與維護單元測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="cc224-119">直接相關的建立這些測試所需的投入量**testability**的基礎的軟體。</span><span class="sxs-lookup"><span data-stu-id="cc224-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="cc224-120">如何輕鬆是測試軟體？</span><span class="sxs-lookup"><span data-stu-id="cc224-120">How easy is the software to test?</span></span> <span data-ttu-id="cc224-121">可測試性記住設計軟體的小組會比使用非可測試軟體的小組更快建立有效的測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="cc224-122">Microsoft 設計 ADO.NET Entity Framework 4.0 (EF4) 可測試性納入考量。</span><span class="sxs-lookup"><span data-stu-id="cc224-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="cc224-123">這並不表示開發人員撰寫針對本身的 framework 程式碼的單元測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="cc224-124">相反地，可測試性的目標 EF4 讓您輕鬆建立可測試架構上建置的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="cc224-125">我們看看特定範例之前，或許值得了解可測試的程式碼的品質。</span><span class="sxs-lookup"><span data-stu-id="cc224-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="cc224-126">可測試的程式碼的品質</span><span class="sxs-lookup"><span data-stu-id="cc224-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="cc224-127">很容易測試的程式碼一律會出現兩個以上的特性。</span><span class="sxs-lookup"><span data-stu-id="cc224-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="cc224-128">第一個、 可測試的程式碼很容易**觀察**。</span><span class="sxs-lookup"><span data-stu-id="cc224-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="cc224-129">指定輸入的某些設定，它應該很容易觀察所輸出的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="cc224-130">例如，測試下列方法很容易因為方法會直接傳回計算的結果。</span><span class="sxs-lookup"><span data-stu-id="cc224-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="cc224-131">測試方法會很困難，如果方法會將計算的值寫入至的網路通訊端、 資料庫資料表或檔案，以類似下列程式碼就行了。</span><span class="sxs-lookup"><span data-stu-id="cc224-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="cc224-132">測試必須執行其他工作，以擷取的值。</span><span class="sxs-lookup"><span data-stu-id="cc224-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="cc224-133">其次，可測試的程式碼很容易**隔離**。</span><span class="sxs-lookup"><span data-stu-id="cc224-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="cc224-134">讓我們使用下列虛擬程式碼可測試的程式碼不正確的範例。</span><span class="sxs-lookup"><span data-stu-id="cc224-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="cc224-135">方法很容易觀察 – 可以傳入的保險原則，並確認傳回的值符合預期的結果。</span><span class="sxs-lookup"><span data-stu-id="cc224-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="cc224-136">不過，若要測試的方法我們必須要有資料庫，使用正確的結構描述中，安裝和設定 SMTP 伺服器，以防方法會嘗試傳送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="cc224-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="cc224-137">單元測試只想要驗證在方法中，計算邏輯，但測試可能會失敗，因為電子郵件伺服器處於離線狀態，或資料庫伺服器已移動。</span><span class="sxs-lookup"><span data-stu-id="cc224-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="cc224-138">這兩個這些失敗無關確認要測試的行為。</span><span class="sxs-lookup"><span data-stu-id="cc224-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="cc224-139">行為很難隔離。</span><span class="sxs-lookup"><span data-stu-id="cc224-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="cc224-140">軟體開發人員致力於撰寫可測試的程式碼通常會致力於維持的重要性分離程式碼中所撰寫。</span><span class="sxs-lookup"><span data-stu-id="cc224-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="cc224-141">上述的方法應該專注於商業計算，並委派給其他元件的資料庫與電子郵件實作詳細資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="cc224-142">Robert C.Martin 會將此稱為 Single Responsibility Principle。</span><span class="sxs-lookup"><span data-stu-id="cc224-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="cc224-143">物件，應該將封裝單一、 窄的責任，例如，計算原則的值。</span><span class="sxs-lookup"><span data-stu-id="cc224-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="cc224-144">所有其他資料庫和通知工作應該是某個其他物件的責任。</span><span class="sxs-lookup"><span data-stu-id="cc224-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="cc224-145">以這種方式撰寫的程式碼會比較容易隔離，因為它著重於單一工作。</span><span class="sxs-lookup"><span data-stu-id="cc224-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="cc224-146">在.NET 中，我們有我們需要依照 Single Responsibility Principle，達成隔離的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="cc224-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="cc224-147">我們可以使用介面定義，並強制使用抽象介面，而不是具象類型的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="cc224-148">稍後在本文中我們會看到如何方法，如以上所顯示的錯誤範例可以使用介面*看起來*像是他們會討論到資料庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="cc224-149">不過，在測試階段，我們可以取代 dummy 的實作不會與資料庫互動，但改為在記憶體中保存資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="cc224-150">這個假的實作會隔離中的資料存取程式碼或資料庫組態的相關問題的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="cc224-151">還有其他優點，來隔離。</span><span class="sxs-lookup"><span data-stu-id="cc224-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="cc224-152">最後一個方法的商業計算應該只需要幾毫秒就能執行，但測試本身可能會執行幾秒鐘的時間與網路及討論的程式碼躍點到各種伺服器。</span><span class="sxs-lookup"><span data-stu-id="cc224-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="cc224-153">單元測試應該執行快速，以便進行小變更。</span><span class="sxs-lookup"><span data-stu-id="cc224-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="cc224-154">單元測試應該也是可重複和不失敗，因為測試與無關的元件發生問題。</span><span class="sxs-lookup"><span data-stu-id="cc224-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="cc224-155">撰寫程式碼，很容易觀察，並找出可讓您表示開發人員必須方便撰寫程式碼的測試，縮短測試執行時，所等待的時間，更重要的是，較少的時間追蹤不存在的 bug。</span><span class="sxs-lookup"><span data-stu-id="cc224-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="cc224-156">希望您能測試的優點，並了解可測試的程式碼表現出品質。</span><span class="sxs-lookup"><span data-stu-id="cc224-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="cc224-157">我們要說明如何撰寫程式碼搭配 EF4 將資料儲存至資料庫，同時保有可觀察和容易隔離，但首先我們會縮小討論資料存取的可測試的設計重點。</span><span class="sxs-lookup"><span data-stu-id="cc224-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="cc224-158">資料持續性的設計模式</span><span class="sxs-lookup"><span data-stu-id="cc224-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="cc224-159">這兩個稍早顯示的錯誤範例有太多職責。</span><span class="sxs-lookup"><span data-stu-id="cc224-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="cc224-160">第一個錯誤範例必須執行計算*和*寫入檔案。</span><span class="sxs-lookup"><span data-stu-id="cc224-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="cc224-161">第二個不正確的範例必須從資料庫讀取資料*並*執行的商業計算*和*傳送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="cc224-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="cc224-162">藉由設計較小的方法，將重點分開，並委派給其他元件的責任，您要進行許多努力來撰寫可測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="cc224-163">目標是要建置功能，藉由撰寫小而聚焦的抽象概念中的動作。</span><span class="sxs-lookup"><span data-stu-id="cc224-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="cc224-164">當它移至資料持續性小而聚焦我們所需的抽象概念很常見它們已記錄在文件做為設計模式。</span><span class="sxs-lookup"><span data-stu-id="cc224-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="cc224-165">Martin Fowler 書籍 Patterns of Enterprise Application Architecture 已描述這些模式中列印的第一個工作。</span><span class="sxs-lookup"><span data-stu-id="cc224-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="cc224-166">我們會示範如何實作這些 ADO.NET Entity Framework，並配合這些模式之前，我們將提供下列各節中的這些模式的簡短描述。</span><span class="sxs-lookup"><span data-stu-id="cc224-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="cc224-167">儲存機制模式</span><span class="sxs-lookup"><span data-stu-id="cc224-167">The Repository Pattern</span></span>

<span data-ttu-id="cc224-168">Fowler 表示儲存機制 」 之間居中協調的網域和資料使用類似集合的介面來存取網域物件的對應層 」。</span><span class="sxs-lookup"><span data-stu-id="cc224-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="cc224-169">儲存機制模式的目標是要隔離程式碼，從資料存取微末節，而我們可了解先前的隔離是針對可測試性所需的特性。</span><span class="sxs-lookup"><span data-stu-id="cc224-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="cc224-170">隔離的關鍵在於存放庫如何公開使用的集合型介面的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="cc224-171">您要使用的儲存機制中有不知道如何撰寫邏輯存放庫會具體化您要求的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="cc224-172">存放庫可能會向資料庫，或它可能只傳回從記憶體中集合的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="cc224-173">您的程式碼必須知道的就是存放庫會出現維護集合，並可擷取、 新增和刪除集合中的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="cc224-174">在現有.NET 應用程式的具體的存放庫通常繼承自泛型介面，如下所示：</span><span class="sxs-lookup"><span data-stu-id="cc224-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="cc224-175">我們將變更幾個介面定義當我們 EF4，提供實作，但基本概念維持不變。</span><span class="sxs-lookup"><span data-stu-id="cc224-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="cc224-176">程式碼可以使用實作這個介面的具象儲存機制來擷取實體的主索引鍵值，來擷取實體的述詞評估為基礎的集合，或只擷取所有可用的實體。</span><span class="sxs-lookup"><span data-stu-id="cc224-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="cc224-177">程式碼也可以新增和移除機構可透過儲存機制介面。</span><span class="sxs-lookup"><span data-stu-id="cc224-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="cc224-178">指定的員工 IRepository 物件，程式碼可以執行下列作業。</span><span class="sxs-lookup"><span data-stu-id="cc224-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="cc224-179">因為程式碼使用介面 （IRepository 的員工），我們可以提供不同的實作介面的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="cc224-180">一個實作可能會受到 EF4 和保存物件到 Microsoft SQL Server 資料庫實作。</span><span class="sxs-lookup"><span data-stu-id="cc224-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="cc224-181">（一個我們在測試期間使用） 的不同實作可能會受到記憶體內的員工清單的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="cc224-182">介面可協助達到隔離程式碼中。</span><span class="sxs-lookup"><span data-stu-id="cc224-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="cc224-183">請注意 IRepository&lt;T&gt;介面不會公開儲存作業。</span><span class="sxs-lookup"><span data-stu-id="cc224-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="cc224-184">我們要如何更新現有的物件？</span><span class="sxs-lookup"><span data-stu-id="cc224-184">How do we update existing objects?</span></span> <span data-ttu-id="cc224-185">您可能會在 IRepository 定義包含儲存作業，與這些儲存機制的實作必須立即將物件保存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="cc224-186">不過，許多應用程式中，我們不想要個別保存物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="cc224-187">相反地，我們想要注入生命的物件，可能是從不同的存放庫，修改這些物件做為一部分的商務活動，然後將所有物件都保存為單一、 不可部分完成作業的一部分。</span><span class="sxs-lookup"><span data-stu-id="cc224-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="cc224-188">幸運的是，沒有一個模式，以允許這種類型的行為。</span><span class="sxs-lookup"><span data-stu-id="cc224-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="cc224-189">工作單位模式</span><span class="sxs-lookup"><span data-stu-id="cc224-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="cc224-190">Fowler 說的工作單位會 「 維持物件的清單受到商務交易，並協調變更寫入和並行處理問題的解決方法 」。</span><span class="sxs-lookup"><span data-stu-id="cc224-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="cc224-191">它負責的工作單位，以追蹤變更的物件，我們注入生命，從存放庫，並將保存任何我們告訴工作單位，以認可變更時，我們所做的物件的變更。</span><span class="sxs-lookup"><span data-stu-id="cc224-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="cc224-192">它也是工作的所要採取的新物件，我們已新增到所有存放庫，並將物件插入至資料庫，以及管理刪除單位的責任。</span><span class="sxs-lookup"><span data-stu-id="cc224-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="cc224-193">如果您曾經做過任何工作與 ADO.NET 資料集然後您就已經很熟悉工作單位模式。</span><span class="sxs-lookup"><span data-stu-id="cc224-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="cc224-194">ADO.NET 資料集必須能夠追蹤我們的更新、 刪除和插入 DataRow 物件和無法 （以 TableAdapter 的說明） 調解我們所有的變更至資料庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="cc224-195">不過，DataSet 物件模型的基礎資料庫已中斷連線的子集。</span><span class="sxs-lookup"><span data-stu-id="cc224-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="cc224-196">工作單位模式表現相同的行為，但使用商務物件和資料存取程式碼與隔離和感知的資料庫的網域物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="cc224-197">模型的.NET 程式碼中的工作單位的抽象概念可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="cc224-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="cc224-198">藉由公開我們可以確保單一工作單位的工作單位對存放庫的參考物件具有追蹤所有實體具體化期間商務交易的能力。</span><span class="sxs-lookup"><span data-stu-id="cc224-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="cc224-199">所有魔力的地方來協調與資料庫的記憶體變更是工作的真實單位的 Commit 方法的實作。</span><span class="sxs-lookup"><span data-stu-id="cc224-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="cc224-200">指定 IUnitOfWork 參考，程式碼可以對從一或多個存放庫擷取商務物件中的變更，並儲存使用不可部分完成的認可作業的所有變更。</span><span class="sxs-lookup"><span data-stu-id="cc224-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="cc224-201">延遲的負載模式</span><span class="sxs-lookup"><span data-stu-id="cc224-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="cc224-202">Fowler 會使用名稱消極式載入來描述 」 物件不包含的所有資料，您需要但知道如何取得它 」。</span><span class="sxs-lookup"><span data-stu-id="cc224-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="cc224-203">透明的消極式載入是一項重要功能有時撰寫可測試的企業程式碼，並使用關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="cc224-204">例如，請考慮下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="cc224-205">如何填入工時卡集合？</span><span class="sxs-lookup"><span data-stu-id="cc224-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="cc224-206">有兩個可能的答案。</span><span class="sxs-lookup"><span data-stu-id="cc224-206">There are two possible answers.</span></span> <span data-ttu-id="cc224-207">答案是員工存放庫中，當系統要求擷取員工，發出查詢來擷取這兩個員工及其員工的相關聯的時間記錄卡資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="cc224-208">這通常需要使用 JOIN 子句的查詢，並可能會導致擷取比應用程式的詳細資訊的關聯式資料庫中必須。</span><span class="sxs-lookup"><span data-stu-id="cc224-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="cc224-209">如果應用程式永遠不需要碰觸的工時卡屬性嗎？</span><span class="sxs-lookup"><span data-stu-id="cc224-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="cc224-210">第二個答案是載入 「 依需求 」 的工時卡屬性。</span><span class="sxs-lookup"><span data-stu-id="cc224-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="cc224-211">此消極式載入是隱含且透明的商務邏輯，因為程式碼不會叫用特殊的 Api 來擷取時間記錄卡資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="cc224-212">程式碼假設存在時所需時間記錄卡資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="cc224-213">還有一些魔法通常牽涉到的方法引動過程的執行階段攔截的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="cc224-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="cc224-214">攔截程式碼是負責與資料庫通訊，並擷取時間記錄卡資訊，同時又可加以商務邏輯的商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="cc224-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="cc224-215">此延遲載入 magic 可讓商務程式碼，將本身從資料擷取作業，會導致更多可測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="cc224-216">消極式載入的缺點是，當應用程式*沒有*需要程式碼會執行其他查詢的時間記錄卡資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="cc224-217">這不是問題，對於許多應用程式，但效能敏感應用程式或應用程式循環的 employee 物件的數目和執行查詢期間所要擷取工時卡 （問題通常稱為 「 N + 1 迴圈的每個反覆項目查詢問題），消極式載入是拖曳。</span><span class="sxs-lookup"><span data-stu-id="cc224-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="cc224-218">在這些情況下應用程式可能會想要提早中最有效率的方式載入時間記錄卡資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="cc224-219">幸運的是，我們會看到如何 EF4 支援這兩個隱含的消極式載入和有效率的積極式載入，因為我們將移至下一節，並實作這些模式。</span><span class="sxs-lookup"><span data-stu-id="cc224-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="cc224-220">實作與 Entity Framework 的模式</span><span class="sxs-lookup"><span data-stu-id="cc224-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="cc224-221">好消息是，最後一節所述的設計模式都容易使用 EF4 實作。</span><span class="sxs-lookup"><span data-stu-id="cc224-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="cc224-222">若要示範我們將使用簡單的 ASP.NET MVC 應用程式來編輯，並顯示員工和其相關聯的時間記錄卡資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="cc224-223">我們一開始先使用下列 「 純舊 CLR 物件 」 (Poco)。</span><span class="sxs-lookup"><span data-stu-id="cc224-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="cc224-224">我們將探討不同的方法和功能 EF4，但其目的是為持續 (PI) 盡可能保留這些類別，這些類別定義會稍有變化。</span><span class="sxs-lookup"><span data-stu-id="cc224-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="cc224-225">PI 物件並不知道*如何*，或甚至*如果*，它會保存的狀態存留在資料庫內。</span><span class="sxs-lookup"><span data-stu-id="cc224-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="cc224-226">PI 和 Poco 同時兼顧可測試軟體。</span><span class="sxs-lookup"><span data-stu-id="cc224-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="cc224-227">使用 POCO 方法的物件是較不受條件約束、 更有彈性且更容易測試，因為它們可以作業不存在的資料庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="cc224-228">與就地 Poco 我們可以在 Visual Studio 中建立 Entity Data Model (EDM) （請參閱 圖 1）。</span><span class="sxs-lookup"><span data-stu-id="cc224-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="cc224-229">我們不會使用 EDM 來為實體產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="cc224-230">相反地，我們想要使用我們 lovingly 製作手動的實體。</span><span class="sxs-lookup"><span data-stu-id="cc224-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="cc224-231">我們只會使用 EDM 產生資料庫結構描述，並提供 EF4 需要將物件對應至資料庫的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![eftest_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="cc224-233">**圖 1**</span><span class="sxs-lookup"><span data-stu-id="cc224-233">**Figure 1**</span></span>

<span data-ttu-id="cc224-234">注意： 如果您想要先開發的 EDM 模型，就可以清除，產生在 EDM 中的 POCO 程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="cc224-235">您可以使用 Data Programmability 小組所提供的 Visual Studio 2010 擴充功能來這樣做。</span><span class="sxs-lookup"><span data-stu-id="cc224-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="cc224-236">若要下載擴充功能，啟動擴充管理員，從 Visual Studio 中的 [工具] 功能表並搜尋 「 POCO"（請參閱圖 2） 的線上資源庫中的範本。</span><span class="sxs-lookup"><span data-stu-id="cc224-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="cc224-237">有數個 POCO 範本，可供使用 EF 的。</span><span class="sxs-lookup"><span data-stu-id="cc224-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="cc224-238">如需有關如何使用範本的詳細資訊，請參閱 「[逐步解說： POCO Entity framework 的範本](http://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)"。</span><span class="sxs-lookup"><span data-stu-id="cc224-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](http://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)”.</span></span>

![eftest_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="cc224-240">**圖 2**</span><span class="sxs-lookup"><span data-stu-id="cc224-240">**Figure 2**</span></span>

<span data-ttu-id="cc224-241">從這個起點的 POCO 我們將探討兩種不同方法，可測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="cc224-242">第一種方法我呼叫 EF 方法，因為它會利用從 Entity Framework API，來實作的工作和儲存機制的單位的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="cc224-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="cc224-243">在第二個方法中我們建立我們自己的自訂儲存機制抽象概念，並接著會看到每一種方法的優缺點。</span><span class="sxs-lookup"><span data-stu-id="cc224-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="cc224-244">我們一開始先瀏覽 EF 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="cc224-245">EF 中心實作</span><span class="sxs-lookup"><span data-stu-id="cc224-245">An EF Centric Implementation</span></span>

<span data-ttu-id="cc224-246">請考慮下列控制器動作從 ASP.NET MVC 專案。</span><span class="sxs-lookup"><span data-stu-id="cc224-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="cc224-247">動作會擷取員工物件，並傳回結果以顯示員工的詳細的檢視。</span><span class="sxs-lookup"><span data-stu-id="cc224-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="cc224-248">是可測試的程式碼嗎？</span><span class="sxs-lookup"><span data-stu-id="cc224-248">Is the code testable?</span></span> <span data-ttu-id="cc224-249">有至少兩個測試，我們需要確認動作的行為。</span><span class="sxs-lookup"><span data-stu-id="cc224-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="cc224-250">首先，我們想要確認此動作會傳回正確的檢視 – 簡單的測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="cc224-251">我們也會想要撰寫測試來確認動作會擷取正確的員工，而且我們想要執行這項操作，但不會執行程式碼來查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="cc224-252">請記住，我們想要隔離受測程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="cc224-253">隔離可確保測試不會因為錯誤中的資料存取程式碼或資料庫組態失敗。</span><span class="sxs-lookup"><span data-stu-id="cc224-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="cc224-254">如果測試失敗，我們會知道我們有 bug，在控制器邏輯，而不是在一些較低的層級的系統元件。</span><span class="sxs-lookup"><span data-stu-id="cc224-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="cc224-255">若要達到我們需要一些抽象概念，如我們所提供的介面稍早儲存機制和工作單位的隔離。</span><span class="sxs-lookup"><span data-stu-id="cc224-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="cc224-256">請記住儲存機制模式設計來作為領域物件和資料對應層之間的橋樑。</span><span class="sxs-lookup"><span data-stu-id="cc224-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="cc224-257">在此案例中 EF4*已*對應的資料層，並已提供名為 IObjectSet 像是存放庫的抽象概念&lt;T&gt; （從 System.Data.Objects 命名空間中）。</span><span class="sxs-lookup"><span data-stu-id="cc224-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="cc224-258">介面定義看起來如下所示。</span><span class="sxs-lookup"><span data-stu-id="cc224-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="cc224-259">IObjectSet&lt;T&gt;符合存放庫的需求，因為它類似於物件的集合 (透過 IEnumerable&lt;T&gt;)，並提供方法來新增和移除模擬的集合中的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="cc224-260">Attach 和 Detach 方法公開 EF4 API 的其他功能。</span><span class="sxs-lookup"><span data-stu-id="cc224-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="cc224-261">若要使用 IObjectSet&lt;T&gt;為存放庫的介面中，我們需要的工作抽象單位，將繫結在一起的存放庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="cc224-262">一個具象實作這個介面會討論到 SQL Server，並輕鬆地建立使用 EF4 的 ObjectContext 類別。</span><span class="sxs-lookup"><span data-stu-id="cc224-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="cc224-263">ObjectContext 類別會是工作的實際 EF4 API 中單位。</span><span class="sxs-lookup"><span data-stu-id="cc224-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="cc224-264">將 IObjectSet&lt;T&gt;化為現實是，只要叫用的 ObjectContext 物件 CreateObjectSet 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="cc224-265">在幕後，架構會使用中繼資料我們提供在 EDM 中，以產生實體的 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="cc224-266">我們將著重於與傳回 IObjectSet&lt;T&gt;介面，因為它可協助保留在用戶端程式碼中的可測試性。</span><span class="sxs-lookup"><span data-stu-id="cc224-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="cc224-267">此具象實作可用於生產環境中，但我們要專注於如何我們將使用我們 IUnitOfWork 抽象概念，以利於進行測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="cc224-268">測試替身</span><span class="sxs-lookup"><span data-stu-id="cc224-268">The Test Doubles</span></span>

<span data-ttu-id="cc224-269">若要隔離的控制器動作，我們必須能夠實際工作單位的 （受 ObjectContext） 和工作 （執行於記憶體中作業） 的測試 double 或 「 假 」 單位之間切換。</span><span class="sxs-lookup"><span data-stu-id="cc224-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="cc224-270">常見的方法來執行這種類型的切換，就是工作的工作的不讓具現化單位，而是工作的工作的到控制器做為建構函式參數傳遞單位的 MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="cc224-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="cc224-271">上述程式碼是相依性插入的範例。</span><span class="sxs-lookup"><span data-stu-id="cc224-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="cc224-272">不允許建立它的相依性 （工作單位），但控制器中插入相依性的控制站。</span><span class="sxs-lookup"><span data-stu-id="cc224-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="cc224-273">在 MVC 專案中常會使用逆轉控制 (IoC) 容器中的組合中的自訂控制器 factory，來自動化相依性插入。</span><span class="sxs-lookup"><span data-stu-id="cc224-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="cc224-274">這些主題已超出本文討論的範圍，但您可以更藉由閱讀下列這篇文章結尾處的參考。</span><span class="sxs-lookup"><span data-stu-id="cc224-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="cc224-275">我們可以用來測試的工作實作的假單位看起來可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="cc224-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="cc224-276">請注意假工作單位的公開認可屬性。</span><span class="sxs-lookup"><span data-stu-id="cc224-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="cc224-277">它有時適合將功能新增至一個假的類別，方便進行測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="cc224-278">在此情況下，它很容易觀察是否程式碼會藉由檢查認可屬性認可的工作單位。</span><span class="sxs-lookup"><span data-stu-id="cc224-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="cc224-279">我們也需要假 IObjectSet&lt;T&gt;保留在記憶體中的員工和工時卡物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="cc224-280">我們可以提供使用泛型的單一實作。</span><span class="sxs-lookup"><span data-stu-id="cc224-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="cc224-281">這個測試替身委派 (delegate) 大部分的工作到基礎的 HashSet&lt;T&gt;物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="cc224-282">請注意該 IObjectSet&lt;T&gt;需要泛型條件約束，強制使用 T 做為類別 （參考型別），而且也會迫使我們實作 IQueryable&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="cc224-283">就可以輕鬆地進行記憶體中的集合會顯示為 IQueryable&lt;T&gt;使用標準的 LINQ 運算子 AsQueryable。</span><span class="sxs-lookup"><span data-stu-id="cc224-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="cc224-284">測試</span><span class="sxs-lookup"><span data-stu-id="cc224-284">The Tests</span></span>

<span data-ttu-id="cc224-285">傳統的單元測試會使用單一測試類別，可保存的所有動作的測試的所有單一的 MVC 控制器中。</span><span class="sxs-lookup"><span data-stu-id="cc224-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="cc224-286">我們可以撰寫這些測試或任何類型的單元測試時，使用記憶體中 fakes 我們所建置。</span><span class="sxs-lookup"><span data-stu-id="cc224-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="cc224-287">不過，本文中我們會避免在整合測試類別方法和改為群組我們將焦點放在特定功能的測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span>  <span data-ttu-id="cc224-288">比方說，「 建立新的員工 」 可能是我們想要測試，因此我們會使用單一測試類別，以確認負責建立新的員工的單一控制器動作的功能。</span><span class="sxs-lookup"><span data-stu-id="cc224-288">For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="cc224-289">沒有需所有這些更細緻的測試類別的一些常見設定程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="cc224-290">比方說，我們總是需要建立我們的記憶體中存放庫和假的工作單位。</span><span class="sxs-lookup"><span data-stu-id="cc224-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="cc224-291">我們也會需要具有假單位的插入工作的員工控制站的執行個體。</span><span class="sxs-lookup"><span data-stu-id="cc224-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="cc224-292">我們將使用的基底類別測試類別上的檔案，以共用這個常見的安裝程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="cc224-293">「 物件發明 「 我們使用的基底類別是一個常見的模式，來建立測試資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="cc224-294">物件母親包含跨多個測試裝置，使用的測試實體具現化的 factory 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="cc224-295">我們可以使用的基底類別的 EmployeeControllerTestBase 數目 （請參閱 圖 3） 的測試裝置。</span><span class="sxs-lookup"><span data-stu-id="cc224-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="cc224-296">每個測試固件會測試特定的控制器動作。</span><span class="sxs-lookup"><span data-stu-id="cc224-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="cc224-297">例如，一個測試固件將著重在測試期間 （以顯示用於建立員工檢視） 的 HTTP GET 要求，使用 [建立] 動作，而不同的裝置將著重在使用 HTTP POST 要求中的 [建立] 動作 (才會提交的資訊使用者若要建立某位員工）。</span><span class="sxs-lookup"><span data-stu-id="cc224-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="cc224-298">每個衍生的類別負責只在特定的內容中，並提供驗證結果，針對其特定測試的內容所需的判斷提示所需的設定。</span><span class="sxs-lookup"><span data-stu-id="cc224-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![eftest_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="cc224-300">**圖 3**</span><span class="sxs-lookup"><span data-stu-id="cc224-300">**Figure 3**</span></span>

<span data-ttu-id="cc224-301">此處所提供的命名慣例和測試樣式不需要再提供可測試的程式碼 – 這是只有一個方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="cc224-302">[圖 4] 顯示在 Jet 大腦 Resharper 中執行的測試會測試執行器外掛程式適用於 Visual Studio 2010。</span><span class="sxs-lookup"><span data-stu-id="cc224-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![eftest_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="cc224-304">**圖 4**</span><span class="sxs-lookup"><span data-stu-id="cc224-304">**Figure 4**</span></span>

<span data-ttu-id="cc224-305">使用基底類別，來處理共用的設定程式碼，每個控制器動作的單元測試是小型且容易撰寫。</span><span class="sxs-lookup"><span data-stu-id="cc224-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="cc224-306">測試會執行快速 （因為我們要執行的記憶體內作業），和不應因不相關的基礎結構或帶來了環保問題而失敗，（因為我們已隔離受測試的單位）。</span><span class="sxs-lookup"><span data-stu-id="cc224-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="cc224-307">在這些測試中，基底類別會執行大部分的設定工作。</span><span class="sxs-lookup"><span data-stu-id="cc224-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="cc224-308">請記住，基底類別建構函式會建立於記憶體中存放庫、 假的工作單位，以及 EmployeeController 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="cc224-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="cc224-309">測試類別衍生自這個基底類別，並著重於測試的 Create 方法的細節。</span><span class="sxs-lookup"><span data-stu-id="cc224-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="cc224-310">在此情況下細節歸結為任何單元測試程序中，您會看到 「 排列，採取行動，並判斷提示 」 的步驟：</span><span class="sxs-lookup"><span data-stu-id="cc224-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="cc224-311">建立 newEmployee 物件來模擬內送資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="cc224-312">叫用的 EmployeeController 的 [建立] 動作，並傳入 newEmployee。</span><span class="sxs-lookup"><span data-stu-id="cc224-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="cc224-313">確認 [建立] 動作會產生預期的結果 （顯示存放庫中的員工）。</span><span class="sxs-lookup"><span data-stu-id="cc224-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="cc224-314">我們所建置，可讓我們來測試任何 EmployeeController 動作。</span><span class="sxs-lookup"><span data-stu-id="cc224-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="cc224-315">比方說，當我們撰寫測試員工控制器的 Index 動作時我們可以繼承自測試基底類別，以建立相同的基底安裝程式進行測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="cc224-316">一次的基底類別會建立於記憶體中存放庫、 假的工作單位和 EmployeeController 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="cc224-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="cc224-317">將焦點放在叫用的索引動作只需要索引動作的測試，並測試模型之動作的品質傳回。</span><span class="sxs-lookup"><span data-stu-id="cc224-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="cc224-318">我們會建立使用記憶體中的 fakes 測試都是測試導向*狀態*的軟體。</span><span class="sxs-lookup"><span data-stu-id="cc224-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="cc224-319">例如，測試建立動作，我們想要建立動作執行 – 之後，請檢查存放庫的狀態時沒有儲存機制保存新進員工？</span><span class="sxs-lookup"><span data-stu-id="cc224-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="cc224-320">稍後我們將探討測試架構的互動。</span><span class="sxs-lookup"><span data-stu-id="cc224-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="cc224-321">如果受測程式碼叫用我們的物件上適當的方法，並傳遞正確的參數，則會要求基礎的互動測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="cc224-322">現在我們將繼續在封面上另一種設計模式-消極式載入。</span><span class="sxs-lookup"><span data-stu-id="cc224-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="cc224-323">積極式載入和消極式載入</span><span class="sxs-lookup"><span data-stu-id="cc224-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="cc224-324">在某個時間點，在 ASP.NET MVC web 應用程式，我們可能會想要顯示員工的資訊，並包含員工的相關聯的工時卡。</span><span class="sxs-lookup"><span data-stu-id="cc224-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="cc224-325">比方說，我們可能會有時間記錄卡的摘要顯示系統中顯示員工的名稱和執行總數的工時卡。</span><span class="sxs-lookup"><span data-stu-id="cc224-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="cc224-326">有數種方法，我們可以拿來實作這項功能。</span><span class="sxs-lookup"><span data-stu-id="cc224-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="cc224-327">Projection</span><span class="sxs-lookup"><span data-stu-id="cc224-327">Projection</span></span>

<span data-ttu-id="cc224-328">一個簡單的方法，建立摘要是以構建模型專屬於我們想要在檢視中顯示的資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="cc224-329">在此案例中模型看起來可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="cc224-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="cc224-330">請注意，EmployeeSummaryViewModel 不是實體，亦即它不是我們想要保存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="cc224-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="cc224-331">我們只有要使用這個類別來檢視隨機處理資料，以強型別的方式。</span><span class="sxs-lookup"><span data-stu-id="cc224-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="cc224-332">檢視模型是資料傳輸物件 (DTO)，因為它不包含的任何行為 （沒有任何方法） – 只有屬性。</span><span class="sxs-lookup"><span data-stu-id="cc224-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="cc224-333">屬性會保留我們需要移動的資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="cc224-334">它很容易就能具現化此使用 LINQ 的標準投射運算子 – Select 運算子的檢視模型。</span><span class="sxs-lookup"><span data-stu-id="cc224-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="cc224-335">有兩個值得注意的功能，上述程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-335">There are two notable features to the above code.</span></span> <span data-ttu-id="cc224-336">第一次 – 程式碼很容易測試，因為仍然容易觀察和隔離。</span><span class="sxs-lookup"><span data-stu-id="cc224-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="cc224-337">Select 運算子對我們的記憶體中 fakes 也一樣好，其方式就如同針對真實的工作單元。</span><span class="sxs-lookup"><span data-stu-id="cc224-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="cc224-338">第二個值得注意的功能是如何將程式碼讓 EF4 來產生單一、 有效率的查詢，以組合在一起的員工和時間記錄卡資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="cc224-339">我們已經將載入員工資訊及時間記錄卡資訊相同的物件而不使用任何特殊的 Api。</span><span class="sxs-lookup"><span data-stu-id="cc224-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="cc224-340">程式碼只被表示它需要使用標準的 LINQ 運算子，針對記憶體中的資料來源，以及遠端資料來源的資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="cc224-341">EF4 無法轉譯 LINQ 查詢與 C 所產生的運算式樹狀架構\#成單一且有效率的 T-SQL 查詢的編譯器。</span><span class="sxs-lookup"><span data-stu-id="cc224-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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
         )  AS [Project1]
    )  AS [Limit1]
```

<span data-ttu-id="cc224-342">有其他的時候，我們不想要檢視模型或 DTO 物件，但實際的實體。</span><span class="sxs-lookup"><span data-stu-id="cc224-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="cc224-343">當我們知道我們必須員工*和*員工的工時卡，我們積極可以載入相關的資料，以不干擾且有效率的方式。</span><span class="sxs-lookup"><span data-stu-id="cc224-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="cc224-344">明確的積極式載入</span><span class="sxs-lookup"><span data-stu-id="cc224-344">Explicit Eager Loading</span></span>

<span data-ttu-id="cc224-345">當我們想要提早載入相關的實體的資訊，我們需要某種機制，商務邏輯 （或在此案例中，控制器動作邏輯） 來表示其想要儲存機制。</span><span class="sxs-lookup"><span data-stu-id="cc224-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="cc224-346">EF4 ObjectQuery&lt;T&gt;類別會定義包含方法，以指定要在查詢期間擷取的相關的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="cc224-347">請記住 EF4 ObjectContext 會公開實體透過實體的 ObjectSet&lt;T&gt;類別繼承自 ObjectQuery&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span>  <span data-ttu-id="cc224-348">如果我們使用 ObjectSet&lt;T&gt;我們控制器動作，我們可以撰寫下列程式碼中的參考指定的時間記錄卡資訊對於每個員工的積極式載入。</span><span class="sxs-lookup"><span data-stu-id="cc224-348">If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="cc224-349">不過，由於我們想要保留我們的程式碼可測試我們都不會公開 ObjectSet&lt;T&gt;從外部工作類別的實際單位。</span><span class="sxs-lookup"><span data-stu-id="cc224-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="cc224-350">相反地，我們依賴 IObjectSet&lt;T&gt;介面，也就是您更輕鬆地假的但 IObjectSet&lt;T&gt;不會定義包含方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="cc224-351">LINQ 的優點是，我們可以建立自己的 Include 運算子。</span><span class="sxs-lookup"><span data-stu-id="cc224-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="cc224-352">請注意包含定義這個運算子作為擴充方法的 IQueryable&lt;T&gt;而不是 IObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="cc224-353">這讓我們能夠使用方法與更廣泛的可能的類型，包括 IQueryable&lt;T&gt;，IObjectSet&lt;T&gt;，ObjectQuery&lt;T&gt;，和 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="cc224-354">萬一基礎序列不是正版的 EF4 ObjectQuery&lt;T&gt;，則沒有完成沒有壞處，且包含運算子會執行任何作業。</span><span class="sxs-lookup"><span data-stu-id="cc224-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="cc224-355">如果基礎序列*已*ObjectQuery&lt;T&gt; (或衍生自 ObjectQuery&lt;T&gt;)，則 EF4 會看到我們的需求，其他的資料，並制定適當的 SQL查詢。</span><span class="sxs-lookup"><span data-stu-id="cc224-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="cc224-356">以此新的運算子，就地我們可以明確要求時間記錄卡資訊的積極式的載入從儲存機制。</span><span class="sxs-lookup"><span data-stu-id="cc224-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="cc224-357">當執行實際的 ObjectContext，程式碼就會產生下列的單一查詢。</span><span class="sxs-lookup"><span data-stu-id="cc224-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="cc224-358">查詢會從一次用來將 employee 物件具體化和完整填入其 [工時] 屬性中的資料庫收集足夠的資訊。</span><span class="sxs-lookup"><span data-stu-id="cc224-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

<span data-ttu-id="cc224-359">好消息是動作方法內的程式碼會保留完整測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="cc224-360">我們不需要提供任何額外的功能，如我們 fakes 以支援包含運算子。</span><span class="sxs-lookup"><span data-stu-id="cc224-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="cc224-361">壞消息是我們必須使用包含運算子內的程式碼，我們想要將非持續性的持續性。</span><span class="sxs-lookup"><span data-stu-id="cc224-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="cc224-362">這是您要建置可測試的程式碼時評估的權衡取捨的型別其基本範例。</span><span class="sxs-lookup"><span data-stu-id="cc224-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="cc224-363">有些的時候，當您需要讓外部儲存機制抽象概念，以符合效能目標的持續性考量流失。</span><span class="sxs-lookup"><span data-stu-id="cc224-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="cc224-364">積極式載入的替代方案是消極式載入。</span><span class="sxs-lookup"><span data-stu-id="cc224-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="cc224-365">我們執行消極式載入方式*不*需要我們的企業程式碼，明確地宣布的需求相關聯的資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="cc224-366">相反地，我們使用應用程式中的實體，以及額外的資料是否需要 Entity Framework 會將資料依需求載入。</span><span class="sxs-lookup"><span data-stu-id="cc224-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="cc224-367">消極式載入</span><span class="sxs-lookup"><span data-stu-id="cc224-367">Lazy Loading</span></span>

<span data-ttu-id="cc224-368">它很容易想像一下，我們不知道需要什麼資料的商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="cc224-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="cc224-369">我們可能會知道邏輯需要 employee 物件，但我們可能會分支到不同的執行路徑，其中部分這些路徑需要時間記錄卡資訊從員工，但某些不。</span><span class="sxs-lookup"><span data-stu-id="cc224-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="cc224-370">這種情況非常適合做為隱含消極式載入，因為神奇地出現在可視需要的資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="cc224-371">消極式載入，也就是延後載入，並置於一些需求實體物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="cc224-372">使用，則為 true 的永續性無知的 Poco 中不會面臨永續層，從任何需求，但是，則為 true 的持續性無知的幾乎不可能達成。</span><span class="sxs-lookup"><span data-stu-id="cc224-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span>  <span data-ttu-id="cc224-373">相反地，我們會測量持續性無知的相對度數。</span><span class="sxs-lookup"><span data-stu-id="cc224-373">Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="cc224-374">它會很不幸，如果我們需要從導向的持續性的基底類別繼承，或是使用特製化的集合，以達到在 Poco 的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="cc224-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="cc224-375">幸運的是，EF4 具有干擾性較低的解決方案。</span><span class="sxs-lookup"><span data-stu-id="cc224-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="cc224-376">幾乎無法偵測到</span><span class="sxs-lookup"><span data-stu-id="cc224-376">Virtually Undetectable</span></span>

<span data-ttu-id="cc224-377">當使用 POCO 物件，EF4 可以動態產生之實體的執行階段 proxy。</span><span class="sxs-lookup"><span data-stu-id="cc224-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="cc224-378">這些 proxy 無形的方式包裝具體化的 Poco 提供的攔截每個屬性的其他服務取得和設定作業來執行其他工作。</span><span class="sxs-lookup"><span data-stu-id="cc224-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="cc224-379">這類服務是我們所需的消極式載入功能。</span><span class="sxs-lookup"><span data-stu-id="cc224-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="cc224-380">另一個服務是高效率變更追蹤機制可以記錄程式變更實體的屬性值時。</span><span class="sxs-lookup"><span data-stu-id="cc224-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="cc224-381">保存任何已修改之實體使用 UPDATE 命令變更的清單是 objectcontext 使用 SaveChanges 方法期間。</span><span class="sxs-lookup"><span data-stu-id="cc224-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="cc224-382">這些 proxy 運作，不過，他們需要要連結到屬性 get 的方法，而且實體和 proxy 上的設定作業達成此目標，藉由覆寫虛擬成員。</span><span class="sxs-lookup"><span data-stu-id="cc224-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="cc224-383">因此，如果我們想要有隱含的消極式載入和高效率變更追蹤我們需要回到我們的 POCO 類別定義，並將標示為虛擬屬性。</span><span class="sxs-lookup"><span data-stu-id="cc224-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="cc224-384">我們仍然可以說 「 員工 」 實體是大部分持續。</span><span class="sxs-lookup"><span data-stu-id="cc224-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="cc224-385">唯一的需求是要使用的虛擬成員，這不會影響程式碼可測試性。</span><span class="sxs-lookup"><span data-stu-id="cc224-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="cc224-386">我們不需要衍生自任何特殊的基底類別，或甚至使用特殊的集合，專用於消極式載入。</span><span class="sxs-lookup"><span data-stu-id="cc224-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="cc224-387">因為程式碼示範，任何類別實作 ICollection&lt;T&gt;可保存相關的實體。</span><span class="sxs-lookup"><span data-stu-id="cc224-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="cc224-388">另外還有我們得先讓我們的工作單位內的其中一個次要變更。</span><span class="sxs-lookup"><span data-stu-id="cc224-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="cc224-389">消極式載入會*關閉*依預設，當直接使用為 ObjectContext 物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="cc224-390">沒有的屬性，我們可以在屬性設定 ContextOptions 啟用延後載入，而且我們可以在我們真正的工作單位，設定此屬性，如果我們想要啟用消極式載入所有位置。</span><span class="sxs-lookup"><span data-stu-id="cc224-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="cc224-391">啟用隱含的消極式載入，應用程式程式碼可以使用員工與員工的工時卡同時保有 ef 將額外的資料所需的工作不知道。</span><span class="sxs-lookup"><span data-stu-id="cc224-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="cc224-392">消極式載入，可讓應用程式程式碼更容易撰寫，並使用 proxy 神奇的程式碼保持完全可測試性。</span><span class="sxs-lookup"><span data-stu-id="cc224-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="cc224-393">記憶體中的工作單位的 fakes 可以只預先載入假的實體與相關聯的資料，需要在測試期間時。</span><span class="sxs-lookup"><span data-stu-id="cc224-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="cc224-394">現在我們將在其中將注意力轉移無法建立存放庫使用 IObjectSet&lt;T&gt;並查看 隱藏所有符號的持續性架構的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="cc224-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="cc224-395">自訂存放庫</span><span class="sxs-lookup"><span data-stu-id="cc224-395">Custom Repositories</span></span>

<span data-ttu-id="cc224-396">當我們在本文中，先介紹工作單位設計模式時我們提供一些範例程式碼的工作單位的外觀。</span><span class="sxs-lookup"><span data-stu-id="cc224-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="cc224-397">讓我們重新顯示這個使用員工和員工時間記錄卡案例，我們已使用原始的想法。</span><span class="sxs-lookup"><span data-stu-id="cc224-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="cc224-398">此工作單位的與我們在上一節中建立的工作單位的主要差異是此工作單位的不使用任何從 EF4 framework 的抽象概念的方式 (沒有任何 IObjectSet&lt;T&gt;)。</span><span class="sxs-lookup"><span data-stu-id="cc224-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="cc224-399">IObjectSet&lt;T&gt;以及儲存機制介面，但它會公開 API 的運作方式可能不完全對齊與我們的應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="cc224-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="cc224-400">在此即將推出的方法中，我們將代表存放庫使用自訂 IRepository&lt;T&gt;抽象概念。</span><span class="sxs-lookup"><span data-stu-id="cc224-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="cc224-401">許多開發人員測試為導向的設計、 行為導向設計，以及網域導向方法設計偏好 IRepository&lt;T&gt;原因有幾種方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="cc224-402">首先，IRepository&lt;T&gt;介面代表 「 防損毀 」 層。</span><span class="sxs-lookup"><span data-stu-id="cc224-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="cc224-403">中所述 Eric evans 網域導向設計著作防損毀層會保留您的網域程式碼離開的基礎結構的 Api，例如持續性 API。</span><span class="sxs-lookup"><span data-stu-id="cc224-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="cc224-404">其次，開發人員可以建置符合應用程式的確切需求 （如撰寫測試時，所發現） 存放庫的方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="cc224-405">比方說，我們可能會經常需要找出使用識別碼值，因此我們可以將 FindById 方法新增至存放庫介面的單一實體。</span><span class="sxs-lookup"><span data-stu-id="cc224-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span>  <span data-ttu-id="cc224-406">我們 IRepository&lt;T&gt;定義看起來如下所示。</span><span class="sxs-lookup"><span data-stu-id="cc224-406">Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="cc224-407">請注意，我們會將 IQueryable 重新置放&lt;T&gt;介面來公開 （expose） 實體集合。</span><span class="sxs-lookup"><span data-stu-id="cc224-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="cc224-408">IQueryable&lt;T&gt;允許流入 EF4 提供者，並讓提供者查詢的整體檢視的 LINQ 運算式樹狀架構。</span><span class="sxs-lookup"><span data-stu-id="cc224-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="cc224-409">第二個選項是以傳回 IEnumerable&lt;T&gt;，這表示 EF4 LINQ 提供者只會看到建立的儲存機制內的運算式。</span><span class="sxs-lookup"><span data-stu-id="cc224-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="cc224-410">任何的分組、 排序和儲存機制之外所做的預測將不被納入 SQL 命令傳送至資料庫，可能會損及效能。</span><span class="sxs-lookup"><span data-stu-id="cc224-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="cc224-411">另一方面，儲存機制傳回只 IEnumerable&lt;T&gt;結果將永遠不會令您吃驚使用新的 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="cc224-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="cc224-412">這兩種方法將會運作，且這兩種方式保持可測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="cc224-413">很容易提供單一實作 IRepository&lt;T&gt;介面使用泛型和 EF4 ObjectContext API。</span><span class="sxs-lookup"><span data-stu-id="cc224-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="cc224-414">IRepository&lt;T&gt;方法會提供一些額外的控制，透過我們的查詢因為用戶端可叫用方法，以回到實體。</span><span class="sxs-lookup"><span data-stu-id="cc224-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="cc224-415">在方法中，我們可以提供額外的檢查和強制執行應用程式的條件約束的 LINQ 運算子。</span><span class="sxs-lookup"><span data-stu-id="cc224-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="cc224-416">請注意，此介面有兩個條件約束的泛型型別參數上。</span><span class="sxs-lookup"><span data-stu-id="cc224-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="cc224-417">第一個條件約束是類別缺點，誤導所需的 ObjectSet&lt;T&gt;，並在第二個條件約束強制實作 IEntity – 建立應用程式的抽象概念實體。</span><span class="sxs-lookup"><span data-stu-id="cc224-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="cc224-418">IEntity 介面會強迫實體必須有可讀取的 Id 屬性，以及我們接著可以使用這個屬性在 FindById 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="cc224-419">IEntity 被定義為下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="cc224-420">因為我們的實體都必須實作這個介面，IEntity 可視為持續性無知的小型違規情形。</span><span class="sxs-lookup"><span data-stu-id="cc224-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="cc224-421">請記住永續性無知即將權衡取捨，而且許多 FindById 功能將勝過所需的介面所加諸的條件約束。</span><span class="sxs-lookup"><span data-stu-id="cc224-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="cc224-422">介面會將不會影響對可測試性。</span><span class="sxs-lookup"><span data-stu-id="cc224-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="cc224-423">具現化即時 IRepository&lt;T&gt;需要 EF4 ObjectContext，所以工作實作的具象單位應該管理的具現化。</span><span class="sxs-lookup"><span data-stu-id="cc224-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="cc224-424">使用自訂存放庫</span><span class="sxs-lookup"><span data-stu-id="cc224-424">Using the Custom Repository</span></span>

<span data-ttu-id="cc224-425">使用我們的自訂存放庫並不是明顯不同於使用存放庫根據 IObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="cc224-426">而不是直接對屬性套用 LINQ 運算子，我們必須先叫用其中一個存放庫的方法，擷取 IQueryable&lt;T&gt;參考。</span><span class="sxs-lookup"><span data-stu-id="cc224-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="cc224-427">請注意我們在先前所實作的自訂包含運算子將運作而不需要變更。</span><span class="sxs-lookup"><span data-stu-id="cc224-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="cc224-428">存放庫的 FindById 方法會移除重複的邏輯，從想要擷取單一實體的動作。</span><span class="sxs-lookup"><span data-stu-id="cc224-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="cc224-429">沒有任何重大的差異，我們已討論過的兩種方法的 testability 中。</span><span class="sxs-lookup"><span data-stu-id="cc224-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="cc224-430">我們可以提供假的實作的 IRepository&lt;T&gt;藉由建置具象類別受到 HashSet&lt;員工&gt;-就像我們在上一節。</span><span class="sxs-lookup"><span data-stu-id="cc224-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="cc224-431">不過，有些開發人員會想要使用模擬 （mock） 物件和模擬物件架構，而不要建立 fakes。</span><span class="sxs-lookup"><span data-stu-id="cc224-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="cc224-432">我們將探討測試我們的實作，並模擬 （mock） 和 fakes 之間的差異下, 一節將討論使用模擬 （mock）。</span><span class="sxs-lookup"><span data-stu-id="cc224-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="cc224-433">使用模擬 （mock） 進行測試</span><span class="sxs-lookup"><span data-stu-id="cc224-433">Testing with Mocks</span></span>

<span data-ttu-id="cc224-434">有不同的方法，若要建置哪些 Martin Fowler 呼叫 「 測試替身 」。</span><span class="sxs-lookup"><span data-stu-id="cc224-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="cc224-435">測試替身 （例如 double 的電影 stunt) 是建置 」，就能 「 實際測試期間的實際執行物件的物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="cc224-436">我們建立記憶體中存放庫是與 SQL Server 的存放庫的測試替身。</span><span class="sxs-lookup"><span data-stu-id="cc224-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="cc224-437">我們已了解如何使用這些測試替身在單元測試期間隔離程式碼，並保留快速執行的測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="cc224-438">我們已建置的測試替身有真實的工作的實作。</span><span class="sxs-lookup"><span data-stu-id="cc224-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="cc224-439">在幕後，每個儲存具象集合的物件，並將新增及從此集合移除物件，因為我們在測試期間操作存放庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="cc224-440">有些開發人員想要建置其測試替身，請在這種方式 – 真正的程式碼與工作實作。</span><span class="sxs-lookup"><span data-stu-id="cc224-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span>  <span data-ttu-id="cc224-441">這些測試雙精度浮點數是我們所謂*fakes*。</span><span class="sxs-lookup"><span data-stu-id="cc224-441">These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="cc224-442">它們有工作的實作，但是並不實際生產環境中使用。</span><span class="sxs-lookup"><span data-stu-id="cc224-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="cc224-443">假的存放庫實際上不會寫入資料庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="cc224-444">假的 SMTP 伺服器實際上不會透過網路傳送的電子郵件訊息。</span><span class="sxs-lookup"><span data-stu-id="cc224-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="cc224-445">與 Fakes 的模擬 （mock)</span><span class="sxs-lookup"><span data-stu-id="cc224-445">Mocks versus Fakes</span></span>

<span data-ttu-id="cc224-446">還有另一種稱為雙精確度的測試*模擬*。</span><span class="sxs-lookup"><span data-stu-id="cc224-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="cc224-447">雖然 fakes 工作實作，mock 就派沒有實作。</span><span class="sxs-lookup"><span data-stu-id="cc224-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="cc224-448">透過模擬物件架構的協助，我們會在執行階段建構這些模擬 （mock） 物件，並將其當做測試替身。</span><span class="sxs-lookup"><span data-stu-id="cc224-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="cc224-449">在本節中，我們將使用模擬 (mock) 架構 Moq 開放原始碼。</span><span class="sxs-lookup"><span data-stu-id="cc224-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="cc224-450">以下是使用 Moq 動態建立測試替身員工儲存機制的簡單範例。</span><span class="sxs-lookup"><span data-stu-id="cc224-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="cc224-451">我們會要求 IRepository Moq&lt;員工&gt;實作並建置一個動態。</span><span class="sxs-lookup"><span data-stu-id="cc224-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="cc224-452">我們可以讓物件實作 IRepository&lt;員工&gt;藉由存取物件屬性的 Mock&lt;T&gt;物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="cc224-453">它是我們可以將它傳遞至我們的控制站，這個內部物件，而且不會知道如果這是測試替身或實際的儲存機制。</span><span class="sxs-lookup"><span data-stu-id="cc224-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="cc224-454">我們可以叫用物件上的方法，就像我們會叫用的實際實作的物件上的方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="cc224-455">您必須想知道什麼模擬儲存機制時一樣我們叫用 Add 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="cc224-456">因為沒有實作的模擬 （mock） 物件後方，加入任何作用。</span><span class="sxs-lookup"><span data-stu-id="cc224-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="cc224-457">像我們使用我們所撰寫，fakes 讓員工會被捨棄，沒有任何在幕後的具象集合。</span><span class="sxs-lookup"><span data-stu-id="cc224-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="cc224-458">傳回值的 FindById 呢？</span><span class="sxs-lookup"><span data-stu-id="cc224-458">What about the return value of FindById?</span></span> <span data-ttu-id="cc224-459">在此情況下模擬 （mock） 物件沒有的唯一可以進行操作，也就是傳回的預設值。</span><span class="sxs-lookup"><span data-stu-id="cc224-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="cc224-460">因為我們會傳回參考型別 （員工），則傳回的值會是 null 值。</span><span class="sxs-lookup"><span data-stu-id="cc224-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="cc224-461">模擬 （mock） 可能看似毫無價值;不過，有兩個我們未討論到的模擬 （mock） 的多個功能。</span><span class="sxs-lookup"><span data-stu-id="cc224-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="cc224-462">首先，Moq framework 記錄進行模擬 （mock） 物件上的所有呼叫。</span><span class="sxs-lookup"><span data-stu-id="cc224-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="cc224-463">稍後在程式碼中，我們可以要求 Moq 任何人都叫用 Add 方法，則任何人都叫用 FindById 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="cc224-464">我們會看到我們可以在稍後如何在測試中使用此 「 黑箱作業 」 錄製功能。</span><span class="sxs-lookup"><span data-stu-id="cc224-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="cc224-465">第二個很棒的功能是如何以程式設計的模擬 （mock） 物件使用 Moq*期望*。</span><span class="sxs-lookup"><span data-stu-id="cc224-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="cc224-466">預期會告訴模擬 （mock） 物件如何回應任何給定的互動。</span><span class="sxs-lookup"><span data-stu-id="cc224-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="cc224-467">比方說，我們可以預期的情況下藉由程式設計納入我們的 mock，並告訴它有人會叫用 FindById 時，傳回將 employee 物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="cc224-468">Moq 架構會使用安裝程式 API 和 lambda 運算式程式這些期待。</span><span class="sxs-lookup"><span data-stu-id="cc224-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="cc224-469">在此範例中，我們要求以動態方式建立儲存機制，Moq，然後我們進行程式設計的人的存放庫。</span><span class="sxs-lookup"><span data-stu-id="cc224-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="cc224-470">預期會告訴某人叫用傳遞的值為 5 FindById 方法時，傳回的 Id 值為 5 的新員工物件的模擬 （mock） 物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="cc224-471">這項測試通過，而且我們不需要建置完整的實作，以假 IRepository&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="cc224-472">讓我們再次瀏覽我們稍早撰寫的測試，並修改他們使用模擬 （mock），而不是 fakes。</span><span class="sxs-lookup"><span data-stu-id="cc224-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="cc224-473">就像之前，我們將使用的基底類別來設定所需的所有控制器的測試基礎結構的常見項目。</span><span class="sxs-lookup"><span data-stu-id="cc224-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="cc224-474">設定程式碼大致保持相同。</span><span class="sxs-lookup"><span data-stu-id="cc224-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="cc224-475">而不是使用 fakes，我們將使用 Moq 建構模擬 （mock） 物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="cc224-476">基底類別排列的模擬 （mock） 的工作單位時要傳回的模擬 （mock） 的存放庫的程式碼會叫用 [員工] 屬性。</span><span class="sxs-lookup"><span data-stu-id="cc224-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="cc224-477">模擬 （mock） 的安裝程式的其餘部分就會專用於每個特定案例的測試裝置內進行。</span><span class="sxs-lookup"><span data-stu-id="cc224-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="cc224-478">比方說，測試固件，索引動作會設定模擬儲存機制時動作會叫用模擬的儲存機制的 FindAll 方法傳回的員工清單。</span><span class="sxs-lookup"><span data-stu-id="cc224-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="cc224-479">除了期望，我們的測試看起來像我們之前的測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="cc224-480">不過，記錄的能力，模擬 （mock） 架構的我們可以方法測試從不同的角度。</span><span class="sxs-lookup"><span data-stu-id="cc224-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="cc224-481">我們將探討這個新的檢視方塊下, 一節。</span><span class="sxs-lookup"><span data-stu-id="cc224-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="cc224-482">與互動的測試狀態</span><span class="sxs-lookup"><span data-stu-id="cc224-482">State versus Interaction Testing</span></span>

<span data-ttu-id="cc224-483">有不同的技術，可用來測試軟體與模擬 （mock） 物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="cc224-484">其中一個方法是使用狀態為基礎的測試，這是我們已經在這份文件中到目前為止。</span><span class="sxs-lookup"><span data-stu-id="cc224-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="cc224-485">狀態為基礎的軟體狀態相關的測試會判斷提示。</span><span class="sxs-lookup"><span data-stu-id="cc224-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="cc224-486">在最後一項測試中我們叫用動作方法的控制站上進行它應該會建置有關模型的判斷提示。</span><span class="sxs-lookup"><span data-stu-id="cc224-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="cc224-487">以下是一些其他的測試狀態的範例：</span><span class="sxs-lookup"><span data-stu-id="cc224-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="cc224-488">確認存放庫建立執行之後，包含 新的 員工 物件。</span><span class="sxs-lookup"><span data-stu-id="cc224-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="cc224-489">請確認索引執行之後，模型會保留一份所有員工。</span><span class="sxs-lookup"><span data-stu-id="cc224-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="cc224-490">確認存放庫未包含特定的員工，刪除執行之後。</span><span class="sxs-lookup"><span data-stu-id="cc224-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="cc224-491">您會看到與模擬 （mock） 物件的另一種方法是確認*互動*。</span><span class="sxs-lookup"><span data-stu-id="cc224-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="cc224-492">當狀態為基礎的物件狀態有關的測試會判斷提示時，互動以有關物件如何互動的測試會判斷提示。</span><span class="sxs-lookup"><span data-stu-id="cc224-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="cc224-493">例如: </span><span class="sxs-lookup"><span data-stu-id="cc224-493">For example:</span></span>

-   <span data-ttu-id="cc224-494">請確認控制器在建立執行時，會叫用儲存機制中的 Add 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="cc224-495">請確認控制器在索引執行時，會叫用儲存機制的 FindAll 方法。</span><span class="sxs-lookup"><span data-stu-id="cc224-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="cc224-496">請確認控制站會叫用的單位工作的 Commit 方法，以編輯執行時，儲存變更。</span><span class="sxs-lookup"><span data-stu-id="cc224-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="cc224-497">測試互動通常需要較少的測試資料，因為我們不在集合內閒晃，並驗證計數。</span><span class="sxs-lookup"><span data-stu-id="cc224-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="cc224-498">例如，如果我們知道的詳細資料動作會叫用的儲存機制 FindById 方法，使用正確的值-然後動作的可能行為正確無誤。</span><span class="sxs-lookup"><span data-stu-id="cc224-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="cc224-499">我們可以確認這種行為，而不需設定 FindById 從傳回的任何測試資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="cc224-500">在上述的測試固件所需的唯一設定是基底類別所提供的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="cc224-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="cc224-501">當我們叫用控制器動作時，Moq 會記錄與模擬 （mock） 的存放庫互動。</span><span class="sxs-lookup"><span data-stu-id="cc224-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="cc224-502">使用驗證 API 的 Moq，我們可以詢問 Moq 是否控制器叫用 FindById 適當的識別碼值。</span><span class="sxs-lookup"><span data-stu-id="cc224-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="cc224-503">如果控制器未叫用方法，或叫用具有非預期的參數值的方法，驗證方法會擲回例外狀況，而且測試會失敗。</span><span class="sxs-lookup"><span data-stu-id="cc224-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="cc224-504">以下是工作的另一個範例若要確認目前單位上建立動作叫用的認可。</span><span class="sxs-lookup"><span data-stu-id="cc224-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="cc224-505">一個危險互動測試是透過指定互動傾向。</span><span class="sxs-lookup"><span data-stu-id="cc224-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="cc224-506">記錄，並驗證與模擬 （mock） 物件的每個互動並不表示測試模擬 （mock） 物件的功能應該嘗試確認每個互動。</span><span class="sxs-lookup"><span data-stu-id="cc224-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="cc224-507">某些互動是實作詳細資料，以及您應該只驗證互動*必要*滿足目前的測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="cc224-508">模擬 （mock） 或 fakes 之間選擇主要取決於您要測試的系統和您的個人 （或小組） 喜好設定。</span><span class="sxs-lookup"><span data-stu-id="cc224-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="cc224-509">模擬 （mock） 物件可大幅減少您需要實作測試替身的程式碼數量，但並非所有人已熟悉程式設計期望和驗證互動。</span><span class="sxs-lookup"><span data-stu-id="cc224-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="cc224-510">結論</span><span class="sxs-lookup"><span data-stu-id="cc224-510">Conclusions</span></span>

<span data-ttu-id="cc224-511">在這份文件中，我們已示範了數種方法來建立可測試的程式碼時使用 ADO.NET Entity Framework，以保存資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="cc224-512">我們可以利用內建的抽象概念，例如 IObjectSet&lt;T&gt;，或建立自己的抽象概念，例如 IRepository&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="cc224-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span>  <span data-ttu-id="cc224-513">在這兩種情況下，ADO.NET Entity Framework 4.0 的 POCO 支援可讓這些抽象概念的取用者，保持一致，非持續性及高度可測試性。</span><span class="sxs-lookup"><span data-stu-id="cc224-513">In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="cc224-514">其他 EF4 功能，例如隱含的消極式載入可讓商務和應用程式的服務程式碼，而不必擔心關聯式資料存放區的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="cc224-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="cc224-515">最後，我們所建立的抽象概念進行模擬 （mock） 或假單元測試內，我們可以使用這些測試替身，以達到快速執行中、 高隔離和可靠的測試。</span><span class="sxs-lookup"><span data-stu-id="cc224-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="cc224-516">其他資源</span><span class="sxs-lookup"><span data-stu-id="cc224-516">Additional Resources</span></span>

-   <span data-ttu-id="cc224-517">Robert C.Martin，「[單一責任原則](http://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="cc224-517">Robert C. Martin, “ [The Single Responsibility Principle](http://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="cc224-518">Martin Fowler[模式目錄](http://www.martinfowler.com/eaaCatalog/index.html)從*的企業應用程式架構模式*</span><span class="sxs-lookup"><span data-stu-id="cc224-518">Martin Fowler, [Catalog of Patterns](http://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="cc224-519">Griffin Caprio 」[相依性插入](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="cc224-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="cc224-520">資料可程式性的部落格，「[逐步解說： 測試為導向的開發與 Entity Framework 4.0](http://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)"。</span><span class="sxs-lookup"><span data-stu-id="cc224-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](http://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)”.</span></span>
-   <span data-ttu-id="cc224-521">資料可程式性的部落格，「[使用的儲存機制和工作單位模式與 Entity Framework 4.0](http://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)"</span><span class="sxs-lookup"><span data-stu-id="cc224-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](http://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)”</span></span>
-   <span data-ttu-id="cc224-522">Dave Astels 」 [BDD 入門](http://blog.daveastels.com/files/BDD_Intro.pdf)"</span><span class="sxs-lookup"><span data-stu-id="cc224-522">Dave Astels, “ [BDD Intro](http://blog.daveastels.com/files/BDD_Intro.pdf)”</span></span>
-   <span data-ttu-id="cc224-523">Aaron Jensen 」[簡介機器規格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="cc224-523">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="cc224-524">Eric Lee 」 [BDD 使用 MSTest](http://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"</span><span class="sxs-lookup"><span data-stu-id="cc224-524">Eric Lee, “ [BDD with MSTest](http://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)”</span></span>
-   <span data-ttu-id="cc224-525">Eric Evans 」[網域導向設計](http://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="cc224-525">Eric Evans, “ [Domain Driven Design](http://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="cc224-526">Martin Fowler 」[模擬 （mock） 不是虛設常式](http://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="cc224-526">Martin Fowler, “ [Mocks Aren’t Stubs](http://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="cc224-527">Martin Fowler 」[測試雙精度浮點數](http://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="cc224-527">Martin Fowler, “ [Test Double](http://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   <span data-ttu-id="cc224-528">Jeremy Miller"[與互動的測試狀態](http://codebetter.com/blogs/jeremy.miller/articles/129544.aspx)"</span><span class="sxs-lookup"><span data-stu-id="cc224-528">Jeremy Miller, “ [State versus Interaction Testing](http://codebetter.com/blogs/jeremy.miller/articles/129544.aspx)”</span></span>
-   <span data-ttu-id="cc224-529">Moq \<http://code.google.com/p/moq/></span><span class="sxs-lookup"><span data-stu-id="cc224-529">Moq, \<http://code.google.com/p/moq/></span></span>

### <a name="biography"></a><span data-ttu-id="cc224-530">自傳</span><span class="sxs-lookup"><span data-stu-id="cc224-530">Biography</span></span>

<span data-ttu-id="cc224-531">技術人員，在 Pluralsight 的成員和 OdeToCode.com 的創辦人 Scott Allen。</span><span class="sxs-lookup"><span data-stu-id="cc224-531">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="cc224-532">在 15 歲商業軟體開發的詳細資訊，Scott 曾解決方案用於從 8 位元的內嵌裝置，到高度可調整的 ASP.NET web 應用程式的所有項目。</span><span class="sxs-lookup"><span data-stu-id="cc224-532">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="cc224-533">您可以在他的部落格 OdeToCode，或在 Twitter 上透過 Scott \< http://twitter.com/OdeToCode>。</span><span class="sxs-lookup"><span data-stu-id="cc224-533">You can reach Scott on his blog at OdeToCode, or on Twitter at \<http://twitter.com/OdeToCode>.</span></span>
