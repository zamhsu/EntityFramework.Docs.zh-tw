---
title: 載入相關資料 - EF Core
description: 使用 Entity Framework Core 載入相關資料的不同策略
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063669"
---
# <a name="loading-related-data"></a>載入相關資料

Entity Framework Core 可讓您在模型中使用導覽屬性來載入相關實體。 有三種常見的 O/RM 模式可用來載入相關資料。

* **[積極式載入](xref:core/querying/related-data/eager)** 表示相關的資料會從資料庫載入做為初始查詢的一部分。
* **[明確載入](xref:core/querying/related-data/explicit)** 表示相關資料會在稍後明確從資料庫載入。
* 消極式**[載入](xref:core/querying/related-data/lazy)** 表示在存取導覽屬性時，會從資料庫以透明的方式載入相關資料。

> [!TIP]
> 您可以在 GitHub 上查看本節中的 [範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) 。
