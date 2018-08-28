---
title: 設計階段 DbContext 建立-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 66fec7605b6ac2da0af1e801f8a1dca0789aea35
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993714"
---
<a name="design-time-dbcontext-creation"></a>設計階段 DbContext 建立
==============================
某些 EF Core 工具命令 (例如，[移轉][ 1]命令) 需要衍生`DbContext`以收集應用程式的相關詳細資料，在設計階段建立的執行個體實體類型和它們如何對應到資料庫結構描述。 在大部分情況下，最好可`DbContext`藉此建立設定類似的方式，是它會如何[設定在執行階段][2]。

有的各種工具會嘗試建立`DbContext`:

<a name="from-application-services"></a>從應用程式服務
-------------------------
如果您的啟始專案的 ASP.NET Core 應用程式，工具會嘗試從應用程式的服務提供者取得 DbContext 物件。

工具會先嘗試叫用來取得服務提供者`Program.BuildWebHost()`，並存取`IWebHost.Services`屬性。

> [!NOTE]
> 當您建立新的 ASP.NET Core 2.0 應用程式時，預設會包含此攔截程序。 在舊版的 EF Core 與 ASP.NET Core，工具會嘗試叫用`Startup.ConfigureServices`直接為了取得應用程式的服務提供者，但這個模式無法再正常運作中 ASP.NET Core 2.0 應用程式。 如果您要升級為 2.0 的 ASP.NET Core 1.x 應用程式，您可以[修改您`Program`類別，以符合新模式][3]。

`DbContext`本身和其建構函式中的任何相依性必須註冊為應用程式的服務提供者中的服務。 這可以輕鬆地讓達成[建構函式`DbContext`採用的執行個體`DbContextOptions<TContext>`做為引數][ 4]並使用[`AddDbContext<TContext>`方法][5].

<a name="using-a-constructor-with-no-parameters"></a>使用不含參數的建構函式
--------------------------------------
如果無法從應用程式服務提供者取得 DbContext，這些工具尋找衍生`DbContext`專案內的型別。 然後嘗試建立使用不含參數的建構函式的執行個體。 如果這是預設建構函式`DbContext`設定時，使用[ `OnConfiguring` ] [ 6]方法。

<a name="from-a-design-time-factory"></a>從設計階段處理站
--------------------------
您也可以告訴工具如何藉由實作來建立您的 DbContext`IDesignTimeDbContextFactory<TContext>`介面： 如果實作此介面的類別將找到在相同的專案衍生的`DbContext`或在應用程式的啟始專案，這些工具略過改為建立 DbContext 和使用的設計階段處理站的其他方法。

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;
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
> `args`參數是目前未使用。 沒有[問題][7]追蹤指定的設計階段引數，從工具的能力。

設計階段處理站可以是特別有用，如果您要以不同的方式設定的設計階段和執行階段的 DbContext，如果`DbContext`建構函式會採用額外的參數不在中註冊 DI，如果您未使用 DI，或者某些原因不想已`BuildWebHost`方法在 ASP.NET Core 應用程式的`Main`類別。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [4]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [5]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [6]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [7]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
