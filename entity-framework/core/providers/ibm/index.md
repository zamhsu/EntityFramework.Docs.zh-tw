---
title: "IBM Data Server (DB2) 資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 02/15/2017
ms.assetid: 825e5332-5aa3-4600-9efb-ab71aaff59ec
ms.technology: entity-framework-core
uid: core/providers/ibm/index
ms.openlocfilehash: a9caa8df63850d4f6b5f2164dad7ac5af7504076
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="ibm-data-server-db2-ef-core-database-providers"></a>IBM Data Server (DB2) EF Core 資料庫提供者

此資料庫提供者可讓 Entity Framework Core 與 IBM Data Server 搭配使用。 此提供者由 IBM 進行維護。

> [!NOTE]  
> 此提供者不會作為 Entity Framework Core 專案的一部分進行維護。 考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保其符合您的需求。

## <a name="install"></a>Install

若要在 Windows 上使用 IBM Data Server，請安裝 [IBM.EntityFrameworkCore NuGet 套件](https://www.nuget.org/packages/IBM.EntityFrameworkCore)。

``` powershell
Install-Package IBM.EntityFrameworkCore
```

若要在 Linux 上使用 IBM Data Server，請安裝 [IBM.EntityFrameworkCore-lnx NuGet 套件](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)。

``` powershell
Install-Package IBM.EntityFrameworkCore-lnx
```

## <a name="get-started"></a>開始使用

[.NET Core 的 IBM .NET 提供者使用者入門](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/DB2DotnetCore?lang=en)

## <a name="supported-database-engines"></a>支援的資料庫引擎

* zOS
* LUW 包含 IBM dashDB
* IBM I
* Informix

## <a name="supported-platforms"></a>支援的平台

* .NET Framework (4.6 及更新版本)
* .NET Core
