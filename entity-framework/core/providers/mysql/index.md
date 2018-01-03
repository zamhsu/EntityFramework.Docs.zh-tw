---
title: "MySQL 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4900b882-79c5-40d2-a44a-ccb0292f6ed9
ms.technology: entity-framework-core
uid: core/providers/mysql/index
ms.openlocfilehash: 1500d017cb463c3f394131a79b9063ff90cce5e2
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="mysql-ef-core-database-provider"></a>MySQL EF Core 資料庫提供者

此資料庫提供者可讓 Entity Framework Core 與 MySQL 搭配使用。 此提供者會維護為 [MySQL 專案](http://dev.mysql.com)的一部分。

> [!WARNING]  
> 此提供者是發行前版本。

> [!NOTE]  
> 此提供者不會作為 Entity Framework Core 專案的一部分進行維護。 考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保其符合您的需求。

## <a name="install"></a>安裝

安裝 [MySql.Data.EntityFrameworkCore NuGet 套件](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)。

``` powershell
Install-Package MySql.Data.EntityFrameworkCore -Pre
```

## <a name="get-started"></a>開始使用

請參閱[從 MySQL EF Core 提供者和 Connector/Net 7.0.4 開始使用](http://insidemysql.com/howto-starting-with-mysql-ef-core-provider-and-connectornet-7-0-4/)。

## <a name="supported-database-engines"></a>支援的資料庫引擎

* MySQL

## <a name="supported-platforms"></a>支援的平台

* .NET Framework (4.5.1 及更新版本)

* .NET 核心

請務必檢閱[這裡](https://dev.mysql.com/doc/connector-net/en/connector-net-versions.html)和[這裡](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)的 MySQL 文件以確認版本相容性資訊
