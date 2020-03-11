---
title: 5\.0 EF Core 的新功能
author: ajcvickers
ms.date: 01/29/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 65d7bd43e8a00c77fd6091a74c677635710d03e3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417963"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="54227-102">5\.0 EF Core 的新功能</span><span class="sxs-lookup"><span data-stu-id="54227-102">What's New in EF Core 5.0</span></span>

<span data-ttu-id="54227-103">EF Core 5.0 目前正在開發中。</span><span class="sxs-lookup"><span data-stu-id="54227-103">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="54227-104">此頁面將包含每個預覽中所引進之有趣變更的總覽。</span><span class="sxs-lookup"><span data-stu-id="54227-104">This page will contain an overview of interesting changes introduced in each preview.</span></span>
<span data-ttu-id="54227-105">第一季（2020）中的 EF Core 5.0 的第一次預覽是暫時預期的。</span><span class="sxs-lookup"><span data-stu-id="54227-105">The first preview of EF Core 5.0 is tentatively expected in in the first quarter of 2020.</span></span>

<span data-ttu-id="54227-106">此頁面不會複製[EF Core 5.0 的計畫](plan.md)。</span><span class="sxs-lookup"><span data-stu-id="54227-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="54227-107">此計畫描述 EF Core 5.0 的整體主題，包括我們打算在交付最終版本之前包含的所有專案。</span><span class="sxs-lookup"><span data-stu-id="54227-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="54227-108">我們會將這裡的連結新增至正式檔，因為它已發佈。</span><span class="sxs-lookup"><span data-stu-id="54227-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-1-not-yet-shipped"></a><span data-ttu-id="54227-109">Preview 1 （尚未出貨）</span><span class="sxs-lookup"><span data-stu-id="54227-109">Preview 1 (Not yet shipped)</span></span>

### <a name="simple-logging"></a><span data-ttu-id="54227-110">簡單記錄</span><span class="sxs-lookup"><span data-stu-id="54227-110">Simple logging</span></span>

