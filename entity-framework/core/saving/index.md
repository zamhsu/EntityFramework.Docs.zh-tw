---
title: 儲存資料 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: c610ea2a9138482f93d2d54c9085ef827af276c8
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998176"
---
# <a name="saving-data"></a><span data-ttu-id="f6d1f-102">儲存資料</span><span class="sxs-lookup"><span data-stu-id="f6d1f-102">Saving Data</span></span>

<span data-ttu-id="f6d1f-103">每個內容執行個體的 `ChangeTracker` 都負責追蹤寫入至資料庫所需的變更。</span><span class="sxs-lookup"><span data-stu-id="f6d1f-103">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="f6d1f-104">當您變更實體類別的執行個體時，這些變更會記錄在 `ChangeTracker` 中，然後在您呼叫 `SaveChanges` 時寫入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="f6d1f-104">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="f6d1f-105">資料庫提供者負責將變更轉譯成資料庫特定作業 (例如關聯式資料庫的 `INSERT`、`UPDATE` 和 `DELETE` 命令)。</span><span class="sxs-lookup"><span data-stu-id="f6d1f-105">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
