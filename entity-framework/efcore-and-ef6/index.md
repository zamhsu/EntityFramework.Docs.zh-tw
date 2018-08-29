---
title: 比較 EF Core 與 EF6
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 09ffd8408ea8575ea367eaf2bdab4002db5c619e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997120"
---
# <a name="compare-ef-core--ef6"></a>比較 EF Core 與 EF6

Entity Framework 分為 Entity Framework Core 及 Entity Framework 6 兩種版本。

## <a name="entity-framework-6"></a>Entity Framework 6

Entity Framework 6 (EF6) 是已經過試用並測試的資料存取技術，具有多年的功能和穩定性。 它是在 2008 年當成 .NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分第一次發行。 從 EF4.1 版開始，它會出貨為 [EntityFramework NuGet 套件](https://www.nuget.org/packages/EntityFramework/)，這目前是 NuGet.org 上的其中一個最常見套件。

EF6 仍然是受支援的產品，在未來的某個時間仍會看到 Bug 修正和小幅度的改善。

## <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework Core (EF Core) 是 Entity Framework 的輕量型、可擴充且跨平台版本。 與 EF6 相較之下，EF Core 引進許多改善和新功能。 同時，EF Core 是新的程式碼基底，不像 EF6 一樣成熟。

EF Core 保有 EF6 的開發人員體驗，而且大部分的最上層 API 也都相同；因此，使用 EF6 的人員會覺得 EF Core 十分熟悉。 同時，EF Core 是透過一組全新的核心元件所建置。 這表示 EF Core 不會自動繼承 EF6 的所有功能。 這些功能中有一些會繼續在未來的版本中出現，但有一些比較不常用的功能將不會出現在 EF Core 中。

我們也可透過這款可擴充輕量型新核心，為 EF Core 新增一些 EF6 沒有的功能 (例如替代鍵、批次更新，以及 LINQ 查詢中的混合用戶端/資料庫評估)。
