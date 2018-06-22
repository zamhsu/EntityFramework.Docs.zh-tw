---
title: 計算資料行的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
ms.technology: entity-framework-core
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: 95312504286bd34cc666b5a21273835c4b35d379
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052478"
---
# <a name="computed-columns"></a><span data-ttu-id="2213a-102">計算資料行</span><span class="sxs-lookup"><span data-stu-id="2213a-102">Computed Columns</span></span>

> [!NOTE]  
> <span data-ttu-id="2213a-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="2213a-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="2213a-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="2213a-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="2213a-105">計算資料行是計算其值在資料庫中的資料行。</span><span class="sxs-lookup"><span data-stu-id="2213a-105">A computed column is a column whose value is calculated in the database.</span></span> <span data-ttu-id="2213a-106">計算資料行可以使用資料表中的其他資料行，來計算其值。</span><span class="sxs-lookup"><span data-stu-id="2213a-106">A computed column can use other columns in the table to calculate its value.</span></span>

## <a name="conventions"></a><span data-ttu-id="2213a-107">慣例</span><span class="sxs-lookup"><span data-stu-id="2213a-107">Conventions</span></span>

<span data-ttu-id="2213a-108">依照慣例，不會在模型中建立計算資料行。</span><span class="sxs-lookup"><span data-stu-id="2213a-108">By convention, computed columns are not created in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="2213a-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="2213a-109">Data Annotations</span></span>

<span data-ttu-id="2213a-110">計算資料行不可以設定使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="2213a-110">Computed columns can not be configured with Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="2213a-111">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="2213a-111">Fluent API</span></span>

<span data-ttu-id="2213a-112">您可以使用 fluent 應用程式開發的應用程式開發介面來指定屬性應該為計算資料行對應。</span><span class="sxs-lookup"><span data-stu-id="2213a-112">You can use the Fluent API to specify that a property should map to a computed column.</span></span>

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