<span data-ttu-id="54227-111">這項功能會新增類似于 EF6 中 `Database.Log` 的功能。</span><span class="sxs-lookup"><span data-stu-id="54227-111">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="54227-112">也就是說，它提供簡單的方法，讓您不需要設定任何類型的外部記錄架構，即可從 EF Core 取得記錄。</span><span class="sxs-lookup"><span data-stu-id="54227-112">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="54227-113">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="54227-113">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="54227-114">其他檔則是由問題[#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-114">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="54227-115">取得產生之 SQL 的簡單方式</span><span class="sxs-lookup"><span data-stu-id="54227-115">Simple way to get generated SQL</span></span>

<span data-ttu-id="54227-116">EF Core 5.0 引進 `ToQueryString` 擴充方法，其會傳回執行 LINQ 查詢時 EF Core 將產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="54227-116">EF Core 5.0 introduces the `ToQueryString` extension method which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="54227-117">初稿檔包含在[2020 年1月9日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)。</span><span class="sxs-lookup"><span data-stu-id="54227-117">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="54227-118">其他檔則是由問題[#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-118">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="54227-119">增強的調試視圖</span><span class="sxs-lookup"><span data-stu-id="54227-119">Enhanced debug views</span></span>

<span data-ttu-id="54227-120">「偵錯工具」是一種簡單的方式，可在偵錯工具發生問題時查看 EF Core 的內部。</span><span class="sxs-lookup"><span data-stu-id="54227-120">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="54227-121">模型的「偵錯工具」視圖已在一段時間前完成。</span><span class="sxs-lookup"><span data-stu-id="54227-121">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="54227-122">針對 EF Core 5.0，我們讓模型視圖更容易閱讀，並在狀態管理員中為追蹤的實體新增新的 [偵錯工具]。</span><span class="sxs-lookup"><span data-stu-id="54227-122">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="54227-123">[2019 年12月12日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="54227-123">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="54227-124">其他檔則是由問題[#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-124">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="54227-125">連接或連接字串可以在初始化的 DbCoNtext 上變更</span><span class="sxs-lookup"><span data-stu-id="54227-125">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="54227-126">現在您可以更輕鬆地建立 DbCoNtext 實例，而不需要任何連接或連接字串。</span><span class="sxs-lookup"><span data-stu-id="54227-126">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="54227-127">此外，連接或連接字串現在可以在內容實例上進行變化。</span><span class="sxs-lookup"><span data-stu-id="54227-127">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="54227-128">這可讓相同的內容實例動態連接到不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="54227-128">This allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="54227-129">檔是由問題[#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-129">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="54227-130">變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="54227-130">Change-tracking proxies</span></span>

<span data-ttu-id="54227-131">EF Core 現在可以產生自動執行[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1)和[INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1)的執行時間 proxy。</span><span class="sxs-lookup"><span data-stu-id="54227-131">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="54227-132">這些之後，實體屬性上的這些值會直接變更為 EF Core，避免需要掃描變更。</span><span class="sxs-lookup"><span data-stu-id="54227-132">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="54227-133">不過，proxy 會有自己的一組限制，因此不適合所有人。</span><span class="sxs-lookup"><span data-stu-id="54227-133">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="54227-134">檔是由問題[#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-134">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="54227-135">改善資料庫 null 語義的處理</span><span class="sxs-lookup"><span data-stu-id="54227-135">Improved handling of database null semantics</span></span>

<span data-ttu-id="54227-136">關係資料庫通常會將 Null 視為未知的值，因此不等於任何其他 Null。</span><span class="sxs-lookup"><span data-stu-id="54227-136">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="54227-137">C#相反地，會將 null 視為已定義的值，其比較是否等於任何其他 null。</span><span class="sxs-lookup"><span data-stu-id="54227-137">C#, on the other hand, treats null as a defined value which compares equal to any other null.</span></span>
<span data-ttu-id="54227-138">EF Core 預設會轉譯查詢，使其使用C# null 的語義。</span><span class="sxs-lookup"><span data-stu-id="54227-138">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="54227-139">EF Core 5.0 大幅提升了這些翻譯的效率。</span><span class="sxs-lookup"><span data-stu-id="54227-139">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="54227-140">檔是由問題[#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-140">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="54227-141">索引子屬性</span><span class="sxs-lookup"><span data-stu-id="54227-141">Indexer properties</span></span>

<span data-ttu-id="54227-142">EF Core 5.0 支援索引子C#屬性的對應。</span><span class="sxs-lookup"><span data-stu-id="54227-142">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="54227-143">這可讓實體作為屬性包，其中的資料行會對應到包中的命名屬性。</span><span class="sxs-lookup"><span data-stu-id="54227-143">This allows entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="54227-144">檔是由問題[#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-144">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="54227-145">列舉對應的檢查條件約束產生</span><span class="sxs-lookup"><span data-stu-id="54227-145">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="54227-146">EF Core 5.0 遷移現在可以產生 enum 屬性對應的檢查條件約束。</span><span class="sxs-lookup"><span data-stu-id="54227-146">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="54227-147">例如：</span><span class="sxs-lookup"><span data-stu-id="54227-147">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="54227-148">檔是由問題[#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-148">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="54227-149">更多日期時間結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="54227-149">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="54227-150">包含新 DataTime 結構的查詢現在已轉譯。</span><span class="sxs-lookup"><span data-stu-id="54227-150">Queries containing new DataTime construction are now translated.</span></span>
<span data-ttu-id="54227-151">此外，SQL Server 函數 DateDiffWeek 現在已經對應。</span><span class="sxs-lookup"><span data-stu-id="54227-151">Also, the SQL Server function DateDiffWeek is now mapped.</span></span>

<span data-ttu-id="54227-152">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-152">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="54227-153">更多位元組陣列結構的查詢翻譯</span><span class="sxs-lookup"><span data-stu-id="54227-153">Query translations for more byte array constructs</span></span>

<span data-ttu-id="54227-154">在 byte [] 屬性上使用 Contains、Length、Enumerable.sequenceequal 等的查詢現在會轉譯為 SQL。</span><span class="sxs-lookup"><span data-stu-id="54227-154">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="54227-155">[2019 年12月5日的 EF 每週狀態](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)會包含初稿檔。</span><span class="sxs-lookup"><span data-stu-id="54227-155">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="54227-156">其他檔則是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-156">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="54227-157">反向的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="54227-157">Query translation for Reverse</span></span>

<span data-ttu-id="54227-158">現在會轉譯使用 `Reverse` 的查詢。</span><span class="sxs-lookup"><span data-stu-id="54227-158">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="54227-159">例如：</span><span class="sxs-lookup"><span data-stu-id="54227-159">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="54227-160">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-160">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="54227-161">位運算子的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="54227-161">Query translation for bitwise operators</span></span>

<span data-ttu-id="54227-162">使用位運算子的查詢現在會在更多情況下轉譯，例如：</span><span class="sxs-lookup"><span data-stu-id="54227-162">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="54227-163">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-163">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="54227-164">Cosmos 上字串的查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="54227-164">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="54227-165">使用 Azure Cosmos DB 提供者時，現在會轉譯使用字串方法 Contains、StartsWith 和 EndsWith 的查詢。</span><span class="sxs-lookup"><span data-stu-id="54227-165">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="54227-166">檔是由問題[#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)追蹤。</span><span class="sxs-lookup"><span data-stu-id="54227-166">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
