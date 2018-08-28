---
title: 記錄檔的提供者影響的變更-EF Core
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: ee73940e3c0030b76e73438b1852cc29ebeadb45
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998349"
---
# <a name="provider-impacting-changes"></a><span data-ttu-id="df6ab-102">提供者影響的變更</span><span class="sxs-lookup"><span data-stu-id="df6ab-102">Provider-impacting changes</span></span>

<span data-ttu-id="df6ab-103">此頁面包含提取要求可能需要其他的資料庫提供者，以回應的作者在 EF Core 存放庫上所做的連結。</span><span class="sxs-lookup"><span data-stu-id="df6ab-103">This page contains links to pull requests made on the EF Core repo that may require authors of other database providers to react.</span></span> <span data-ttu-id="df6ab-104">目的是要讓現有的協力廠商資料庫提供者作者提供一個起始點，其提供者更新為新版本時。</span><span class="sxs-lookup"><span data-stu-id="df6ab-104">The intention is to provide a starting point for authors of existing third-party database providers when updating their provider to a new version.</span></span>

<span data-ttu-id="df6ab-105">我們會將此記錄檔開頭 2.1 2.2 的變更。</span><span class="sxs-lookup"><span data-stu-id="df6ab-105">We are starting this log with changes from 2.1 to 2.2.</span></span> <span data-ttu-id="df6ab-106">我們用之前 2.1 [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)並[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)上我們的問題和提取要求標籤。</span><span class="sxs-lookup"><span data-stu-id="df6ab-106">Prior to 2.1 we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our issues and pull requests.</span></span>

### <a name="21-----22"></a><span data-ttu-id="df6ab-107">2.1---> 2.2</span><span class="sxs-lookup"><span data-stu-id="df6ab-107">2.1 ---> 2.2</span></span>

#### <a name="test-only-changes"></a><span data-ttu-id="df6ab-108">僅限測試的變更</span><span class="sxs-lookup"><span data-stu-id="df6ab-108">Test-only changes</span></span>

* <span data-ttu-id="df6ab-109">https://github.com/aspnet/EntityFrameworkCore/pull/12057 -在測試中允許可自訂的 SQL 分隔符號</span><span class="sxs-lookup"><span data-stu-id="df6ab-109">https://github.com/aspnet/EntityFrameworkCore/pull/12057 - Allow customizable SQL delimeters in tests</span></span>
  * <span data-ttu-id="df6ab-110">測試可讓非嚴格的浮動點比較 BuiltInDataTypesTestBase 中的變更</span><span class="sxs-lookup"><span data-stu-id="df6ab-110">Test changes that allow non-strict floating point comparisons in BuiltInDataTypesTestBase</span></span>
  * <span data-ttu-id="df6ab-111">允許要重複使用不同的 SQL 分隔符號使用的查詢測試的測試變更</span><span class="sxs-lookup"><span data-stu-id="df6ab-111">Test changes that allow query tests to be re-used with different SQL delimeters</span></span>
* <span data-ttu-id="df6ab-112">https://github.com/aspnet/EntityFrameworkCore/pull/12072 -將 DbFunction 測試加入至關聯式規格測試</span><span class="sxs-lookup"><span data-stu-id="df6ab-112">https://github.com/aspnet/EntityFrameworkCore/pull/12072 - Add DbFunction tests to the relational specification tests</span></span>
  * <span data-ttu-id="df6ab-113">例如，針對所有的資料庫提供者，可以執行這些測試</span><span class="sxs-lookup"><span data-stu-id="df6ab-113">Such that these tests can be run against all database providers</span></span>
* <span data-ttu-id="df6ab-114">https://github.com/aspnet/EntityFrameworkCore/pull/12362 -非同步測試清除</span><span class="sxs-lookup"><span data-stu-id="df6ab-114">https://github.com/aspnet/EntityFrameworkCore/pull/12362 - Async test cleanup</span></span>
  * <span data-ttu-id="df6ab-115">移除`Wait`呼叫，不需要非同步處理，並重新命名某些測試方法</span><span class="sxs-lookup"><span data-stu-id="df6ab-115">Remove `Wait` calls, unneeded async, and renamed some test methods</span></span>
