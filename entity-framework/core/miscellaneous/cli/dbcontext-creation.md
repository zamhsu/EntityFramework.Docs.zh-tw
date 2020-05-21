---
title: 設計階段 DbCoNtext 建立-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 0b0271dcabea63a2529c091cc14cb9059d56ac8d
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672954"
---
# <a name="design-time-dbcontext-creation"></a>設計階段 DbContext 建立

某些 EF Core 工具命令（例如，[遷移][1]命令）需要 `DbContext` 在設計階段建立衍生實例，才能收集應用程式實體類型的詳細資料，以及它們如何對應到資料庫架構。 在大部分的情況下，最好是以 `DbContext` 類似于[在執行時間設定][2]它的方式來設定建立的。

工具有各種方式可嘗試建立 `DbContext` ：

## <a name="from-application-services"></a>從應用程式服務

如果您的啟始專案使用[ASP.NET Core Web 主機][3]或[.Net Core 泛型主機][4]，則工具會嘗試從應用程式的服務提供者取得 DbCoNtext 物件。

這些工具會先藉由 `Program.CreateHostBuilder()` 叫用、呼叫 `Build()` ，然後存取屬性，來嘗試取得服務提供者 `Services` 。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> 當您建立新的 ASP.NET Core 應用程式時，預設會包含此攔截。

`DbContext`本身和其任何函式中的任何相依性都必須在應用程式的服務提供者中註冊為服務。 [在 `DbContext` 接受實例 `DbContextOptions<TContext>` 做為引數][5]並使用[ `AddDbContext<TContext>` 方法][6]的上，就可以輕鬆達成此目的。

## <a name="using-a-constructor-with-no-parameters"></a>使用不含參數的函式

如果無法從應用程式服務提供者取得 DbCoNtext，工具會在專案內尋找衍生的 `DbContext` 型別。 然後，他們會嘗試使用不含參數的函式來建立實例。 如果 `DbContext` 是使用方法設定，這可以是預設的函式 [`OnConfiguring`][7] 。

## <a name="from-a-design-time-factory"></a>從設計階段 factory

您也可以藉由執行介面，告訴工具如何建立您的 DbCoNtext `IDesignTimeDbContextFactory<TContext>` ：如果在與所衍生或應用程式啟始專案相同的專案中找到執行此介面的類別 `DbContext` ，則工具會略過建立 DbCoNtext 的其他方式，並改用設計階段 factory。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> 在 EFCore 5.0 之前， `args` 參數未使用（請參閱[此問題][8]）。
> 這在 EFCore 5.0 中是固定的，而且任何其他設計階段引數都會透過該參數傳遞至應用程式。

如果您需要以不同的方式設定設計階段的 DbCoNtext，而不是在執行時間時，如果 `DbContext` 您不是使用 di，或基於某些原因，您不想在 `BuildWebHost` ASP.NET Core 應用程式的類別中有方法，設計階段 factory 會特別有用 `Main` 。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
