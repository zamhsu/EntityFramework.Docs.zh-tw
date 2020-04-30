---
title: 使用 EF 記憶體內部資料庫進行測試-EF Core
author: ajcvickers
description: 使用 EF 記憶體內部資料庫來測試 EF Core 應用程式
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538355"
---
# <a name="testing-with-the-ef-in-memory-database"></a>使用 EF 記憶體內部資料庫進行測試

> [!WARNING]
> EF 記憶體內部資料庫的行為通常與關係資料庫不同。
> 請在完全瞭解涉及的問題和取捨之後，才使用 EF 記憶體內部資料庫，如[使用 EF Core 的測試程式碼](xref:core/miscellaneous/testing/index)中所述。  

> [!TIP]
> SQLite 是關聯式提供者，也可以使用記憶體內部資料庫。
> 請考慮使用這項測試，以更密切地比對常見的關係資料庫行為。
> 這會在[使用 SQLite 來測試 EF Core 應用程式](xref:core/miscellaneous/testing/sqlite)中討論。   

此頁面上的資訊現在位於其他位置：
* 如需使用 EF 記憶體內部資料庫進行測試的一般資訊，請參閱[使用 EF Core 的測試程式碼](xref:core/miscellaneous/testing/index)。
* 如需使用 EF 記憶體內部資料庫的範例，請參閱[示範如何測試使用 EF Core 的應用程式](xref:core/miscellaneous/testing/testing-sample)。
* 如需 EF 記憶體內部資料庫的一般資訊，請參閱[ef 記憶體內部資料庫提供者](xref:core/providers/in-memory/index)。
