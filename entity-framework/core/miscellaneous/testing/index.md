---
title: 使用 Entity Framework 來測試元件 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
uid: core/miscellaneous/testing/index
ms.openlocfilehash: fc751b9053c337e4911f4016b65b370d1276046b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997833"
---
# <a name="testing"></a><span data-ttu-id="64afb-102">測試</span><span class="sxs-lookup"><span data-stu-id="64afb-102">Testing</span></span>

<span data-ttu-id="64afb-103">您可能會想要使用近似連線至實際資料庫，而不實際進行資料庫 I/O 作業的方式來測試元件。</span><span class="sxs-lookup"><span data-stu-id="64afb-103">You may want to test components using something that approximates connecting to the real database, without the overhead of actual database I/O operations.</span></span>

<span data-ttu-id="64afb-104">有兩種主要的方式可以這麼做：</span><span class="sxs-lookup"><span data-stu-id="64afb-104">There are two main options for doing this:</span></span>
 * <span data-ttu-id="64afb-105">[SQLite 記憶體內部模式](sqlite.md) 可讓您對行為類似關聯式資料庫的提供者寫入有效率的測試。</span><span class="sxs-lookup"><span data-stu-id="64afb-105">[SQLite in-memory mode](sqlite.md) allows you to write efficient tests against a provider that behaves like a relational database.</span></span>
 * <span data-ttu-id="64afb-106">[InMemory 提供者](in-memory.md) 是相依性最低的輕量型提供者，但行為不一定永遠都如同關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="64afb-106">[The InMemory provider](in-memory.md) is a lightweight provider that has minimal dependencies, but does not always behave like a relational database.</span></span>
