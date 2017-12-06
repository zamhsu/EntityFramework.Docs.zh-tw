---
title: "全域查詢篩選器的 EF 核心"
author: anpete
ms.author: anpete
ms.date: 11/03/2017
ms.technology: entity-framework-core
uid: core/querying/filters
ms.openlocfilehash: 4e3c3c99d155f69e00fed99c415f519808ea1a68
ms.sourcegitcommit: 6e379265e4f087fb7cf180c824722c81750554dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="global-query-filters"></a>全域查詢篩選器

全域查詢篩選器是 LINQ 查詢述詞 (布林值運算式通常傳遞至 LINQ*其中*查詢運算子) 套用至實體類型的中繼資料模型中 (通常在*OnModelCreating*)。 這類篩選器會自動套用至涉及這些實體類型，包括實體類型的直接或間接，例如透過使用包含參考導覽屬性參考任何 LINQ 查詢。 這項功能的一些常見的應用程式如下：

* **虛刪除**-實體類型定義*IsDeleted*屬性。
* **多租用戶**-實體類型定義*TenantId*屬性。

## <a name="example"></a>範例

下列範例會示範如何使用全域查詢篩選器來實作簡單的部落格模型中的虛刪除和多租用戶的查詢行為。

> [!TIP]
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters)GitHub 上。

首先，定義實體：

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Entities)]

請注意 _ 宣告_tenantId_欄位_部落格_實體。 這會用來將每個部落格執行個體與特定租用戶產生關聯。 也定義是_IsDeleted_屬性_Post_實體類型。 這使用此選項以追蹤是否_Post_執行個體已 「 虛刪除 」。 也就是執行個體標示為已刪除 withouth 實際移除基礎資料。

接下來，設定中的查詢篩選器_OnModelCreating_使用```HasQueryFilter```應用程式開發介面。

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Configuration)]

述詞運算式傳遞至_HasQueryFilter_呼叫，現在將會自動套用至任何 LINQ 查詢，針對這些型別。

> [!TIP]
> 請注意使用 DbContext 執行個體層級欄位：```_tenantId```用來設定目前的租用戶。 模型層級篩選器會使用正確的內容執行個體的值。 也就是正在執行查詢的執行個體。

## <a name="disabling-filters"></a>停用篩選器

篩選器可能會停用個別的 LINQ 查詢使用```IgnoreQueryFilters()```運算子。

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>限制

全域查詢篩選器有下列限制：

* 篩選條件不能包含參考導覽屬性。
* 只可以定義篩選，根實體類型的繼承階層架構。
