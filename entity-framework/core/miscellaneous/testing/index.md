---
title: "使用 Entity Framework 來測試元件 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/index
ms.openlocfilehash: c82c25da393c39cf5e2deb46c7322e7395051937
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="testing"></a><span data-ttu-id="37976-102">測試</span><span class="sxs-lookup"><span data-stu-id="37976-102">Testing</span></span>

<span data-ttu-id="37976-103">您可能會想要使用近似連線至實際資料庫，而不實際進行資料庫 I/O 作業的方式來測試元件。</span><span class="sxs-lookup"><span data-stu-id="37976-103">You may want to test components using something that approximates connecting to the real database, without the overhead of actual database I/O operations.</span></span>

<span data-ttu-id="37976-104">有兩種主要的方式可以這麼做：</span><span class="sxs-lookup"><span data-stu-id="37976-104">There are two main options for doing this:</span></span>
 * <span data-ttu-id="37976-105">[SQLite 記憶體內部模式](sqlite.md) 可讓您對行為類似關聯式資料庫的提供者寫入有效率的測試。</span><span class="sxs-lookup"><span data-stu-id="37976-105">[SQLite in-memory mode](sqlite.md) allows you to write efficient tests against a provider that behaves like a relational database.</span></span>
 * <span data-ttu-id="37976-106">[InMemory 提供者](in-memory.md) 是相依性最低的輕量型提供者，但行為不一定永遠都如同關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="37976-106">[The InMemory provider](in-memory.md) is a lightweight provider that has minimal dependencies, but does not always behave like a relational database.</span></span>
