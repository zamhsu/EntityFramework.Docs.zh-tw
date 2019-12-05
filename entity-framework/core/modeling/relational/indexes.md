---
title: 索引（關係資料庫）-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/modeling/relational/indexes
ms.openlocfilehash: e14615275f85ee9b6b32d080905465d33963feca
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824571"
---
# <a name="indexes-relational-database"></a><span data-ttu-id="c8bc8-102">索引（關係資料庫）</span><span class="sxs-lookup"><span data-stu-id="c8bc8-102">Indexes (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="c8bc8-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c8bc8-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c8bc8-105">關係資料庫中的索引會對應到與 Entity Framework 核心中的索引相同的概念。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="c8bc8-106">慣例</span><span class="sxs-lookup"><span data-stu-id="c8bc8-106">Conventions</span></span>

<span data-ttu-id="c8bc8-107">依照慣例，索引會命名為 `IX_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="c8bc8-108">若為複合索引，`<property name>` 會變成以底線分隔的屬性名稱清單。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c8bc8-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="c8bc8-109">Data Annotations</span></span>

<span data-ttu-id="c8bc8-110">無法使用資料批註來設定索引。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c8bc8-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c8bc8-111">Fluent API</span></span>

<span data-ttu-id="c8bc8-112">您可以使用流暢的 API 來設定索引的名稱。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-112">You can use the Fluent API to configure the name of an index.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexName.cs?name=Model&highlight=9)]

<span data-ttu-id="c8bc8-113">您也可以指定篩選準則。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-113">You can also specify a filter.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexFilter.cs?name=Model&highlight=9)]

<span data-ttu-id="c8bc8-114">使用 SQL Server 提供者 EF 時，會為屬於唯一索引一部分的所有可為 null 的資料行加入 `'IS NOT NULL'` 篩選準則。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-114">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="c8bc8-115">若要覆寫此慣例，您可以提供 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-115">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a><span data-ttu-id="c8bc8-116">在 SQL Server 索引中包含資料行</span><span class="sxs-lookup"><span data-stu-id="c8bc8-116">Include Columns in SQL Server Indexes</span></span>

<span data-ttu-id="c8bc8-117">當查詢中的所有資料行都包含在索引中當做索引鍵或非索引鍵資料行時，您可以設定[內含資料行的索引](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)，以大幅提升查詢效能。</span><span class="sxs-lookup"><span data-stu-id="c8bc8-117">You can configure [indexes with included columns](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns) to significantly improve query performance when all columns in the query are included in the index as key or non-key columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexInclude.cs?name=Model)]
