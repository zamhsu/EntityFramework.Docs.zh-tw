---
title: 預設結構描述的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 800551bbadd0a9e8b5eb7070a8ccf6ed2407e3d2
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995362"
---
# <a name="default-schema"></a><span data-ttu-id="76725-102">預設結構描述</span><span class="sxs-lookup"><span data-stu-id="76725-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="76725-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="76725-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="76725-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="76725-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="76725-105">預設結構描述是結構描述未明確設定為該物件，將會在建立物件的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="76725-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="76725-106">慣例</span><span class="sxs-lookup"><span data-stu-id="76725-106">Conventions</span></span>

<span data-ttu-id="76725-107">依照慣例，此資料庫提供者會選擇最適合的預設結構描述。</span><span class="sxs-lookup"><span data-stu-id="76725-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="76725-108">例如，將會使用 Microsoft SQL Server`dbo`結構描述和 SQLite 不會使用結構描述 （由於 SQLite 中不支援結構描述）。</span><span class="sxs-lookup"><span data-stu-id="76725-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="76725-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="76725-109">Data Annotations</span></span>

<span data-ttu-id="76725-110">您可以設定使用資料註解的預設結構描述。</span><span class="sxs-lookup"><span data-stu-id="76725-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="76725-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="76725-111">Fluent API</span></span>

<span data-ttu-id="76725-112">您可以使用 Fluent API，來指定預設結構描述。</span><span class="sxs-lookup"><span data-stu-id="76725-112">You can use the Fluent API to specify a default schema.</span></span>

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
