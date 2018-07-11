---
title: 從 EF6 移植到 EF Core -驗證需求
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 65bdc8bb9574d37db697aa47c8e8c480cefcb4f7
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949110"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a>之前從 EF6 移植到 EF Core： 驗證您的應用程式需求

在開始移轉程序之前務必驗證 EF Core 符合您的應用程式的資料存取需求。

## <a name="missing-features"></a>遺漏的功能

請確定 EF Core 包含您需要在您的應用程式中使用的所有功能。 請參閱[功能比較](../features.md)的 EF Core 中設定的功能如何與 EF6 比較的詳細比較。 如果遺漏任何必要的功能，請確定您可以先移植到 EF Core 補償缺少這些功能。

## <a name="behavior-changes"></a>行為變更

這是 EF6 和 EF Core 之間的行為中的某些變更非完整清單。 請務必牢記您的連接埠為您的應用程式因為它們可能會變更您的應用程式的行為，但是不會顯示以編譯錯誤互換之後到 EF Core 的方式。

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet.Add/Attach 和圖形的行為

於 EF6，呼叫`DbSet.Add()`中遞迴搜尋來參考它的導覽屬性中的所有實體的實體結果。 任何實體，找不到，並已不會追蹤內容，也會標示為已新增。 `DbSet.Attach()` 行為相同，但所有的實體會標示為不變。

**EF Core 執行類似的遞迴搜尋，但有一些稍有不同的規則。**

*  根實體一定會處於要求的狀態 (加入`DbSet.Add`而不變的`DbSet.Attach`)。

*  **導覽屬性的遞迴搜尋期間找到的實體：**

    *  **如果實體的主索引鍵是產生的存放區**

        * 如果未設定的主索引鍵的值，狀態會設定為已新增。 主索引鍵值會被視為 「 未設定 」 指派的屬性類型的 CLR 預設值 (例如`0`針對`int`，`null`的`string`等。)。

        * 如果主索引鍵設定為值，狀態會設為不變。

    *  主索引鍵不是產生的資料庫，實體會放入相同的狀態，以 root 身分執行。

### <a name="code-first-database-initialization"></a>程式碼的第一個資料庫初始化

**EF6 中有大量的 magic，它會執行選取的資料庫連線，以及將資料庫初始化。這些規則包括：**

* 如果不執行任何設定，則 EF6 會選取 SQL Express 或 LocalDb 資料庫。

* 如果應用程式中的連接字串具有相同名稱做為內容是`App/Web.config`檔案，將會使用此連接。

* 如果資料庫不存在，它會建立它。

* 如果沒有任何模型中的資料表存在於資料庫中，目前模型的結構描述會加入至資料庫。 如果已啟用移轉，它們用來建立資料庫。

* 如果資料庫存在，而且 EF6 先前已建立結構描述，與目前模型的相容性檢查結構描述。 如果模型已變更，因為已建立結構描述，則會擲回例外狀況。

**EF Core 不會執行任何此識別常數。**

* 程式碼中，必須明確設定資料庫連接。

* 不會執行初始化。 您必須使用`DbContext.Database.Migrate()`以套用移轉 (或`DbContext.Database.EnsureCreated()`和`EnsureDeleted()`建立/刪除資料庫而不使用移轉)。

### <a name="code-first-table-naming-convention"></a>程式碼的第一個資料表命名慣例

EF6 執行複數表示服務，以計算預設的資料表名稱，此實體會對應至實體類別名稱。

EF Core 使用的名稱`DbSet`實體中公開衍生內容的屬性。 如果實體沒有`DbSet`用屬性，則類別名稱。
