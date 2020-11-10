---
title: 資料植入-EF Core
description: 使用資料植入來以 Entity Framework Core 的初始資料集填入資料庫
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: fefa6232496cd250d52a436971251f59af09f5c6
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429672"
---
# <a name="data-seeding"></a>資料植入

資料植入是以初始資料集填入資料庫的程式。

有幾種方式可以在 EF Core 中完成：

* 模型種子資料
* 手動遷移自訂
* 自訂初始化邏輯

## <a name="model-seed-data"></a>模型種子資料

不同于 EF6，在 EF Core 中，植入資料可以與實體類型相關聯，做為模型設定的一部分。 然後 EF Core 的 [遷移](xref:core/managing-schemas/migrations/index) 可以自動計算將資料庫升級至新版本的模型時，必須套用的插入、更新或刪除作業。

> [!NOTE]
> 當您決定應該執行哪一種作業來讓種子資料進入預期狀態時，遷移只會考慮模型變更。 因此，在遷移外部所執行之資料的任何變更都可能遺失或造成錯誤。

例如，這會在中設定的種子資料 `Blog` `OnModelCreating` ：

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

若要加入具有關聯性的實體，必須指定外鍵值：

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

如果實體類型在陰影狀態中有任何屬性，則可以使用匿名類別來提供這些值：

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

擁有的實體類型可以用類似的方式植入：

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

請參閱 [完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) ，以取得更多內容。

一旦將資料加入至模型之後，就應該使用 [遷移](xref:core/managing-schemas/migrations/index) 來套用變更。

> [!TIP]
> 如果您需要將遷移套用為自動化部署的一部分，您可以建立可在執行前預覽的 [SQL 腳本](xref:core/managing-schemas/migrations/index#generate-sql-scripts) 。

或者，您可以使用 `context.Database.EnsureCreated()` 來建立包含種子資料的新資料庫，例如針對測試資料庫，或是在使用記憶體中提供者或任何非關聯資料庫時。 請注意，如果資料庫已經存在， `EnsureCreated()` 將不會在資料庫中更新架構或植入資料。 如果您打算使用遷移，則不應呼叫關係資料庫 `EnsureCreated()` 。

### <a name="limitations-of-model-seed-data"></a>模型種子資料的限制

這種類型的種子資料是由遷移所管理，而且需要產生腳本來更新資料庫中已有的資料，而不需要連接到資料庫。 這會造成一些限制：

* 您必須指定主鍵值（即使它通常是由資料庫所產生）。 它將用來偵測遷移之間的資料變更。
* 如果主鍵以任何方式變更，則會移除先前植入的資料。

因此，這項功能最適用于不需要在遷移之外變更的靜態資料，而且不會相依于資料庫中的任何其他資料，例如郵遞區號。

如果您的案例包含下列任何一項，建議您使用上一節中所述的自訂初始化邏輯：

* 用於測試的暫存資料
* 相依于資料庫狀態的資料
* 大量 (植入資料的資料會在遷移快照集中捕捉，而大型資料可能很快會導致大型檔案和效能降低) 。
* 需要資料庫產生的索引鍵值的資料，包括使用替代索引鍵做為身分識別的實體
* 需要自訂轉換 (的資料，不是由 [值轉換](xref:core/modeling/value-conversions)) 處理，例如一些密碼雜湊
* 需要呼叫外部 API 的資料，例如 ASP.NET Core 身分識別角色和使用者建立

## <a name="manual-migration-customization"></a>手動遷移自訂

當您將所指定資料的變更轉換為、和的呼叫時，會將所指定之資料的變更 `HasData` 轉換成 `InsertData()` `UpdateData()` `DeleteData()` 。 解決某些限制的其中一種方式 `HasData` ，是手動將這些呼叫或 [自訂作業](xref:core/managing-schemas/migrations/operations) 加入至遷移作業。

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>自訂初始化邏輯

執行資料植入的簡單、強大方式，是在 [`DbContext.SaveChanges()`](xref:core/saving/index) 主要應用程式邏輯開始執行之前使用。

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> 植入程式碼不應該是一般應用程式執行的一部分，因為這可能會在多個實例正在執行時造成並行處理問題，而且也會要求應用程式具有修改資料庫架構的許可權。

根據您的部署條件約束，可以用不同的方式執行初始化程式碼：

* 在本機執行初始化應用程式
* 使用主應用程式部署初始化應用程式、叫用初始化常式，以及停用或移除初始化應用程式。

這通常可以使用 [發行設定檔](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)進行自動化。
