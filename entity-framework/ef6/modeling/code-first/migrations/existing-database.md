---
title: 使用現有的資料庫 Code First 移轉 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: f0cc4f93-67dd-4664-9753-0a9f913814db
ms.openlocfilehash: eb7948eafb1322cabcf69b47bd5411f762fe8498
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182588"
---
# <a name="code-first-migrations-with-an-existing-database"></a>現有資料庫的 Code First 移轉
> [!NOTE]
> **僅限 ef 4.3** ，此頁面中所討論的功能、api 等已在 Entity Framework 4.1 中引進。 如果您使用的是較早版本，則不適用部分或全部的資訊。

本文說明如何使用 Code First 移轉搭配現有的資料庫，而不是由 Entity Framework 所建立。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，則必須先閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)，才能繼續進行。

## <a name="screencasts"></a>螢幕錄製影片

如果您想要觀賞螢幕錄製影片，而不是閱讀本文，下列兩段影片涵蓋的內容與本文相同。

### <a name="video-one-migrations---under-the-hood"></a>影片一：「遷移-幕後」

[此螢幕錄製影片](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood)涵蓋遷移如何追蹤和使用模型的相關資訊，以偵測模型的變更。

### <a name="video-two-migrations---existing-databases"></a>影片二：「遷移-現有的資料庫」

