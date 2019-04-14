---
title: InMemory-EF Core 的測試
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 8aaea52f22954ef6a2b7d9b9c5627597c61ac644
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562542"
---
# <a name="testing-with-inmemory"></a>使用 InMemory 進行測試

InMemory 提供者時，您想要測試使用近似連線至實際的資料庫，而不實際的資料庫作業的額外負荷的元件。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) \(英文\)。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory 不是關聯式資料庫

EF Core 資料庫提供者可能沒有關聯式資料庫。 InMemory 設計為一般用途的資料庫進行測試，而且不是模擬的關聯式資料庫。

包括的一些範例：

* InMemory 可讓您將會違反參考完整性條件約束，關聯式資料庫中的資料儲存。
* 如果您使用 DefaultValueSql(string) 屬性在模型中，這是關聯式資料庫 API，並對 InMemory 執行時，會有任何作用。
* [透過時間戳記/資料列版本的並行存取](xref:core/modeling/concurrency#timestamprow-version)(`[Timestamp]`或`IsRowVersion`) 不支援。 否[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)如果完成的更新將會擲回使用舊的並行語彙基元。

> [!TIP]  
> 有許多測試用途的這些差異會造成影響。 不過，如果您想要測試其行為與類似關聯式資料庫，則為 true 的項目，然後考慮使用[SQLite 記憶體中模式](sqlite.md)。

## <a name="example-testing-scenario"></a>範例測試案例

請考慮下列服務，可讓應用程式程式碼來執行一些部落格與相關的作業。 它會在內部使用`DbContext`連線至 SQL Server 資料庫。 它會有用交換此內容，以連線至 InMemory 資料庫，以便我們可以撰寫有效率的測試，這項服務，而不需要修改程式碼，或執行許多工作，以建立測試 double 的內容。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>準備您的內容

### <a name="avoid-configuring-two-database-providers"></a>避免設定兩個資料庫提供者

在測試中您要從外部設定要使用 InMemory 提供者的內容。 如果您要設定資料庫提供者藉由覆寫`OnConfiguring`在您的內容，然後您需要新增一些條件式的程式碼，以確保您只設定資料庫提供者，如果其中有尚未設定。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> 如果您使用 ASP.NET Core，然後您應該不需要此程式碼由於外部 （在 Startup.cs) 的內容已設定您的資料庫提供者。

### <a name="add-a-constructor-for-testing"></a>新增測試的建構函式

若要啟用對不同的資料庫測試最簡單的方式是修改您的內容公開 （expose） 的建構函式可接受`DbContextOptions<TContext>`。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` 告知其設定，例如要連接到資料庫的所有內容。 這是由您的內容中執行 OnConfiguring 方法相同的物件。

## <a name="writing-tests"></a>撰寫測試

要測試與此提供者的索引鍵是能夠告訴使用 InMemory 提供者，並控制記憶體中資料庫範圍的內容。 通常您會想乾淨的資料庫的每個測試方法。

以下是使用 InMemory 資料庫的測試類別的範例。 每個測試方法指定唯一的資料庫名稱，這表示每一種方法有它自己的 InMemory 資料庫。

>[!TIP]
> 若要使用`.UseInMemoryDatabase()`擴充方法，參考的 NuGet 套件[Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
