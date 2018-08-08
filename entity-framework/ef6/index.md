---
title: 概觀 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 8ae74d63-6bad-4686-b325-bbf9d68f3743
caps.latest.revision: 5
uid: ef6/index
ms.openlocfilehash: c74626593c4109badd75f2a0a4f051c840f7bc7f
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614371"
---
# <a name="entity-framework-6"></a><span data-ttu-id="388b0-102">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="388b0-102">Entity Framework 6</span></span>
<span data-ttu-id="388b0-103">Entity Framework 6 (EF6) 是通過試驗及測試的 .NET 物件關聯式對應程式 (O/RM)，歷經多年的功能開發和穩固。</span><span class="sxs-lookup"><span data-stu-id="388b0-103">Entity Framework 6 (EF6) is a tried and tested object-relational mapper (O/RM) for .NET with many years of feature development and stabilization.</span></span>

<span data-ttu-id="388b0-104">作為 O/RM，EF6 可減少關聯式與物件導向環境之間的阻抗不相符情況，讓開發人員能夠撰寫使用強型別 .NET 物件 (其代表應用程式網域) 與關聯式資料庫中儲存的資料進行互動的應用程式，因此通常不需要撰寫大部分的資料存取「配管」程式碼。</span><span class="sxs-lookup"><span data-stu-id="388b0-104">As an O/RM, EF6 reduces the impedance mismatch between the relational and object-oriented worlds, enabling developers to write applications that interact with data stored in relational databases using strongly-typed .NET objects that represent the application's domain, and eliminating the need for a large portion of the data access "plumbing" code that they usually need to write.</span></span>

