---
title: 查詢的運作方式 - EF Core
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e8a50efe31468ea8df211602636dd474550bc0ef
ms.sourcegitcommit: c3b8386071d64953ee68788ef9d951144881a6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80136245"
---
# <a name="how-queries-work"></a><span data-ttu-id="2fb35-102">查詢的運作方式</span><span class="sxs-lookup"><span data-stu-id="2fb35-102">How Queries Work</span></span>

<span data-ttu-id="2fb35-103">Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。</span><span class="sxs-lookup"><span data-stu-id="2fb35-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="2fb35-104">LINQ 可讓您使用 C# (或您選擇的 .NET 語言)，根據衍生內容和實體類別來寫入強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="2fb35-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="2fb35-105">查詢的生命週期</span><span class="sxs-lookup"><span data-stu-id="2fb35-105">The life of a query</span></span>

<span data-ttu-id="2fb35-106">以下是每個查詢所經歷程序的高階概觀。</span><span class="sxs-lookup"><span data-stu-id="2fb35-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="2fb35-107">LINQ 查詢會由 Entity Framework Core 所處理，以建置已準備好由資料庫提供者所處理的表示方式</span><span class="sxs-lookup"><span data-stu-id="2fb35-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="2fb35-108">結果會被快取，因此並不需要在每次執行查詢時進行此處理程序</span><span class="sxs-lookup"><span data-stu-id="2fb35-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="2fb35-109">結果會傳遞給資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="2fb35-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="2fb35-110">資料庫提供者會識別查詢的哪些組件可在資料庫中評估</span><span class="sxs-lookup"><span data-stu-id="2fb35-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="2fb35-111">查詢的這些組件會轉譯為資料庫特定的查詢語言 (例如，針對關聯式資料庫會轉譯為 SQL)</span><span class="sxs-lookup"><span data-stu-id="2fb35-111">These parts of the query are translated to database specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="2fb35-112">查詢會傳送至資料庫，並傳回結果集（結果是來自資料庫的值，而不是實體實例）</span><span class="sxs-lookup"><span data-stu-id="2fb35-112">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="2fb35-113">針對結果集中的每個項目</span><span class="sxs-lookup"><span data-stu-id="2fb35-113">For each item in the result set</span></span>
   1. <span data-ttu-id="2fb35-114">如果這是追蹤查詢，EF 就會檢查資料是否代表已存在於內容執行個體變更追蹤器中的實體</span><span class="sxs-lookup"><span data-stu-id="2fb35-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="2fb35-115">如果是，就會傳回現有的實體</span><span class="sxs-lookup"><span data-stu-id="2fb35-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="2fb35-116">如果不是，則會建立新的實體，設定變更追蹤，並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="2fb35-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="2fb35-117">如果這是沒有追蹤的查詢，則一律會建立並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="2fb35-117">If this is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="2fb35-118">查詢執行時</span><span class="sxs-lookup"><span data-stu-id="2fb35-118">When queries are executed</span></span>

<span data-ttu-id="2fb35-119">當您呼叫 LINQ 運算子時，只是單純在建置查詢的記憶體內表示方式。</span><span class="sxs-lookup"><span data-stu-id="2fb35-119">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="2fb35-120">只有在取用結果時，才會將查詢傳送到資料庫。</span><span class="sxs-lookup"><span data-stu-id="2fb35-120">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="2fb35-121">以下是導致將查詢傳送到資料庫的最常見作業：</span><span class="sxs-lookup"><span data-stu-id="2fb35-121">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="2fb35-122">在 `for` 迴圈中逐一查看結果</span><span class="sxs-lookup"><span data-stu-id="2fb35-122">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="2fb35-123">使用運算子，例如 `ToList`、`ToArray`、`Single`、`Count` 或對等的非同步多載</span><span class="sxs-lookup"><span data-stu-id="2fb35-123">Using an operator such as `ToList`, `ToArray`, `Single`, `Count` or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="2fb35-124">**一律驗證使用者輸入：** 雖然 EF Core 使用參數以及在查詢中逸出常值來防止 SQL 插入式攻擊，但是它不會驗證輸入。</span><span class="sxs-lookup"><span data-stu-id="2fb35-124">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="2fb35-125">根據應用程式的需求，應該在 LINQ 查詢中使用不受信任來源的值、指派給實體屬性，或傳遞至其他 EF Core Api 之前，先執行適當的驗證。</span><span class="sxs-lookup"><span data-stu-id="2fb35-125">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="2fb35-126">這包括用來以動態方式建構查詢的任何使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="2fb35-126">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="2fb35-127">即使在使用 LINQ 時，如果您接受使用者輸入來建置運算式，就必須確定只會建構預期的運算式。</span><span class="sxs-lookup"><span data-stu-id="2fb35-127">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
