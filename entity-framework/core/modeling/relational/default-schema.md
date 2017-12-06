---
title: "預設結構描述的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
ms.technology: entity-framework-core
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 26106deb2d4e35ecf33e97790a83f9af77991aed
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="default-schema"></a><span data-ttu-id="541ee-102">預設結構描述</span><span class="sxs-lookup"><span data-stu-id="541ee-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="541ee-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="541ee-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="541ee-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="541ee-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="541ee-105">預設結構描述是結構描述未明確設定為該物件，會在中建立物件的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="541ee-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="541ee-106">慣例</span><span class="sxs-lookup"><span data-stu-id="541ee-106">Conventions</span></span>

<span data-ttu-id="541ee-107">依照慣例，資料庫提供者會選擇最適合的預設結構描述。</span><span class="sxs-lookup"><span data-stu-id="541ee-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="541ee-108">例如，Microsoft SQL Server 會使用`dbo`結構描述和 SQLite 將不會使用結構描述 （因為 SQLite 中不支援結構描述）。</span><span class="sxs-lookup"><span data-stu-id="541ee-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="541ee-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="541ee-109">Data Annotations</span></span>

<span data-ttu-id="541ee-110">您可以設定使用資料註解的預設結構描述。</span><span class="sxs-lookup"><span data-stu-id="541ee-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="541ee-111">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="541ee-111">Fluent API</span></span>

<span data-ttu-id="541ee-112">您可以使用 fluent 應用程式開發的應用程式開發介面指定預設結構描述。</span><span class="sxs-lookup"><span data-stu-id="541ee-112">You can use the Fluent API to specify a default schema.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/DefaultSchema.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasDefaultSchema("blogging");
    }
}
```
