---
title: 函數對應-Azure Cosmos DB 提供者-EF Core
description: Azure Cosmos DB EF Core 提供者的函式對應
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066591"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Azure Cosmos DB EF Core 提供者的函式對應

此頁面會顯示使用 Azure Cosmos DB 提供者時，哪些 .NET 成員會轉譯成哪些 SQL 函數。

.NET                          | SQL                              | 加入
----------------------------- | -------------------------------- | --------
收集。包含 (專案)      | @item 在 @collection
stringValue 包含 (值)    | 包含 (@stringValue 、 @value)    | EF Core 5.0
stringValue. EndsWith (值)    | ENDSWITH (@stringValue ， @value)    | EF Core 5.0
stringValue. FirstOrDefault ( # A1  | 左 (@stringValue ，1)             | EF Core 5.0
stringValue. LastOrDefault ( # A1   | RIGHT (@stringValue ，1)            | EF Core 5.0
stringValue. StartsWith (值)  | STARTSWITH (@stringValue ， @value)  | EF Core 5.0
