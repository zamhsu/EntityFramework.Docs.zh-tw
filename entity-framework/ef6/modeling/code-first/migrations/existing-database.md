---
title: Code First 移轉現有的資料庫-EF6
description: Code First 移轉 Entity Framework 6 中的現有資料庫
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/existing-database
ms.openlocfilehash: 5dd38f3fb9a59805a9b23b7da5e770995866f715
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072105"
---
# <a name="code-first-migrations-with-an-existing-database"></a>Code First 移轉現有的資料庫
> [!NOTE]
> **Ef 4.3 僅限** ，本頁面所討論的功能、api 等已于 Entity Framework 4.1 中引進。 如果您使用的是較早版本，則不適用部分或全部的資訊。

本文涵蓋如何使用 Code First 移轉搭配現有的資料庫，而不是由 Entity Framework 所建立。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，就必須先閱讀 [Code First 移轉](xref:ef6/modeling/code-first/migrations/index) ，再繼續進行操作。

## <a name="screencasts"></a>螢幕錄製影片

如果您想要觀看螢幕錄製影片，而不是閱讀本文，下列兩段影片涵蓋與本文相同的內容。

### <a name="video-one-migrations---under-the-hood"></a>影片1：「正在進行的遷移-幕後」

[此螢幕錄製影片](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) 涵蓋了遷移如何追蹤和使用模型的相關資訊，以偵測模型變更。

### <a name="video-two-migrations---existing-databases"></a>影片二：「遷移-現有的資料庫」

