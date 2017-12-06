---
title: "如何查詢工作的 EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
ms.technology: entity-framework-core
uid: core/querying/overview
ms.openlocfilehash: 7fd2940d559f82016d7a8fc3fdcf3af0d5b8bc8f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="how-queries-work"></a><span data-ttu-id="9fe3d-102">查詢的運作方式</span><span class="sxs-lookup"><span data-stu-id="9fe3d-102">How Queries Work</span></span>

<span data-ttu-id="9fe3d-103">Entity Framework Core 會使用從資料庫查詢資料的語言整合 Query (LINQ)。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-103">Entity Framework Core uses Language Integrate Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="9fe3d-104">LINQ 可讓您使用 C# （或您所選擇的.NET 語言） 撰寫強型別衍生內容和實體類別為基礎的查詢。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="9fe3d-105">生命週期的查詢</span><span class="sxs-lookup"><span data-stu-id="9fe3d-105">The life of a query</span></span>

<span data-ttu-id="9fe3d-106">以下是每個查詢所經歷的程序的高層級概觀。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="9fe3d-107">建立表示準備好要處理的資料庫提供者的 Entity Framework Core 處理 LINQ 查詢</span><span class="sxs-lookup"><span data-stu-id="9fe3d-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="9fe3d-108">結果會快取，因此不需要每次執行查詢來完成這項處理</span><span class="sxs-lookup"><span data-stu-id="9fe3d-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="9fe3d-109">結果會傳遞至資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="9fe3d-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="9fe3d-110">資料庫提供者會識別查詢的哪些部分可以評估在資料庫中</span><span class="sxs-lookup"><span data-stu-id="9fe3d-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="9fe3d-111">這些組件的查詢會轉譯為資料庫特定的查詢語言 (例如關聯式資料庫的 SQL)</span><span class="sxs-lookup"><span data-stu-id="9fe3d-111">These parts of the query are translated to database specific query language (e.g. SQL for a relational database)</span></span>
   3. <span data-ttu-id="9fe3d-112">一個或多個查詢會傳送至資料庫而傳回的結果集 （結果會是從資料庫中的實體執行個體的值）</span><span class="sxs-lookup"><span data-stu-id="9fe3d-112">One or more queries are sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="9fe3d-113">針對結果集中的每個項目</span><span class="sxs-lookup"><span data-stu-id="9fe3d-113">For each item in the result set</span></span>
   1. <span data-ttu-id="9fe3d-114">如果這是追蹤查詢，會檢查 EF 如果其資料代表的實體已在變更追蹤程式的內容執行個體</span><span class="sxs-lookup"><span data-stu-id="9fe3d-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="9fe3d-115">如果是的話，會傳回現有的實體</span><span class="sxs-lookup"><span data-stu-id="9fe3d-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="9fe3d-116">如果不是，建立新的實體，變更追蹤，則安裝程式，並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="9fe3d-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="9fe3d-117">如果這不是追蹤查詢，會檢查 EF 如果其資料代表的實體已在設定這個查詢的結果</span><span class="sxs-lookup"><span data-stu-id="9fe3d-117">If this is a no-tracking query, EF checks if the data represents an entity already in the result set for this query</span></span>
      * <span data-ttu-id="9fe3d-118">如果現有的實體，則會傳回<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="9fe3d-118">If so, the existing entity is returned <sup>(1)</sup></span></span>
      * <span data-ttu-id="9fe3d-119">如果不是，建立並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="9fe3d-119">If not, a new entity is created and returned</span></span>

<span data-ttu-id="9fe3d-120"><sup>(1)</sup>沒有追蹤的查詢使用弱式參考來追蹤已傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-120"><sup>(1)</sup> No tracking queries use weak references to keep track of entities that have already been returned.</span></span> <span data-ttu-id="9fe3d-121">如果具有相同識別是之前的結果超出範圍，而且執行記憶體回收，可能會收到新的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-121">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="9fe3d-122">執行查詢時</span><span class="sxs-lookup"><span data-stu-id="9fe3d-122">When queries are executed</span></span>

<span data-ttu-id="9fe3d-123">當您呼叫 LINQ 運算子時，而只累積在記憶體中表示的查詢。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-123">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="9fe3d-124">當結果被耗用時，查詢才會傳送到資料庫。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-124">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="9fe3d-125">造成查詢傳送給資料庫的最常見作業為：</span><span class="sxs-lookup"><span data-stu-id="9fe3d-125">The most common operations that result in the query being sent to the database are:</span></span>
* <span data-ttu-id="9fe3d-126">逐一查看結果`for`迴圈</span><span class="sxs-lookup"><span data-stu-id="9fe3d-126">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="9fe3d-127">使用運算子，例如`ToList`， `ToArray`， `Single`，`Count`</span><span class="sxs-lookup"><span data-stu-id="9fe3d-127">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`</span></span>
* <span data-ttu-id="9fe3d-128">資料繫結至 UI 查詢的結果</span><span class="sxs-lookup"><span data-stu-id="9fe3d-128">Databinding the results of a query to a UI</span></span>

> [!WARNING]  
> <span data-ttu-id="9fe3d-129">**一律驗證使用者輸入：**時的 EF 提供從 SQL 資料隱碼攻擊的防護，不會執行輸入的任何一般驗證。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-129">**Always validate user input:** While EF does provide protection from SQL injection attacks, it does not do any general validation of input.</span></span> <span data-ttu-id="9fe3d-130">因此如果值傳遞至應用程式開發介面，在 LINQ 查詢中，指派給等實體屬性，使用來自不受信任的來源則適當的驗證，每個應用程式需求，應該執行。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-130">Therefore if values being passed to APIs, used in LINQ queries, assigned to entity properties, etc., come from an untrusted source then appropriate validation, per your application requirements, should be performed.</span></span> <span data-ttu-id="9fe3d-131">這包括用來以動態方式建構查詢的任何使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-131">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="9fe3d-132">即使使用 LINQ，如果您接受使用者輸入建立運算式，您必須確定比預期運算式只可用於建構。</span><span class="sxs-lookup"><span data-stu-id="9fe3d-132">Even when using LINQ, if you are accepting user input to build expressions you need to make sure than only intended expressions can be constructed.</span></span>
