---
title: 管理資料庫結構描述 - EF Core
author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: 2da17865cb0192fb3e6e3396e4ca5f31fde9c52a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412733"
---
# <a name="managing-database-schemas"></a>管理資料庫結構描述

EF Core 提供兩種主要方式來保持 EF Core 模型與資料庫結構描述同步。若要從兩者中進行選擇，請決定 EF Core 模型還是資料庫結構描述為真實來源。

如果您想要 EF Core 模型成為真實來源，請使用[移轉][1]。 當您變更 EF Core 模型時，這種方式會以遞增方式將對應的結構描述變更套用至資料庫；因此，它會與 EF Core 模型相容。

如果您想要資料庫結構描述成為真實來源，請使用[反向工程][2]。 此方式可讓您將資料庫結構描述反向工程到 EF Core 模型，以支援 DbContext 和實體類型類別。

> [!NOTE]
> [建立和捨棄 API][3] 也可以從 EF Core 模型建立資料庫結構描述。 不過，它們主要用於測試、原型設計，以及可接受捨棄資料庫的其他案例。


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
