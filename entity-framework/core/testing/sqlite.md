---
title: 使用 SQLite EF Core 測試
description: 使用 SQLite 測試 Entity Framework Core 應用程式
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: da2504cfe7997a10a5ee8c447b1c6ef00dd02369
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129053"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a>使用 SQLite 測試 EF Core 應用程式

> [!WARNING]
> 使用 SQLite 可以是測試 EF Core 應用程式的有效方式。
> 不過，SQLite 的行為會與其他資料庫系統不同，因此可能會發生問題。
> 請參閱使用 EF Core 討論問題和取捨的 [測試程式碼](xref:core/testing/index) 。

本檔是以範例中所介紹的概念為基礎， [示範如何測試使用 EF Core 的應用程式](xref:core/testing/testing-sample)。
此處所示的程式碼範例來自此範例。

## <a name="using-sqlite-in-memory-databases"></a>使用 SQLite 記憶體內部資料庫

一般來說，SQLite 會將資料庫建立為簡單的檔案，並以您的應用程式在同進程存取檔案。
這非常快速，尤其是在使用快速 [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)時。

SQLite 也可以使用純粹在記憶體中建立的資料庫。
只要您瞭解記憶體內部資料庫存留期，就很容易使用 EF Core：

* 當資料庫的連接開啟時，就會建立資料庫
* 當資料庫的連接關閉時，就會刪除資料庫。

EF Core 將會使用已開啟的連接（如果有的話），而且永遠不會嘗試關閉它。
因此，搭配記憶體內部 SQLite 資料庫使用 EF Core 的關鍵是先開啟連接，然後再將它傳遞給 EF。

此 [範例](xref:core/testing/testing-sample) 會使用下列程式碼來達成此程式碼：

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

注意：

* `CreateInMemoryDatabase`方法會在記憶體內部資料庫中建立 SQLite，並開啟與其的連接。
* 建立的 `DbConnection` 會從解壓縮 `ContextOptions` 並儲存。
* 當處置測試時，就會處置此連接，如此就不會流失資源。

> [!NOTE]
> [問題 #16103](https://github.com/dotnet/efcore/issues/16103) 正在追蹤讓此連接管理變得更容易的方法。
