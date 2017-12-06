---
title: "設計階段 DbContext 建立-EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 5fcd9e362d76127e7acadd9e552ef3ac90967a37
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="design-time-dbcontext-creation"></a>設計階段 DbContext 建立
==============================
有些 EF 工具命令需要在設計上建立的 DbContext 執行個體的時間 （例如，當執行移轉命令）。 有許多種，工具會嘗試建立它。

<a name="from-application-services"></a>從應用程式服務
-------------------------
如果您的啟始專案的 ASP.NET Core 應用程式，工具會嘗試從應用程式的服務提供者取得 DbContext 物件。 他們取得叫用`Program.BuildWebHost()`和存取`IWebHost.Services`屬性。 使用註冊任何 DbContext`IServiceCollection.AddDbContext<TContext>()`可以找到並以此方式建立。 此模式已[ASP.NET Core 2.0 中導入][1]

<a name="using-the-default-constructor"></a>使用預設建構函式
-----------------------------
如果無法從應用程式服務提供者取得 DbContext，工具會尋找專案內的 DbContext 類型。 嘗試使用其預設建構函式加以建立。

<a name="from-a-design-time-factory"></a>從設計階段 factory
--------------------------
您也可以分辨工具如何實作來建立您的 DbContext `IDesignTimeDbContextFactory`。 如果您的專案內找到實作此介面的類別，則工具會略過之其他方式建立的 DbContext。
它們一律會使用在設計階段的處理站。 此處理站是特別有用，如果您需要在執行階段，於設計階段的不同的方式設定 DbContext。

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Infrastructure;

namespace MyProject
{
    public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
    {
        public BloggingContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
            optionsBuilder.UseSqlite("Data Source=blog.db");

            return new BloggingContext(optionsBuilder.Options);
        }
    }
}
```

> [!NOTE]
> `args`參數是目前未使用。 沒有[問題][ 2]追蹤指定設計階段工具的引數的能力。

  [1]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [2]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
