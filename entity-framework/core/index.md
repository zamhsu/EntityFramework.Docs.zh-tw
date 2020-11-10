---
title: Entity Framework Core 概觀 - EF Core
description: Entity Framework Core 的一般入門總覽
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: ccb1fa4cbc0bd1a02e1aeb613475bfe4b2c1d118
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429893"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core 是常見 Entity Framework 資料存取技術的輕量型、可擴充、[開放原始碼](https://github.com/dotnet/efcore)且跨平台版本。

EF Core 可以作為物件關聯式對應程式 (O/RM) ，其：

* 可讓 .NET 開發人員使用 .NET 物件來處理資料庫。
* 免除通常需要撰寫的大部分資料存取程式碼的需求。

EF Core 支援許多資料庫引擎，如需詳細資料，請參閱[資料庫提供者](xref:core/providers/index)。

## <a name="the-model"></a>模型

運用 EF Core，使用模型來執行資料存取。 模型是由實體類別和表示與資料庫之會話的內容物件所組成。 內容物件可讓您查詢和儲存資料。 如需詳細資訊，請參閱 [建立模型](xref:core/modeling/index)。

EF 支援下列模型開發方法：

* 從現有的資料庫產生模型。
* 將模型手動編寫成符合資料庫的程式碼。
* 建立模型之後，請使用 [EF 遷移](xref:core/managing-schemas/migrations/index) 從模型建立資料庫。 當模型變更時，遷移可讓資料庫演進。

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>查詢

您可以使用 [ (LINQ) 的語言整合式查詢 ](/dotnet/csharp/programming-guide/concepts/linq/)，從資料庫取出實體類別的實例。 如需詳細資訊，請參閱 [查詢資料](xref:core/querying/index)。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>儲存資料

使用您實體類別的執行個體，建立、刪除和修改資料庫中的資料。 若要深入了解，請參閱[儲存資料](xref:core/saving/index)。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a>EF O/RM 考慮

雖然 EF Core 在抽象化許多程式設計細節時很好用，但還是有一些適用于任何 O/RM 的最佳作法，可協助避免生產應用程式中的常見陷阱：

* 基礎資料庫伺服器的中繼層級知識或更高版本，對於在高效能的生產環境應用程式中設計、分析、分析和遷移資料而言是不可或缺的。 例如，主要與外鍵、條件約束、索引、正規化、DML 和 DDL 語句、資料類型、分析等等的知識。
* 功能和整合測試：請務必盡可能將生產環境複寫至：
  * 找出應用程式中只有在使用特定版本的資料庫伺服器時才會顯示的問題。
  * 升級 EF Core 和其他相依性時攔截重大變更。 例如，加入或升級 ASP.NET Core、OData 或 Automapper 等架構。 這些相依性可能會以非預期的方式影響 EF Core。
* 具有代表性負載的效能和壓力測試。 部分功能的最簡單用途無法妥善調整。 例如，多個集合包括過度使用消極式載入、非索引資料行上的條件式查詢、具有存放區產生值的大量更新和插入、缺乏並行處理、大型模型、不適當的快取原則。
* 安全性審核：例如，處理連接字串和其他秘密、非部署作業的資料庫許可權、原始 SQL 的輸入驗證、機密資料的加密。
* 請確定記錄和診斷功能足夠且可用。 例如，適當的記錄設定、查詢標記以及 Application Insights。
* 錯誤復原。 針對常見的失敗案例（例如版本復原、回溯伺服器、向外延展和負載平衡、DoS 緩和和資料備份）做好應變。
* 應用程式部署和遷移。 規劃如何在部署期間套用遷移;在應用程式啟動時執行它可能會受到並行處理問題的影響，且需要的許可權比一般作業所需的更高。 使用暫存有助於在遷移期間發生嚴重錯誤時進行修復。 如需詳細資訊，請參閱套用 [遷移](xref:core/managing-schemas/migrations/applying)。
* 已產生之遷移的詳細檢查和測試。 在將遷移套用至生產資料之前，應先徹底測試。 當資料表包含生產資料時，架構的圖形和資料行類型不容易變更。 例如，在 SQL Server 上， `nvarchar(max)` 和 `decimal(18, 2)` 不是對應至字串和 decimal 屬性之資料行的最佳類型，但這些是 EF 使用的預設值，因為它並不知道您的特定案例。

## <a name="next-steps"></a>後續步驟

如需簡介教學課程，請參閱 [Entity Framework Core 使用者入門](xref:core/get-started/overview/first-app)。
