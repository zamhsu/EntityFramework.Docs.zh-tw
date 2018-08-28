---
title: InMemory 資料庫提供者 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: ca802f55d973b9f79073c2507c1e0c7a853a1fce
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993316"
---
# <a name="ef-core-in-memory-database-provider"></a>EF Core 記憶體中資料庫提供者

此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。 雖然記憶體內部模式中的 SQLite 提供者作為關聯式資料庫的替代測試項目可能較為合適，但此資料庫作為測試用途仍相當實用。 [Entity Framework Core 專案](https://github.com/aspnet/EntityFrameworkCore)的維護包含此提供者。

## <a name="install"></a>安裝

安裝 [Microsoft.EntityFrameworkCore.InMemory NuGet 套件](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="get-started"></a>開始使用

下列資源將協助您開始使用此提供者。
* [使用 InMemory 測試](../../miscellaneous/testing/in-memory.md)

* [UnicornStore 範例應用程式測試](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>支援的資料庫引擎

* 內建記憶體中資料庫 (僅供測試用途所設計)

## <a name="supported-platforms"></a>支援的平台

* .NET Framework (4.5.1 及更新版本)

* .NET Core

* Mono (4.2.0 及更新版本)

* 通用 Windows 平台
