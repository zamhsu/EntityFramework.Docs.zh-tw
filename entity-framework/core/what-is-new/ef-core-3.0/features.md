---
title: EF Core 3.0 的新功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: b6774f615b04bf9579aac5dea217e7321631da0c
ms.sourcegitcommit: a709054b2bc7a8365201d71f59325891aacd315f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2019
ms.locfileid: "57829183"
---
# <a name="new-features-included-in-ef-core-30-currently-in-preview"></a>EF Core 3.0 (目前為預覽版) 中包含的新功能

> [!IMPORTANT]
> 請注意，後續版本的功能集與上市時間，隨時可能會變動，此外，即使我們試圖將此頁面保持在最新狀態，可能還是無法隨時反映我們最新的計劃。

下列清單包含為 EF Core 3.0 新規劃的重要功能。
大部分功能並未包含在目前的預覽版中，但隨著逐步往 RTM 發展，多數功能終將能夠使用。

原因是在發行的一開始，我們即著重於實作規劃的[中斷性變更](xref:core/what-is-new/ef-core-3.0/breaking-changes)。
許多中斷性變更本身使得 EF Core 有所改善。
此外，還需要更多重大變更，以便改善能更加詳盡。 

如需 Bug 修正和開發中增強功能的完整清單，您可以在[我們的問題追蹤中查看此查詢](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)。

## <a name="linq-improvements"></a>LINQ 改善 

[追蹤問題 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

已著手進行此功能的開發，但未包含在目前的預覽版中。

LINQ 可讓您以偏好的語言撰寫資料庫查詢，進而利用豐富的型別資訊優勢來取得 IntelliSense 與編譯階段型別檢查。
但是 LINQ 也可以讓您撰寫不限數目的複雜查詢，這通常是 LINQ 提供者所面臨的艱難挑戰。
在 EF Core 的前幾個版本中，我們透過搞清楚查詢的哪些部分可以轉譯為 SQL，接著允許查詢的剩餘部分在用戶端上的記憶體中執行來克服其中的一些問題。
這個用戶端執行在某些案例中是令人滿意的，但在其他案例中會導致無效率的查詢，而在應用程式部署到生產階段之前，您可能無法發現這種無效率的查詢。
在 EF Core 3.0 中，我們計劃在 LINQ 實作的運作方式及其測試方式進行重大變更。
目標是讓它更強固 (例如避免中斷修補程式版本中的查詢)、能夠將更多運算式正確地轉譯為 SQL、在更多案例中產生有效率的查詢，以及防止未偵測到無效率查詢的情況。

## <a name="cosmos-db-support"></a>Cosmos DB 支援 

[追蹤問題 #8443](https://github.com/aspnet/EntityFrameworkCore/issues/8443)

此功能包含在目前的預覽版中，但尚未完成。 

我們正在處理適用於 EF Core 的 Cosmos DB 提供者，以讓開發人員熟悉 EF 程式設計模型，以輕鬆地使用 Azure Cosmos DB 做為應用程式資料庫。
目標是讓 .NET 開發人員能更輕鬆地發揮 Cosmos DB 的一些優點，例如全域散發、"Always On" 可用性、彈性的延展性，以及低延遲。
提供者將啟用大部分的 EF Core 功能，例如自動變更追蹤、LINQ 與值轉換 (根據 Cosmos DB 中的 SQL API)。
我們在 EF Core 2.2 之前開始此工作，而且[我們已建立一些預覽版提供者供使用](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。
新的計畫是以 EF Core 3.0 為基礎繼續開發提供者。 

## <a name="c-80-support"></a>C# 8.0 支援

[追蹤問題 #12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[追蹤問題 #10347](https://github.com/aspnet/EntityFrameworkCore/issues/10347)

已著手進行此功能的開發，但未包含在目前的預覽版中。

我們想要讓客戶在使用 EF Core 時，能夠利用 [C# 8.0 即將推出的一些新功能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)，例如非同步串流 (包括 `await foreach`) 與可為 Null 的參考型別。

## <a name="reverse-engineering-of-database-views"></a>資料庫檢視的反向工程

[追蹤問題 #1679](https://github.com/aspnet/EntityFrameworkCore/issues/1679)

此功能並未包含在目前的預覽版中。

[查詢類型](xref:core/modeling/query-types)已於 EF Core 2.1 中推出，並在 EF Core 3.0 中視為沒有索引鍵的實體類型，這些類型代表可從資料庫讀取但無法更新的資料。
此特性讓它們非常適用於大多時候的資料庫檢視，因此我們想要在對資料庫檢視進行反向工程時，將沒有索引鍵的實體類型建立作業自動化。

## <a name="property-bag-entities"></a>屬性包實體 

[追蹤問題 #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 和 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)

已著手進行此功能的開發，但未包含在目前的預覽版中。 

此功能是有關於啟用會在已編製索引之屬性內而非正常屬性內存放資料的實體，也有關於能夠使用相同.NET 類別的執行個體 (可能是像 `Dictionary<string, object>` 這麼簡單的東西) 以代表相同 EF Core 模型中的不同實體類型。
此功能是在不需要聯結實體的情況下支援多對多關聯性 ([問題 #1368](https://github.com/aspnet/EntityFrameworkCore/issues/1368)) 的墊腳石，這是使用者最希望擁有的 EF Core 改良功能。

## <a name="ef-63-on-net-core"></a>.NET Core 上的 EF 6.3 

[追蹤問題 EF6#271](https://github.com/aspnet/EntityFramework6/issues/271)

已著手進行此功能的開發，但未包含在目前的預覽版中。 

我們了解許多現有的應用程式都使用舊版 EF，而且因為要獲得 .NET Core 的優點而將它們移植到 EF Core 有時候需要非常多的精力。
因此，我們會讓下一個版本的 EF 6 能夠在 .NET Core 3.0 上直執行。
我們這樣做的原因是要讓您能以變更最小的方式移植現有的應用程式。
但免不了有些限制。 例如：
- 除了 .NET Core 包含的 SQL Server 支援，新的提供者還必須使用其他資料庫
- SQL Server 的空間支援將不會啟用

另請注意，EF 6 目前並未規劃任何新功能。
