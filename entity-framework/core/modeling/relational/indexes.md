---
title: 索引的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: 605b30ce710d9034deab97f695496ec66a576565
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993213"
---
# <a name="indexes"></a><span data-ttu-id="88583-102">索引</span><span class="sxs-lookup"><span data-stu-id="88583-102">Indexes</span></span>

> [!NOTE]  
> <span data-ttu-id="88583-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="88583-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="88583-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="88583-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="88583-105">關聯式資料庫中的索引會對應至相同的概念為 Entity framework core 中的索引。</span><span class="sxs-lookup"><span data-stu-id="88583-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="88583-106">慣例</span><span class="sxs-lookup"><span data-stu-id="88583-106">Conventions</span></span>

<span data-ttu-id="88583-107">依照慣例，索引會命名為`IX_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="88583-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="88583-108">對於複合索引`<property name>`會變成屬性名稱的底線分隔清單。</span><span class="sxs-lookup"><span data-stu-id="88583-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="88583-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="88583-109">Data Annotations</span></span>

<span data-ttu-id="88583-110">索引不可以使用資料註解來設定。</span><span class="sxs-lookup"><span data-stu-id="88583-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="88583-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="88583-111">Fluent API</span></span>

<span data-ttu-id="88583-112">您可以使用 Fluent API 來設定索引的名稱。</span><span class="sxs-lookup"><span data-stu-id="88583-112">You can use the Fluent API to configure the name of an index.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

<span data-ttu-id="88583-113">您也可以指定篩選條件。</span><span class="sxs-lookup"><span data-stu-id="88583-113">You can also specify a filter.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

<span data-ttu-id="88583-114">當使用 SQL Server 提供者 EF 加上 ' IS NOT NULL' 篩選所有可為 null 的資料行，則唯一索引的一部分。</span><span class="sxs-lookup"><span data-stu-id="88583-114">When using the SQL Server provider EF adds a 'IS NOT NULL' filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="88583-115">若要覆寫此慣例，您可以提供`null`值。</span><span class="sxs-lookup"><span data-stu-id="88583-115">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
