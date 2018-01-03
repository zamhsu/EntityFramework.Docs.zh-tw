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
# <a name="mysql-ef-core-database-provider"></a><span data-ttu-id="a45af-102">MySQL EF Core 資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="a45af-102">MySQL EF Core Database Provider</span></span>

<span data-ttu-id="a45af-103">此資料庫提供者可讓 Entity Framework Core 與 MySQL 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="a45af-103">This database provider allows Entity Framework Core to be used with MySQL.</span></span> <span data-ttu-id="a45af-104">此提供者會維護為 [MySQL 專案](http://dev.mysql.com)的一部分。</span><span class="sxs-lookup"><span data-stu-id="a45af-104">The provider is maintained as part of the [MySQL project](http://dev.mysql.com).</span></span>

> [!WARNING]  
> <span data-ttu-id="a45af-105">此提供者是發行前版本。</span><span class="sxs-lookup"><span data-stu-id="a45af-105">This provider is pre-release.</span></span>

> [!NOTE]  
> <span data-ttu-id="a45af-106">此提供者不會作為 Entity Framework Core 專案的一部分進行維護。</span><span class="sxs-lookup"><span data-stu-id="a45af-106">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="a45af-107">考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保其符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="a45af-107">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="a45af-108">安裝</span><span class="sxs-lookup"><span data-stu-id="a45af-108">Install</span></span>

<span data-ttu-id="a45af-109">安裝 [MySql.Data.EntityFrameworkCore NuGet 套件](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)。</span><span class="sxs-lookup"><span data-stu-id="a45af-109">Install the [MySql.Data.EntityFrameworkCore NuGet package](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore).</span></span>

``` powershell
Install-Package MySql.Data.EntityFrameworkCore -Pre
```

## <a name="get-started"></a><span data-ttu-id="a45af-110">開始使用</span><span class="sxs-lookup"><span data-stu-id="a45af-110">Get Started</span></span>

<span data-ttu-id="a45af-111">請參閱[從 MySQL EF Core 提供者和 Connector/Net 7.0.4 開始使用](http://insidemysql.com/howto-starting-with-mysql-ef-core-provider-and-connectornet-7-0-4/)。</span><span class="sxs-lookup"><span data-stu-id="a45af-111">See [Starting with MySQL EF Core provider and Connector/Net 7.0.4](http://insidemysql.com/howto-starting-with-mysql-ef-core-provider-and-connectornet-7-0-4/).</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="a45af-112">支援的資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="a45af-112">Supported Database Engines</span></span>

* <span data-ttu-id="a45af-113">MySQL</span><span class="sxs-lookup"><span data-stu-id="a45af-113">MySQL</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="a45af-114">支援的平台</span><span class="sxs-lookup"><span data-stu-id="a45af-114">Supported Platforms</span></span>

* <span data-ttu-id="a45af-115">.NET Framework (4.5.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="a45af-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="a45af-116">.NET 核心</span><span class="sxs-lookup"><span data-stu-id="a45af-116">.NET Core</span></span>

<span data-ttu-id="a45af-117">請務必檢閱[這裡](https://dev.mysql.com/doc/connector-net/en/connector-net-versions.html)和[這裡](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)的 MySQL 文件以確認版本相容性資訊</span><span class="sxs-lookup"><span data-stu-id="a45af-117">Be sure to review the MySQL documentation for version compatibility information [here](https://dev.mysql.com/doc/connector-net/en/connector-net-versions.html) and [here](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)</span></span>
