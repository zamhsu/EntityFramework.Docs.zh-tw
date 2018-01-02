---
title: "資料庫提供者 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
ms.technology: entity-framework-core
uid: core/providers/index
ms.openlocfilehash: 19c275b7e89c62e79c8bded977e39b2cfb2b439a
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="database-providers"></a><span data-ttu-id="1ab66-102">資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="1ab66-102">Database Providers</span></span>

<span data-ttu-id="1ab66-103">Entity Framework Core 使用提供者模型，允許使用 EF 來存取許多不同的資料庫。</span><span class="sxs-lookup"><span data-stu-id="1ab66-103">Entity Framework Core uses a provider model to allow EF to be used to access many different databases.</span></span> <span data-ttu-id="1ab66-104">有些概念通用於大多數資料庫，並且包含在主要 EF Core 元件中。</span><span class="sxs-lookup"><span data-stu-id="1ab66-104">Some concepts are common to most databases, and are included in the primary EF Core components.</span></span> <span data-ttu-id="1ab66-105">這類概念包含以 LINQ 表示查詢、異動，以及在從資料庫載入物件之後追蹤其變更。</span><span class="sxs-lookup"><span data-stu-id="1ab66-105">Such concepts include expressing queries in LINQ, transactions, and tacking changes to objects once they are loaded from the database.</span></span> <span data-ttu-id="1ab66-106">有些概念是特定提供者所特有。</span><span class="sxs-lookup"><span data-stu-id="1ab66-106">Some concepts are specific to a particular provider.</span></span> <span data-ttu-id="1ab66-107">例如，SQL Server 提供者可讓您設定記憶體最佳化資料表 (SQL Server 特有的功能)。</span><span class="sxs-lookup"><span data-stu-id="1ab66-107">For example, the SQL Server provider allows you to configure memory-optimized tables (a feature specific to SQL Server).</span></span> <span data-ttu-id="1ab66-108">其他概念是提供者類別特有的。</span><span class="sxs-lookup"><span data-stu-id="1ab66-108">Other concepts are specific to a class of providers.</span></span> <span data-ttu-id="1ab66-109">例如，關聯式資料庫的 EF Core 提供者是根據一般 `Microsoft.EntityFrameworkCore.Relational` 程式庫所建置，而此程式庫提供 API 來設定資料表和資料行對應、外部索引鍵條件約束等等。</span><span class="sxs-lookup"><span data-stu-id="1ab66-109">For example, EF Core providers for relational databases build on the common `Microsoft.EntityFrameworkCore.Relational` library, which provides APIs for configuring table and column mappings, foreign key constraints, etc.</span></span>

<span data-ttu-id="1ab66-110">EF Core 提供者是透過各種來源所建置。</span><span class="sxs-lookup"><span data-stu-id="1ab66-110">EF Core providers are built by a variety of sources.</span></span> <span data-ttu-id="1ab66-111">並非所有提供者都會維護為 Entity Framework Core 專案的一部分。</span><span class="sxs-lookup"><span data-stu-id="1ab66-111">Not all providers are maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="1ab66-112">考慮使用協力廠商提供者時，請務必評估品質、授權、支援等，確保它們符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="1ab66-112">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>
