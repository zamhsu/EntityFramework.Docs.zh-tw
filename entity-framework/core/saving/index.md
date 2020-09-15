---
title: 儲存資料 - EF Core
description: 使用 Entity Framework Core 儲存資料的概觀
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: 13e7c470e71f9cf9a1112aad18a8902e2235cef1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616317"
---
# <a name="saving-data"></a>儲存資料

每個內容執行個體的 `ChangeTracker` 都負責追蹤寫入至資料庫所需的變更。 當您變更實體類別的執行個體時，這些變更會記錄在 `ChangeTracker` 中，然後在您呼叫 `SaveChanges` 時寫入至資料庫。 資料庫提供者負責將變更轉譯成資料庫特定作業 (例如關聯式資料庫的 `INSERT`、`UPDATE` 和 `DELETE` 命令)。
