---
title: 函數對應-Azure Cosmos DB 提供者-EF Core
description: Azure Cosmos DB EF Core 提供者的函式對應
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543584"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Azure Cosmos DB EF Core 提供者的函式對應

此頁面會顯示使用 Azure Cosmos DB 提供者時，哪些 .NET 成員會轉譯成哪些 SQL 函數。

.NET                          | SQL                              | 加入
----------------------------- | -------------------------------- | --------
收集。包含 (專案)      | @item 在 @collection
英 孚。函數。隨機 ()          | RAND ()                            | EF Core 6.0
stringValue 包含 (值)    | 包含 (@stringValue 、 @value)    | EF Core 5.0
stringValue. EndsWith (值)    | ENDSWITH (@stringValue ， @value)    | EF Core 5.0
stringValue. FirstOrDefault ()   | 左 (@stringValue ，1)             | EF Core 5.0
stringValue. LastOrDefault ()    | RIGHT (@stringValue ，1)            | EF Core 5.0
stringValue. StartsWith (值)  | STARTSWITH (@stringValue ， @value)  | EF Core 5.0
