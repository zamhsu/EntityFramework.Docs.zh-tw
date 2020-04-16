---
title: 可測試性和實體框架 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434309"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="5670b-102">可測試性和實體框架 4.0</span><span class="sxs-lookup"><span data-stu-id="5670b-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="5670b-103">斯科特·艾倫</span><span class="sxs-lookup"><span data-stu-id="5670b-103">Scott Allen</span></span>

<span data-ttu-id="5670b-104">發佈日期：2010 年 5 月</span><span class="sxs-lookup"><span data-stu-id="5670b-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="5670b-105">簡介</span><span class="sxs-lookup"><span data-stu-id="5670b-105">Introduction</span></span>

<span data-ttu-id="5670b-106">本白皮書介紹並演示了如何使用 ADO.NET 實體框架 4.0 和 Visual Studio 2010 編寫可測試代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="5670b-107">本文不嘗試專注於特定的測試方法,如測試驅動設計 (TDD) 或行為驅動設計 (BDD)。</span><span class="sxs-lookup"><span data-stu-id="5670b-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="5670b-108">相反,本文將重點介紹如何使用ADO.NET實體框架編寫代碼,但仍然易於以自動化方式隔離和測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="5670b-109">我們將瞭解在數據訪問方案中簡化測試的常見設計模式,並瞭解如何在使用框架時應用這些模式。</span><span class="sxs-lookup"><span data-stu-id="5670b-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="5670b-110">我們還將查看框架的特定功能,瞭解這些功能如何在可測試代碼中工作。</span><span class="sxs-lookup"><span data-stu-id="5670b-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="5670b-111">什麼是可測試代碼?</span><span class="sxs-lookup"><span data-stu-id="5670b-111">What Is Testable Code?</span></span>

<span data-ttu-id="5670b-112">使用自動單元測試驗證軟體的能力提供了許多理想的好處。</span><span class="sxs-lookup"><span data-stu-id="5670b-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="5670b-113">每個人都知道,好的測試將減少應用程式中的軟體缺陷數量,並提高應用程式的品質,但單元測試的到位遠不止發現錯誤。</span><span class="sxs-lookup"><span data-stu-id="5670b-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="5670b-114">一個好的單元測試套件允許開發團隊節省時間並保持對所創建軟體的控制。</span><span class="sxs-lookup"><span data-stu-id="5670b-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="5670b-115">團隊可以對現有代碼進行更改、重構、重新設計和重組軟體以滿足新要求,並在知道測試套件可以驗證應用程式的行為的同時,將新元件添加到應用程式中。</span><span class="sxs-lookup"><span data-stu-id="5670b-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="5670b-116">單元測試是快速反饋週期的一部分,用於隨著複雜性的增加,促進更改並保留軟體的可維護性。</span><span class="sxs-lookup"><span data-stu-id="5670b-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="5670b-117">然而,單元測試是有代價的。</span><span class="sxs-lookup"><span data-stu-id="5670b-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="5670b-118">團隊必須投入時間創建和維護單元測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="5670b-119">創建這些測試所需的工作量與基礎軟體的**可測試性**直接相關。</span><span class="sxs-lookup"><span data-stu-id="5670b-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="5670b-120">軟體測試有多容易?</span><span class="sxs-lookup"><span data-stu-id="5670b-120">How easy is the software to test?</span></span> <span data-ttu-id="5670b-121">設計具有可測試性的軟體的團隊將比使用不可測試軟體的團隊更快地創建有效的測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="5670b-122">Microsoft 設計了ADO.NET實體框架 4.0 (EF4), 並考慮到可測試性。</span><span class="sxs-lookup"><span data-stu-id="5670b-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="5670b-123">這並不意味著開發人員將針對框架代碼本身編寫單元測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="5670b-124">相反,EF4 的可測試性目標便於創建基於框架的可測試代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="5670b-125">在查看特定示例之前,瞭解可測試代碼的品質是值得的。</span><span class="sxs-lookup"><span data-stu-id="5670b-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="5670b-126">可測試代碼的品質</span><span class="sxs-lookup"><span data-stu-id="5670b-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="5670b-127">易於測試的代碼始終表現出至少兩個特徵。</span><span class="sxs-lookup"><span data-stu-id="5670b-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="5670b-128">首先,可測試程式碼易於**觀察**。</span><span class="sxs-lookup"><span data-stu-id="5670b-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="5670b-129">給定一組輸入,應很容易觀察代碼的輸出。</span><span class="sxs-lookup"><span data-stu-id="5670b-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="5670b-130">例如,測試以下方法非常簡單,因為該方法直接返回計算結果。</span><span class="sxs-lookup"><span data-stu-id="5670b-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="5670b-131">如果方法將計算的值寫入網路套接字、資料庫表或檔(如以下代碼)中,則測試方法非常困難。</span><span class="sxs-lookup"><span data-stu-id="5670b-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="5670b-132">測試必須執行其他工作才能檢索該值。</span><span class="sxs-lookup"><span data-stu-id="5670b-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="5670b-133">其次,可測試代碼易於**隔離**。</span><span class="sxs-lookup"><span data-stu-id="5670b-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="5670b-134">讓我們使用以下偽代碼作為可測試代碼的壞示例。</span><span class="sxs-lookup"><span data-stu-id="5670b-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="5670b-135">該方法易於觀察 – 我們可以在保險單中傳遞並驗證返回價值與預期結果匹配。</span><span class="sxs-lookup"><span data-stu-id="5670b-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="5670b-136">但是,要測試該方法,我們需要使用正確的架構安裝資料庫,並配置 SMTP 伺服器,以防該方法嘗試發送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="5670b-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="5670b-137">單元測試只想驗證方法內的計算邏輯,但測試可能會失敗,因為電子郵件伺服器處於脫機狀態,或者因為資料庫伺服器移動。</span><span class="sxs-lookup"><span data-stu-id="5670b-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="5670b-138">這兩個失敗都與測試要驗證的行為無關。</span><span class="sxs-lookup"><span data-stu-id="5670b-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="5670b-139">該行為難以隔離。</span><span class="sxs-lookup"><span data-stu-id="5670b-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="5670b-140">努力編寫可測試代碼的軟體開發人員通常努力在編寫的代碼中保持關注點的分離。</span><span class="sxs-lookup"><span data-stu-id="5670b-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="5670b-141">上述方法應側重於業務計算,並將資料庫和電子郵件實現詳細資訊委派給其他元件。</span><span class="sxs-lookup"><span data-stu-id="5670b-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="5670b-142">羅伯特·馬丁稱之為單一責任原則。</span><span class="sxs-lookup"><span data-stu-id="5670b-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="5670b-143">對象應封裝單個窄責任,例如計算策略的值。</span><span class="sxs-lookup"><span data-stu-id="5670b-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="5670b-144">所有其他資料庫和通知工作應由其他對象負責。</span><span class="sxs-lookup"><span data-stu-id="5670b-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="5670b-145">以這種方式編寫的代碼更易於隔離,因為它側重於單個任務。</span><span class="sxs-lookup"><span data-stu-id="5670b-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="5670b-146">在 .NET 中,我們有遵循單一責任原則並實現隔離所需的抽象。</span><span class="sxs-lookup"><span data-stu-id="5670b-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="5670b-147">我們可以使用介面定義,並強制代碼使用介面抽象而不是具體類型。</span><span class="sxs-lookup"><span data-stu-id="5670b-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="5670b-148">在本文的後面部分,我們將瞭解類似上面介紹的壞示例這樣的方法如何使用*看起來*他們將與資料庫對話的介面。</span><span class="sxs-lookup"><span data-stu-id="5670b-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="5670b-149">但是,在測試時,我們可以替換不與資料庫對話但將數據存儲在記憶體中的虛擬實現。</span><span class="sxs-lookup"><span data-stu-id="5670b-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="5670b-150">此虛擬實現將隔離代碼與數據訪問代碼或資料庫配置中的不相關問題。</span><span class="sxs-lookup"><span data-stu-id="5670b-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="5670b-151">隔離還有其他好處。</span><span class="sxs-lookup"><span data-stu-id="5670b-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="5670b-152">最後一種方法中的業務計算只需幾毫秒即可執行,但測試本身可能會運行幾秒鐘,因為代碼在網路中跳躍並與各種伺服器進行對話。</span><span class="sxs-lookup"><span data-stu-id="5670b-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="5670b-153">單元測試應快速運行,以方便進行小更改。</span><span class="sxs-lookup"><span data-stu-id="5670b-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="5670b-154">單元測試也應是可重複的,而不是失敗的,因為與測試無關的元件有問題。</span><span class="sxs-lookup"><span data-stu-id="5670b-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="5670b-155">編寫易於觀察和隔離的代碼意味著開發人員將更容易地為代碼編寫測試,花更少的時間等待測試執行,更重要的是,花費更少的時間來跟蹤不存在的 Bug。</span><span class="sxs-lookup"><span data-stu-id="5670b-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="5670b-156">希望您能夠理解測試的好處,並瞭解可測試代碼所展示的品質。</span><span class="sxs-lookup"><span data-stu-id="5670b-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="5670b-157">我們將討論如何編寫與 EF4 配合使用的代碼,將數據保存到資料庫中,同時保持可觀察且易於隔離,但首先,我們將縮小重點,以討論數據訪問的可測試設計。</span><span class="sxs-lookup"><span data-stu-id="5670b-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="5670b-158">資料持久性設計模式</span><span class="sxs-lookup"><span data-stu-id="5670b-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="5670b-159">前面提出的兩個不好的例子都有太多的責任。</span><span class="sxs-lookup"><span data-stu-id="5670b-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="5670b-160">第一個壞示例必須執行計算*並*寫入檔。</span><span class="sxs-lookup"><span data-stu-id="5670b-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="5670b-161">第二個壞例子必須從資料庫中讀取數據 *,並*執行業務計算*併*發送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="5670b-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="5670b-162">通過設計更小的方法,將關注點分離並委派給其他元件,您將在編寫可測試代碼方面取得重大進展。</span><span class="sxs-lookup"><span data-stu-id="5670b-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="5670b-163">目標是通過從小型抽象和重點抽象中組合操作來構建功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="5670b-164">在數據持久性方面,我們正在尋找的小型且重點突出的抽象非常普遍,已記錄為設計模式。</span><span class="sxs-lookup"><span data-stu-id="5670b-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="5670b-165">Martin Fowler 的著作《企業應用程式體系結構模式》是描述這些模式的第一部著作。</span><span class="sxs-lookup"><span data-stu-id="5670b-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="5670b-166">我們將在以下各節中簡要介紹這些模式,然後介紹這些ADO.NET實體框架如何實現和使用這些模式。</span><span class="sxs-lookup"><span data-stu-id="5670b-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="5670b-167">存放庫模式</span><span class="sxs-lookup"><span data-stu-id="5670b-167">The Repository Pattern</span></span>

