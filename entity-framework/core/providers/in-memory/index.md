---
title: InMemory 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: fd31c8ef2dc2e35e69f9845933a5578a5ff84c9c
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413003"
---
# <a name="ef-core-in-memory-database-provider"></a>EF Core 記憶體中資料庫提供者

此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。 雖然記憶體內部模式中的 SQLite 提供者作為關聯式資料庫的替代測試項目可能較為合適，但此資料庫作為測試用途仍相當實用。 [Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。

## <a name="install"></a>安裝

安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a>開始使用

下列資源將協助您開始使用此提供者。

* [使用 InMemory 測試](../../miscellaneous/testing/in-memory.md)
* [UnicornStore 範例應用程式測試](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>支援的資料庫引擎

同處理序記憶體資料庫 (專為測試目的而設計)
