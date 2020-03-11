---
title: 資料植入-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 5c056c600f696ad1443ddb7b8c95c4b0ead06d21
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417223"
---
# <a name="data-seeding"></a>資料植入

資料植入是以初始資料集填入資料庫的程式。

有數種方式可以在 EF Core 中完成：

* 模型種子資料
* 手動遷移自訂
* 自訂初始化邏輯

## <a name="model-seed-data"></a>模型種子資料

> [!NOTE]
> 此功能是 EF Core 2.1 中的新功能。

不同于 EF6，在 EF Core 中，植入資料可以與實體類型相關聯，做為模型設定的一部分。 然後 EF Core[遷移](xref:core/managing-schemas/migrations/index)可以自動計算將資料庫升級至新版本的模型時，需要套用的插入、更新或刪除作業。

> [!NOTE]
> 只有在決定應該執行哪一種作業以將種子資料轉換成所需的狀態時，遷移才會考慮模型變更。 因此，在遷移外部執行之資料的任何變更可能會遺失或造成錯誤。

例如，這會在 `OnModelCreating`中設定 `Blog` 的種子資料：

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

若要加入具有關聯性的實體，必須指定外鍵值：

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

如果實體類型具有「陰影狀態」中的任何屬性，則可以使用匿名類別來提供值：

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

擁有的實體類型可以用類似的方式植入：

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

如需詳細內容，請參閱[完整的範例專案](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)。

將資料加入至模型之後，應該使用[遷移](xref:core/managing-schemas/migrations/index)來套用變更。

> [!TIP]
> 如果您需要將遷移套用為自動化部署的一部分，您可以建立可在執行前預覽的[SQL 腳本](xref:core/managing-schemas/migrations/index#generate-sql-scripts)。

或者，您可以使用 `context.Database.EnsureCreated()` 來建立包含種子資料的新資料庫（例如測試資料庫），或使用記憶體內部提供者或任何非關聯資料庫。 請注意，如果資料庫已經存在，`EnsureCreated()` 將不會更新資料庫中的架構或植入資料。 針對關係資料庫，如果您打算使用遷移，則不應呼叫 `EnsureCreated()`。

### <a name="limitations-of-model-seed-data"></a>模型種子資料的限制

這種類型的種子資料是由遷移所管理，而且必須在不連接到資料庫的情況下，才會產生更新已在資料庫中之資料的腳本。 這會造成一些限制：

* 您必須指定主要金鑰值，即使它通常是由資料庫所產生。 它會用來偵測遷移之間的資料變更。
* 如果主要金鑰以任何方式變更，則會移除先前植入的資料。

因此，這項功能最適用于不應在遷移之外變更的靜態資料，而且不會相依于資料庫中的任何其他專案，例如郵遞區號。

如果您的案例包含下列任何一項，建議使用最後一節中所述的自訂初始化邏輯：

* 用於測試的暫存資料
* 相依于資料庫狀態的資料
* 需要由資料庫產生索引鍵值的資料，包括使用替代索引鍵做為身分識別的實體
* 需要自訂轉換（不會由[值轉換](xref:core/modeling/value-conversions)處理）的資料，例如一些密碼雜湊
* 需要呼叫外部 API 的資料，例如 ASP.NET Core 身分識別角色和使用者建立

## <a name="manual-migration-customization"></a>手動遷移自訂

當遷移新增時，會將 `HasData` 所指定的資料變更轉換成 `InsertData()`、`UpdateData()`和 `DeleteData()`的呼叫。 解決 `HasData` 限制的其中一種方法，就是手動將這些呼叫或[自訂作業](xref:core/managing-schemas/migrations/operations)新增至遷移。

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>自訂初始化邏輯

執行資料植入的簡單且功能強大的方式，就是在主要應用程式邏輯開始執行之前，先使用[`DbContext.SaveChanges()`](xref:core/saving/index) 。

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> 植入程式碼不應該是正常應用程式執行的一部分，因為這可能會在多個實例正在執行時造成並行問題，而且也需要具有修改資料庫架構之許可權的應用程式。

視部署的條件約束而定，可以透過不同的方式來執行初始化程式碼：

* 在本機執行初始化應用程式
* 使用主要應用程式部署初始化應用程式、叫用初始化常式，以及停用或移除初始化應用程式。

這通常可以使用[發行設定檔](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)來自動化。