<span data-ttu-id="5670b-168">福勒說,一個存儲庫"使用類似集合的介面訪問域物件,在域和數據映射層之間進行仲介"。</span><span class="sxs-lookup"><span data-stu-id="5670b-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="5670b-169">存儲庫模式的目標是將代碼與數據訪問的細枝末節隔離開來,正如我們之前看到的,隔離是可測試性的必要特徵。</span><span class="sxs-lookup"><span data-stu-id="5670b-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="5670b-170">隔離的關鍵是存儲庫如何使用類似集合的介面公開物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="5670b-171">您編寫用於使用儲存庫的邏輯不知道儲存庫將如何實現請求的物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="5670b-172">存儲庫可能與資料庫對話,或者可能只是從記憶體中集合返回物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="5670b-173">所有代碼需要知道的是,存儲庫似乎維護集合,並且可以從集合中檢索、添加和刪除物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="5670b-174">在現有 .NET 應用程式中,具體存儲庫通常從通用介面繼承,如下所示:</span><span class="sxs-lookup"><span data-stu-id="5670b-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="5670b-175">在為 EF4 提供實現時,我們將對介面定義進行一些更改,但基本概念保持不變。</span><span class="sxs-lookup"><span data-stu-id="5670b-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="5670b-176">代碼可以使用實現此介面的具體存儲庫按其主鍵值檢索實體,基於謂詞的評估檢索實體集合,或者僅檢索所有可用實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="5670b-177">代碼還可以透過儲存庫介面添加和刪除實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="5670b-178">給定「員工」物件的 IRepository,代碼可以執行以下操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="5670b-179">由於代碼使用介面(員工 IRepository),我們可以為代碼提供介面的不同實現。</span><span class="sxs-lookup"><span data-stu-id="5670b-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="5670b-180">一個實現可能是由 EF4 支援的實現,並將物件持久化到 Microsoft SQL Server 資料庫中。</span><span class="sxs-lookup"><span data-stu-id="5670b-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="5670b-181">不同的實現(我們在測試期間使用的實現)可能由記憶體中的員工物件列表支援。</span><span class="sxs-lookup"><span data-stu-id="5670b-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="5670b-182">該介面將有助於在代碼中實現隔離。</span><span class="sxs-lookup"><span data-stu-id="5670b-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="5670b-183">請注意,IRepository&lt; &gt; T 介面不會公開"保存"操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="5670b-184">我們如何更新現有物件?</span><span class="sxs-lookup"><span data-stu-id="5670b-184">How do we update existing objects?</span></span> <span data-ttu-id="5670b-185">您可能會遇到包含 Save 操作的 IRepository 定義,這些儲存庫的實現需要立即將物件保存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="5670b-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="5670b-186">但是,在許多應用程式中,我們不想單獨保留物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="5670b-187">相反,我們希望將物件(可能來自不同的存儲庫)帶入生活,將這些物件修改為商務活動的一部分,然後將所有物件保留為單個原子操作的一部分。</span><span class="sxs-lookup"><span data-stu-id="5670b-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="5670b-188">幸運的是,有一個模式允許這種類型的行為。</span><span class="sxs-lookup"><span data-stu-id="5670b-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="5670b-189">工作單位模式</span><span class="sxs-lookup"><span data-stu-id="5670b-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="5670b-190">福勒說,一個工作單元將"維護受業務交易影響的物件清單,並協調更改的寫入和併發問題的解決」。。</span><span class="sxs-lookup"><span data-stu-id="5670b-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="5670b-191">工作單位有責任跟蹤我們從存儲庫中引入的物件的更改,並在告訴工作單位提交更改時保留我們對物件所做的任何更改。</span><span class="sxs-lookup"><span data-stu-id="5670b-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="5670b-192">工作單元也有責任將我們添加到所有存儲庫的新對象並插入資料庫,以及進行刪除。</span><span class="sxs-lookup"><span data-stu-id="5670b-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="5670b-193">如果您曾經做過ADO.NET數據集的任何工作,那麼你已經熟悉工作模式的單位。</span><span class="sxs-lookup"><span data-stu-id="5670b-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="5670b-194">ADO.NET DataSet 能夠追蹤 DataRow 物件的更新、刪除和插入,並且可以(在表適配器的説明下)協調我們對資料庫的所有更改。</span><span class="sxs-lookup"><span data-stu-id="5670b-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="5670b-195">但是,DataSet 物件對基礎資料庫的斷開連接子集建模。</span><span class="sxs-lookup"><span data-stu-id="5670b-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="5670b-196">工作模式單元表現出相同的行為,但適用於與數據訪問代碼隔離且不知道資料庫的業務物件和域物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="5670b-197">對 .NET 代碼中工作單元建模的抽象可能如下所示:</span><span class="sxs-lookup"><span data-stu-id="5670b-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="5670b-198">通過公開來自工作單元的存儲庫引用,我們可以確保單個工作單元的對象能夠跟蹤業務事務期間實現的所有實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="5670b-199">實際工作單元的 Commit 方法的實現是所有魔術都恰好在記憶體中更改與資料庫協調的地方。</span><span class="sxs-lookup"><span data-stu-id="5670b-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="5670b-200">給定 IUnitOfWork 引用,代碼可以更改從一個或多個儲存庫檢索的業務物件,並使用原子提交操作保存所有更改。</span><span class="sxs-lookup"><span data-stu-id="5670b-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="5670b-201">延遲載入模式</span><span class="sxs-lookup"><span data-stu-id="5670b-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="5670b-202">Fowler 使用名稱延遲負載來描述"一個不包含所需所有數據但知道如何獲取數據的物件"。</span><span class="sxs-lookup"><span data-stu-id="5670b-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="5670b-203">透明延遲載入是編寫可測試業務代碼和使用關係資料庫時需要具備的重要功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="5670b-204">例如,請考慮以下代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="5670b-205">如何填充時間卡集合?</span><span class="sxs-lookup"><span data-stu-id="5670b-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="5670b-206">有兩個可能的答案。</span><span class="sxs-lookup"><span data-stu-id="5670b-206">There are two possible answers.</span></span> <span data-ttu-id="5670b-207">一個答案是,當員工存儲庫被要求獲取員工時,會發出查詢以檢索員工以及員工關聯的時卡資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="5670b-208">在關係資料庫中,這通常需要使用 JOIN 子句的查詢,並且可能導致檢索比應用程式需要的資訊更多。</span><span class="sxs-lookup"><span data-stu-id="5670b-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="5670b-209">如果應用程式不需要觸摸 TimeCard 屬性,該怎麼辦?</span><span class="sxs-lookup"><span data-stu-id="5670b-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="5670b-210">第二個答案是"按需"載入 TimeCard 屬性。</span><span class="sxs-lookup"><span data-stu-id="5670b-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="5670b-211">此延遲載入對業務邏輯是隱式的和透明的,因為代碼不會調用特殊的 API 來檢索時卡資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="5670b-212">代碼假定時間卡資訊在需要時存在。</span><span class="sxs-lookup"><span data-stu-id="5670b-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="5670b-213">延遲載入涉及一些魔術,通常涉及方法調用的運行時攔截。</span><span class="sxs-lookup"><span data-stu-id="5670b-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="5670b-214">攔截代碼負責與資料庫交談和檢索時卡資訊,同時使業務邏輯自由成為業務邏輯。</span><span class="sxs-lookup"><span data-stu-id="5670b-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="5670b-215">這種惰性載入魔力允許業務代碼將自己與數據檢索操作隔離開來,並生成更可測試的代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="5670b-216">延遲載入的缺點是,當應用程式*確實需要*時卡資訊時,程式碼將執行其他查詢。</span><span class="sxs-lookup"><span data-stu-id="5670b-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="5670b-217">這不是許多應用程式的問題,但對於性能敏感的應用程式或應用程式迴圈訪問多個員工物件和執行查詢以在迴圈的每次反覆運算期間檢索時間卡(通常稱為 N+1 查詢問題),延遲載入是一種阻力。</span><span class="sxs-lookup"><span data-stu-id="5670b-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="5670b-218">在這些情況下,應用程式可能希望以最有效的方式緊急載入時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="5670b-219">幸運的是,我們將瞭解 EF4 如何支援隱式惰性負載和高效的熱要負載,同時進入下一節並實現這些模式。</span><span class="sxs-lookup"><span data-stu-id="5670b-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="5670b-220">使用實體框架實現模式</span><span class="sxs-lookup"><span data-stu-id="5670b-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="5670b-221">好消息是,我們在最後一節中介紹的所有設計模式都非常簡單地使用 EF4 實現。</span><span class="sxs-lookup"><span data-stu-id="5670b-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="5670b-222">為了展示我們將使用簡單的ASP.NET MVC 應用程式來編輯和顯示員工及其關聯的時卡資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="5670b-223">我們將首先使用以下「普通舊 CLR 物件」(POCO)。</span><span class="sxs-lookup"><span data-stu-id="5670b-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="5670b-224">當我們探索 EF4 的不同方法和功能時,這些類定義將略有變化,但目的是盡可能將這些類保留為持久性無知 (PI)。</span><span class="sxs-lookup"><span data-stu-id="5670b-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="5670b-225">PI*物件不知道如何,\*\*甚至即使它*保持的狀態生活在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="5670b-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="5670b-226">PI 和 POCO 與可測試軟體齊頭並進。</span><span class="sxs-lookup"><span data-stu-id="5670b-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="5670b-227">使用 POCO 方法的物件約束較小、更靈活且更易於測試,因為它們可以在沒有資料庫存在的情況下運行。</span><span class="sxs-lookup"><span data-stu-id="5670b-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="5670b-228">在 POCO 到位後,我們可以在可視化工作室中創建實體數據模型 (EDM),參見圖 1)。</span><span class="sxs-lookup"><span data-stu-id="5670b-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="5670b-229">我們不會使用 EDM 為我們的實體生成代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="5670b-230">相反,我們希望使用我們精心打造的實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="5670b-231">我們只會使用 EDM 生成資料庫架構並提供將物件映射到資料庫所需的元數據 EF4。</span><span class="sxs-lookup"><span data-stu-id="5670b-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![埃夫·test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="5670b-233">**圖 1**</span><span class="sxs-lookup"><span data-stu-id="5670b-233">**Figure 1**</span></span>

