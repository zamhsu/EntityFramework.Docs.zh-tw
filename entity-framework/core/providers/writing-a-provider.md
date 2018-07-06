---
title: 撰寫資料庫提供者的 EF Core
author: anmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
ms.technology: entity-framework-core
uid: core/providers/writing-a-provider
ms.openlocfilehash: 4bddf5858ab2c6b2fd22571a20edb3f7c85e2853
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678956"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="fea63-102">撰寫資料庫提供者</span><span class="sxs-lookup"><span data-stu-id="fea63-102">Writing a Database Provider</span></span>

<span data-ttu-id="fea63-103">撰寫 Entity Framework Core 資料庫提供者的相關資訊，請參閱[因此您想要撰寫的 EF Core 提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)由[Arthur Vickers](https://github.com/ajcvickers)。</span><span class="sxs-lookup"><span data-stu-id="fea63-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

<span data-ttu-id="fea63-104">EF Core 程式碼基底是開放原始碼，並包含數個可做為參考資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="fea63-104">The EF Core code base is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="fea63-105">您可以在 https://github.com/aspnet/EntityFrameworkCore 尋找原始碼。</span><span class="sxs-lookup"><span data-stu-id="fea63-105">You can find the source code at https://github.com/aspnet/EntityFrameworkCore.</span></span>

## <a name="the-providers-beware-label"></a><span data-ttu-id="fea63-106">提供者注意標籤</span><span class="sxs-lookup"><span data-stu-id="fea63-106">The providers-beware label</span></span>

<span data-ttu-id="fea63-107">當您開始在提供者上時，監看[ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)索引標籤上，我們的 GitHub 問題和提取要求。</span><span class="sxs-lookup"><span data-stu-id="fea63-107">Once you begin work on a provider, watch for the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) label on our GitHub issues and pull requests.</span></span> <span data-ttu-id="fea63-108">我們可以使用這個標籤以識別可能會影響提供者撰寫人員的變更。</span><span class="sxs-lookup"><span data-stu-id="fea63-108">We use this label to identify changes that may impact provider writers.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="fea63-109">建議的協力廠商提供者命名</span><span class="sxs-lookup"><span data-stu-id="fea63-109">Suggested naming of third party providers</span></span>

<span data-ttu-id="fea63-110">我們建議使用下列命名的 NuGet 封裝。</span><span class="sxs-lookup"><span data-stu-id="fea63-110">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="fea63-111">這是與 EF Core 小組所傳遞的封裝的名稱一致。</span><span class="sxs-lookup"><span data-stu-id="fea63-111">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="fea63-112">例如: </span><span class="sxs-lookup"><span data-stu-id="fea63-112">For example:</span></span>
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
