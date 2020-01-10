---
title: 使用 InMemory 進行測試-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: fcd2f99ad06fd30ef9e36fd1e5a6a09fe0a45d07
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781114"
---
# <a name="testing-with-inmemory"></a>使用 InMemory 進行測試

當您想要使用近似連接到實際資料庫的專案來測試元件，而不會產生實際資料庫作業的額外負荷時，InMemory 提供者會很有用。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) \(英文\)。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory 不是關係資料庫

EF Core 資料庫提供者不一定要是關係資料庫。 InMemory 的設計是用來測試的一般用途資料庫，而不是設計來模擬關係資料庫。

其中一些範例包括：

* InMemory 可讓您儲存在關係資料庫中違反參考完整性條件約束的資料。
* 如果您針對模型中的屬性使用 DefaultValueSql （string），這就是關係資料庫 API，且在對 InMemory 執行時不會有任何作用。
* 不支援透過[時間戳記/資料列版本](xref:core/modeling/concurrency#timestamprowversion)（`[Timestamp]` 或 `IsRowVersion`）進行平行存取。 如果使用舊的並行標記來進行更新，則不會擲回任何[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) 。

> [!TIP]  
> 基於許多測試目的，這些差異並不重要。 不過，如果您想要針對行為更像是真正關係資料庫的專案進行測試，請考慮使用[SQLite 記憶體中模式](sqlite.md)。

## <a name="example-testing-scenario"></a>範例測試案例

請考慮下列可讓應用程式程式碼執行與 blog 相關之作業的服務。 在內部，它會使用連接到 SQL Server 資料庫的 `DbContext`。 交換此內容以連接到 InMemory 資料庫，讓我們可以為此服務撰寫有效率的測試，而不需要修改程式碼，或執行許多工作來建立內容的測試 double，會是很有用的做法。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>讓您的內容就緒

### <a name="avoid-configuring-two-database-providers"></a>避免設定兩個資料庫提供者

在您的測試中，您將會在外部設定內容以使用 InMemory 提供者。 如果您要藉由覆寫內容中的 `OnConfiguring` 來設定資料庫提供者，則必須新增一些條件式程式碼，以確保只有在資料庫提供者尚未設定時，才會進行設定。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> 如果您使用 ASP.NET Core，則不應該需要此程式碼，因為您的資料庫提供者已經設定在內容外部（在 Startup.cs 中）。

### <a name="add-a-constructor-for-testing"></a>新增用於測試的函數

針對不同的資料庫啟用測試的最簡單方式是修改您的內容，以公開接受 `DbContextOptions<TContext>`的函式。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` 會告訴內容其所有設定，例如要連接的資料庫。 這是在您的內容中執行 OnConfiguring 方法所建立的相同物件。

## <a name="writing-tests"></a>撰寫測試

使用此提供者進行測試的關鍵在於能夠告訴內容使用 InMemory 提供者，並控制記憶體內部資料庫的範圍。 您通常會想要針對每個測試方法使用乾淨的資料庫。

以下是使用 InMemory 資料庫的測試類別範例。 每個測試方法都會指定唯一的資料庫名稱，這表示每個方法都有自己的 InMemory 資料庫。

>[!TIP]
> 若要使用 `.UseInMemoryDatabase()` 的擴充方法，請參考[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)的 NuGet 套件。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