* <span data-ttu-id="df6ab-116">https://github.com/aspnet/EntityFrameworkCore/pull/12666 -統一記錄測試基礎結構</span><span class="sxs-lookup"><span data-stu-id="df6ab-116">https://github.com/aspnet/EntityFrameworkCore/pull/12666 - Unify logging test infrastructure</span></span>
  * <span data-ttu-id="df6ab-117">新增`CreateListLoggerFactory`和移除一些先前記錄基礎結構，而這需要使用這些測試回應的提供者</span><span class="sxs-lookup"><span data-stu-id="df6ab-117">Added `CreateListLoggerFactory` and removed some previous logging infrastructure, which will require providers using these tests to react</span></span>
* <span data-ttu-id="df6ab-118">https://github.com/aspnet/EntityFrameworkCore/pull/12500 -執行更多的查詢測試，以同步和非同步</span><span class="sxs-lookup"><span data-stu-id="df6ab-118">https://github.com/aspnet/EntityFrameworkCore/pull/12500 - Run more query tests both synchronously and asynchronously</span></span>
  * <span data-ttu-id="df6ab-119">測試名稱和分解已經變更，這將需要使用這些測試回應的提供者</span><span class="sxs-lookup"><span data-stu-id="df6ab-119">Test names and factoring has changed, which will require providers using these tests to react</span></span>
* <span data-ttu-id="df6ab-120">https://github.com/aspnet/EntityFrameworkCore/pull/12766 -重新命名 ComplexNavigations 模型中的巡覽</span><span class="sxs-lookup"><span data-stu-id="df6ab-120">https://github.com/aspnet/EntityFrameworkCore/pull/12766 - Renaming navigations in the ComplexNavigations model</span></span>
  * <span data-ttu-id="df6ab-121">使用這些測試的提供者可能需要做出回應</span><span class="sxs-lookup"><span data-stu-id="df6ab-121">Providers using these tests may need to react</span></span>
* <span data-ttu-id="df6ab-122">https://github.com/aspnet/EntityFrameworkCore/pull/12141 -傳回集區，而不是功能測試在處置內容</span><span class="sxs-lookup"><span data-stu-id="df6ab-122">https://github.com/aspnet/EntityFrameworkCore/pull/12141 - Return the context to the pool instead of disposing in functional tests</span></span>
  * <span data-ttu-id="df6ab-123">這項變更會包含可能需要提供者，以因應一些測試重構</span><span class="sxs-lookup"><span data-stu-id="df6ab-123">This change includes some test refactoring which may require providers to react</span></span>


#### <a name="test-and-product-code-changes"></a><span data-ttu-id="df6ab-124">測試和產品程式碼變更</span><span class="sxs-lookup"><span data-stu-id="df6ab-124">Test and product code changes</span></span>

* <span data-ttu-id="df6ab-125">https://github.com/aspnet/EntityFrameworkCore/pull/12109 -彙總 RelationalTypeMapping.Clone 方法</span><span class="sxs-lookup"><span data-stu-id="df6ab-125">https://github.com/aspnet/EntityFrameworkCore/pull/12109 - Consolidate RelationalTypeMapping.Clone methods</span></span>
  * <span data-ttu-id="df6ab-126">允許在衍生類別中簡化 RelationalTypeMapping 的 2.1 中的變更。</span><span class="sxs-lookup"><span data-stu-id="df6ab-126">Changes in 2.1 to the RelationalTypeMapping allowed for a simplification in derived classes.</span></span> <span data-ttu-id="df6ab-127">我們不認為這重大提供者，但提供者可以利用這項變更其衍生類型中對應的類別。</span><span class="sxs-lookup"><span data-stu-id="df6ab-127">We don't believe this was breaking to providers, but providers can take advantage of this change in their derived type mapping classes.</span></span>
* <span data-ttu-id="df6ab-128">https://github.com/aspnet/EntityFrameworkCore/pull/12069 -已標記或具名查詢</span><span class="sxs-lookup"><span data-stu-id="df6ab-128">https://github.com/aspnet/EntityFrameworkCore/pull/12069 - Tagged or named queries</span></span>
  * <span data-ttu-id="df6ab-129">新增用於標記 LINQ 查詢以及做為 SQL 中的註解會顯示這些標記的基礎結構。</span><span class="sxs-lookup"><span data-stu-id="df6ab-129">Adds infrastructure for tagging LINQ queries and having those tags show up as comments in the SQL.</span></span> <span data-ttu-id="df6ab-130">這可能需要以回應 SQL 層代中的提供者。</span><span class="sxs-lookup"><span data-stu-id="df6ab-130">This may require providers to react in SQL generation.</span></span>