<span data-ttu-id="388b0-105">EF6 會實作許多常用的 O/RM 功能：</span><span class="sxs-lookup"><span data-stu-id="388b0-105">EF6 implements many popular O/RM features:</span></span>
- <span data-ttu-id="388b0-106">對應 [POCO](~/ef6/resources/glossary.md#poco) 實體類別，其不相依於任何 EF 類型</span><span class="sxs-lookup"><span data-stu-id="388b0-106">Mapping of [POCO](~/ef6/resources/glossary.md#poco) entity classes which do not depend on any EF types</span></span>
- <span data-ttu-id="388b0-107">自動變更追蹤</span><span class="sxs-lookup"><span data-stu-id="388b0-107">Automatic change tracking</span></span>
- <span data-ttu-id="388b0-108">識別解析和工作單位</span><span class="sxs-lookup"><span data-stu-id="388b0-108">Identity resolution and Unit of Work</span></span>
- <span data-ttu-id="388b0-109">積極式、消極式和明確式載入</span><span class="sxs-lookup"><span data-stu-id="388b0-109">Eager, lazy and explicit loading</span></span>
- <span data-ttu-id="388b0-110">使用 LINQ (Language INtegrated Query) 的強型別查詢轉譯</span><span class="sxs-lookup"><span data-stu-id="388b0-110">Translation of strongly-typed queries using LINQ (Language INtegrated Query)</span></span>
- <span data-ttu-id="388b0-111">豐富的對應功能，包括下列項目的支援：</span><span class="sxs-lookup"><span data-stu-id="388b0-111">Rich mapping capabilities, including support for:</span></span>
  - <span data-ttu-id="388b0-112">一對一、一對多及多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="388b0-112">One-to-one, one-to-many and many-to-many relationships</span></span>
  - <span data-ttu-id="388b0-113">繼承 (每個階層的資料表、每個類型的資料表，以及每個實體類別的資料表)</span><span class="sxs-lookup"><span data-stu-id="388b0-113">Inheritance (table per hierarchy, table per type and table per concrete class)</span></span>
  - <span data-ttu-id="388b0-114">複雜類型</span><span class="sxs-lookup"><span data-stu-id="388b0-114">Complex types</span></span>
  - <span data-ttu-id="388b0-115">預存程序</span><span class="sxs-lookup"><span data-stu-id="388b0-115">Stored procedures</span></span>
- <span data-ttu-id="388b0-116">用來建立實體模型的視覺化設計工具。</span><span class="sxs-lookup"><span data-stu-id="388b0-116">A visual designer to create entity models.</span></span>
- <span data-ttu-id="388b0-117">藉由撰寫程式碼來建立實體模型的「Code First」體驗。</span><span class="sxs-lookup"><span data-stu-id="388b0-117">A "Code First" experience to create entity models by writing code.</span></span>
- <span data-ttu-id="388b0-118">您可以從現有資料庫產生模型，然後手動進行編輯，也可以從頭開始建立模型，然後用來產生新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="388b0-118">Models can either be generated form existing databases and then hand-edited, or they can be created from scratch and then used to generate new databases.</span></span>
- <span data-ttu-id="388b0-119">與 .NET Framework 應用程式模型 (包括 ASP.NET) 的整合，或透過資料繫結與 WPF 和 WinForms 的整合。</span><span class="sxs-lookup"><span data-stu-id="388b0-119">Integration with .NET Framework application models, including ASP.NET, and through databinding, with WPF and WinForms.</span></span>
- <span data-ttu-id="388b0-120">依據 ADO.NET 的資料庫連線和許多提供者可用來連線至 SQL Server、Oracle、MySQL、SQLite、PostgreSQL、DB2 等等。</span><span class="sxs-lookup"><span data-stu-id="388b0-120">Database connectivity based on ADO.NET and numerous providers available to connect to SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.</span></span>

## <a name="should-i-use-ef6-or-ef-core"></a><span data-ttu-id="388b0-121">我應該使用 EF6 還是 EF Core？</span><span class="sxs-lookup"><span data-stu-id="388b0-121">Should I use EF6 or EF Core?</span></span>

<span data-ttu-id="388b0-122">EF Core 是 Entity Framework　的新式輕量型可延伸版本，具有非常類似於 EF6 的功能和優點。</span><span class="sxs-lookup"><span data-stu-id="388b0-122">EF Core is a more modern, lightweight and extensible version of Entity Framework that has very similar capabilities and benefits to EF6.</span></span>
<span data-ttu-id="388b0-123">EF Core 是完全重新撰寫的版本，包含許多 EF6 並未提供的新功能，但它仍然缺少一些 EF6 最先進的對應功能。</span><span class="sxs-lookup"><span data-stu-id="388b0-123">EF Core is a complete rewrite and contains many new features not available in EF6, although it also still lacks some of the most advanced mapping capabilities of EF6.</span></span>
<span data-ttu-id="388b0-124">建議您只要功能集符合您的需求，就在新的應用程式中使用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="388b0-124">We recommend using EF Core in new applications as long as the feature set matches your requirements.</span></span>
<span data-ttu-id="388b0-125">[比較 EF Core 與 EF6](xref:efcore-and-ef6/index)可更詳細地檢查這項選擇。</span><span class="sxs-lookup"><span data-stu-id="388b0-125">[Compare EF Core & EF6](xref:efcore-and-ef6/index) examines this choice in greater detail.</span></span>

## <a name="get-startedef6get-startedmd"></a>[<span data-ttu-id="388b0-126">開始使用</span><span class="sxs-lookup"><span data-stu-id="388b0-126">Get Started</span></span>](~/ef6/get-started.md)

<span data-ttu-id="388b0-127">將 EntityFramework NuGet 套件新增至您的專案，或安裝 Entity Framework Tools for Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="388b0-127">Add the EntityFramework NuGet package to your project or install the Entity Framework Tools for Visual Studio.</span></span> <span data-ttu-id="388b0-128">接著觀看影片、閱讀教學課程和進階文件，以協助您充分利用 EF6。</span><span class="sxs-lookup"><span data-stu-id="388b0-128">Then watch videos, read tutorials, and advanced documentation to help you make the most of EF6.</span></span>

## <a name="past-entity-framework-versions"></a><span data-ttu-id="388b0-129">過去的 Entity Framework 版本</span><span class="sxs-lookup"><span data-stu-id="388b0-129">Past Entity Framework Versions</span></span>

<span data-ttu-id="388b0-130">這是適用於最新版 Entity Framework 6 的文件，但其中的大部分內容也適用於過去的版本。</span><span class="sxs-lookup"><span data-stu-id="388b0-130">This is the documentation for the latest version of Entity Framework 6, although much of it also applies to past releases.</span></span>
<span data-ttu-id="388b0-131">如需 EF 版本和它們所引進功能的完整清單，請參閱[新功能](~/ef6/what-is-new/index.md)和[過去的版本](~/ef6/what-is-new/past-releases.md)。</span><span class="sxs-lookup"><span data-stu-id="388b0-131">Check out [What's New](~/ef6/what-is-new/index.md) and [Past Releases](~/ef6/what-is-new/past-releases.md) for a complete list of EF releases and the features they introduced.</span></span>
