---
title: Entity Framework Core 概觀 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: e6127f775d6bbbdf81debf5519388fe252fe079d
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655614"
---
# <a name="entity-framework-core"></a><span data-ttu-id="b4ab7-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b4ab7-102">Entity Framework Core</span></span>

<span data-ttu-id="b4ab7-103">Entity Framework (EF) Core 是常見 Entity Framework 資料存取技術的輕量型、可擴充、[開放原始碼](https://github.com/aspnet/EntityFrameworkCore)且跨平台版本。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-103">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="b4ab7-104">EF Core 可以當成物件關聯式對應程式 (O/RM)，讓 .NET 開發人員使用 .NET 物件來處理資料庫，而可以省略大部分以往必須自行撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="b4ab7-105">EF Core 支援許多資料庫引擎，如需詳細資料，請參閱[資料庫提供者](providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="b4ab7-106">模型</span><span class="sxs-lookup"><span data-stu-id="b4ab7-106">The Model</span></span>

<span data-ttu-id="b4ab7-107">運用 EF Core，使用模型來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-107">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="b4ab7-108">模型包含多個實體類別以及一個代表含資料庫之工作階段的內容物件，可讓您查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-108">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="b4ab7-109">若要深入了解，請參閱[建立模型](modeling/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-109">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="b4ab7-110">您可以從現有資料庫產生模型、撰寫符合您資料庫的模型程式碼，或使用[EF 移轉](managing-schemas/migrations/index.md)來從您的模型建立資料庫，然後讓它隨著您模型的變更逐步發展。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-110">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](managing-schemas/migrations/index.md) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="b4ab7-111">查詢</span><span class="sxs-lookup"><span data-stu-id="b4ab7-111">Querying</span></span>

<span data-ttu-id="b4ab7-112">使用 Language Integrated Query (LINQ)，從資料庫中擷取實體類別執行個體。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-112">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="b4ab7-113">若要深入了解，請參閱[查詢資料](querying/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-113">See [Querying Data](querying/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="b4ab7-114">儲存資料</span><span class="sxs-lookup"><span data-stu-id="b4ab7-114">Saving Data</span></span>

<span data-ttu-id="b4ab7-115">使用您實體類別的執行個體，建立、刪除和修改資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-115">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="b4ab7-116">若要深入了解，請參閱[儲存資料](saving/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-116">See [Saving Data](saving/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="b4ab7-117">後續步驟</span><span class="sxs-lookup"><span data-stu-id="b4ab7-117">Next steps</span></span>

<span data-ttu-id="b4ab7-118">如需簡介教學課程，請參閱 [Entity Framework Core 使用者入門](get-started/index.md)。</span><span class="sxs-lookup"><span data-stu-id="b4ab7-118">For introductory tutorials, see [Getting Started with Entity Framework Core](get-started/index.md).</span></span>
