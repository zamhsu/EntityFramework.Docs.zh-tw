---
title: InMemory 資料庫提供者 - EF Core
description: Entity Framework Core 記憶體內部資料庫提供者的相關資訊
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1af75088ae892e3b428caf6bdb31dd2b750a05fe
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430166"
---
# <a name="ef-core-in-memory-database-provider"></a>EF Core 記憶體中資料庫提供者

此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。 記憶體內部資料庫可用於測試，雖然記憶體內部模式中的 SQLite 提供者可能是更適當的測試取代關係資料庫。 記憶體內部資料庫是專為測試所設計。 [Entity Framework Core 專案](https://github.com/dotnet/efcore)的維護包含此提供者。

## <a name="install"></a>安裝

安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a>開始使用

下列資源將協助您開始使用此提供者。

* [使用 InMemory 測試](xref:core/testing/in-memory)
* [UnicornStore 範例應用程式測試](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>支援的資料庫引擎

同進程記憶體資料庫，專為測試目的而設計。
