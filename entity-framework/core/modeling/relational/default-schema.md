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
# <a name="default-schema"></a>預設結構描述

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

預設結構描述是結構描述未明確設定為該物件，將會在建立物件的資料庫結構描述。

## <a name="conventions"></a>慣例

依照慣例，此資料庫提供者會選擇最適合的預設結構描述。 例如，將會使用 Microsoft SQL Server`dbo`結構描述和 SQLite 不會使用結構描述 （由於 SQLite 中不支援結構描述）。

## <a name="data-annotations"></a>資料註釋

您可以設定使用資料註解的預設結構描述。

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，來指定預設結構描述。

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
