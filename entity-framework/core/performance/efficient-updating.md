---
title: 有效率的更新-EF Core
description: 使用 Entity Framework Core 有效率更新的效能指南
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: 92766d2339fb04ed5ebc3123429171cc9be424b1
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657797"
---
# <a name="efficient-updating"></a>有效率的更新

## <a name="batching"></a>批次處理

EF Core 藉由在單一往返中自動將所有更新批次處理在一起，有助於將往返次數降至最低。 請考慮下列事項：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

上述內容會從資料庫載入 blog、變更其名稱，然後新增兩個新的 blog;若要套用此項，請將兩個 SQL INSERT 語句和一個 UPDATE 語句傳送至資料庫。 它不會逐一傳送，因為加入了 Blog 實例，EF Core 在內部追蹤這些變更，並在呼叫時以單一往返方式執行這些變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。

EF 在單一往返中批次處理的語句數目，取決於所使用的資料庫提供者。 例如，效能分析顯示在牽涉到4個以上的語句時，對 SQL Server 來說效率通常較低。 同樣地，在 SQL Server 的40語句前後，批次處理的優點也會降低，因此 EF Core 預設只會在單一批次中執行最多42語句，並在個別的往返中執行其他語句。

使用者也可以調整這些閾值來達到可能較高的效能，但在修改這些閾值之前，請先仔細檢驗：

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a>大量更新

讓我們假設您想要讓所有員工都能獲得提升。 在 EF Core 中，這項工作的典型執行如下所示：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

雖然這是完全有效的程式碼，但讓我們從效能的觀點來分析它的功用：

* 執行資料庫往返，以載入所有相關的員工;請注意，這會將所有員工的資料列資料帶入用戶端，即使只需要薪資也是如此。
* EF Core 的變更追蹤會在載入實體時建立快照集，然後將這些快照集與實例進行比較，以找出變更了哪些屬性。
* 執行第二個資料庫往返以儲存所有變更。 雖然所有變更都是在單次往返中完成，但是有了批次處理，但 EF Core 仍會傳送每個員工的 UPDATE 語句，這必須由資料庫執行。

關係資料庫也支援 *大量更新*，因此可以將上述的單一 SQL 語句重寫為下列各項：

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

這會在單一往返中執行整個作業，而不需要將任何實際資料載入或傳送至資料庫，也不會使用 EF 的變更追蹤機制，這會造成額外的負荷。

可惜的是，EF 目前未提供用來執行大量更新的 Api。 在引入這些之前，您可以使用原始 SQL 來執行效能敏感性的作業：

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
