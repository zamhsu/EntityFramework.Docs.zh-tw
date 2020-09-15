---
title: 管理資料庫結構描述 - EF Core
description: 使用 Entity Framework Core 管理資料庫結構描述的策略概觀
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619502"
---
# <a name="managing-database-schemas"></a>管理資料庫結構描述

EF Core 提供兩種主要方式來保持 EF Core 模型與資料庫結構描述同步。若要從兩者中進行選擇，請決定 EF Core 模型還是資料庫結構描述為真實來源。

如果您想要 EF Core 模型成為真實來源，請使用[移轉][1]。 當您變更 EF Core 模型時，這種方式會以遞增方式將對應的結構描述變更套用至資料庫；因此，它會與 EF Core 模型相容。

如果您想要資料庫結構描述成為真實來源，請使用[反向工程][2]。 此方式可讓您將資料庫結構描述反向工程到 EF Core 模型，以支援 DbContext 和實體類型類別。

> [!NOTE]
> [建立和捨棄 API][3] 也可以從 EF Core 模型建立資料庫結構描述。 不過，它們主要用於測試、原型設計，以及可接受捨棄資料庫的其他案例。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
