---
title: "比較 EF Core 與 EF6"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 4442e6931327f6a07d98aee47211ddbeed51a467
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="compare-ef-core--ef6"></a>比較 EF Core 與 EF6

有兩種版本的 Entity Framework：Entity Framework Core 和 Entity Framework 6。

## <a name="entity-framework-6"></a>Entity Framework 6

Entity Framework 6 (EF6) 是已經過試用並測試的資料存取技術，具有多年的功能和穩定性。 它是在 2008 年當成 .NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分第一次發行。 從 EF4.1 版開始，它會出貨為 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)，這目前是 NuGet.org 上的最常見套件。

EF6 仍然是受支援的產品，在未來的某個時間仍會看到 Bug 修正和小幅度的改善。

## <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework Core (EF Core) 是 Entity Framework 的輕量型、可擴充且跨平台版本。 與 EF6 相較之下，EF Core 引進許多改善和新功能。 同時，EF Core 是新的程式碼基底，不像 EF6 一樣成熟。

EF Core 保有 EF6 的開發人員體驗，而且大部分的最上層 API 也都相同；因此，使用 EF6 的人員會覺得 EF Core 十分熟悉。 同時，EF Core 是透過一組全新的核心元件所建置。 這表示 EF Core 不會自動繼承 EF6 的所有功能。 其中某些功能會顯示在未來版本中 (例如，消極式載入和連線恢復功能)，而在 EF Core 中將不會實作其他較不常用的功能。

全新、可擴充和輕量型核心也可讓我們將一些功能新增至不會在 EF6 中實作的 EF Core (例如 LINQ 查詢中的替代索引鍵以及混合式用戶端/資料庫評估)。
