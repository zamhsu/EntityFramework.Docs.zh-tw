---
title: 設計階段 DbCoNtext 建立-EF Core
description: 使用 Entity Framework Core 建立設計階段 DbCoNtext 的策略
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dbcontext-creation
ms.openlocfilehash: c5acf025407b6903d09cc1aff253d35a8801ab40
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166100"
---
# <a name="design-time-dbcontext-creation"></a>設計階段 DbContext 建立

某些 EF Core 工具命令 (例如，) 需要在設計階段建立 [衍生的][1] 實例，才能 `DbContext` 收集有關應用程式之實體類型的詳細資料，以及它們如何對應到資料庫架構。 在大部分的情況下，最好是以 `DbContext` 類似于 [執行時間設定][2]它的方式來設定建立。

工具會嘗試建立下列各種方式 `DbContext` ：

## <a name="from-application-services"></a>從應用程式服務

如果您的啟始專案使用 [ASP.NET Core Web 主機][3] 或 [.Net Core 泛型主機][4]，則工具會嘗試從應用程式的服務提供者取得 DbCoNtext 物件。

這些工具會先藉由 `Program.CreateHostBuilder()` 叫用、呼叫 `Build()` ，然後存取屬性來嘗試取得服務提供者 `Services` 。

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs#ApplicationService)]

> [!NOTE]
> 當您建立新的 ASP.NET Core 應用程式時，預設會包含此攔截程式。

`DbContext`本身和其任何程式中的任何相依性都必須註冊為應用程式服務提供者中的服務。 只要將的函[ `DbContext` 式 `DbContextOptions<TContext>` 作為引數][5]，然後使用[ `AddDbContext<TContext>` 方法][6]，就可以輕鬆達成這個目的。

## <a name="using-a-constructor-with-no-parameters"></a>使用沒有參數的函式

如果無法從應用程式服務提供者取得 DbCoNtext，工具會尋找專案內的衍生 `DbContext` 類型。 然後，他們會嘗試使用不含參數的函式來建立實例。 如果是使用方法進行設定，這可以是預設的函式 `DbContext` [`OnConfiguring`][7] 。

## <a name="from-a-design-time-factory"></a>從設計階段工廠

您也可以藉由執行介面，告訴工具如何建立您的 DbCoNtext <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601?displayProperty=nameWithType> ：如果在相同的專案中找到實作為此介面的類別 `DbContext` ，或在應用程式的啟始專案中找到這個介面，則工具會略過其他建立 DbCoNtext 的方法，並改為使用設計階段 factory。

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs#BloggingContextFactory)]

> [!NOTE]
> 在 EFCore 5.0 之前， `args` 參數未使用 () 會看到 [此問題][8] 。
> 這在 EFCore 5.0 中已修正，而且任何額外的設計階段引數都會透過該參數傳遞至應用程式。

如果您需要在 `DbContext` 設計階段與執行時間不同的情況下設定不同的設計階段，則如果在 `DbContext` di 中使用其他參數，則如果您完全不使用 di，或如果基於某些原因，您不想在 `CreateHostBuilder` ASP.NET Core 應用程式的類別中擁有方法，設計階段處理站可能會特別有用 `Main` 。

## <a name="args"></a>Args

<xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601.CreateDbContext%2A?displayProperty=nameWithType>和都 `Program.CreateHostBuilder` 接受命令列引數。

從 EF Core 5.0 開始，您可以從工具指定這些引數：

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update -- --environment Production
```

`--`標記會指示 `dotnet ef` 將後面的所有專案視為引數，而不會嘗試將它們剖析為選項。 未使用的任何額外引數 `dotnet ef` 都會轉送至應用程式。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database -Args '--environment Production'
```

***

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/dbcontext-configuration/index
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/dbcontext-configuration/index#constructor-argument
  [6]: xref:core/dbcontext-configuration/index#using-dbcontext-with-dependency-injection
  [7]: xref:core/dbcontext-configuration/index#onconfiguring
  [8]: https://github.com/dotnet/efcore/issues/8332
