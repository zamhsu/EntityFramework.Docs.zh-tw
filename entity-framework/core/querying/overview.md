---
title: 查詢的運作方式 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
ms.technology: entity-framework-core
uid: core/querying/overview
ms.openlocfilehash: 1d28d215302625cf2b6788359527a93a77b7e9fd
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "42447790"
---
# <a name="how-queries-work"></a><span data-ttu-id="a0f0e-102">查詢的運作方式</span><span class="sxs-lookup"><span data-stu-id="a0f0e-102">How Queries Work</span></span>

<span data-ttu-id="a0f0e-103">Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="a0f0e-104">LINQ 可讓您使用 C# (或您選擇的 .NET 語言)，根據衍生內容和實體類別來寫入強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="a0f0e-105">查詢的生命週期</span><span class="sxs-lookup"><span data-stu-id="a0f0e-105">The life of a query</span></span>

<span data-ttu-id="a0f0e-106">以下是每個查詢所經歷程序的高階概觀。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="a0f0e-107">LINQ 查詢會由 Entity Framework Core 所處理，以建置已準備好由資料庫提供者所處理的表示方式</span><span class="sxs-lookup"><span data-stu-id="a0f0e-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="a0f0e-108">結果會被快取，因此並不需要在每次執行查詢時進行此處理程序</span><span class="sxs-lookup"><span data-stu-id="a0f0e-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="a0f0e-109">結果會傳遞給資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="a0f0e-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="a0f0e-110">資料庫提供者會識別查詢的哪些組件可在資料庫中評估</span><span class="sxs-lookup"><span data-stu-id="a0f0e-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="a0f0e-111">查詢的這些組件會轉譯為資料庫特定的查詢語言 (例如，針對關聯式資料庫會轉譯為 SQL)</span><span class="sxs-lookup"><span data-stu-id="a0f0e-111">These parts of the query are translated to database specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="a0f0e-112">系統會將一或多個查詢傳送到資料庫，並會傳回結果集 (結果是來自資料庫的值，而非實體執行個體)</span><span class="sxs-lookup"><span data-stu-id="a0f0e-112">One or more queries are sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="a0f0e-113">針對結果集中的每個項目</span><span class="sxs-lookup"><span data-stu-id="a0f0e-113">For each item in the result set</span></span>
   1. <span data-ttu-id="a0f0e-114">如果這是追蹤查詢，EF 就會檢查資料是否代表已存在於內容執行個體變更追蹤器中的實體</span><span class="sxs-lookup"><span data-stu-id="a0f0e-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="a0f0e-115">如果是，就會傳回現有的實體</span><span class="sxs-lookup"><span data-stu-id="a0f0e-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="a0f0e-116">如果不是，則會建立新的實體，設定變更追蹤，並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="a0f0e-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="a0f0e-117">如果這是非追蹤查詢，EF 就會檢查資料是否代表已存在於此查詢結果集中的實體</span><span class="sxs-lookup"><span data-stu-id="a0f0e-117">If this is a no-tracking query, EF checks if the data represents an entity already in the result set for this query</span></span>
      * <span data-ttu-id="a0f0e-118">如果是，就會傳回現有的實體 <sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="a0f0e-118">If so, the existing entity is returned <sup>(1)</sup></span></span>
      * <span data-ttu-id="a0f0e-119">如果不是，則會建立並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="a0f0e-119">If not, a new entity is created and returned</span></span>

<span data-ttu-id="a0f0e-120"><sup>(1)</sup> 非追蹤查詢會使用弱式參考來持續追蹤已經傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-120"><sup>(1)</sup> No tracking queries use weak references to keep track of entities that have already been returned.</span></span> <span data-ttu-id="a0f0e-121">如果先前具有相同身分識別的結果超出範圍，且執行記憶體回收，您可能會得到新的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-121">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="a0f0e-122">查詢執行時</span><span class="sxs-lookup"><span data-stu-id="a0f0e-122">When queries are executed</span></span>

<span data-ttu-id="a0f0e-123">當您呼叫 LINQ 運算子時，只是單純在建置查詢的記憶體內表示方式。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-123">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="a0f0e-124">只有在取用結果時，才會將查詢傳送到資料庫。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-124">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="a0f0e-125">以下是導致將查詢傳送到資料庫的最常見作業：</span><span class="sxs-lookup"><span data-stu-id="a0f0e-125">The most common operations that result in the query being sent to the database are:</span></span>
* <span data-ttu-id="a0f0e-126">在 `for` 迴圈中逐一查看結果</span><span class="sxs-lookup"><span data-stu-id="a0f0e-126">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="a0f0e-127">使用 `ToList`、`ToArray`、`Single`、`Count` 之類的運算子</span><span class="sxs-lookup"><span data-stu-id="a0f0e-127">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`</span></span>
* <span data-ttu-id="a0f0e-128">將查詢的結果資料繫結到 UI</span><span class="sxs-lookup"><span data-stu-id="a0f0e-128">Databinding the results of a query to a UI</span></span>

> [!WARNING]  
> <span data-ttu-id="a0f0e-129">**一律驗證使用者輸入：** 儘管 EF 會提供保護以防止受到 SQL 插入式攻擊，它並不會對輸入執行任何一般驗證。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-129">**Always validate user input:** While EF does provide protection from SQL injection attacks, it does not do any general validation of input.</span></span> <span data-ttu-id="a0f0e-130">因此，如果傳遞到 API、用於 LINQ 查詢中、指派給實體屬性等的值是來自不受信任的來源，則應根據您應用程式的需求執行適當的驗證。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-130">Therefore if values being passed to APIs, used in LINQ queries, assigned to entity properties, etc., come from an untrusted source then appropriate validation, per your application requirements, should be performed.</span></span> <span data-ttu-id="a0f0e-131">這包括用來以動態方式建構查詢的任何使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-131">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="a0f0e-132">即使在使用 LINQ 時，如果您接受使用者輸入來建置運算式，就必須確定只會建構預期的運算式。</span><span class="sxs-lookup"><span data-stu-id="a0f0e-132">Even when using LINQ, if you are accepting user input to build expressions you need to make sure than only intended expressions can be constructed.</span></span>
