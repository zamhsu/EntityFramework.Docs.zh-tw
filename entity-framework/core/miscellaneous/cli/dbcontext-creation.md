---
title: 設計階段 DbCoNtext 建立-EF Core
description: 使用 Entity Framework Core 建立設計階段 DbCoNtext 的策略
author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: a9d7da2bb76d60ca63eb0dc189f924df125f0a7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062005"
---
# <a name="design-time-dbcontext-creation"></a>設計階段 DbContext 建立

某些 EF Core 工具命令 (例如，) 需要在設計階段建立 [衍生的][1] 實例，才能 `DbContext` 收集有關應用程式之實體類型的詳細資料，以及它們如何對應到資料庫架構。 在大部分的情況下，最好是以 `DbContext` 類似于 [執行時間設定][2]它的方式來設定建立。

工具會嘗試建立下列各種方式 `DbContext` ：

## <a name="from-application-services"></a>從應用程式服務

如果您的啟始專案使用 [ASP.NET Core Web 主機][3] 或 [.Net Core 泛型主機][4]，則工具會嘗試從應用程式的服務提供者取得 DbCoNtext 物件。

這些工具會先藉由 `Program.CreateHostBuilder()` 叫用、呼叫 `Build()` ，然後存取屬性來嘗試取得服務提供者 `Services` 。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> 當您建立新的 ASP.NET Core 應用程式時，預設會包含此攔截程式。

`DbContext`本身和其任何程式中的任何相依性都必須註冊為應用程式服務提供者中的服務。 只要將的函[ `DbContext` 式 `DbContextOptions<TContext>` 作為引數][5]，然後使用[ `AddDbContext<TContext>` 方法][6]，就可以輕鬆達成這個目的。

## <a name="using-a-constructor-with-no-parameters"></a>使用沒有參數的函式

如果無法從應用程式服務提供者取得 DbCoNtext，工具會尋找專案內的衍生 `DbContext` 類型。 然後，他們會嘗試使用不含參數的函式來建立實例。 如果是使用方法進行設定，這可以是預設的函式 `DbContext` [`OnConfiguring`][7] 。

## <a name="from-a-design-time-factory"></a>從設計階段工廠

您也可以藉由執行介面，告訴工具如何建立您的 DbCoNtext `IDesignTimeDbContextFactory<TContext>` ：如果在相同的專案中找到實作為此介面的類別 `DbContext` ，或在應用程式的啟始專案中找到這個介面，則工具會略過其他建立 DbCoNtext 的方法，並改為使用設計階段 factory。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> 在 EFCore 5.0 之前， `args` 參數未使用 () 會看到 [此問題][8] 。
> 這在 EFCore 5.0 中已修正，而且任何額外的設計階段引數都會透過該參數傳遞至應用程式。

如果您需要以不同于執行時間的方式來設定設計階段的 DbCoNtext，則設計階段處理站會特別有用，如果函式 `DbContext` 接受其他參數，則不會在 di 中註冊，如果您根本不使用 di，或基於某些原因，您不想 `BuildWebHost` 在 ASP.NET Core 應用程式的類別中有方法 `Main` 。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
