---
title: 查詢的運作方式 - EF Core
description: Entity Framework Core 如何在內部編譯及執行查詢的一般資訊
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e0e489f5f797b6f7d8f4860539a538dba90bd1c2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616366"
---
# <a name="how-queries-work"></a><span data-ttu-id="77bd6-103">查詢的運作方式</span><span class="sxs-lookup"><span data-stu-id="77bd6-103">How Queries Work</span></span>

<span data-ttu-id="77bd6-104">Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。</span><span class="sxs-lookup"><span data-stu-id="77bd6-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="77bd6-105">LINQ 可讓您使用 C# (或您選擇的 .NET 語言)，根據衍生內容和實體類別來寫入強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="77bd6-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="77bd6-106">查詢的生命週期</span><span class="sxs-lookup"><span data-stu-id="77bd6-106">The life of a query</span></span>

<span data-ttu-id="77bd6-107">下列描述是每個查詢所經歷之進程的概要說明。</span><span class="sxs-lookup"><span data-stu-id="77bd6-107">The following description is a high-level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="77bd6-108">LINQ 查詢會由 Entity Framework Core 所處理，以建置已準備好由資料庫提供者所處理的表示方式</span><span class="sxs-lookup"><span data-stu-id="77bd6-108">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="77bd6-109">結果會被快取，因此並不需要在每次執行查詢時進行此處理程序</span><span class="sxs-lookup"><span data-stu-id="77bd6-109">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="77bd6-110">結果會傳遞給資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="77bd6-110">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="77bd6-111">資料庫提供者會識別查詢的哪些組件可在資料庫中評估</span><span class="sxs-lookup"><span data-stu-id="77bd6-111">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="77bd6-112">查詢的這些元件會轉譯為資料庫特定的查詢語言 (例如，關係資料庫的 SQL) </span><span class="sxs-lookup"><span data-stu-id="77bd6-112">These parts of the query are translated to database-specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="77bd6-113">系統會將查詢傳送至資料庫，並傳回結果集 (結果是資料庫的值，而不是實體實例) </span><span class="sxs-lookup"><span data-stu-id="77bd6-113">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="77bd6-114">針對結果集中的每個項目</span><span class="sxs-lookup"><span data-stu-id="77bd6-114">For each item in the result set</span></span>
   1. <span data-ttu-id="77bd6-115">如果查詢是追蹤查詢，EF 就會檢查資料是否代表已在內容實例的變更追蹤器中的實體。</span><span class="sxs-lookup"><span data-stu-id="77bd6-115">If the query is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="77bd6-116">如果是，就會傳回現有的實體</span><span class="sxs-lookup"><span data-stu-id="77bd6-116">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="77bd6-117">如果沒有，則會建立新的實體、設定變更追蹤，並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="77bd6-117">If not, a new entity is created, change tracking is set up, and the new entity is returned</span></span>
   2. <span data-ttu-id="77bd6-118">如果查詢是無追蹤查詢，則一律會建立並傳回新的實體</span><span class="sxs-lookup"><span data-stu-id="77bd6-118">If the query is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="77bd6-119">查詢執行時</span><span class="sxs-lookup"><span data-stu-id="77bd6-119">When queries are executed</span></span>

<span data-ttu-id="77bd6-120">當您呼叫 LINQ 運算子時，您只是要建立查詢的記憶體中標記法。</span><span class="sxs-lookup"><span data-stu-id="77bd6-120">When you call LINQ operators, you're simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="77bd6-121">只有在取用結果時，才會將查詢傳送到資料庫。</span><span class="sxs-lookup"><span data-stu-id="77bd6-121">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="77bd6-122">以下是導致將查詢傳送到資料庫的最常見作業：</span><span class="sxs-lookup"><span data-stu-id="77bd6-122">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="77bd6-123">在 `for` 迴圈中逐一查看結果</span><span class="sxs-lookup"><span data-stu-id="77bd6-123">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="77bd6-124">使用運算子 `ToList` ，例如、、 `ToArray` `Single` 、 `Count` 或對等的非同步多載</span><span class="sxs-lookup"><span data-stu-id="77bd6-124">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`, or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="77bd6-125">**一律驗證使用者輸入：** 雖然 EF Core 使用參數以及在查詢中逸出常值來防止 SQL 插入式攻擊，但是它不會驗證輸入。</span><span class="sxs-lookup"><span data-stu-id="77bd6-125">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="77bd6-126">每個應用程式需求的適當驗證都應該在 LINQ 查詢中使用不受信任來源的值、指派給實體屬性或傳遞給其他 EF Core Api 之前執行。</span><span class="sxs-lookup"><span data-stu-id="77bd6-126">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="77bd6-127">這包括用來以動態方式建構查詢的任何使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="77bd6-127">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="77bd6-128">即使在使用 LINQ 時，如果您接受使用者輸入來建置運算式，就必須確定只會建構預期的運算式。</span><span class="sxs-lookup"><span data-stu-id="77bd6-128">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