<span data-ttu-id="5670b-234">注意:如果要首先開發 EDM 模型,則可以從 EDM 生成乾淨的 POCO 代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="5670b-235">您可以使用資料可程式設計團隊提供的 Visual Studio 2010 擴展來執行此操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="5670b-236">要下載擴展,請從 Visual Studio 中的「工具」功能表啟動擴展管理器,並搜索「POCO」樣本的線上庫(見圖 2)。</span><span class="sxs-lookup"><span data-stu-id="5670b-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="5670b-237">有幾種 POCO 範本可用於 EF。</span><span class="sxs-lookup"><span data-stu-id="5670b-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="5670b-238">有關使用範本的詳細資訊,請參閱"[演練:實體框架的 POCO 範本](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)"。</span><span class="sxs-lookup"><span data-stu-id="5670b-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![埃夫test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="5670b-240">**圖 2**</span><span class="sxs-lookup"><span data-stu-id="5670b-240">**Figure 2**</span></span>

<span data-ttu-id="5670b-241">從這個 POCO 起點,我們將探討兩種不同的可測試代碼方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="5670b-242">我稱之為 EF 方法的第一種方法是因為它利用實體框架 API 中的抽象來實現工作單元和儲存庫。</span><span class="sxs-lookup"><span data-stu-id="5670b-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="5670b-243">在第二種方法中,我們將創建自己的自定義存儲庫抽象,然後瞭解每種方法的優缺點。</span><span class="sxs-lookup"><span data-stu-id="5670b-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="5670b-244">我們將首先探索 EF 方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="5670b-245">以 EF 為中心的實施</span><span class="sxs-lookup"><span data-stu-id="5670b-245">An EF Centric Implementation</span></span>

<span data-ttu-id="5670b-246">請考慮 ASP.NET MVC 專案中的以下控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="5670b-247">該操作檢索"員工"物件並返回結果以顯示員工的詳細視圖。</span><span class="sxs-lookup"><span data-stu-id="5670b-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="5670b-248">代碼是可測試的嗎?</span><span class="sxs-lookup"><span data-stu-id="5670b-248">Is the code testable?</span></span> <span data-ttu-id="5670b-249">我們至少需要測試兩個來驗證操作的行為。</span><span class="sxs-lookup"><span data-stu-id="5670b-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="5670b-250">首先,我們要驗證操作返回正確的視圖 - 一個簡單的測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="5670b-251">我們還希望編寫一個測試來驗證操作檢索正確的員工,並且我們希望在不執行代碼來查詢資料庫的情況下執行此操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="5670b-252">請記住,我們要隔離被測的代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="5670b-253">隔離將確保測試不會因為數據訪問代碼或資料庫配置中的 Bug 而失敗。</span><span class="sxs-lookup"><span data-stu-id="5670b-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="5670b-254">如果測試失敗,我們將知道控制器邏輯中存在 Bug,而不是某些較低級別的系統元件中的錯誤。</span><span class="sxs-lookup"><span data-stu-id="5670b-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="5670b-255">為了實現隔離,我們需要一些抽象,例如我們前面為存儲庫和工作單元提供的介面。</span><span class="sxs-lookup"><span data-stu-id="5670b-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="5670b-256">請記住,存儲庫模式旨在在域物件和數據映射層之間進行仲介。</span><span class="sxs-lookup"><span data-stu-id="5670b-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="5670b-257">在這種情況下,EF4*是*資料映射層,並且已經提供了一個名為 IObjectSet&lt;T(&gt;來自 System.Data.object 命名空間)的類似儲存庫的抽象。</span><span class="sxs-lookup"><span data-stu-id="5670b-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="5670b-258">介面定義如下所示。</span><span class="sxs-lookup"><span data-stu-id="5670b-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="5670b-259">IObjectSet&lt; &gt; T 滿足儲存庫的要求,因為它類似於物件集合&lt;&gt;(透過IE55t),並提供從類比集合中添加和刪除物件的方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="5670b-260">附加和分離方法公開 EF4 API 的其他功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="5670b-261">要使用&lt;IObjectSet&gt; T 作為儲存庫的介面,我們需要一個工作抽象單元來將儲存庫綁定在一起。</span><span class="sxs-lookup"><span data-stu-id="5670b-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="5670b-262">此介面的一個具體實現將與 SQL Server 對話,並且使用 EF4 中的 ObjectContext 類輕鬆創建。</span><span class="sxs-lookup"><span data-stu-id="5670b-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="5670b-263">ObjectContext 類別是 EF4 API 中的實際工作單元。</span><span class="sxs-lookup"><span data-stu-id="5670b-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="5670b-264">將 IObjectSet&lt; &gt; T 變為生命就像調用物件上下文物件的 CreateObjectSet 方法一樣簡單。</span><span class="sxs-lookup"><span data-stu-id="5670b-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="5670b-265">在幕後,架構將使用我們在 EDM 中提供的中繼資料來產生具體的 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="5670b-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5670b-266">我們將堅持返回&lt;IObjectSet&gt;T 介面,因為它將有助於在客戶端代碼中保留可測試性。</span><span class="sxs-lookup"><span data-stu-id="5670b-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="5670b-267">此具體實現在生產中非常有用,但我們需要關注如何使用IUnitOfWork抽象來簡化測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="5670b-268">測試雙精度值</span><span class="sxs-lookup"><span data-stu-id="5670b-268">The Test Doubles</span></span>