以上一段影片的概念為基礎，[此螢幕錄製影片](https://channel9.msdn.com/blogs/ef/migrations-existing-databases)涵蓋如何啟用和使用現有資料庫的遷移。

## <a name="step-1-create-a-model"></a>步驟 1:建立模型

您的第一個步驟是建立以現有資料庫為目標的 Code First 模型。 [現有資料庫的 Code First](~/ef6/modeling/code-first/workflows/existing-database.md)主題提供如何執行此動作的詳細指引。

>[!NOTE]
> 請務必遵循本主題中的其餘步驟，再對需要變更資料庫架構的模型進行任何變更。 下列步驟需要模型與資料庫架構同步。

## <a name="step-2-enable-migrations"></a>步驟 2:啟用遷移

下一個步驟是啟用遷移。 您可以在 [套件管理員主控台] 中執行 [**啟用-遷移**] 命令來完成此動作。

此命令會在您的解決方案中建立名為「遷移」的資料夾，並在其中放入名為 Configuration 的單一類別。 設定類別是您為應用程式設定遷移的位置，您可以在[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)主題中找到更多的相關資訊。

## <a name="step-3-add-an-initial-migration"></a>步驟 3：新增初始遷移

一旦建立遷移並套用至本機資料庫，您可能也會想要將這些變更套用至其他資料庫。 例如，您的本機資料庫可能是測試資料庫，而且您最終可能也會想要將變更套用到生產資料庫和/或其他開發人員測試資料庫。 此步驟有兩個選項，而您應挑選的選項取決於其他任何資料庫的架構是否空白，或目前是否符合本機資料庫的架構。

-   **Option 一個：使用現有的架構做為起點。** 當未來將套用其他的資料庫時，您應該使用這個方法，其架構與您的本機資料庫相同。 例如，如果您的本機測試資料庫目前符合生產資料庫的 v1，而且您稍後將會套用這些遷移，將您的生產資料庫更新為 v2，您可能會使用此專案。
-   **Option 二：使用空白資料庫做為起點。** 當未來將套用的其他資料庫是空的（或尚不存在）時，您應該使用此方法。 例如，如果您使用測試資料庫開始開發應用程式，但未使用遷移，則您可能會使用此設定，而您稍後想要從頭建立生產資料庫。

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>選項一：使用現有的架構做為起點

Code First 移轉會使用儲存在最新的遷移中的模型快照集來偵測模型的變更（您可以在[小組環境的 Code First 移轉](~/ef6/modeling/code-first/migrations/teams.md)中找到詳細資訊）。 因為我們將假設資料庫已經有目前模型的架構，所以我們會產生一個空的（無 op）遷移，其以目前的模型做為快照集。

1.  執行 [套件管理員主控台] 中的 [**新增-遷移 InitialCreate – IgnoreChanges** ] 命令。 這會使用目前的模型做為快照集來建立空的遷移。
2.  在 [套件管理員主控台] 中執行 [**更新-資料庫**] 命令。 這會將 InitialCreate 遷移套用至資料庫。 因為實際的遷移並未包含任何變更，所以只會將資料列新增至 \_ @ no__t-1MigrationsHistory 資料表，表示已套用此遷移。

### <a name="option-two-use-empty-database-as-a-starting-point"></a>選項二：使用空白資料庫做為起點

在此情況下，我們需要進行遷移，才能從頭開始建立整個資料庫，包括已存在於本機資料庫中的資料表。 我們即將產生 InitialCreate 遷移，其中包含建立現有架構的邏輯。 接著，我們會讓現有的資料庫看起來像這種遷移已經套用。

1.  在 [套件管理員主控台] 中執行 [**新增-遷移 InitialCreate** ] 命令。 這會建立一個遷移來建立現有的架構。
2.  將新建立之遷移的 Up 方法中的所有程式碼標記為批註。 這可讓我們「將」遷移到本機資料庫，而不需嘗試重新建立所有資料表等等。
3.  在 [套件管理員主控台] 中執行 [**更新-資料庫**] 命令。 這會將 InitialCreate 遷移套用至資料庫。 因為實際的遷移不會包含任何變更（因為我們暫時將其批註化），所以只會將資料列新增至 \_ @ no__t-1MigrationsHistory 資料表，表示已套用此遷移。
4.  將 Up 方法中的程式碼取消批註。 這表示當此遷移套用至未來的資料庫時，會透過遷移來建立已存在於本機資料庫中的架構。

## <a name="things-to-be-aware-of"></a>要注意的事項

針對現有的資料庫使用遷移時，您必須注意幾件事。

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>預設/計算的名稱可能與現有的架構不相符

在 scaffold 遷移時，遷移會明確指定資料行和資料表的名稱。 不過，在套用遷移時，還有其他資料庫物件可供遷移計算的預設名稱。 這包括索引和外鍵條件約束。 以現有的架構為目標時，這些匯出名稱可能不會符合資料庫中實際存在的內容。

以下是一些需要注意的範例：

@no__t 0If 您使用了 ' Option One：使用現有的架構做為步驟3： ** 的起點

-   如果模型中的未來變更需要變更或卸載以不同方式命名的其中一個資料庫物件，您將需要修改 scaffold 遷移來指定正確的名稱。 遷移 Api 具有選擇性的 Name 參數，可讓您執行此動作。
    例如，您現有的架構可能會有一個具有 BlogId 外鍵資料行的 Post 資料表，其索引名為 IndexFk @ no__t-0BlogId。 不過，根據預設，遷移會預期此索引會命名為 IX @ no__t-0BlogId。 如果您對模型所做的變更會導致卸載此索引，您將需要修改 scaffold DropIndex 呼叫以指定 IndexFk @ no__t-0BlogId 名稱。

@no__t 0If 您使用了 ' Option 二：使用空白資料庫作為步驟3： ** 的起點

-   嘗試對本機資料庫執行初始遷移的向下方法（也就是還原為空的資料庫）可能會失敗，因為遷移會嘗試使用不正確的名稱來卸載索引和外鍵條件約束。 這只會影響您的本機資料庫，因為將會使用初始遷移的 Up 方法從頭開始建立其他資料庫。
    如果您想要將現有的本機資料庫降級為空的狀態，您可以藉由卸載資料庫或卸載所有資料表，以手動方式執行這項操作。 在初始降級之後，將會使用預設名稱重新建立所有的資料庫物件，因此這個問題不會再次出現。
-   如果模型中的未來變更需要變更或卸載以不同方式命名的其中一個資料庫物件，這將無法用於您現有的本機資料庫，因為名稱不會符合預設值。 不過，它會針對「從頭開始」建立的資料庫使用，因為它們會使用遷移所選擇的預設名稱。
    您可以在本機現有資料庫上手動進行這些變更，或考慮讓遷移從頭開始重新建立資料庫–因為它會在其他電腦上。
-   使用初始遷移的 Up 方法所建立的資料庫，可能會與本機資料庫略有不同，因為將會使用計算出的索引和外鍵條件約束的預設名稱。 最後，您可能也會有額外的索引，因為在預設的情況下，遷移將會在外鍵資料行上建立索引，這在您的原始本機資料庫中可能不會是這種情況。

### <a name="not-all-database-objects-are-represented-in-the-model"></a>並非所有資料庫物件都是以模型表示

遷移不會處理不屬於您模型的資料庫物件。 這可能包括視圖、預存程式、許可權、不屬於您模型的資料表、其他索引等等。

以下是一些需要注意的範例：

-   不論您在「步驟3」中選擇哪一個選項，如果您模型中的未來變更需要變更或卸載這些額外的物件，遷移將不知道要進行這些變更。 例如，如果您卸載的資料行上有額外的索引，則遷移並不知道要卸載索引。 您必須手動將此新增至 scaffold 遷移。
-   如果您使用了 ' Option 二：使用空白資料庫做為起點 '，這些額外的物件將不會由初始遷移的 Up 方法建立。
    如有需要，您可以修改 Up 和 Down 方法，以處理這些額外的物件。 對於在遷移 API 中原本不支援的物件（例如 views），您可以使用[Sql](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx)方法來執行原始 sql 來建立/卸載它們。
