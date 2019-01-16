---
title: 資料植入的 EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 1c450b142573368d043430f55a3144b6696a8691
ms.sourcegitcommit: b4a5ed177b86bf7f81602106dab6b4acc18dfc18
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54316630"
---
# <a name="data-seeding"></a>資料植入

資料植入是填入一組初始的資料與資料庫的程序。

有數種方式，這可在 EF Core:
* 模型種子資料
* 手動移轉自訂
* 自訂的初始化邏輯

## <a name="model-seed-data"></a>模型種子資料

> [!NOTE]
> 這項功能是在 EF Core 2.1 中新功能。

不同於 EF6，EF core 中植入資料可以是相關聯的模型組態一部分的實體類型。 然後 EF Core[移轉](xref:core/managing-schemas/migrations/index)可以自動計算項目插入、 更新或刪除作業必須升級至新版的模型資料庫時套用。

> [!NOTE]
> 判斷應該執行哪一個作業，以取得種子資料到期望的狀態時，移轉只會將模型變更。 因此資料移轉之外執行的任何變更可能會遺失或造成錯誤。

例如，這會設定種子資料，如`Blog`在`OnModelCreating`:

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

若要新增具有關聯性外部索引鍵值的實體必須指定：

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

如果實體類型會有任何屬性，在匿名類別可用來提供值的陰影狀態：

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

擁有的實體類型可以做為種子以類似的方式：

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

請參閱[完整的範例專案](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)詳細內容。

一旦資料加入至模型，[移轉](xref:core/managing-schemas/migrations/index)應該用來套用變更。

> [!TIP]
> 如果您要自動化的部署過程中套用移轉即可[建立的 SQL 指令碼](xref:core/managing-schemas/migrations/index#generate-sql-scripts)，可以預覽之前執行。

或者，您可以使用`context.Database.EnsureCreated()`來建立新的資料庫包含種子資料，例如針對測試資料庫，或使用的記憶體提供者或任何非關聯性的資料庫。 請注意，如果資料庫已經存在，`EnsureCreated()`都不會更新資料庫中的結構描述或種子資料。 關聯式資料庫，您不應該呼叫`EnsureCreated()`如果您打算使用移轉。

### <a name="limitations-of-model-seed-data"></a>模型種子資料的限制

這種類型的種子資料由管理移轉，需要產生而不需要連線到資料庫的指令碼，以更新資料庫中的資料。 這具有一些限制︰
* 主索引鍵值，就必須指定即使通常會由資料庫產生。 它會用來偵測之間移轉的資料變更。
* 先前植入的資料將會移除，如果主索引鍵以任何方式變更。

因此這項功能是最適合用於靜態資料具有不應該變更移轉之外，不需依賴任何在資料庫中，例如郵遞區號的其他項目。

如果您的案例包含下列任何一項建議使用自訂的初始化邏輯中的最後一節所述：
* 暫存資料來進行測試
* 取決於資料庫狀態的資料
* 需要資料庫，包括替代索引鍵做為身分識別的實體所產生的索引鍵值的資料
* 需要自訂的轉換的資料 (可不由[值轉換](xref:core/modeling/value-conversions))，例如某些密碼雜湊
* 需要呼叫外部 API，例如 ASP.NET Core 身分識別的角色和使用者建立的資料

## <a name="manual-migration-customization"></a>手動移轉自訂

當移轉新增時所做的變更與指定的資料`HasData`會進行轉換，以呼叫`InsertData()`， `UpdateData()`，和`DeleteData()`。 其中一個方法解決某些限制，其中`HasData`是以手動方式新增這些呼叫或[自訂作業](xref:core/managing-schemas/migrations/operations)移轉至改。

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>自訂的初始化邏輯

執行資料植入的簡單且功能強大的方法是使用[ `DbContext.SaveChanges()` ](xref:core/saving/index)邏輯開始執行之前將主應用程式。

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> 植入的程式碼不應該在一般應用程式執行期間，當多個執行個體執行，而且也需要擁有修改資料庫結構描述的權限的應用程式時，這可能會造成並行處理問題。

根據您的部署的條件約束可執行的初始化程式碼以不同的方式：
* 在本機執行初始化應用程式
* 部署主要應用程式時，叫用的初始化常式和停用或移除初始化應用程式初始化應用程式。

這通常是使用自動化[發行設定檔](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles)。
