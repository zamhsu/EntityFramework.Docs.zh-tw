---
title: 索引的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
ms.technology: entity-framework-core
uid: core/modeling/relational/indexes
ms.openlocfilehash: f577fccfefc6908edf2ac47ae630323d7a9f5f2b
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678995"
---
# <a name="indexes"></a><span data-ttu-id="9192b-102">索引</span><span class="sxs-lookup"><span data-stu-id="9192b-102">Indexes</span></span>

> [!NOTE]  
> <span data-ttu-id="9192b-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="9192b-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="9192b-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="9192b-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="9192b-105">關聯式資料庫中的索引對應到相同的概念與 Entity Framework 的核心中的索引。</span><span class="sxs-lookup"><span data-stu-id="9192b-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="9192b-106">慣例</span><span class="sxs-lookup"><span data-stu-id="9192b-106">Conventions</span></span>

<span data-ttu-id="9192b-107">根據慣例，索引會命名為`IX_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="9192b-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="9192b-108">針對複合索引`<property name>`變成屬性名稱的底線分隔清單。</span><span class="sxs-lookup"><span data-stu-id="9192b-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="9192b-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="9192b-109">Data Annotations</span></span>

<span data-ttu-id="9192b-110">索引不可以使用資料註解來設定。</span><span class="sxs-lookup"><span data-stu-id="9192b-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="9192b-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="9192b-111">Fluent API</span></span>

<span data-ttu-id="9192b-112">您可以使用 fluent 應用程式開發的應用程式開發介面來設定索引的名稱。</span><span class="sxs-lookup"><span data-stu-id="9192b-112">You can use the Fluent API to configure the name of an index.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

<span data-ttu-id="9192b-113">您也可以指定篩選器。</span><span class="sxs-lookup"><span data-stu-id="9192b-113">You can also specify a filter.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

<span data-ttu-id="9192b-114">當使用 SQL Server 提供者 EF 加上 ' IS NOT NULL' 篩選所有可為 null 的資料行唯一索引的一部分。</span><span class="sxs-lookup"><span data-stu-id="9192b-114">When using the SQL Server provider EF adds a 'IS NOT NULL' filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="9192b-115">若要覆寫您可以提供這個慣例`null`值。</span><span class="sxs-lookup"><span data-stu-id="9192b-115">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
