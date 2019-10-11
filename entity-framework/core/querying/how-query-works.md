---
title: 查詢的運作方式 - EF Core
author: rowanmiller
ms.date: 09/26/2018
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: bc085755f39b1288f092a8b2df892c1bf82a89f1
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72186264"
---
# <a name="how-queries-work"></a><span data-ttu-id="4afbb-102">查詢的運作方式</span><span class="sxs-lookup"><span data-stu-id="4afbb-102">How Queries Work</span></span>

<span data-ttu-id="4afbb-103">Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。</span><span class="sxs-lookup"><span data-stu-id="4afbb-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="4afbb-104">LINQ 可讓您使用 C# (或您選擇的 .NET 語言)，根據衍生內容和實體類別來寫入強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="4afbb-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="4afbb-105">查詢的生命週期</span><span class="sxs-lookup"><span data-stu-id="4afbb-105">The life of a query</span></span>

<span data-ttu-id="4afbb-106">以下是每個查詢所經歷程序的高階概觀。</span><span class="sxs-lookup"><span data-stu-id="4afbb-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="4afbb-107">LINQ 查詢會由 Entity Framework Core 所處理，以建置已準備好由資料庫提供者所處理的表示方式</span><span class="sxs-lookup"><span data-stu-id="4afbb-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="4afbb-108">結果會被快取，因此並不需要在每次執行查詢時進行此處理程序</span><span class="sxs-lookup"><span data-stu-id="4afbb-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="4afbb-109">結果會傳遞給資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="4afbb-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="4afbb-110">資料庫提供者會識別查詢的哪些組件可在資料庫中評估</span><span class="sxs-lookup"><span data-stu-id="4afbb-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="4afbb-111">查詢的這些組件會轉譯為資料庫特定的查詢語言 (例如，針對關聯式資料庫會轉譯為 SQL)</span><span class="sxs-lookup"><span data-stu-id="4afbb-111">These parts of the query are translated to database specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="4afbb-112">系統會將一或多個查詢傳送到資料庫，並會傳回結果集 (結果是來自資料庫的值，而非實體執行個體)</span><span class="sxs-lookup"><span data-stu-id="4afbb-112">One or more queries are sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="4afbb-113">針對結果集中的每個項目</span><span class="sxs-lookup"><span data-stu-id="4afbb-113">For each item in the result set</span></span>
   1. <span data-ttu-id="4afbb-114">如果這是追蹤查詢，EF 就會檢查資料是否代表已存在於內容執行個體變更追蹤器中的實體</span><span class="sxs-lookup"><span data-stu-id="4afbb-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="4afbb-115">如果是，就會傳回現有的實體</span><span class="sxs-lookup"><span data-stu-id="4afbb-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="4afbb-116">如果不是，則會建立新的實體，設定變更追蹤，並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="4afbb-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="4afbb-117">如果這是非追蹤查詢，EF 就會檢查資料是否代表已存在於此查詢結果集中的實體</span><span class="sxs-lookup"><span data-stu-id="4afbb-117">If this is a no-tracking query, EF checks if the data represents an entity already in the result set for this query</span></span>
      * <span data-ttu-id="4afbb-118">如果是，就會傳回現有的實體 <sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="4afbb-118">If so, the existing entity is returned <sup>(1)</sup></span></span>
      * <span data-ttu-id="4afbb-119">如果不是，則會建立並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="4afbb-119">If not, a new entity is created and returned</span></span>

<span data-ttu-id="4afbb-120"><sup>(1)</sup> 非追蹤查詢會使用弱式參考來持續追蹤已經傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="4afbb-120"><sup>(1)</sup> No tracking queries use weak references to keep track of entities that have already been returned.</span></span> <span data-ttu-id="4afbb-121">如果先前具有相同身分識別的結果超出範圍，且執行記憶體回收，您可能會得到新的實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="4afbb-121">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="4afbb-122">查詢執行時</span><span class="sxs-lookup"><span data-stu-id="4afbb-122">When queries are executed</span></span>

<span data-ttu-id="4afbb-123">當您呼叫 LINQ 運算子時，只是單純在建置查詢的記憶體內表示方式。</span><span class="sxs-lookup"><span data-stu-id="4afbb-123">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="4afbb-124">只有在取用結果時，才會將查詢傳送到資料庫。</span><span class="sxs-lookup"><span data-stu-id="4afbb-124">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="4afbb-125">以下是導致將查詢傳送到資料庫的最常見作業：</span><span class="sxs-lookup"><span data-stu-id="4afbb-125">The most common operations that result in the query being sent to the database are:</span></span>
* <span data-ttu-id="4afbb-126">在 `for` 迴圈中逐一查看結果</span><span class="sxs-lookup"><span data-stu-id="4afbb-126">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="4afbb-127">使用 `ToList`、`ToArray`、`Single`、`Count` 之類的運算子</span><span class="sxs-lookup"><span data-stu-id="4afbb-127">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`</span></span>
* <span data-ttu-id="4afbb-128">將查詢的結果資料繫結到 UI</span><span class="sxs-lookup"><span data-stu-id="4afbb-128">Databinding the results of a query to a UI</span></span>

> [!WARNING]  
> <span data-ttu-id="4afbb-129">**一律驗證使用者輸入：** 雖然 EF Core 使用參數來防止 SQL 插入式攻擊，並在查詢中將常值進行轉義，但它並不會驗證輸入。</span><span class="sxs-lookup"><span data-stu-id="4afbb-129">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="4afbb-130">在將不受信任來源的值用於 LINQ 查詢中、指派給實體屬性或傳遞至其他 EF Core API 之前，應該執行針對每個應用程式需求的適當驗證。</span><span class="sxs-lookup"><span data-stu-id="4afbb-130">Appropriate validation, per the application's requirements, should be performed before values from untrusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="4afbb-131">這包括用來以動態方式建構查詢的任何使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="4afbb-131">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="4afbb-132">即使在使用 LINQ 時，如果您接受使用者輸入來建置運算式，就必須確定只會建構預期的運算式。</span><span class="sxs-lookup"><span data-stu-id="4afbb-132">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
