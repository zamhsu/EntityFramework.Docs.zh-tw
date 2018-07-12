---
title: 查詢資料 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
ms.technology: entity-framework-core
uid: core/querying/index
ms.openlocfilehash: 447f48b780bc48b7a79153d17dcc1b8ef0cc508c
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949271"
---
# <a name="querying-data"></a><span data-ttu-id="e4b73-102">查詢資料</span><span class="sxs-lookup"><span data-stu-id="e4b73-102">Querying Data</span></span>

<span data-ttu-id="e4b73-103">Entity Framework Core 使用 Language Integrated Query (LINQ) 查詢來自資料庫的資料。</span><span class="sxs-lookup"><span data-stu-id="e4b73-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="e4b73-104">LINQ 可讓您使用 C# (或您選擇的 .NET 語言)，根據衍生內容和實體類別來寫入強型別查詢。</span><span class="sxs-lookup"><span data-stu-id="e4b73-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span> <span data-ttu-id="e4b73-105">LINQ 查詢的表示法會傳遞至資料庫提供者，以資料庫特定查詢語言 (例如 SQL 用於關聯式資料庫) 進行轉譯。</span><span class="sxs-lookup"><span data-stu-id="e4b73-105">A representation of the LINQ query is passed to the database provider, to be translated in database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="e4b73-106">如需查詢處理方式的詳細資訊，請參閱[查詢運作方式](overview.md)。</span><span class="sxs-lookup"><span data-stu-id="e4b73-106">For more detailed information on how a query is processed, see [How Query Works](overview.md).</span></span>
