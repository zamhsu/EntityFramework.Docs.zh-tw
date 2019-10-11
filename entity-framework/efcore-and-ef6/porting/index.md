---
title: 從 EF6 移植至 EF Core - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 826b58bd-77b0-4bbc-bfcd-24d1ed3a8f38
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 77096b9bffba6b8c2a3d7bfb0c2e41e2d170a7db
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182084"
---
# <a name="porting-from-ef6-to-ef-core"></a>從 EF6 移植至 EF Core

因為 EF Core 在本質上有所變更，我們不建議嘗試將 EF6 應用程式移至 EF Core，除非您有充足的理由進行該變更。
您應該將從 EF6 移至 EF Core 視為移植，而非升級。

> [!IMPORTANT]
> 在開始移植程序之前，請務必確認 EF Core 符合應用程式的資料存取需求。

## <a name="missing-features"></a>遺漏的功能

請確定 EF Core 具有應用程式中所需的所有功能。 如需 EF Core 中的功能集與 EF6 相比的詳細比較，請參閱[功能比較](xref:efcore-and-ef6/index)。 如果遺漏任何必要的功能，請確保在移植到 EF Core 之前可以彌補這些功能的不足。

## <a name="behavior-changes"></a>行為變更

這是 EF6 和 EF Core 之間一些行為變更的不完整清單。 在移植應用程式時，請務必牢記這些問題，因為它們可能會變更應用程式的行為方式，但在切換到 EF Core 之後，不會顯示為編譯錯誤。

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet.Add/附加和圖形行為

在 EF6 中，在實體上呼叫 `DbSet.Add()` 會導致以遞迴方式搜尋其導覽屬性中參考的所有實體。 任何找到的實體 (而且尚未執行內容追蹤)，也會標示為已加入。 `DbSet.Attach()` 行為相同，不同之處在於所有實體都會標示為未變更。

**EF Core 會執行類似的遞迴搜尋，但有一些略有不同的規則。**

*  根實體一律處於要求的狀態 (針對 `DbSet.Add` 進行新增，針對 `DbSet.Attach` 則未變更)。

*  **針對在遞迴搜尋導覽屬性期間找到的實體：**

    *  **如果實體的主索引鍵是存放區產生的**

        * 如果主索引鍵未設定為值，則狀態會設定為「已加入」。 如果為主索引鍵指派了屬性類型的 CLR 預設值 (例如，`0` 代表 `int`，`null` 代表 `string` 等)，則會將它視為「未設定」。

        * 如果主索引鍵設定為值，則狀態會設定為「未變更」。

    *  如果主索引鍵不是資料庫產生的，實體會置於與根相同的狀態。

### <a name="code-first-database-initialization"></a>Code First 資料庫初始化

**EF6 在選取資料庫連接和初始化資料庫方面，有非常神奇之處。其中一些規則包括：**

* 如果沒有執行任何設定，EF6 將在 SQL Express 或 LocalDb 上選取一個資料庫。

* 如果與執行內容同名的連接字串位於應用程式 `App/Web.config` 檔案中，則會使用此連線。

* 如果資料庫不存在，則會建立資料庫。

* 如果模型中沒有任何資料表存在於資料庫中，則會將目前模型的結構描述新增至資料庫。 如果已啟用移轉，則會使用它們來建立資料庫。

* 如果資料庫存在，而且 EF6 先前已建立結構描述，則會檢查結構描述是否與目前的模型相容。 如果模型在建立結構描述之後已變更，就會擲回例外狀況。

**EF Core 無法執行任何此類操作。**

* 資料庫連接必須在程式碼中明確設定。

* 不會執行任何初始化。 您必須使用 `DbContext.Database.Migrate()` 來套用移轉 (或 `DbContext.Database.EnsureCreated()` 和 `EnsureDeleted()`，以建立/刪除資料庫，而不使用移轉)。

### <a name="code-first-table-naming-convention"></a>Code First 資料表命名慣例

EF6 會透過複數服務執行實體類別名稱，以計算實體所對應的預設資料表名稱。

EF Core 會使用在衍生內容上公開實體的 `DbSet` 屬性名稱。 如果實體沒有 `DbSet` 屬性，則會使用類別名稱。
