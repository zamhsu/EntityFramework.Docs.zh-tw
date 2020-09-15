---
title: 為產生的屬性設定明確值 - EF Core
description: 針對設定為隨 Entity Framework Core 產生的屬性，明確設定值的相關資訊
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: efaa87356a78c4ec7e11d57c1effad776bd01cba
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072482"
---
# <a name="setting-explicit-values-for-generated-properties"></a>為產生的屬性設定明確值

產生的屬性係指其值會在新增和/或更新實體時產生 (由 EF 或資料庫產生) 的屬性。 如需詳細資訊，請參閱[產生的屬性](xref:core/modeling/generated-properties)。

在一些情況下，您可能會想要為產生的屬性設定明確值，而不是使用產生的值。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/ExplicitValuesGenerateProperties/) \(英文\)。

## <a name="the-model"></a>模型

本文中使用的模型包含單一 `Employee` 實體。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>在新增期間儲存明確值

`Employee.EmploymentStarted` 屬性已設定為採用資料庫為新實體產生的值 (使用預設值)。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

下列程式碼會將兩個員工插入到資料庫中。

* 針對第一個員工，由於未指派任何值給 `Employee.EmploymentStarted` 屬性，因此它會維持設定為 `DateTime` 的 CLR 預設值。
* 針對第二個員工，我們已設定明確值 `1-Jan-2000`。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

輸出顯示資料庫為第一個員工產生了一個值，而我們的明確值則用於第二個員工。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>將明確值插入到 SQL Server IDENTITY 資料行中

依照慣例，`Employee.EmployeeId` 屬性是一個存放區產生的 `IDENTITY` 資料行。

就大多數情況而言，上面所示的方法對索引鍵屬性都適用。 不過，若要將明確值插入到 SQL Server `IDENTITY` 資料行中，您將必須在呼叫 `SaveChanges()` 之前，先手動啟用 `IDENTITY_INSERT`。

> [!NOTE]  
> 在我們的待辦項目上有一個在 SQL Server 提供者內自動進行此操作的[功能要求](https://github.com/aspnet/EntityFramework/issues/703)。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

輸出顯示所提供的識別碼已儲存至資料庫。

``` Console
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>在更新期間設定明確值

`Employee.LastPayRaise` 屬性已設定為採用資料庫在更新期間產生的值。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]  
> 根據預設，如果您嘗試針對已設定為要在更新期間產生的屬性儲存明確值，EF Core 將會擲回例外狀況。 若要避免此問題，您必須降到較低層級的中繼資料 API 並設定 `AfterSaveBehavior` (如以上所示)。

> [!NOTE]  
> **EF Core 2.0 中的變更：** 在舊版中，是透過 `IsReadOnlyAfterSave` 旗標來控制儲存後的行為。 此旗標已淘汰並被 `AfterSaveBehavior` 取代。

資料庫中也有一個可在 `UPDATE` 作業期間為 `LastPayRaise` 資料行產生值的觸發程序。

[!code-sql[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

下列程式碼會為資料庫中的兩個員工加薪。

* 針對第一個員工，由於未指派任何值給 `Employee.LastPayRaise` 屬性，因此它會維持設定為 Null。
* 針對第二個員工，我們已將明確值設定為一週前 (回溯加薪日期)。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

輸出顯示資料庫為第一個員工產生了一個值，而我們的明確值則用於第二個員工。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
