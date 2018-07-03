---
title: 設計階段 DbContext 建立-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
ms.technology: entity-framework-core
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 8b38d300d31038bdf5cd877aa3c42b7f5f97eabc
ms.sourcegitcommit: 7113e8675f26cbb546200824512078bf360225df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
ms.locfileid: "30202480"
---
<a name="design-time-dbcontext-creation"></a>設計階段 DbContext 建立
==============================
某些 EF Core 工具命令 (例如，[移轉][ 1]命令) 需要衍生`DbContext`以收集應用程式的相關詳細資料，在設計階段建立的執行個體實體類型和它們如何對應到資料庫結構描述。 在大部分情況下，最好的`DbContext`藉此建立要如何將以類似方式設定[設定在執行階段][2]。

工具會嘗試建立可以使用各種方式`DbContext`:

<a name="from-application-services"></a>從應用程式服務
-------------------------
如果您的啟始專案的 ASP.NET Core 應用程式，工具會嘗試從應用程式的服務提供者取得 DbContext 物件。

此工具第一次嘗試叫用來取得服務提供者`Program.BuildWebHost()`和存取`IWebHost.Services`屬性。

> [!NOTE]
> 當您建立新的 ASP.NET Core 2.0 應用程式時，預設會包含此攔截程序。 在舊版的 EF Core 與 ASP.NET Core，工具會嘗試叫用`Startup.ConfigureServices`直接為了取得應用程式的服務提供者，但這個模式無法再正常運作中 ASP.NET Core 2.0 應用程式。 如果您要升級為 2.0 的 ASP.NET Core 1.x 應用程式，您可以[修改您`Program`類別，以符合新模式][3]。

`DbContext`本身和任何相依性，其建構函式中的需要註冊應用程式的服務提供者服務的身分。 這可以輕易地達成具有[建構函式`DbContext`的執行個體的`DbContextOptions<TContext>`做為引數][ 4]和使用[ `AddDbContext<TContext>` 方法][5].

<a name="using-a-constructor-with-no-parameters"></a>使用不含任何參數的建構函式
--------------------------------------
如果無法從應用程式服務提供者取得 DbContext，工具尋找衍生`DbContext`專案內的類型。 然後嘗試建立使用不含任何參數的建構函式的執行個體。 這可以是預設建構函式，如果`DbContext`已設定為使用[ `OnConfiguring` ] [ 6]方法。

<a name="from-a-design-time-factory"></a>從設計階段 factory
--------------------------
您也可以分辨工具如何實作來建立您的 DbContext`IDesignTimeDbContextFactory<TContext>`介面： 如果實作此介面的類別中找到 衍生的專案`DbContext`或在應用程式的啟始專案，這些工具略過以其他方式改為建立的 DbContext 和使用的設計階段處理站。

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
> `args`參數是目前未使用。 沒有[問題][ 7]追蹤指定設計階段工具的引數的能力。

設計階段處理站會特別有用，如果您需要以不同的方式設定的設計階段和執行階段的 DbContext 如果`DbContext`建構函式會採用額外的參數未登錄在 DI，如果您未使用 DI 完全或部分原因不想要有`BuildWebHost`中 ASP.NET Core 應用程式的方法  
`Main` 類別。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [4]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [5]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [6]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [7]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
