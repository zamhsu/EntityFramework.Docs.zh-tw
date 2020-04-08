---
title: 儲存資料 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: c610ea2a9138482f93d2d54c9085ef827af276c8
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413093"
---
# <a name="saving-data"></a>儲存資料

每個內容執行個體的 `ChangeTracker` 都負責追蹤寫入至資料庫所需的變更。 當您變更實體類別的執行個體時，這些變更會記錄在 `ChangeTracker` 中，然後在您呼叫 `SaveChanges` 時寫入至資料庫。 資料庫提供者負責將變更轉譯成資料庫特定作業 (例如關聯式資料庫的 `INSERT`、`UPDATE` 和 `DELETE` 命令)。
