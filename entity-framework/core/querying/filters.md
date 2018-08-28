---
title: 全域查詢篩選條件 - EF Core
author: anpete
ms.author: anpete
ms.date: 11/03/2017
ms.technology: entity-framework-core
uid: core/querying/filters
ms.openlocfilehash: 6b7a4069917c93015a218c131ff0d0a3920fb69d
ms.sourcegitcommit: 4467032fd6ca223e5965b59912d74cf88a1dd77f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2018
ms.locfileid: "42447683"
---
# <a name="global-query-filters"></a>全域查詢篩選條件

全域查詢篩選條件是中繼資料模型 (通常位於 *OnModelCreating*) 中套用到實體類型的 LINQ 查詢述詞 (布林運算式通常會傳遞到 LINQ *Where* 查詢運算子)。 這類篩選會自動套用至任何涉及這些「實體類型」(包含間接參考的「實體類型」) 的 LINQ 查詢 (例如，使用 Include 或直接導覽屬性參考)。 此功能的一些常見應用如下：

* **虛刪除**：實體類型會定義 *IsDeleted* 屬性。
* **多租用戶**：實體類型會定義 *TenantId* 屬性。

## <a name="example"></a>範例

下列範例示範如何使用全域查詢篩選條件，在簡單的部落格模型中實作虛刪除和多租用戶查詢行為。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFrameworkCore/tree/master/samples/QueryFilters) \(英文\)。

首先，定義實體：

[!code-csharp[Main](../../../efcore-repo/samples/QueryFilters/Program.cs#Entities)]

請注意 _Blog_ 實體上 __tenantId_ 欄位的宣告。 這會用來將每個 Blog 執行個體與特定租用戶產生關聯。 此外，還定義了 _Post_ 實體類型上的 _IsDeleted_ 屬性。 這會用來追蹤是否已將 _Post_ 執行個體「虛刪除」。 亦即，將執行個體標示為已刪除，而不實際移除底層資料。

接下來，使用 ```HasQueryFilter``` API，在 _OnModelCreating_ 中設定查詢篩選條件。

[!code-csharp[Main](../../../efcore-repo/samples/QueryFilters/Program.cs#Configuration)]

傳遞到 _HasQueryFilter_ 呼叫的述詞運算式，現在將針對那些類型自動套用到任何 LINQ 查詢。

> [!TIP]
> 請注意 DbContext 執行個體層級欄位的使用方式：```_tenantId``` 用來設定目前的租用戶。 模型層級篩選將會使用正確內容執行個體 (亦即，執行查詢的執行個體) 中的值。

## <a name="disabling-filters"></a>停用篩選條件

可能會使用 ```IgnoreQueryFilters()``` 運算子來停用個別 LINQ 查詢的篩選條件。

[!code-csharp[Main](../../../efcore-repo/samples/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>限制

全域查詢篩選條件具有下列限制：

* 篩選條件不能包含對導覽屬性的參考。
* 只能針對繼承階層的根實體類型定義篩選條件。
