---
title: Microsoft SQL Server 資料庫提供者-索引-EF Core
description: Entity Framework Core SQL Server 提供者特有的索引功能
author: roji
ms.author: shrojans
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 3830377562fcc6a59239cd2c09d1419bbd00922e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620731"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a>Entity Framework Core SQL Server 提供者特有的索引功能

此頁面詳細說明 SQL Server 提供者特定的索引設定選項。

## <a name="clustering"></a>叢集

叢集索引將資料表或檢視中的資料列依其索引鍵值排序與儲存。 為您的資料表建立正確的叢集索引，可以大幅提升查詢的速度，因為資料已經以最佳順序配置。 因為資料列本身只能以一種順序排序，所以每個資料表只能有一個叢集索引。 如需詳細資訊，請參閱 [SQL Server 叢集和非叢集索引的檔](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)集。

依預設，資料表的主鍵資料行是由叢集索引隱含支援，而其他所有索引則為非叢集。

您可以設定要叢集化的索引或索引鍵，如下所示：

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

## <a name="fill-factor"></a>填滿因數

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。

索引填滿因數選項是為了微調索引資料儲存和效能而提供。 如需詳細資訊，請參閱 [關於填滿因數的 SQL Server 檔](/sql/relational-databases/indexes/specify-fill-factor-for-an-index)。

您可以設定索引的填滿因數，如下所示：

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a>線上建立

ONLINE 選項允許在索引建立期間，並行使用者存取基礎資料表或叢集索引資料以及任何相關聯的非叢集索引，讓使用者可以繼續更新和查詢基礎資料。 當您離線執行資料定義語言 (DDL) 作業 (例如建立或重建叢集索引) 時，這些作業會保有基礎資料和關聯索引的獨佔鎖定。 如需詳細資訊，請參閱 [線上索引選項的 SQL Server 檔](/sql/relational-databases/indexes/perform-index-operations-online)。

您可以使用 ONLINE 選項來設定索引，如下所示：

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
