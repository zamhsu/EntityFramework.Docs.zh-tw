---
title: 從 EF6 移植到 EF Core-驗證需求
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 2f45039e63546d266ec6ce0bfa66ef7e9fb3d7e7
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052858"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a>之前從 EF6 移植到 EF 核心： 驗證您的應用程式需求

在開始移轉程序之前務必驗證 EF 核心符合您的應用程式的資料存取需求。

## <a name="missing-features"></a>遺失的功能

請確定 EF 核心包含您需要在您的應用程式中使用的所有功能。 請參閱[功能比較](../features.md)的 EF 核心中設定的功能如何與 EF6 比較的詳細比較。 如果遺漏任何必要的功能，請確定您可以先移植到 EF 核心補償缺少這些功能。

## <a name="behavior-changes"></a>行為變更

這是 EF6 和 EF 核心之間的行為中的某些變更非完整清單。 請務必牢記您的連接埠為您的應用程式因為它們可能會變更您的應用程式的行為，但是不會顯示以編譯錯誤互換之後到 EF Core 的方式。

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet.Add/Attach 和圖形行為

EF6，在呼叫`DbSet.Add()`中遞迴搜尋，它的導覽屬性中所參考的所有實體的實體結果。 任何實體，找不到，並會不已經受到內容追蹤時，也會被標記為加入。 `DbSet.Attach()`行為相同，除了所有實體會都標示為不變。

**EF 核心執行類似的遞迴搜尋，但有一些稍有不同的規則。**

*  根實體一律是所要求的狀態 (針對加入`DbSet.Add`和不變`DbSet.Attach`)。

*  **導覽屬性的遞迴搜尋期間找到的實體：**

    *  **如果實體的主索引鍵是產生的存放區**

        * 如果未設定的主索引鍵的值，狀態會設為加入的。 主索引鍵值被視為 「 未設定 」 如果它被指派為屬性類型的 CLR 預設值 (也就是`0`如`int`，`null`如`string`等。)。

        * 如果主索引鍵的值設定，狀態會設定為不變。

    *  如果主索引鍵不是產生的資料庫，實體是放在根狀態相同。

### <a name="code-first-database-initialization"></a>第一個資料庫的初始化程式碼

**EF6 有大量的識別常數，它會執行選取的資料庫連接和初始化資料庫。這些規則包括：**

* 如果不執行任何設定，則 EF6 將選取的資料庫，SQL Express 或 LocalDb。

* 連接字串是否具有相同名稱做為內容的應用程式中`App/Web.config`檔案，將會使用此連接。

* 如果資料庫不存在，則會建立它。

* 如果沒有任何模型中的資料表存在於資料庫中，目前模型的結構描述會加入至資料庫。 如果已啟用移轉，它們用來建立資料庫。

* 如果資料庫存在，而且 EF6 先前建立的結構描述，與目前模型的相容性檢查的結構描述。 如果模型已變更，因為建立結構描述，則會擲回例外狀況。

**EF 核心不會執行任何此識別常數。**

* 程式碼中，必須明確設定資料庫連接。

* 不會執行初始化。 您必須使用`DbContext.Database.Migrate()`套用移轉 (或`DbContext.Database.EnsureCreated()`和`EnsureDeleted()`來建立/刪除資料庫而不需使用 移轉)。

### <a name="code-first-table-naming-convention"></a>程式碼的第一個資料表命名慣例

EF6 會透過計算預設的資料表名稱對應至實體的複數表示服務執行的實體類別名稱。

EF 核心使用的名稱`DbSet`實體中公開衍生內容的屬性。 如果沒有實體`DbSet`用屬性，然後才是類別名稱。
