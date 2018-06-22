---
title: 建立模型 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
ms.technology: entity-framework-core
uid: core/modeling/index
ms.openlocfilehash: 1ad0f6891fbc8ba2e4d102cc9997f053a9dddb66
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812433"
---
# <a name="creating-a-model"></a>建立模型

Entity Framework 會使用一組慣例，根據您實體類別的圖形建置模型。 您可以指定其他組態來補充及 (或) 覆寫慣例探索到的項目。

本文涵蓋可套用至將目標設為任何資料存放區之模型的組態，以及將目標設為任何關聯式資料庫時可套用的組態。 提供者也可以啟用特定資料存放區專屬的組態。 針對提供者專屬組態的文件，請參閱[資料庫提供者](../providers/index.md)一節。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples)。

## <a name="methods-of-configuration"></a>組態方法

### <a name="fluent-api"></a>Fluent API

您可以覆寫衍生內容中的 `OnModelCreating` 方法，並使用 `ModelBuilder API` 來設定模型。 這是最強大的組態方法，讓您能夠指定組態而不修改實體類別。 Fluent API 組態具有最高的優先順序，且會覆寫慣例及資料註解。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Required.cs?range=5-15&highlight=5-10)] -->

``` csharp
    class MyContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Blog>()
                .Property(b => b.Url)
                .IsRequired();
        }
    }
```

### <a name="data-annotations"></a>資料註釋

您也可以將屬性 (又稱資料註解) 套用到類別及屬性。 資料註解會覆寫慣例，但會受到 Fluent API 組態覆寫。

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?range=11-16&highlight=4)] -->
``` csharp
    public class Blog
    {
        public int BlogId { get; set; }
        [Required]
        public string Url { get; set; }
    }
```
