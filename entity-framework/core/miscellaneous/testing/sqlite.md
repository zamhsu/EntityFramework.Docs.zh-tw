---
title: SQLite-EF Core 的測試
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: e8ff204a09d50064b4f0d4376f02b05c8681ac25
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562529"
---
# <a name="testing-with-sqlite"></a>使用 SQLite 進行測試

SQLite 具有記憶體中模式，可讓您使用 SQLite 撰寫測試針對關聯式資料庫，而不實際的資料庫作業的額外負荷。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)GitHub 上

## <a name="example-testing-scenario"></a>範例測試案例

請考慮下列服務，可讓應用程式程式碼來執行一些部落格與相關的作業。 它會在內部使用`DbContext`連線至 SQL Server 資料庫。 它會有用交換此內容，連接到記憶體中的 SQLite 資料庫，以便我們可以撰寫有效率的測試，這項服務，而不需要修改程式碼，或執行許多工作，以建立測試 double 的內容。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>準備您的內容

### <a name="avoid-configuring-two-database-providers"></a>避免設定兩個資料庫提供者

在測試中您要從外部設定要使用 InMemory 提供者的內容。 如果您要設定資料庫提供者藉由覆寫`OnConfiguring`在您的內容，然後您需要新增一些條件式的程式碼，以確保您只設定資料庫提供者，如果其中有尚未設定。

> [!TIP]  
> 如果您使用 ASP.NET Core，然後您應該不需要此程式碼由於資料庫提供者設定 （在 Startup.cs) 的內容之外。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a>新增測試的建構函式

若要啟用對不同的資料庫測試最簡單的方式是修改您的內容公開 （expose） 的建構函式可接受`DbContextOptions<TContext>`。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` 告知其設定，例如要連接到資料庫的所有內容。 這是由您的內容中執行 OnConfiguring 方法相同的物件。

## <a name="writing-tests"></a>撰寫測試

要測試與此提供者的索引鍵是能夠告訴地使用 SQLite，及控制記憶體中資料庫範圍的內容。 資料庫的範圍會受到開啟及關閉連接。 資料庫範圍的連接已開啟的持續時間。 通常您會想乾淨的資料庫的每個測試方法。

>[!TIP]
> 若要使用`SqliteConnection()`而`.UseSqlite()`擴充方法，參考的 NuGet 套件[Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
