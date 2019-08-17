---
title: 從 EF6 移植到 EF Core -驗證需求
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 07caa39e8a56db71e493331ea9f0286309abc52a
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565353"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a>從 EF6 移植到 EF Core 之前:驗證您應用程式的需求

在開始移轉程序之前務必驗證 EF Core 符合您的應用程式的資料存取需求。

## <a name="missing-features"></a>遺漏的功能

請確定 EF Core 包含您需要在您的應用程式中使用的所有功能。 請參閱[功能比較](../features.md)的 EF Core 中設定的功能如何與 EF6 比較的詳細比較。 如果遺漏任何必要的功能，請確定您可以先移植到 EF Core 補償缺少這些功能。

## <a name="behavior-changes"></a>行為變更

這是 EF6 和 EF Core 之間的行為中的某些變更非完整清單。 請務必牢記您的連接埠為您的應用程式因為它們可能會變更您的應用程式的行為，但是不會顯示以編譯錯誤互換之後到 EF Core 的方式。

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet。新增/附加和圖形行為

在 EF6 中, `DbSet.Add()`在實體上呼叫會導致以遞迴方式搜尋其導覽屬性中參考的所有實體。 任何找到的實體, 而且尚未被內容追蹤, 也會標示為已加入。 `DbSet.Attach()`行為相同, 不同之處在于所有實體都會標示為未變更。

**EF Core 執行類似的遞迴搜尋，但有一些稍有不同的規則。**

*  根實體一律處於要求的狀態 (針對進行`DbSet.Add`新增, 且`DbSet.Attach`不變更)。

*  **針對在遞迴搜尋導覽屬性期間找到的實體:**

    *  **如果實體的主要金鑰是存放區產生的**

        * 如果主要金鑰未設定為值, 則狀態會設定為 [已加入]。 如果主要金鑰值被指派屬性類型的`0` CLR 預設值 (例如, for `int`、 `null` for `string`等等), 則會將其視為「未設定」。

        * 如果主要金鑰設定為值, 則狀態會設定為 [未變更]。

    *  如果主要索引鍵不是資料庫產生的, 實體會置於與根相同的狀態。

### <a name="code-first-database-initialization"></a>Code First 資料庫初始化

**EF6 在選取資料庫連接和初始化資料庫方面, 有相當大的神奇之處。其中一些規則包括:**

* 如果沒有執行任何設定, EF6 會選取 SQL Express 或 LocalDb 上的資料庫。

* 如果與內容同名的連接字串位於應用程式`App/Web.config`檔案中, 則會使用此連接。

* 如果資料庫不存在, 則會加以建立。

* 如果模型中沒有任何資料表存在於資料庫中, 則會將目前模型的架構加入至資料庫。 如果已啟用遷移, 則會使用它們來建立資料庫。

* 如果資料庫存在, 而且 EF6 先前已建立架構, 則會檢查架構是否與目前的模型相容。 如果在建立架構之後, 模型已經變更, 就會擲回例外狀況 (exception)。

**EF Core 不會執行任何此識別常數。**

* 資料庫連接必須在程式碼中明確設定。

* 不會執行任何初始化。 您必須使用`DbContext.Database.Migrate()`來套用遷移 (或`DbContext.Database.EnsureCreated()`和`EnsureDeleted()` , 以建立/刪除資料庫, 而不使用遷移)。

### <a name="code-first-table-naming-convention"></a>Code First 資料表命名慣例

EF6 會透過複數表示服務來執行實體類別名稱, 以計算實體所對應的預設資料表名稱。

EF Core 使用的名稱`DbSet`實體中公開衍生內容的屬性。 如果實體`DbSet`沒有屬性, 則會使用類別名稱。
