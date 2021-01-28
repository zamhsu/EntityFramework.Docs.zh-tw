---
title: Microsoft SQL Server 資料庫提供者-值產生-EF Core
description: SQL Server Entity Framework Core 資料庫提供者專用的值產生模式
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987149"
---
# <a name="sql-server-value-generation"></a>產生 SQL Server 值

此頁面詳細說明 SQL Server 提供者特定的值產生設定和模式。 建議您先閱讀 [值產生的 [一般] 頁面](xref:core/modeling/generated-properties)。

## <a name="identity-columns"></a>IDENTITY 資料行

依照慣例，設定為在 add 上產生值的數值資料行，會設定為 [SQL SERVER 識別欄位](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property)。

### <a name="seed-and-increment"></a>種子和遞增

根據預設，識別資料行會從1開始 (種子) ，而且每次將資料列加入 (遞增) 時，每次遞增1。 您可以設定不同的種子和增量，如下所示：

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> 在 EF Core 3.0 中引進了設定識別種子和遞增的功能。

### <a name="inserting-explicit-values-into-identity-columns"></a>將明確值插入識別欄位

根據預設，SQL Server 不允許將明確的值插入識別欄位中。 若要這樣做，您必須在呼叫之前手動啟用，如下所示 `IDENTITY_INSERT` `SaveChanges()` ：

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> 在我們的待辦項目上有一個在 SQL Server 提供者內自動進行此操作的[功能要求](https://github.com/aspnet/EntityFramework/issues/703)。

## <a name="sequences"></a>序列

除了識別資料行之外，您還可以使用標準順序。 這在各種案例中都很有用;例如，您可能想要讓多個資料行從單一序列繪製其預設值。

SQL Server 可讓您建立序列並使用它們，如 [序列的 [一般] 頁面中所](xref:core/modeling/sequences)述。 您可以透過設定屬性來使用序列 `HasDefaultValueSql()` 。
