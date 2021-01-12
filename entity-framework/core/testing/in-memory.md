---
title: 使用 EF In-Memory 資料庫進行測試-EF Core
description: 使用 EF 記憶體內部資料庫來測試 Entity Framework Core 的應用程式
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128806"
---
# <a name="testing-with-the-ef-in-memory-database"></a>使用 EF In-Memory 資料庫進行測試

> [!WARNING]
> EF 記憶體內部資料庫的行為通常與關係資料庫不同。
> 在完全瞭解所涉及的問題和取捨之後，請只使用 EF 記憶體內部資料庫，如 [使用 EF Core 的測試程式碼](xref:core/testing/index)中所述。

> [!TIP]
> SQLite 是關聯式提供者，也可以使用記憶體內部資料庫。
> 請考慮使用此項進行測試，以更接近常見的關係資料庫行為。
> 這涵蓋在 [使用 SQLite 來測試 EF Core 應用程式](xref:core/testing/sqlite)。

此頁面上的資訊現在存在於其他位置：

* 請參閱 [使用 EF Core 的測試程式碼，](xref:core/testing/index) 以取得使用 EF 記憶體內部資料庫進行測試的一般資訊。
* 請參閱範例，其中顯示如何使用 EF in memory 資料庫 [測試使用 EF Core 的應用程式](xref:core/testing/testing-sample) 。
* 如需 EF 記憶體內部資料庫的一般資訊，請參閱 [ef 記憶體內部資料庫提供者](xref:core/providers/in-memory/index) 。