以上一段影片的概念為基礎， [此螢幕錄製影片](https://channel9.msdn.com/blogs/ef/migrations-existing-databases) 涵蓋如何啟用和使用現有資料庫的遷移。

## <a name="step-1-create-a-model"></a>步驟1：建立模型

您的第一個步驟將是建立以現有資料庫為目標的 Code First 模型。 [現有資料庫的 Code First](xref:ef6/modeling/code-first/workflows/existing-database)主題提供有關如何進行此作業的詳細指引。

>[!NOTE]
> 在對需要變更資料庫架構的模型進行任何變更之前，請務必遵循本主題中的其餘步驟。 下列步驟需要將模型與資料庫架構同步。

## <a name="step-2-enable-migrations"></a>步驟2：啟用遷移

下一步是啟用遷移。 若要這麼做，您可以在封裝管理員主控台中執行 [ **啟用-遷移** ] 命令。

此命令會在您的解決方案中建立名為「遷移」的資料夾，並在其中放置稱為「設定」的單一類別。 設定類別可讓您設定應用程式的遷移，您可以在 [Code First 移轉](xref:ef6/modeling/code-first/migrations/index) 主題中找到更多相關資訊。

## <a name="step-3-add-an-initial-migration"></a>步驟3：新增初始遷移

一旦建立並套用至本機資料庫的遷移，您可能也會想要將這些變更套用至其他資料庫。 例如，您的本機資料庫可能是測試資料庫，而且您最終可能會想要將變更套用至實際執行的資料庫及/或其他開發人員測試資料庫。 此步驟有兩個選項，而您應挑選的選項取決於是否有任何其他資料庫的架構是空的，還是目前與本機資料庫的架構相符。

-   **選項1：使用現有的架構做為起點。** 當未來將套用遷移的其他資料庫時，您應該使用此方法，其架構與您的本機資料庫相同。 例如，如果您的本機測試資料庫目前與生產資料庫的 v1 相符，您就可以使用此專案，稍後您將會套用這些遷移，以將生產資料庫更新為 v2。
-   **選項二：使用空白資料庫作為起點。** 當未來將套用遷移的其他資料庫為空白 (或尚未存在) 時，您應該使用此方法。 例如，如果您使用測試資料庫開始開發應用程式，但不使用遷移，而您稍後想要從頭開始建立生產資料庫，則可以使用此設定。

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>選項1：使用現有的架構做為起點

Code First 移轉使用儲存在最近一次遷移中的模型快照集來偵測模型的變更， (您可以在 [Team 環境]) 的 [Code First 移轉中](xref:ef6/modeling/code-first/migrations/teams) 找到此項的詳細資訊。 因為我們會假設資料庫已經有目前模型的架構，所以我們會產生空的 (不會) 遷移，並將目前的模型設定為快照集。

1.  在封裝管理員主控台中，執行 [ **新增-遷移 InitialCreate – IgnoreChanges** ] 命令。 這會以目前的模型做為快照集來建立空白的遷移。
2.  在封裝管理員主控台中執行 **更新資料庫** 命令。 這會將 InitialCreate 遷移套用至資料庫。 由於實際的遷移不包含任何變更，因此只會在 >migrationshistory 資料表中新增一個資料列，表示已套用 \_ \_ 此遷移。

### <a name="option-two-use-empty-database-as-a-starting-point"></a>選項二：使用空白資料庫做為起點

在此案例中，我們需要遷移以從頭開始建立整個資料庫，包括已存在於本機資料庫中的資料表。 我們即將產生 InitialCreate 遷移，其中包含建立現有架構的邏輯。 接著，我們會讓現有的資料庫看起來已套用此遷移。

1.  在封裝管理員主控台中執行 [ **新增-遷移 InitialCreate** ] 命令。 這會建立遷移以建立現有的架構。
2.  將新建立之遷移的 Up 方法中的所有程式碼標記為批註。 這可讓我們「套用」至本機資料庫的遷移，而不需要重新建立已存在的所有資料表等等。
3.  在封裝管理員主控台中執行 **更新資料庫** 命令。 這會將 InitialCreate 遷移套用至資料庫。 因為實際的遷移不包含任何變更 (因為我們暫時將它們批註) ，所以只會在 >migrationshistory 資料表中新增一個資料列，指出已套用 \_ \_ 此遷移。
4.  將 Up 方法中的程式碼取消批註。 這表示，當此遷移套用至未來的資料庫時，將會由遷移來建立已存在於本機資料庫中的架構。

## <a name="things-to-be-aware-of"></a>注意事項

針對現有的資料庫使用遷移時，您需要注意幾件事。

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>預設/計算的名稱可能不符合現有的架構

在 scaffold 遷移時，遷移會明確指定資料行和資料表的名稱。 不過，其他資料庫物件則是在套用遷移時，會計算預設名稱的其他資料庫物件。 這包括索引和 foreign key 條件約束。 以現有的架構為目標時，這些計算的名稱可能不符合資料庫中實際存在的名稱。

以下是一些您需要注意的範例：

**如果您在步驟3中使用了 [選項1：使用現有的架構做為起始點]：**

-   如果模型中的未來變更需要變更或卸載以不同方式命名的其中一個資料庫物件，您將需要修改 scaffold 遷移來指定正確的名稱。 遷移 Api 有選擇性的 Name 參數，可讓您這樣做。
    例如，您現有的架構可能會有一個具有 BlogId 外鍵資料行的 Post 資料表，且該資料行具有名為 IndexFk BlogId 的索引 \_ 。 不過，根據預設，遷移會預期此索引必須命名為 IX \_ BlogId。 如果您對模型所做的變更會導致卸載此索引，則必須修改 scaffold DropIndex 呼叫以指定 IndexFk \_ BlogId 名稱。

**如果您在步驟3中使用了「選項二：使用空白資料庫作為起點」：**

-   如果您嘗試執行初始遷移的向下方法 (也就是，針對本機資料庫還原至空的資料庫) 可能會失敗，因為遷移將會嘗試使用不正確的名稱來卸載索引和 foreign key 條件約束。 這只會影響您的本機資料庫，因為系統會使用初始遷移的 Up 方法從頭建立其他資料庫。
    如果您想要將現有的本機資料庫降級為空白狀態，您可以藉由卸載資料庫或卸載所有資料表，以手動方式輕鬆地進行。 在這個初始降級之後，將會使用預設名稱重新建立所有的資料庫物件，因此這個問題不會再次出現。
-   如果模型中的未來變更需要變更或卸載以不同方式命名的其中一個資料庫物件，這將無法針對您現有的本機資料庫運作，因為這些名稱不會符合預設值。 不過，它會針對「從頭建立」所建立的資料庫運作，因為它們會使用遷移所選擇的預設名稱。
    您可以在本機現有的資料庫上手動進行這些變更，或考慮讓遷移從頭重新建立資料庫，就像在其他電腦上一樣。
-   使用初始遷移的 Up 方法所建立的資料庫，可能會與本機資料庫稍有不同，因為會使用計算出的索引和 foreign key 條件約束的預設名稱。 您最後也可能會有額外的索引，因為在預設情況下，遷移會在外鍵資料行上建立索引，因此在您的原始本機資料庫中可能不會發生這種情況。

### <a name="not-all-database-objects-are-represented-in-the-model"></a>並非所有資料庫物件都在模型中表示

不是模型一部分的資料庫物件不會由遷移處理。 這可能包括視圖、預存程式、許可權、不是模型一部分的資料表、額外的索引等。

以下是一些您需要注意的範例：

-   無論您在 [步驟 3] 中選擇的選項為何，如果您模型中的未來變更需要變更或卸載這些額外的物件，則遷移將不知道要進行這些變更。 例如，如果您卸載的資料行有額外的索引，則遷移將不知道要卸載索引。 您必須手動將其新增至 scaffold 遷移。
-   如果您使用 [選項二：使用空白資料庫做為起點]，這些額外的物件將不會由您初始遷移的 Up 方法建立。
    您可以視需要修改向上和向下的方法，以處理這些額外的物件。 對於遷移 API 中原本不支援的物件（例如 views），您可以使用 [Sql](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx) 方法來執行原始 sql 來建立/卸載它們。
