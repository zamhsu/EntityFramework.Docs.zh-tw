---
title: "產生的內容-EF 核心設定明確的值"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3f1993c2-cdf5-425b-bac2-a2665a20322b
ms.technology: entity-framework-core
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: f34e92d9a3b10b6ff904257ccd047a8acdaad231
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="setting-explicit-values-for-generated-properties"></a>明確的值設定為產生的屬性

產生的屬性是的屬性 （可由 EF 或資料庫），其值會產生當實體已加入及/或更新。 請參閱[產生屬性](../modeling/generated-properties.md)如需詳細資訊。

可能會有您要設定產生的屬性，而不需要產生的外顯值的情況。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/ExplicitValuesGenerateProperties/)GitHub 上。

## <a name="the-model"></a>模型

在本文中使用的模型包含單一`Employee`實體。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>儲存期間新增明確的值

`Employee.EmploymentStarted`屬性已設定為具有新的實體 （使用預設值） 的資料庫所產生的值。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

下列程式碼會插入資料庫中的兩個員工。
* 第一個，指定任何值給`Employee.EmploymentStarted`屬性，以便維持設定為的 CLR 預設值`DateTime`。
* 秒，我們已設定的明確值`1-Jan-2000`。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

輸出顯示資料庫產生值的第一位員工，並使用第二個我們明確的值。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>明確值插入 SQL Server IDENTITY 資料行

依照慣例`Employee.EmployeeId`屬性是存放區產生`IDENTITY`資料行。

大部分的情況下，如上所示的方法可用於索引鍵屬性。 不過，將明確值插入 SQL Server`IDENTITY`資料行中，您必須手動啟用`IDENTITY_INSERT`之前先呼叫`SaveChanges()`。

> [!NOTE]  
> 我們有[功能要求](https://github.com/aspnet/EntityFramework/issues/703)在我們的待辦項目，SQL Server 提供者內自動執行此動作。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

輸出會顯示所提供的識別碼已儲存至資料庫。

``` Console
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>更新期間設定明確的值

`Employee.LastPayRaise`屬性已設定為具有由資料庫更新時所產生的值。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]  
> 根據預設，EF Core 將會擲回例外狀況，如果您嘗試儲存屬性設定為在更新期間產生的外顯值。 若要避免這個問題，您需要較低層級的中繼資料 API 下拉式清單，然後設定`AfterSaveBehavior`（如上所示）。

> [!NOTE]  
> **在 EF 核心 2.0 中的變更：**在舊版本中則包含儲存行為透過控制`IsReadOnlyAfterSave`旗標。 這個旗標已經認定為過時並取代`AfterSaveBehavior`。

也沒有觸發程序在資料庫中產生的值對`LastPayRaise`期間的資料行`UPDATE`作業。

[!code-sql[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

下列程式碼會增加資料庫中的兩個員工的薪資。
* 第一個，指定任何值給`Employee.LastPayRaise`屬性，因此它會維持設定為 null。
* 秒，我們設定一週前 （目付款後引發） 明確的值。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

輸出顯示資料庫產生值的第一位員工，並使用第二個我們明確的值。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