<span data-ttu-id="5670b-269">要隔離控制器操作,我們需要能夠在實際工作單元(由 ObjectContext 支援)和測試雙精度或「假」工作單元(執行記憶體中操作)之間切換。</span><span class="sxs-lookup"><span data-stu-id="5670b-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="5670b-270">執行此類切換的常見方法是不要讓 MVC 控制器實例化工作單元,而是將工作單元作為構造函數參數傳遞到控制器中。</span><span class="sxs-lookup"><span data-stu-id="5670b-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="5670b-271">上述代碼是依賴項注入的範例。</span><span class="sxs-lookup"><span data-stu-id="5670b-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="5670b-272">我們不允許控制器創建其依賴項(工作單位),但將依賴項注入控制器。</span><span class="sxs-lookup"><span data-stu-id="5670b-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="5670b-273">在 MVC 專案中,通常使用自訂控制器工廠與控制反轉 (IoC) 容器一起使用,以自動執行依賴項注入。</span><span class="sxs-lookup"><span data-stu-id="5670b-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="5670b-274">這些主題超出了本文的範圍,但您可以通過關注本文末尾的引用來閱讀更多內容。</span><span class="sxs-lookup"><span data-stu-id="5670b-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="5670b-275">我們可以用於測試的假工作實現單元可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="5670b-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="5670b-276">請注意,假工作單元會公開已提交的屬性。</span><span class="sxs-lookup"><span data-stu-id="5670b-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="5670b-277">有時將功能添加到便於測試的假類中非常有用。</span><span class="sxs-lookup"><span data-stu-id="5670b-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="5670b-278">在這種情況下,通過檢查"提交"屬性,很容易觀察代碼是否提交工作單元。</span><span class="sxs-lookup"><span data-stu-id="5670b-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="5670b-279">我們還需要一個假的 IObjectSet&lt; &gt; T 來將員工和 TimeCard 物件保留在記憶體中。</span><span class="sxs-lookup"><span data-stu-id="5670b-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="5670b-280">我們可以使用泛型提供單個實現。</span><span class="sxs-lookup"><span data-stu-id="5670b-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="5670b-281">此測試將其大部分工作雙重委託給基礎哈希集&lt;&gt;T 物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="5670b-282">請注意,IObjectSet&lt; &gt; T 需要一個泛型約束,強制 T 作為類別(引用類型),&lt;並且&gt;還強制我們 實現 IQuery T 。</span><span class="sxs-lookup"><span data-stu-id="5670b-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="5670b-283">使用標準 LINQ 運算子 AsQuery 使記憶體中集合&lt;&gt;顯示為 IQuery T 是很容易的。</span><span class="sxs-lookup"><span data-stu-id="5670b-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="5670b-284">測試</span><span class="sxs-lookup"><span data-stu-id="5670b-284">The Tests</span></span>

