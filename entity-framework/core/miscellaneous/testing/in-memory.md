---
title: InMemory-EF Core 的測試
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 33690e3424d0777930d3cb8167575fb0f4ddd8f7
ms.sourcegitcommit: d096484dcf9eff73d9943fa60db7a418b10ca0b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2018
ms.locfileid: "27995583"
---
# <a name="testing-with-inmemory"></a>使用 InMemory 測試

InMemory 提供者時，您想要測試元件使用的近似於連接至實際的資料庫，而不實際的資料庫作業的額外負荷。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) \(英文\)。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory 不是關聯式資料庫

EF Core 資料庫提供者可能沒有關聯式資料庫。 InMemory 的設計是為了測試，一般用途的資料庫，而且不是模擬的關聯式資料庫。

包括的一些範例：
* InMemory 可讓您儲存違反參考完整性條件約束，關聯式資料庫中的資料。

* 如果您使用 DefaultValueSql(string) 屬性在模型中，這是關聯式資料庫 API 並不會影響對 InMemory 執行時。

> [!TIP]  
> 針對許多測試目的將不重要了這些差異。 不過，如果您想要測試的行為與類似，則為 true 的關聯式資料庫，請考慮使用[SQLite 記憶體中模式](sqlite.md)。

## <a name="example-testing-scenario"></a>範例測試案例

請考慮下列服務，可讓應用程式程式碼來執行一些部落格與相關的作業。 它會在內部使用`DbContext`連線至 SQL Server 資料庫。 它可能有助於交換此內容，讓我們可以撰寫有效率測試此服務而不必修改程式碼，或執行許多工作，建立測試連接到 InMemory 資料庫內容的 double。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>準備您的內容

### <a name="avoid-configuring-two-database-providers"></a>避免設定兩個資料庫提供者

在測試中您要從外部設定要使用的 InMemory 提供者的內容。 如果您要設定資料庫提供者藉由覆寫`OnConfiguring`您的內容，則您需要加入一些條件式的程式碼，以確保您只設定資料庫提供者，如果其中一個已尚未設定。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> 如果您使用 ASP.NET Core，然後您應該不需要此程式碼由於外部 （在 Startup.cs) 的內容已設定您的資料庫提供者。

### <a name="add-a-constructor-for-testing"></a>加入測試的建構函式

若要啟用測試不同的資料庫最簡單的方式是修改內容公開 （expose） 的建構函式可接受`DbContextOptions<TContext>`。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>`告知其設定，例如連接到哪一個資料庫的所有內容。 這是建立在您的內容中執行 OnConfiguring 方法相同的物件。

## <a name="writing-tests"></a>撰寫測試

要測試與此提供者的索引鍵是能夠判斷要使用的 InMemory 提供者，並控制記憶體中資料庫範圍的內容。 您通常想全新資料庫的每個測試方法。

以下是使用 InMemory 資料庫測試類別的範例。 每個測試方法指定唯一的資料庫名稱，這表示每一種方法有它自己的 InMemory 資料庫。

>[!TIP]
> 若要使用`.UseInMemoryDatabase()`擴充方法，參考的 NuGet 套件`Microsoft.EntityFrameworkCore.InMemory`。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
