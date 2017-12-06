---
title: "SQLite 資料庫提供者-限制-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
ms.technology: entity-framework-core
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 08a4b8c26a3678491d412b333a7415cb45d4231f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="11a2f-102">SQLite EF 核心資料庫提供者的限制</span><span class="sxs-lookup"><span data-stu-id="11a2f-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="11a2f-103">SQLite 提供者有幾項移轉限制。</span><span class="sxs-lookup"><span data-stu-id="11a2f-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="11a2f-104">大部分的這些限制是限制基礎 SQLite database engine 中的結果，並不專屬於 EF。</span><span class="sxs-lookup"><span data-stu-id="11a2f-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="11a2f-105">模型限制</span><span class="sxs-lookup"><span data-stu-id="11a2f-105">Modeling limitations</span></span>

<span data-ttu-id="11a2f-106">通用的關聯式程式庫 （Entity Framework 關聯式資料庫提供者所共用） 會定義應用程式開發介面模型通用於大多數的關聯式資料庫引擎的概念。</span><span class="sxs-lookup"><span data-stu-id="11a2f-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="11a2f-107">SQLite 提供者不支援幾個這些概念。</span><span class="sxs-lookup"><span data-stu-id="11a2f-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="11a2f-108">結構描述</span><span class="sxs-lookup"><span data-stu-id="11a2f-108">Schemas</span></span>
* <span data-ttu-id="11a2f-109">序列</span><span class="sxs-lookup"><span data-stu-id="11a2f-109">Sequences</span></span>

## <a name="migrations-limitations"></a><span data-ttu-id="11a2f-110">移轉限制</span><span class="sxs-lookup"><span data-stu-id="11a2f-110">Migrations limitations</span></span>

<span data-ttu-id="11a2f-111">SQLite database engine 不支援大多數的其他關聯式資料庫所支援的結構描述作業的數目。</span><span class="sxs-lookup"><span data-stu-id="11a2f-111">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="11a2f-112">如果您嘗試以不支援作業的其中一套用於 SQLite 資料庫則`NotSupportedException`就會擲回。</span><span class="sxs-lookup"><span data-stu-id="11a2f-112">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="11a2f-113">運算</span><span class="sxs-lookup"><span data-stu-id="11a2f-113">Operation</span></span>            | <span data-ttu-id="11a2f-114">支援？</span><span class="sxs-lookup"><span data-stu-id="11a2f-114">Supported?</span></span> |
| -------------------- | ---------- |
| <span data-ttu-id="11a2f-115">AddColumn</span><span class="sxs-lookup"><span data-stu-id="11a2f-115">AddColumn</span></span>            | <span data-ttu-id="11a2f-116">✔</span><span class="sxs-lookup"><span data-stu-id="11a2f-116">✔</span></span>          |
| <span data-ttu-id="11a2f-117">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="11a2f-117">AddForeignKey</span></span>        | <span data-ttu-id="11a2f-118">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-118">✗</span></span>          |
| <span data-ttu-id="11a2f-119">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="11a2f-119">AddPrimaryKey</span></span>        | <span data-ttu-id="11a2f-120">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-120">✗</span></span>          |
| <span data-ttu-id="11a2f-121">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="11a2f-121">AddUniqueConstraint</span></span>  | <span data-ttu-id="11a2f-122">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-122">✗</span></span>          |
| <span data-ttu-id="11a2f-123">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="11a2f-123">AlterColumn</span></span>          | <span data-ttu-id="11a2f-124">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-124">✗</span></span>          |
| <span data-ttu-id="11a2f-125">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="11a2f-125">CreateIndex</span></span>          | <span data-ttu-id="11a2f-126">✔</span><span class="sxs-lookup"><span data-stu-id="11a2f-126">✔</span></span>          |
| <span data-ttu-id="11a2f-127">CreateTable</span><span class="sxs-lookup"><span data-stu-id="11a2f-127">CreateTable</span></span>          | <span data-ttu-id="11a2f-128">✔</span><span class="sxs-lookup"><span data-stu-id="11a2f-128">✔</span></span>          |
| <span data-ttu-id="11a2f-129">DropColumn</span><span class="sxs-lookup"><span data-stu-id="11a2f-129">DropColumn</span></span>           | <span data-ttu-id="11a2f-130">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-130">✗</span></span>          |
| <span data-ttu-id="11a2f-131">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="11a2f-131">DropForeignKey</span></span>       | <span data-ttu-id="11a2f-132">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-132">✗</span></span>          |
| <span data-ttu-id="11a2f-133">DropIndex</span><span class="sxs-lookup"><span data-stu-id="11a2f-133">DropIndex</span></span>            | <span data-ttu-id="11a2f-134">✔</span><span class="sxs-lookup"><span data-stu-id="11a2f-134">✔</span></span>          |
| <span data-ttu-id="11a2f-135">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="11a2f-135">DropPrimaryKey</span></span>       | <span data-ttu-id="11a2f-136">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-136">✗</span></span>          |
| <span data-ttu-id="11a2f-137">DropTable</span><span class="sxs-lookup"><span data-stu-id="11a2f-137">DropTable</span></span>            | <span data-ttu-id="11a2f-138">✔</span><span class="sxs-lookup"><span data-stu-id="11a2f-138">✔</span></span>          |
| <span data-ttu-id="11a2f-139">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="11a2f-139">DropUniqueConstraint</span></span> | <span data-ttu-id="11a2f-140">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-140">✗</span></span>          |
| <span data-ttu-id="11a2f-141">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="11a2f-141">RenameColumn</span></span>         | <span data-ttu-id="11a2f-142">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-142">✗</span></span>          |
| <span data-ttu-id="11a2f-143">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="11a2f-143">RenameIndex</span></span>          | <span data-ttu-id="11a2f-144">✗</span><span class="sxs-lookup"><span data-stu-id="11a2f-144">✗</span></span>          |
| <span data-ttu-id="11a2f-145">RenameTable</span><span class="sxs-lookup"><span data-stu-id="11a2f-145">RenameTable</span></span>          | <span data-ttu-id="11a2f-146">✔</span><span class="sxs-lookup"><span data-stu-id="11a2f-146">✔</span></span>          |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="11a2f-147">移轉限制因應措施</span><span class="sxs-lookup"><span data-stu-id="11a2f-147">Migrations limitations workaround</span></span>

<span data-ttu-id="11a2f-148">您可以解決一些手動中撰寫程式碼來執行資料表移轉這些限制的重建。</span><span class="sxs-lookup"><span data-stu-id="11a2f-148">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="11a2f-149">資料表重建牽涉到重新命名現有的資料表、 建立新的資料表、 將資料複製到新的資料表，以及卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="11a2f-149">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="11a2f-150">您必須使用`Sql(string)`方法，以執行這些步驟。</span><span class="sxs-lookup"><span data-stu-id="11a2f-150">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="11a2f-151">請參閱[進行其他種類的資料表結構描述變更](http://sqlite.org/lang_altertable.html#otheralter)SQLite 文件以取得詳細資料中。</span><span class="sxs-lookup"><span data-stu-id="11a2f-151">See [Making Other Kinds Of Table Schema Changes](http://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="11a2f-152">未來，EF 可能支援這些作業的某些使用背後的資料表重建方法。</span><span class="sxs-lookup"><span data-stu-id="11a2f-152">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="11a2f-153">您可以[我們的 GitHub 專案上追蹤這項功能](https://github.com/aspnet/EntityFramework/issues/329)。</span><span class="sxs-lookup"><span data-stu-id="11a2f-153">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFramework/issues/329).</span></span>
