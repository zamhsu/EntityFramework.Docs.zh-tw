---
title: 使用 SQLite EF Core 進行測試
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: f7f847d8c766c0d4d7577ea6760ee72a17f84933
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417299"
---
# <a name="testing-with-sqlite"></a>使用 SQLite 進行測試

SQLite 具有記憶體中模式，可讓您使用 SQLite 針對關係資料庫撰寫測試，而不會產生實際資料庫作業的額外負荷。

> [!TIP]  
> 您可以在 GitHub 上查看這篇文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)

## <a name="example-testing-scenario"></a>範例測試案例

請考慮下列可讓應用程式程式碼執行與 blog 相關之作業的服務。 在內部，它會使用連接到 SQL Server 資料庫的 `DbContext`。 交換此內容以連接到記憶體中的 SQLite 資料庫，讓我們可以為此服務撰寫有效率的測試，而不需要修改程式碼，或執行許多工作來建立內容的測試 double，這會很有説明。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>讓您的內容就緒

### <a name="avoid-configuring-two-database-providers"></a>避免設定兩個資料庫提供者

在您的測試中，您將會在外部設定內容以使用 InMemory 提供者。 如果您要藉由覆寫內容中的 `OnConfiguring` 來設定資料庫提供者，則必須新增一些條件式程式碼，以確保只有在資料庫提供者尚未設定時，才會進行設定。

> [!TIP]  
> 如果您使用 ASP.NET Core，則不應該需要此程式碼，因為您的資料庫提供者是在內容外部設定（在 Startup.cs 中）。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a>新增用於測試的函數

針對不同的資料庫啟用測試的最簡單方式是修改您的內容，以公開接受 `DbContextOptions<TContext>`的函式。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` 會告訴內容其所有設定，例如要連接的資料庫。 這是在您的內容中執行 OnConfiguring 方法所建立的相同物件。

## <a name="writing-tests"></a>撰寫測試

使用此提供者進行測試的關鍵在於能夠告訴內容使用 SQLite，並控制記憶體內部資料庫的範圍。 資料庫的範圍是藉由開啟和關閉連接來控制。 資料庫的範圍是連接開啟的持續時間。 您通常會想要針對每個測試方法使用乾淨的資料庫。

>[!TIP]
> 若要使用 `SqliteConnection()` 和 `.UseSqlite()` 擴充方法，請參考[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)的 NuGet 套件。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
