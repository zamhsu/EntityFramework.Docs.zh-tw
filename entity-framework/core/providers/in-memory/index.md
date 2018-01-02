---
title: "InMemory 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
ms.technology: entity-framework-core
uid: core/providers/in-memory/index
ms.openlocfilehash: a8e05f50837f3da554b338475d24215706dfa2ec
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="ef-core-in-memory-database-provider"></a>EF Core 記憶體中資料庫提供者

此資料庫提供者可讓 Entity Framework Core 搭配使用記憶體內部資料庫。 對使用 Entity Framework Core 的程式碼進行測試時，這十分有用。 此提供者會維護為 [EntityFramework GitHub 專案](https://github.com/aspnet/EntityFramework)的一部分。

## <a name="install"></a>Install

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

* .NET Framework (4.5.1 和更新版本)

* .NET Core

* Mono (4.2.0 和更新版本)

* 通用 Windows 平台
