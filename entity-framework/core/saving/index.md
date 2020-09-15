---
title: 儲存資料 - EF Core
description: 使用 Entity Framework Core 儲存資料的概觀
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 98a23fd4956a8646e9760f9ecb0b48166ad6caa1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072469"
---
# <a name="saving-data"></a><span data-ttu-id="e994f-103">儲存資料</span><span class="sxs-lookup"><span data-stu-id="e994f-103">Saving Data</span></span>

<span data-ttu-id="e994f-104">每個內容執行個體的 `ChangeTracker` 都負責追蹤寫入至資料庫所需的變更。</span><span class="sxs-lookup"><span data-stu-id="e994f-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="e994f-105">當您變更實體類別的執行個體時，這些變更會記錄在 `ChangeTracker` 中，然後在您呼叫 `SaveChanges` 時寫入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="e994f-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="e994f-106">資料庫提供者負責將變更轉譯成資料庫特定作業 (例如關聯式資料庫的 `INSERT`、`UPDATE` 和 `DELETE` 命令)。</span><span class="sxs-lookup"><span data-stu-id="e994f-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
