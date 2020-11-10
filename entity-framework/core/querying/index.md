---
title: 查詢資料 - EF Core
description: 在 Entity Framework Core 中進行查詢的相關資訊概觀
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: da5177dda4f2df6537ee9133edf4f1240a4b5e94
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430114"
---
# <a name="querying-data"></a><span data-ttu-id="5fb55-103">查詢資料</span><span class="sxs-lookup"><span data-stu-id="5fb55-103">Querying Data</span></span>

<span data-ttu-id="5fb55-104">Entity Framework Core 使用 Language-Integrated 查詢 (LINQ) 來查詢資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="5fb55-104">Entity Framework Core uses Language-Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="5fb55-105">LINQ 可讓您使用 C# (或您選擇的 .NET 語言) 來撰寫強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="5fb55-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="5fb55-106">它使用您的衍生內容與實體類別來參考資料庫物件。</span><span class="sxs-lookup"><span data-stu-id="5fb55-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="5fb55-107">EF Core 會將 LINQ 查詢的表示法剖析為資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="5fb55-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="5fb55-108">資料庫提供者接著會將它轉譯為資料庫特定查詢語言 (例如，關聯式資料庫的 SQL)。</span><span class="sxs-lookup"><span data-stu-id="5fb55-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="5fb55-109">即使在結果中傳回的實體已經存在於內容中，也一律會對資料庫執行查詢。</span><span class="sxs-lookup"><span data-stu-id="5fb55-109">Queries are always executed against the database even if the entities returned in the result already exist in the context.</span></span>

> [!TIP]
> <span data-ttu-id="5fb55-110">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="5fb55-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="5fb55-111">下列片段顯示一些有關如何使用 Entity Framework Core 完成常見工作的範例。</span><span class="sxs-lookup"><span data-stu-id="5fb55-111">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="5fb55-112">載入所有資料</span><span class="sxs-lookup"><span data-stu-id="5fb55-112">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="5fb55-113">載入單一實體</span><span class="sxs-lookup"><span data-stu-id="5fb55-113">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="5fb55-114">篩選</span><span class="sxs-lookup"><span data-stu-id="5fb55-114">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="5fb55-115">進階閱讀</span><span class="sxs-lookup"><span data-stu-id="5fb55-115">Further readings</span></span>

- <span data-ttu-id="5fb55-116">深入了解 [LINQ 查詢運算式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="5fb55-116">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="5fb55-117">如需有關 EF Core 中查詢處理方式的詳細資訊，請參閱[查詢運作方式](xref:core/querying/how-query-works)。</span><span class="sxs-lookup"><span data-stu-id="5fb55-117">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
