---
title: 計算資料行的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: b88efdf69e5100e4eff55f3a41925d2d8e7c3178
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993949"
---
# <a name="computed-columns"></a><span data-ttu-id="3390c-102">計算資料行</span><span class="sxs-lookup"><span data-stu-id="3390c-102">Computed Columns</span></span>

> [!NOTE]  
> <span data-ttu-id="3390c-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="3390c-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="3390c-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="3390c-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="3390c-105">計算資料行是計算其值在資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="3390c-105">A computed column is a column whose value is calculated in the database.</span></span> <span data-ttu-id="3390c-106">計算資料行可以使用資料表中其他資料行，來計算其值。</span><span class="sxs-lookup"><span data-stu-id="3390c-106">A computed column can use other columns in the table to calculate its value.</span></span>

## <a name="conventions"></a><span data-ttu-id="3390c-107">慣例</span><span class="sxs-lookup"><span data-stu-id="3390c-107">Conventions</span></span>

<span data-ttu-id="3390c-108">依照慣例，不會在模型中建立計算資料行。</span><span class="sxs-lookup"><span data-stu-id="3390c-108">By convention, computed columns are not created in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="3390c-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="3390c-109">Data Annotations</span></span>

<span data-ttu-id="3390c-110">計算資料行不可以設定使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="3390c-110">Computed columns can not be configured with Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="3390c-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="3390c-111">Fluent API</span></span>

<span data-ttu-id="3390c-112">您可以使用 Fluent API，來指定屬性應該對應至計算資料行。</span><span class="sxs-lookup"><span data-stu-id="3390c-112">You can use the Fluent API to specify that a property should map to a computed column.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/ComputedColumn.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .Property(p => p.DisplayName)
            .HasComputedColumnSql("[LastName] + ', ' + [FirstName]");
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string DisplayName { get; set; }
}
```