<span data-ttu-id="5670b-285">傳統的單元測試將使用單個測試類來保存單個 MVC 控制器中所有操作的所有測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="5670b-286">我們可以使用我們構建的記憶體中的假,編寫這些測試或任何類型的單元測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="5670b-287">但是,在本文中,我們將避免採用單片式測試類方法,而是將測試分組以專注於特定的功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="5670b-288">例如,"創建新員工"可能是我們想要測試的功能,因此我們將使用單個測試類來驗證負責創建新員工的單個控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="5670b-289">對於所有這些細粒度測試類,我們需要一些常見的設置代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="5670b-290">例如,我們總是需要創建記憶體中存儲庫和假工作單元。</span><span class="sxs-lookup"><span data-stu-id="5670b-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="5670b-291">我們還需要一個員工控制器的實例,並注入假的工作單元。</span><span class="sxs-lookup"><span data-stu-id="5670b-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="5670b-292">我們將使用基類跨測試類共用此公共設置代碼。</span><span class="sxs-lookup"><span data-stu-id="5670b-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="5670b-293">我們在基類中使用的「物件母體」是創建測試數據的一種常見模式。</span><span class="sxs-lookup"><span data-stu-id="5670b-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="5670b-294">物件母體包含用於實例化測試實體的工廠方法,用於跨多個測試夾具使用。</span><span class="sxs-lookup"><span data-stu-id="5670b-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="5670b-295">我們可以將員工控制器測試Base作為許多測試夾具的基礎類(見圖 3)。</span><span class="sxs-lookup"><span data-stu-id="5670b-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="5670b-296">每個測試夾具將測試特定的控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="5670b-297">例如,一個測試夾具將側重於測試 HTTP GET 請求期間使用的「創建」操作(以顯示創建員工的檢視),其他韌體將側重於 HTTP POST 請求中使用的「創建」操作(以獲取使用者提交的資訊以創建員工)。</span><span class="sxs-lookup"><span data-stu-id="5670b-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="5670b-298">每個派生類只負責其特定上下文中所需的設置,並提供驗證其特定測試上下文結果所需的斷言。</span><span class="sxs-lookup"><span data-stu-id="5670b-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![埃夫·test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="5670b-300">**圖 3**</span><span class="sxs-lookup"><span data-stu-id="5670b-300">**Figure 3**</span></span>

<span data-ttu-id="5670b-301">此處介紹的命名約定和測試樣式對於可測試代碼來說並不是必需的,這隻是一種方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="5670b-302">圖 4 顯示了 Visual Studio 2010 的 Jet Brains Resharper 測試運行外掛程式中運行的測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![埃夫test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="5670b-304">**圖 4**</span><span class="sxs-lookup"><span data-stu-id="5670b-304">**Figure 4**</span></span>

<span data-ttu-id="5670b-305">使用處理共用設置代碼的基類時,每個控制器操作的單位測試都很小且易於編寫。</span><span class="sxs-lookup"><span data-stu-id="5670b-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="5670b-306">測試將快速執行(因為我們正在執行記憶體中操作),並且不應因為不相關的基礎結構或環境問題而失敗(因為我們隔離了被測單元)。</span><span class="sxs-lookup"><span data-stu-id="5670b-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="5670b-307">在這些測試中,基類執行大多數設置工作。</span><span class="sxs-lookup"><span data-stu-id="5670b-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="5670b-308">請記住,基類構造函數創建記憶體中存儲庫、假工作單元和 EmployController 類的實例。</span><span class="sxs-lookup"><span data-stu-id="5670b-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="5670b-309">測試類派生自此基類,並側重於測試 Create 方法的具體細節。</span><span class="sxs-lookup"><span data-stu-id="5670b-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="5670b-310">在這種情況下,具體情況可歸結為您將在任何單元測試過程中看到的「安排、操作和斷言」步驟:</span><span class="sxs-lookup"><span data-stu-id="5670b-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="5670b-311">創建新的「員工」物件以模擬傳入數據。</span><span class="sxs-lookup"><span data-stu-id="5670b-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="5670b-312">調用"創建員工控制程式"的操作並傳遞給新員工。</span><span class="sxs-lookup"><span data-stu-id="5670b-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="5670b-313">驗證"創建"操作生成預期結果(員工顯示在存儲庫中)。</span><span class="sxs-lookup"><span data-stu-id="5670b-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="5670b-314">我們構建的內容允許我們測試任何員工控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="5670b-315">例如,當我們為"員工"控制器的 Index 操作編寫測試時,我們可以從測試基類繼承,為測試建立相同的基本設置。</span><span class="sxs-lookup"><span data-stu-id="5670b-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="5670b-316">再次,基類將創建記憶體中存儲庫、假工作單元和員工控制器的實例。</span><span class="sxs-lookup"><span data-stu-id="5670b-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="5670b-317">索引操作的測試只需要專注於調用 Index 操作並測試操作返回的模型的品質。</span><span class="sxs-lookup"><span data-stu-id="5670b-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="5670b-318">我們使用記憶體中假體建立測試測試測試軟體*的狀態*。</span><span class="sxs-lookup"><span data-stu-id="5670b-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="5670b-319">例如,在測試「創建」操作時,我們希望在創建操作執行後檢查存儲庫的狀態 - 儲存庫是否留住新員工?</span><span class="sxs-lookup"><span data-stu-id="5670b-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="5670b-320">稍後我們將介紹基於交互的測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="5670b-321">基於交互的測試將詢問被測代碼是否在我們的物件上調用了正確的方法並傳遞了正確的參數。</span><span class="sxs-lookup"><span data-stu-id="5670b-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="5670b-322">現在,我們將在封面上移動另一種設計模式 - 延遲負載。</span><span class="sxs-lookup"><span data-stu-id="5670b-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="5670b-323">熱裝和延遲載入</span><span class="sxs-lookup"><span data-stu-id="5670b-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="5670b-324">在 mVC Web 應用程式ASP.NET的某個時刻,我們可能希望顯示員工的資訊,並包括員工的相關時間卡。</span><span class="sxs-lookup"><span data-stu-id="5670b-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="5670b-325">例如,我們可能有一個時間卡摘要顯示,顯示員工的姓名和系統中的考勤卡總數。</span><span class="sxs-lookup"><span data-stu-id="5670b-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="5670b-326">我們可以採取幾種方法來實現此功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="5670b-327">投射</span><span class="sxs-lookup"><span data-stu-id="5670b-327">Projection</span></span>

<span data-ttu-id="5670b-328">創建摘要的一種簡單方法是構建一個模型,專門用於要在視圖中顯示的資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="5670b-329">在這種情況下,模型可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="5670b-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="5670b-330">請注意,員工摘要視圖模型不是實體 ,換句話說,它不是我們想要保留在資料庫中的內容。</span><span class="sxs-lookup"><span data-stu-id="5670b-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="5670b-331">我們只會使用此類以強類型方式將數據隨機排列到檢視中。</span><span class="sxs-lookup"><span data-stu-id="5670b-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="5670b-332">視圖模型類似於數據傳輸物件 (DTO),因為它不包含任何行為(沒有方法) - 僅包含屬性。</span><span class="sxs-lookup"><span data-stu-id="5670b-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="5670b-333">屬性將保存我們需要移動的數據。</span><span class="sxs-lookup"><span data-stu-id="5670b-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="5670b-334">使用 LINQ 的標準投影運算符 - Select 運算符,可以輕鬆實例化此檢視模型。</span><span class="sxs-lookup"><span data-stu-id="5670b-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="5670b-335">上述代碼有兩個值得注意的功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-335">There are two notable features to the above code.</span></span> <span data-ttu-id="5670b-336">首先 , 程式碼很容易測試, 因為它仍然易於觀察和隔離.</span><span class="sxs-lookup"><span data-stu-id="5670b-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="5670b-337">Select 運算符與針對實際工作單元的記憶體假一樣出色。</span><span class="sxs-lookup"><span data-stu-id="5670b-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="5670b-338">第二個值得注意的特徵是,代碼如何允許 EF4 生成單個高效的查詢,以組合員工和時卡資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="5670b-339">我們將員工資訊和工時卡資訊載入到同一物件中,而無需使用任何特殊的 API。</span><span class="sxs-lookup"><span data-stu-id="5670b-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="5670b-340">代碼僅表示它使用標準 LINQ 運算元對記憶體中數據源和遠端數據來源執行所需的資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="5670b-341">EF4 能夠將 LINQ\#查詢和 C 編譯器生成的運算式樹轉換為單個高效的 T-SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="5670b-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="5670b-342">有時,我們不想使用視圖模型或 DTO 物件,而是使用實際實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="5670b-343">當我們知道我們需要員工*和員工*的時間卡時,我們可以以不顯眼和高效的方式急切地載入相關數據。</span><span class="sxs-lookup"><span data-stu-id="5670b-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="5670b-344">明確渴望載入</span><span class="sxs-lookup"><span data-stu-id="5670b-344">Explicit Eager Loading</span></span>

<span data-ttu-id="5670b-345">當我們想要急切地載入相關的實體資訊時,我們需要一些業務邏輯機制(或在這種情況下,控制器操作邏輯)來表達對存儲庫的需求。</span><span class="sxs-lookup"><span data-stu-id="5670b-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="5670b-346">EF4 物件&lt;&gt;查詢 T 類定義一個 Include 方法,以指定在查詢期間要檢索的相關物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="5670b-347">請記住 EF4 物件上下&lt;文通過&gt;&lt;從&gt;物件查詢 T 繼承的具體 ObjectSet T 類公開實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="5670b-348">如果我們在控制器操作中使用&lt;ObjectSet&gt;T引用,我們可以編寫以下代碼來為每個員工指定熱切的時卡資訊負載。</span><span class="sxs-lookup"><span data-stu-id="5670b-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="5670b-349">但是,由於我們試圖保持代碼的可測試性,因此我們不會在工作類的實際單元&lt;&gt;之外 公開ObjectSet T。</span><span class="sxs-lookup"><span data-stu-id="5670b-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="5670b-350">相反,我們&lt;依賴於 IObjectSet&gt;T 介面,該介面更易於偽造,但 IObjectSet&lt;T&gt;不定義 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="5670b-351">LINQ 的優點是我們可以創建自己的"包括"運算符。</span><span class="sxs-lookup"><span data-stu-id="5670b-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="5670b-352">請注意,此包含運算元定義&lt;為 I 可查詢&gt;T 而不是 IObjectSet&lt;T&gt;的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5670b-353">這&lt;使我們能夠將該方法用於更廣泛的可能類型,包括 IQuery T、IObjectSet&gt;&lt;T、ObjectQuery&gt;&lt;T&gt;和&lt;&gt;Objectset T 。</span><span class="sxs-lookup"><span data-stu-id="5670b-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5670b-354">如果基礎序列不是真正的 EF4&lt;物件查詢&gt;T, 則不會造成任何傷害,並且 Include 運算符是 no-op。</span><span class="sxs-lookup"><span data-stu-id="5670b-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="5670b-355">如果基礎序列*是*&lt;ObjectQuery&gt;T( 或從 ObjectQuery&lt;&gt;T 派生),則 EF4 將看到我們對其他資料的要求,並制定正確的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="5670b-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="5670b-356">有了這個新的運算符,我們可以顯式要求從存儲庫中載入時間卡資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="5670b-357">當對實際物件上下文運行時,代碼將生成以下單個查詢。</span><span class="sxs-lookup"><span data-stu-id="5670b-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="5670b-358">查詢在一次行程中從資料庫中收集足夠的資訊,以實現員工物件並完全填充其 TimeCard 屬性。</span><span class="sxs-lookup"><span data-stu-id="5670b-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="5670b-359">好消息是操作方法內的代碼仍然完全可測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="5670b-360">我們不需要為我們的假貨提供任何其他功能來支援 Include 運算符。</span><span class="sxs-lookup"><span data-stu-id="5670b-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="5670b-361">壞消息是,我們必須在代碼內使用 Include 運算符,以保持持久性無知。</span><span class="sxs-lookup"><span data-stu-id="5670b-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="5670b-362">這是構建可測試代碼時需要評估的權衡類型的範例。</span><span class="sxs-lookup"><span data-stu-id="5670b-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="5670b-363">有時,您需要讓持久性問題洩漏到存儲庫抽象之外,以滿足性能目標。</span><span class="sxs-lookup"><span data-stu-id="5670b-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="5670b-364">熱裝的替代是延遲載入。</span><span class="sxs-lookup"><span data-stu-id="5670b-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="5670b-365">延遲載入意味著我們不需要業務*not*代碼來顯式聲明關聯數據的要求。</span><span class="sxs-lookup"><span data-stu-id="5670b-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="5670b-366">相反,我們在應用程式中使用我們的實體,如果需要其他數據,實體框架將按需載入數據。</span><span class="sxs-lookup"><span data-stu-id="5670b-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="5670b-367">消極式載入</span><span class="sxs-lookup"><span data-stu-id="5670b-367">Lazy Loading</span></span>

<span data-ttu-id="5670b-368">很容易想像一個場景,即我們不知道業務邏輯需要哪些數據。</span><span class="sxs-lookup"><span data-stu-id="5670b-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="5670b-369">我們可能知道邏輯需要員工物件,但我們可能分支到不同的執行路徑,其中某些路徑需要員工提供時卡資訊,而有些路徑不需要。</span><span class="sxs-lookup"><span data-stu-id="5670b-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="5670b-370">像這樣的方案非常適合隱式延遲載入,因為數據會根據需要神奇地顯示。</span><span class="sxs-lookup"><span data-stu-id="5670b-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="5670b-371">延遲載入(也稱為延遲載入)確實對我們的實體物件提出了一些要求。</span><span class="sxs-lookup"><span data-stu-id="5670b-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="5670b-372">具有真正持久性無知的 POCO 不會面臨來自持久性層的任何要求,但真正的持久性無知幾乎不可能實現。</span><span class="sxs-lookup"><span data-stu-id="5670b-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="5670b-373">相反,我們測量相對程度的持久性無知。</span><span class="sxs-lookup"><span data-stu-id="5670b-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="5670b-374">如果我們需要從面向持久性的基類繼承或使用專用集合在 POCO 中實現延遲載入,那將是不幸的。</span><span class="sxs-lookup"><span data-stu-id="5670b-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="5670b-375">幸運的是,EF4 具有侵入性較低的解決方案。</span><span class="sxs-lookup"><span data-stu-id="5670b-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="5670b-376">幾乎偵測不到</span><span class="sxs-lookup"><span data-stu-id="5670b-376">Virtually Undetectable</span></span>

<span data-ttu-id="5670b-377">使用 POCO 物件時,EF4 可以動態生成實體的運行時代理。</span><span class="sxs-lookup"><span data-stu-id="5670b-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="5670b-378">這些代理無形地包裝了具體化的 POCO,並通過攔截每個屬性獲取和設置操作來執行其他工作來提供其他服務。</span><span class="sxs-lookup"><span data-stu-id="5670b-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="5670b-379">其中一項服務是我們要查找的延遲載入功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="5670b-380">另一個服務是一種有效的更改跟蹤機制,它可以記錄程式何時更改實體的屬性值。</span><span class="sxs-lookup"><span data-stu-id="5670b-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="5670b-381">儲存變更方法期間,ObjectContext 使用更改清單來保留使用 UPDATE 命令的任何已修改實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="5670b-382">但是,要使這些代理正常工作,他們需要一種方法來挂鉤到實體上獲取和設置操作,而代理通過重寫虛擬成員來實現此目標。</span><span class="sxs-lookup"><span data-stu-id="5670b-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="5670b-383">因此,如果我們想要隱式延遲載入和高效的更改跟蹤,我們需要回到 POCO 類定義並將屬性標記為虛擬。</span><span class="sxs-lookup"><span data-stu-id="5670b-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="5670b-384">我們仍然可以說,員工實體大多是堅持無知。</span><span class="sxs-lookup"><span data-stu-id="5670b-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="5670b-385">唯一的要求是使用虛擬成員,這不會影響代碼的可測試性。</span><span class="sxs-lookup"><span data-stu-id="5670b-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="5670b-386">我們不需要從任何特殊的基類派生,甚至不需要使用專用於延遲載入的特殊集合。</span><span class="sxs-lookup"><span data-stu-id="5670b-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="5670b-387">如代碼所示,任何實現&lt;ICollection&gt;T 的類都可以保留相關實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="5670b-388">在我們的工作單元內,我們還需要進行一些小的變化。</span><span class="sxs-lookup"><span data-stu-id="5670b-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="5670b-389">預設情況下,當直接使用 ObjectContext 物件時,延遲載入*處於關閉狀態*。</span><span class="sxs-lookup"><span data-stu-id="5670b-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="5670b-390">我們可以在 ContextOptions 屬性上設置一個屬性,以啟用延遲載入,如果我們想要在任何地方啟用延遲載入,我們可以在實際工作單元中設置此屬性。</span><span class="sxs-lookup"><span data-stu-id="5670b-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="5670b-391">啟用隱式延遲載入後,應用程式代碼可以使用員工和員工關聯的時間卡,同時對 EF 載入額外資料所需的工作一無所知。</span><span class="sxs-lookup"><span data-stu-id="5670b-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="5670b-392">延遲載入使應用程式代碼更易於編寫,並且使用代理魔法,程式碼保持完全可測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="5670b-393">工作單元的記憶體中偽造可以簡單地在測試期間在需要時預載入帶有相關數據的假實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="5670b-394">此時,我們將從使用&lt;IObjectSet&gt;T 構建儲存庫轉向我們的注意力,並查看抽象來隱藏持久性框架的所有跡象。</span><span class="sxs-lookup"><span data-stu-id="5670b-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="5670b-395">自訂儲存函式庫</span><span class="sxs-lookup"><span data-stu-id="5670b-395">Custom Repositories</span></span>

<span data-ttu-id="5670b-396">當我們在本文中首次介紹工作設計單元模式時,我們提供了一些示例代碼,說明工作單元的外觀。</span><span class="sxs-lookup"><span data-stu-id="5670b-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="5670b-397">讓我們使用我們一直在處理的員工和員工時卡方案重新呈現這一原始想法。</span><span class="sxs-lookup"><span data-stu-id="5670b-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="5670b-398">此工作單元與我們在上一節中創建的工作單元之間的主要區別是此工作單元不使用 EF4 框架中的任何抽象(沒有&lt;IObjectSet&gt; T)。</span><span class="sxs-lookup"><span data-stu-id="5670b-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="5670b-399">IObjectSet&lt; &gt; T 作為儲存庫介面工作良好,但它公開的 API 可能不能完全與我們的應用程式需求一致。</span><span class="sxs-lookup"><span data-stu-id="5670b-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="5670b-400">在即將採用的方法中,我們將使用自定義&lt;IRepository&gt;T 抽象表示儲存庫。</span><span class="sxs-lookup"><span data-stu-id="5670b-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="5670b-401">許多遵循測試驅動設計、行為驅動設計和域驅動方法設計的開發人員都更喜歡&lt;IRepository&gt;T 方法,原因有多種。</span><span class="sxs-lookup"><span data-stu-id="5670b-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="5670b-402">首先,IRepository&lt;&gt;T 介面表示"反腐敗"層。</span><span class="sxs-lookup"><span data-stu-id="5670b-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="5670b-403">正如 Eric Evans 在他的域驅動設計書中描述的那樣,反腐敗層使域代碼遠離基礎結構 API,如持久性 API。</span><span class="sxs-lookup"><span data-stu-id="5670b-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="5670b-404">其次,開發人員可以將方法構建到存儲庫中,以滿足應用程序的確切需求(如編寫測試時發現的那樣)。</span><span class="sxs-lookup"><span data-stu-id="5670b-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="5670b-405">例如,我們可能需要使用 ID 值查找單個實體,以便我們可以將 FindById 方法添加到儲存庫介面。</span><span class="sxs-lookup"><span data-stu-id="5670b-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="5670b-406">我們的&lt;IRepository&gt; T 定義如下所示。</span><span class="sxs-lookup"><span data-stu-id="5670b-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="5670b-407">請注意,我們將回到使用&lt;IQuery&gt;T 介面來公開實體集合。</span><span class="sxs-lookup"><span data-stu-id="5670b-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="5670b-408">IQueryT&lt;&gt;允許 LINQ 運算式樹流入 EF4 提供程式,並賦予提供程式查詢的整體檢視。</span><span class="sxs-lookup"><span data-stu-id="5670b-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="5670b-409">第二個選項是返回 IE55t&lt;&gt;T ,這意味著 EF4 LINQ 提供程式將只看到儲存庫內生成的運算式。</span><span class="sxs-lookup"><span data-stu-id="5670b-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="5670b-410">在存儲庫之外完成的任何分組、排序和投影將不會組成發送到資料庫的 SQL 命令,這可能會損害性能。</span><span class="sxs-lookup"><span data-stu-id="5670b-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="5670b-411">另一方面,僅返回 IE550t&lt;&gt;結果的儲存庫永遠不會讓您使用新的 SQL 命令感到驚訝。</span><span class="sxs-lookup"><span data-stu-id="5670b-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="5670b-412">這兩種方法都工作,並且兩種方法都是可測試的。</span><span class="sxs-lookup"><span data-stu-id="5670b-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="5670b-413">使用泛型和 EF4 ObjectContext&lt;&gt;API 提供 IRepository T 介面的單個實現非常簡單。</span><span class="sxs-lookup"><span data-stu-id="5670b-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="5670b-414">IRepository&lt;&gt;T 方法為我們提供了對查詢的一些附加控制,因為客戶端必須調用方法才能訪問實體。</span><span class="sxs-lookup"><span data-stu-id="5670b-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="5670b-415">在該方法內,我們可以提供額外的檢查和LINQ運算符來強制執行應用程式約束。</span><span class="sxs-lookup"><span data-stu-id="5670b-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="5670b-416">請注意,介面對泛型類型參數有兩個約束。</span><span class="sxs-lookup"><span data-stu-id="5670b-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="5670b-417">第一個約束是&lt;ObjectSet&gt;T 所需的類缺點,第二個約束強制我們的實體實現 IEntity - 為應用程式創建的抽象。</span><span class="sxs-lookup"><span data-stu-id="5670b-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="5670b-418">IEntity 介面強制實體具有可讀 Id 屬性,然後我們可以在 FindById 方法中使用此屬性。</span><span class="sxs-lookup"><span data-stu-id="5670b-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="5670b-419">IEntity 使用以下代碼定義。</span><span class="sxs-lookup"><span data-stu-id="5670b-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="5670b-420">IEntity 可被視為對持久性無知的輕微違反,因為我們的實體需要實現此介面。</span><span class="sxs-lookup"><span data-stu-id="5670b-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="5670b-421">請記住,持久性無知是關於權衡的,對於許多 FindById 功能來說,它將超過介面施加的約束。</span><span class="sxs-lookup"><span data-stu-id="5670b-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="5670b-422">該介面對可測試性沒有影響。</span><span class="sxs-lookup"><span data-stu-id="5670b-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="5670b-423">實例化即時&lt;IRepository&gt;T 需要 EF4 物件上下文,因此具體的工作實現單元應管理實例化。</span><span class="sxs-lookup"><span data-stu-id="5670b-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="5670b-424">使用自訂儲存函式庫</span><span class="sxs-lookup"><span data-stu-id="5670b-424">Using the Custom Repository</span></span>

<span data-ttu-id="5670b-425">使用我們的自訂儲存庫與使用基於&lt;IObjectSet&gt;T 的儲存庫沒有顯著差異。</span><span class="sxs-lookup"><span data-stu-id="5670b-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5670b-426">我們首先需要調用儲存庫的方法來獲取 IQuery&lt;&gt;T 引用,而不是將 LINQ 運算符直接應用於屬性。</span><span class="sxs-lookup"><span data-stu-id="5670b-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="5670b-427">請注意,我們以前實現的自定義"包括"運算符將工作而不進行更改。</span><span class="sxs-lookup"><span data-stu-id="5670b-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="5670b-428">存儲庫的 FindById 方法從嘗試檢索單個實體的操作中刪除重複的邏輯。</span><span class="sxs-lookup"><span data-stu-id="5670b-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="5670b-429">我們研究過的兩種方法的可測試性沒有顯著差異。</span><span class="sxs-lookup"><span data-stu-id="5670b-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="5670b-430">我們可以通過&lt;構建由 HashSet&gt;&lt;員工&gt;支援的混凝土類來提供 IRepository T 的虛假實現-就像我們在上一節中所做的那樣。</span><span class="sxs-lookup"><span data-stu-id="5670b-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="5670b-431">但是,一些開發人員更喜歡使用類比物件和類比物件框架,而不是構建假物。</span><span class="sxs-lookup"><span data-stu-id="5670b-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="5670b-432">我們將在下一節中介紹使用模擬來測試我們的實現,並討論類比和假之間的差異。</span><span class="sxs-lookup"><span data-stu-id="5670b-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="5670b-433">使用模擬測試</span><span class="sxs-lookup"><span data-stu-id="5670b-433">Testing with Mocks</span></span>

<span data-ttu-id="5670b-434">構建馬丁·福勒所謂的"雙測試雙"的方法不同。</span><span class="sxs-lookup"><span data-stu-id="5670b-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="5670b-435">測試雙精度(如電影特技雙)是您在測試期間為實際生產物件構建為「站立」的物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="5670b-436">我們創建的記憶體記憶體記憶體庫是與 SQL Server 對話的儲存庫的測試雙精度值。</span><span class="sxs-lookup"><span data-stu-id="5670b-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="5670b-437">我們已經看到如何在單元測試期間使用這些測試雙精度值來隔離代碼並保持測試快速運行。</span><span class="sxs-lookup"><span data-stu-id="5670b-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="5670b-438">我們構建的測試雙精度值具有實際的工作實現。</span><span class="sxs-lookup"><span data-stu-id="5670b-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="5670b-439">在後台,每個物件都存儲一個具體的物件集合,並且在測試期間操作存儲庫時,它們將從此集合中添加和刪除物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="5670b-440">一些開發人員喜歡用實際代碼和工作實現來構建測試雙精度值。</span><span class="sxs-lookup"><span data-stu-id="5670b-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="5670b-441">這些測試雙號是我們稱之為*假貨*的。</span><span class="sxs-lookup"><span data-stu-id="5670b-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="5670b-442">它們具有工作實現,但它們不夠真實,可用於生產。</span><span class="sxs-lookup"><span data-stu-id="5670b-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="5670b-443">假存儲庫實際上不會寫入資料庫。</span><span class="sxs-lookup"><span data-stu-id="5670b-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="5670b-444">假 SMTP 伺服器實際上不會透過網路發送電子郵件。</span><span class="sxs-lookup"><span data-stu-id="5670b-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="5670b-445">類比與假像</span><span class="sxs-lookup"><span data-stu-id="5670b-445">Mocks versus Fakes</span></span>

<span data-ttu-id="5670b-446">還有另一種類型的測試雙稱為*模擬*。</span><span class="sxs-lookup"><span data-stu-id="5670b-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="5670b-447">雖然假有工作實現,但模擬沒有實現。</span><span class="sxs-lookup"><span data-stu-id="5670b-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="5670b-448">在類比物件框架的説明下,我們在運行時構造這些類比物件,並將其用作測試雙精度值。</span><span class="sxs-lookup"><span data-stu-id="5670b-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="5670b-449">在本節中,我們將使用開源類比框架 Moq。</span><span class="sxs-lookup"><span data-stu-id="5670b-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="5670b-450">下面是使用 Moq 動態為員工存儲庫創建測試雙精度值的簡單示例。</span><span class="sxs-lookup"><span data-stu-id="5670b-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="5670b-451">我們要求 Moq&lt;提供 IRepository 員工&gt;實現,並動態構建一個。</span><span class="sxs-lookup"><span data-stu-id="5670b-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="5670b-452">通過&lt;存取&gt;&lt;&gt;Mock T 物件的 Object 屬性,我們可以造訪實現 IRepository 員工的物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="5670b-453">它是這個內部對象,我們可以傳遞到我們的控制器,他們不知道這是一個測試雙或真正的存儲庫。</span><span class="sxs-lookup"><span data-stu-id="5670b-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="5670b-454">我們可以調用對象上的方法,就像在具有實際實現的對象上調用方法一樣。</span><span class="sxs-lookup"><span data-stu-id="5670b-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="5670b-455">您必須想知道類比儲存庫在調用 Add 方法時將執行什麼操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="5670b-456">由於類比物件後面沒有實現,因此 Add 不執行任何操作。</span><span class="sxs-lookup"><span data-stu-id="5670b-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="5670b-457">幕後沒有像我們寫的假貨那樣的具體收藏,所以員工被丟棄了。</span><span class="sxs-lookup"><span data-stu-id="5670b-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="5670b-458">FindById 的返回值如何?</span><span class="sxs-lookup"><span data-stu-id="5670b-458">What about the return value of FindById?</span></span> <span data-ttu-id="5670b-459">在這種情況下,模擬物件執行它唯一能做的,即返回預設值。</span><span class="sxs-lookup"><span data-stu-id="5670b-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="5670b-460">由於我們要返回引用類型(員工),因此返回值為null值。</span><span class="sxs-lookup"><span data-stu-id="5670b-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="5670b-461">類比聽起來可能毫無價值;然而,還有兩個類比功能,我們還沒有談到。</span><span class="sxs-lookup"><span data-stu-id="5670b-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="5670b-462">首先,Moq 框架記錄對模擬對象進行的所有調用。</span><span class="sxs-lookup"><span data-stu-id="5670b-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="5670b-463">在代碼的後面部分,我們可以問 Moq 是否有人調用了 Add 方法,或者是否有人調用了 FindById 方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="5670b-464">稍後我們將瞭解如何在測試中使用此"黑匣子"錄製功能。</span><span class="sxs-lookup"><span data-stu-id="5670b-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="5670b-465">第二個偉大的功能是我們如何使用 Moq 編程一個模擬物件*與期望*。</span><span class="sxs-lookup"><span data-stu-id="5670b-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="5670b-466">期望值告訴類比物件如何回應任何給定的交互。</span><span class="sxs-lookup"><span data-stu-id="5670b-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="5670b-467">例如,我們可以將期望值程式設計到類比中,並告訴它當有人調用 FindById 時返回員工物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="5670b-468">Moq 框架使用安裝程式 API 和 lambda 運算式來對這些期望進行程式設計。</span><span class="sxs-lookup"><span data-stu-id="5670b-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="5670b-469">在此示例中,我們要求 Moq 動態構建存儲庫,然後我們對儲存庫進行程式設計。</span><span class="sxs-lookup"><span data-stu-id="5670b-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="5670b-470">當某人調用傳遞值為 5 的 FindById 方法時,期望告訴模擬物件返回 Id 值為 5 的新員工物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="5670b-471">此測試通過,我們不需要建構一個完整的實作假 IRepository&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="5670b-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="5670b-472">讓我們重溫我們之前編寫的測試,並重新編寫它們以使用類比而不是假測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="5670b-473">與以前一樣,我們將使用基類來設置所有控制器測試所需的通用基礎結構部分。</span><span class="sxs-lookup"><span data-stu-id="5670b-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="5670b-474">設置代碼大致相同。</span><span class="sxs-lookup"><span data-stu-id="5670b-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="5670b-475">我們將使用 Moq 來建構模擬物件,而不是使用假物。</span><span class="sxs-lookup"><span data-stu-id="5670b-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="5670b-476">基類安排類比工作單元在代碼調用"員工"屬性時返回類比存儲庫。</span><span class="sxs-lookup"><span data-stu-id="5670b-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="5670b-477">類比設置的其餘部分將發生在專用於每個特定場景的測試夾具內。</span><span class="sxs-lookup"><span data-stu-id="5670b-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="5670b-478">例如,當操作調用類比儲存庫的 FindAll 方法時,Index 操作的測試韌體將設置類比儲存庫以返回員工清單。</span><span class="sxs-lookup"><span data-stu-id="5670b-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="5670b-479">除了期望值之外,我們的測試看起來與之前的測試相似。</span><span class="sxs-lookup"><span data-stu-id="5670b-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="5670b-480">然而,隨著類比框架的錄製能力,我們可以從不同的角度進行測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="5670b-481">我們將在下一節中介紹這一新觀點。</span><span class="sxs-lookup"><span data-stu-id="5670b-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="5670b-482">狀態與互動測試</span><span class="sxs-lookup"><span data-stu-id="5670b-482">State versus Interaction Testing</span></span>

<span data-ttu-id="5670b-483">您可以使用不同的技術來測試使用類比物件的軟體。</span><span class="sxs-lookup"><span data-stu-id="5670b-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="5670b-484">一種方法是使用基於狀態的測試,這就是我們在本文中到目前為止所做的。</span><span class="sxs-lookup"><span data-stu-id="5670b-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="5670b-485">基於狀態的測試對軟體的狀態進行斷言。</span><span class="sxs-lookup"><span data-stu-id="5670b-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="5670b-486">在上一次測試中,我們在控制器上調用了一個操作方法,並斷言它應該構建的模型。</span><span class="sxs-lookup"><span data-stu-id="5670b-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="5670b-487">下面是測試狀態的其他一些範例:</span><span class="sxs-lookup"><span data-stu-id="5670b-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="5670b-488">在執行"創建"後驗證存儲庫包含新員工物件。</span><span class="sxs-lookup"><span data-stu-id="5670b-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="5670b-489">在索引執行後驗證模型包含所有員工的清單。</span><span class="sxs-lookup"><span data-stu-id="5670b-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="5670b-490">在執行"刪除"後,驗證存儲庫不包含給定員工。</span><span class="sxs-lookup"><span data-stu-id="5670b-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="5670b-491">使用模擬物件會看到的另一種方法是驗證*互動*。</span><span class="sxs-lookup"><span data-stu-id="5670b-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="5670b-492">雖然基於狀態的測試對物件的狀態進行斷言,但基於交互的測試會斷言物件如何交互。</span><span class="sxs-lookup"><span data-stu-id="5670b-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="5670b-493">例如：</span><span class="sxs-lookup"><span data-stu-id="5670b-493">For example:</span></span>

-   <span data-ttu-id="5670b-494">在執行"創建"時驗證控制器呼叫儲存庫的 Add 方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="5670b-495">當索引執行時,驗證控制器調用存儲庫的 FindAll 方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="5670b-496">驗證控制器調用工作單元的提交方法,以在"編輯"執行時保存更改。</span><span class="sxs-lookup"><span data-stu-id="5670b-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="5670b-497">交互測試通常需要較少的測試數據,因為我們不是在集合內部進行戳造和驗證計數。</span><span class="sxs-lookup"><span data-stu-id="5670b-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="5670b-498">例如,如果我們知道「詳細資訊」操作調用具有正確值的存儲庫的 FindById 方法,則操作可能運行正常。</span><span class="sxs-lookup"><span data-stu-id="5670b-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="5670b-499">我們可以驗證此行為,而無需設置任何測試數據從 FindById 返回。</span><span class="sxs-lookup"><span data-stu-id="5670b-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="5670b-500">上述測試夾具中唯一需要的設置是基類提供的設置。</span><span class="sxs-lookup"><span data-stu-id="5670b-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="5670b-501">當我們調用控制器操作時,Moq 將記錄與類比儲存庫的交互。</span><span class="sxs-lookup"><span data-stu-id="5670b-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="5670b-502">使用 Moq 的驗證 API,我們可以詢問 Moq 控制器是否使用正確的 ID 值呼叫 FindById。</span><span class="sxs-lookup"><span data-stu-id="5670b-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="5670b-503">如果控制器未調用方法,或者使用意外參數值調用該方法,則 Verify 方法將引發異常,並且測試將失敗。</span><span class="sxs-lookup"><span data-stu-id="5670b-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="5670b-504">下面是另一個範例,用於驗證"創建操作在當前工作單元上調用提交"</span><span class="sxs-lookup"><span data-stu-id="5670b-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="5670b-505">交互測試的一個危險是過度指定交互的傾向。</span><span class="sxs-lookup"><span data-stu-id="5670b-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="5670b-506">類比物件記錄和驗證與模擬物件的每次交互的能力並不意味著測試應嘗試驗證每個交互。</span><span class="sxs-lookup"><span data-stu-id="5670b-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="5670b-507">某些互動是實現詳細資訊,您只應驗證滿足當前測試*所需的*交互。</span><span class="sxs-lookup"><span data-stu-id="5670b-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="5670b-508">在類比或假貨之間進行選擇很大程度上取決於您測試的系統以及您的個人(或團隊)偏好。</span><span class="sxs-lookup"><span data-stu-id="5670b-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="5670b-509">Mock 物件可以大大減少實現測試雙精度值所需的代碼量,但並不是每個人都會舒適地實現程式設計期望和驗證交互。</span><span class="sxs-lookup"><span data-stu-id="5670b-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="5670b-510">結論</span><span class="sxs-lookup"><span data-stu-id="5670b-510">Conclusions</span></span>

<span data-ttu-id="5670b-511">在本文中,我們演示了幾種在使用ADO.NET實體框架進行數據持久性時創建可測試代碼的方法。</span><span class="sxs-lookup"><span data-stu-id="5670b-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="5670b-512">我們可以利用內置的抽象&lt;,如IObjectSet&gt;T, 或者創建我們自己的抽象,如IRepositoryT。&gt; &lt;</span><span class="sxs-lookup"><span data-stu-id="5670b-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="5670b-513">在這兩種情況下,ADO.NET實體框架 4.0 中的 POCO 支援允許這些抽象的消費者保持持久無知和高度可測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="5670b-514">隱式延遲載入等其他 EF4 功能允許業務和應用程式服務代碼工作,而不必擔心關係數據存儲的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="5670b-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="5670b-515">最後,我們創建的抽象在單元測試中很容易類比或偽造,我們可以使用這些測試雙精度值來實現快速運行、高度隔離和可靠的測試。</span><span class="sxs-lookup"><span data-stu-id="5670b-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="5670b-516">其他資源</span><span class="sxs-lookup"><span data-stu-id="5670b-516">Additional Resources</span></span>

-   <span data-ttu-id="5670b-517">羅伯特·馬丁,"[單一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="5670b-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="5670b-518">馬丁·福勒[Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html),*企業應用程序體系結構模式模式目錄*</span><span class="sxs-lookup"><span data-stu-id="5670b-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="5670b-519">格裡芬·卡普里奧,"[依賴注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="5670b-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="5670b-520">數據可程式設計性部落格,演[練:使用實體框架 4.0 的測試驅動開發](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)"。</span><span class="sxs-lookup"><span data-stu-id="5670b-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="5670b-521">數據可程式設計性博客,"[使用具有實體框架 4.0 的儲存庫和工作單元模式](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="5670b-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="5670b-522">亞倫·詹森,"[介紹機器規格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="5670b-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="5670b-523">埃裡克·李[,"BDD與MSTest"](https://saintgimp.org/2009/01/20/bdd-with-mstest/)</span><span class="sxs-lookup"><span data-stu-id="5670b-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="5670b-524">埃裡克·埃文斯,"[領域驅動設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="5670b-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="5670b-525">馬丁·福勒,"[模仿不是斯圖布斯](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="5670b-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="5670b-526">馬丁·福勒,"[測試雙](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="5670b-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="5670b-527">莫克</span><span class="sxs-lookup"><span data-stu-id="5670b-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="5670b-528">傳記</span><span class="sxs-lookup"><span data-stu-id="5670b-528">Biography</span></span>

<span data-ttu-id="5670b-529">Scott Allen 是多元視的技術人員,也是 OdeToCode.com的創始人。</span><span class="sxs-lookup"><span data-stu-id="5670b-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="5670b-530">在 15 年的商業軟體開發中,Scott 一直致力於從 8 位嵌入式裝置到高度可擴展 ASP.NET Web 應用程式等所有解決方案。</span><span class="sxs-lookup"><span data-stu-id="5670b-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="5670b-531">您可以在 OdeToCode 的部落[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)格上或在推特上 聯繫斯科特。</span><span class="sxs-lookup"><span data-stu-id="5670b-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
