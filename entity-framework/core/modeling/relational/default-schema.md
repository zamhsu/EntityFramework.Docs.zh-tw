---
title: 預設架構-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: ae903ed7200859430aecc55073651236759bc6ce
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197125"
---
# <a name="default-schema"></a>預設結構描述

> [!NOTE]  
> 本節中的組態一般適用於關聯式資料庫。 當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。

如果未針對該物件明確設定架構，預設架構就是將在其中建立物件的資料庫架構。

## <a name="conventions"></a>慣例

依照慣例，資料庫提供者會選擇最適當的預設架構。 例如，Microsoft SQL Server 將使用`dbo`架構，而 sqlite 不會使用架構（因為 SQLite 中不支援架構）。

## <a name="data-annotations"></a>資料註釋

您不能使用資料批註來設定預設的架構。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來指定預設架構。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/DefaultSchema.cs?highlight=7)] -->
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
