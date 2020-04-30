---
title: 使用 SQLite EF Core 進行測試
description: 使用 SQLite 測試 EF Core 應用程式
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 327fdc230df2a3b4094accf93fffa81f92e0a931
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538279"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a>使用 SQLite 測試 EF Core 應用程式

> [!WARNING]
> 使用 SQLite 可以是測試 EF Core 應用程式的有效方式。
> 不過，SQLite 的行為與其他資料庫系統不同，可能會發生問題。 如需問題和取捨的討論，請參閱[測試使用 EF Core 的程式碼](xref:core/miscellaneous/testing/index)。  

本檔會針對[示範如何測試使用 EF Core 之應用程式的範例](xref:core/miscellaneous/testing/testing-sample)中所介紹的概念來使用。
此處顯示的程式碼範例來自此範例。

## <a name="using-sqlite-in-memory-databases"></a>使用 SQLite 記憶體內部資料庫

一般來說，SQLite 會將資料庫建立為簡單檔案，並使用您的應用程式在同進程中存取檔案。
這非常快速，特別是在使用快速[SSD](https://en.wikipedia.org/wiki/Solid-state_drive)時。 

SQLite 也可以使用純粹在記憶體中建立的資料庫。
只要您瞭解記憶體內部資料庫存留期，這就很容易與 EF Core 搭配使用：
* 當資料庫的連接開啟時，就會建立該資料庫。
* 當資料庫的連接已關閉時，就會刪除該資料庫

EF Core 在指定時，將會使用已開啟的連接，而且永遠不會嘗試關閉它。
因此，使用 EF Core 搭配記憶體內部 SQLite 資料庫的關鍵，是在將連接傳遞給 EF 之前，先開啟連線。  

此[範例](xref:core/miscellaneous/testing/testing-sample)會使用下列程式碼來達到此動作：

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

注意：
* `CreateInMemoryDatabase`方法會建立 SQLite 記憶體內部資料庫，並開啟其連接。
* 建立`DbConnection`的會從解壓縮`ContextOptions`並儲存。
* 處置測試時，會處置此連接，而不會流失資源。 

> [!NOTE]
> [問題 #16103](https://github.com/dotnet/efcore/issues/16103)是追蹤讓此連接管理變得更容易的方式。 
