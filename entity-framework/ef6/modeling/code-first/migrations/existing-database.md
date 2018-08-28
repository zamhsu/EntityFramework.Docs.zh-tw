---
title: 與現有的資料庫-EF6 code First 移轉
author: divega
ms.date: 2016-10-23
ms.assetid: f0cc4f93-67dd-4664-9753-0a9f913814db
ms.openlocfilehash: 06aabf3f57ca451f4d9cba469f6de40fd9aa8f23
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998193"
---
# <a name="code-first-migrations-with-an-existing-database"></a>與現有的資料庫的 code First 移轉
> [!NOTE]
> **EF4.3 及更新版本僅**-功能、 Api、 Entity Framework 4.1 中導入等本頁所述。 如果您使用的是較早版本，則不適用部分或全部的資訊。

本文章涵蓋使用現有的資料庫，一個不由 Entity Framework Code First 移轉。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果不這麼做，則您將需要閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)再繼續進行。

## <a name="screencasts"></a>螢幕錄製影片

如果您想而不是觀看螢幕錄製影片比閱讀這篇文章，下列兩個影片涵蓋這份文件相同的內容。

### <a name="video-one-migrations---under-the-hood"></a>影片的一個:"移轉-在幕後"

[這段螢幕錄製影片](http://channel9.msdn.com/blogs/ef/migrations-under-the-hood)涵蓋如何移轉追蹤，並使用模型的相關資訊，來偵測模型變更。

### <a name="video-two-migrations---existing-databases"></a>影片的兩個: 「 移轉-現有資料庫 」

在上一段影片中，從概念上建置[這段螢幕錄製影片](http://channel9.msdn.com/blogs/ef/migrations-existing-databases)說明如何啟用及使用現有的資料庫中的移轉。

## <a name="step-1-create-a-model"></a>步驟 1： 建立模型

您的第一個步驟會建立您的現有資料庫為目標的 Code First 模型。 [Code First 至現有的資料庫](~/ef6/modeling/code-first/workflows/existing-database.md)主題提供如何執行這項操作的詳細指導方針。

>[!NOTE]
> 請務必遵循本主題中步驟的其餘部分之前對您的模型會需要變更資料庫結構描述進行任何變更。 下列步驟需要為同步模型與資料庫結構描述。

## <a name="step-2-enable-migrations"></a>步驟 2： 啟用移轉

下一個步驟是啟用移轉。 您可以藉由執行**Enable-migrations**命令在套件管理員主控台。

此命令會呼叫移轉，在方案中建立資料夾，並將單一類別稱為 「 設定其內部放置。 設定類別可讓您設定移轉應用程式，您可以深入了解它在[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)主題。

## <a name="step-3-add-an-initial-migration"></a>步驟 3： 新增初始移轉

一旦移轉已建立並套用至本機資料庫，您也可以在套用這些變更至其他資料庫。 比方說，您的本機資料庫可能是測試資料庫和您最終需要也將變更套用至生產環境資料庫和/或其他開發人員測試資料庫。 有兩個選項，此步驟中，您就應該挑選一個取決是空的或目前符合的本機資料庫結構描述的任何其他的資料庫結構描述。

-   **選項 1： 使用現有的結構描述做為起點。** 其他移轉將會套用至未來的資料庫會有相同的結構描述，因為目前有您的本機資料庫時，您應該使用這種方法。 例如，您可能會以此如果您的本機測試資料庫目前符合 v1 的生產資料庫，而且您稍後會套用這些移轉，以更新您的生產資料庫為 v2。
-   **選項 2： 使用空的資料庫做為起點。** 其他移轉將會套用至未來的資料庫是空的 （或尚不存在） 時，您應該使用這種方法。 例如，您可能會以此如果您開始開發您的應用程式使用的測試資料庫，但不使用移轉，您將會稍後想要從頭開始建立實際執行的資料庫。

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>選項 1： 使用現有的結構描述做為起點

Code First 移轉使用快照集儲存在最新的移轉中的模型來偵測模型的變更 (您可以找到詳細說明這[小組環境中的 Code First 移轉](~/ef6/modeling/code-first/migrations/teams.md))。 因為我們假設資料庫已經有目前模型的結構描述，我們將會產生空的 （無作業） 移轉具有目前的模型，當做快照集。

1.  執行**Add-migration 為 InitialCreate – IgnoreChanges**命令在套件管理員主控台。 這與目前模型中建立空白的移轉，當做快照集。
2.  執行**Update-database**命令在套件管理員主控台。 這會套用為 InitialCreate 移轉至資料庫。 在實際移轉未包含任何變更，因為它將只會新增至一個資料列\_ \_MigrationsHistory 資料表表示已套用此移轉。

### <a name="option-two-use-empty-database-as-a-starting-point"></a>選項 2： 使用做為起點的空白資料庫

在此案例中，我們必須要能夠從頭 – 包括已出現在本機資料庫中的資料表建立整個資料庫的移轉。 我們將產生的狀態為 InitialCreate 移轉，其中包含建立現有的結構描述的邏輯。 接著，我們要讓我們看起來像是已套用此移轉現有的資料庫。

1.  執行**Add-migration 為 InitialCreate**命令在套件管理員主控台。 這會建立建立現有的結構描述移轉。
2.  新建立的移轉最多方法中的所有程式碼註解化。 這可讓我們 '' 將移轉套用至本機資料庫但不會嘗試重新建立所有資料表等已存在。
3.  執行**Update-database**命令在套件管理員主控台。 這會套用為 InitialCreate 移轉至資料庫。 由於在實際移轉未包含任何變更 （因為我們暫時標記為註解方式），它將只會新增至一個資料列\_ \_MigrationsHistory 資料表表示已套用此移轉。
4.  取消註解中的總方法的程式碼。 這表示，當此移轉套用至未來的資料庫，在本機資料庫中已經有結構描述就會建立藉由移轉。

## <a name="things-to-be-aware-of"></a>要注意的事項

有幾件事，您需要使用移轉對現有的資料庫時應該注意。

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>預設/計算的名稱可能不符合現有的結構描述

移轉會在它則移轉時，明確指定資料行和資料表的名稱。 不過，有其他資料庫物件時套用移轉，移轉會計算的預設名稱。 這包括索引和 foreign key 條件約束。 當目標設為現有的結構描述，這些導出的名稱可能不符合實際存在於您的資料庫。

當您需要特別注意這一點時，以下是一些範例：

**如果您使用 '一個選項： 使用現有的結構描述做為起點' 從步驟 3:**

-   如果您在模型中的未來變更需要變更或卸除其中一個名稱不同的資料庫物件，您必須修改 scaffold 的移轉至指定的正確名稱。 移轉 Api 有選擇性的 Name 參數，可讓您執行這項操作。
    例如，您現有的結構描述可能有 BlogId 外部索引鍵資料行具有名為 IndexFk 索引的 Post 資料表\_BlogId。 不過，根據預設移轉預期這個索引命名為 IX\_BlogId。 如果您變更您的模型會導致卸除此索引時，您必須修改包含 scaffold 的 DropIndex 呼叫，可指定 IndexFk\_BlogId 名稱。

**如果您使用 '兩個選項： 使用空的資料庫做為起點' 從步驟 3:**

-   嘗試對您的本機資料庫執行初始移轉 （也就是空的資料庫即還原） 的清單方法可能會失敗，因為移轉將會嘗試卸除索引和 foreign key 條件約束使用不正確的名稱。 因為其他資料庫會使用初始移轉的總方法的從頭開始建立，這只會影響您的本機資料庫。
    如果您想要將現有本機資料庫降級至空白的狀態是最簡單的方式手動執行此動作，藉由卸除資料庫，或卸除所有資料表。 之後會重新建立所有資料庫物件，預設名稱與此初始降級，因此這個問題不會提供本身一次。
-   如果您在模型中的未來變更需要變更或卸除其中一個名稱不同的資料庫物件，這將無法運作對您現有的本機資料庫 – 由於名稱不符合預設值。 不過，它就能對資料庫所建立 '從頭'，因為它們會使用移轉所選擇的預設名稱。
    您可以手動進行這些變更，在您本機的現有資料庫，或考慮讓移轉重新建立您的資料庫，從零開始 – 它會在其他電腦上。
-   使用初始移轉的方法所建立的資料庫可能稍有不同的本機資料庫自索引的導出的預設名稱，並將使用 foreign key 條件約束。 您也會有額外的索引，移轉將會建立外部索引鍵資料行上索引根據預設，這可能不是原始的本機資料庫中的案例。

### <a name="not-all-database-objects-are-represented-in-the-model"></a>並非所有的資料庫物件都代表在模型中

藉由移轉不會處理不屬於您的模型的資料庫物件。 這可能包括檢視、 預存程序、 權限、 資料表，並不屬於您的模型、 額外的索引等等。

當您需要特別注意這一點時，以下是一些範例：

-   不論選擇您已選擇在步驟 3，如果您在模型中的未來變更需要變更或卸除這些額外的物件移轉不會知道要進行這些變更。 比方說，如果您卸除的資料行有其他的索引，移轉將不會知道要卸除索引。 您必須手動加入至包含 scaffold 的移轉。
-   如果您使用 ' 兩個選項： 使用空的資料庫做為起點 '，這些額外的物件將不會建立初始移轉的總方法。
    您可以修改向上，而在您想除如果負責這些其他物件的方法。 您可以使用原生不支援移轉 API – 例如檢視表-中的物件[Sql](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx)方法來執行原始的 SQL 來建立/卸除它們。
