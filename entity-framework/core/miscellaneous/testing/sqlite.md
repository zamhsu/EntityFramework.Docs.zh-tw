---
title: SQLite-EF Core的測試
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 8fcb4b1a37da6f52219ebe3c672160627fe28fab
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052698"
---
# <a name="testing-with-sqlite"></a>使用 SQLite 測試

SQLite 有的記憶體中模式，可讓您使用 SQLite 撰寫測試針對關聯式資料庫，而不實際的資料庫作業的額外負荷。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)GitHub 上

## <a name="example-testing-scenario"></a>範例測試案例

請考慮下列服務，可讓應用程式程式碼來執行一些部落格與相關的作業。 它會在內部使用`DbContext`連線至 SQL Server 資料庫。 它可能有助於交換此內容，讓我們可以撰寫有效率測試此服務而不必修改程式碼，或執行許多工作，建立測試連接到記憶體中 SQLite 資料庫內容的雙引號。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>準備您的內容

### <a name="avoid-configuring-two-database-providers"></a>避免設定兩個資料庫提供者

在測試中您要從外部設定要使用的 InMemory 提供者的內容。 如果您要設定資料庫提供者藉由覆寫`OnConfiguring`您的內容，則您需要加入一些條件式的程式碼，以確保您只設定資料庫提供者，如果其中一個已尚未設定。

> [!TIP]  
> 如果您使用 ASP.NET Core，然後您應該不需要此程式碼由於資料庫提供者設定 （在 Startup.cs) 的內容之外。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a>加入測試的建構函式

若要啟用測試不同的資料庫最簡單的方式是修改內容公開 （expose） 的建構函式可接受`DbContextOptions<TContext>`。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>`告知其設定，例如連接到哪一個資料庫的所有內容。 這是建立在您的內容中執行 OnConfiguring 方法相同的物件。

## <a name="writing-tests"></a>撰寫測試

要測試與此提供者的索引鍵是能夠告訴使用 SQLite，並控制記憶體中資料庫範圍的內容。 開啟和關閉連接會控制資料庫的範圍。 資料庫範圍的連接已開啟的持續時間。 您通常想全新資料庫的每個測試方法。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
