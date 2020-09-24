---
title: DbCoNtext 共用
description: Entity Framework Core 中的 DbCoNtext 共用
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: fd5f53ff97a73895f0c4239439730dd8cb3ecc29
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2020
ms.locfileid: "91215595"
---
# <a name="dbcontext-pooling"></a>DbContext 共用

`AddDbContextPool` 啟用實例的共用 `DbContext` 。 內容共用可以藉由重複使用內容實例來增加高規模案例的輸送量，例如 web 伺服器，而不是為每個要求建立新的實例。

使用 EF Core 的 ASP.NET Core 應用程式中的一般模式牽涉到將自訂類型註冊至相依性 <xref:Microsoft.EntityFrameworkCore.DbContext> [插入](/aspnet/core/fundamentals/dependency-injection) 容器，並透過控制器或 Razor Pages 中的函式參數取得該類型的實例。 使用函式插入時，會為每個要求建立新的內容實例。

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 啟用可重複使用的內容實例集區。 若要使用內容共用，請使用 `AddDbContextPool` 方法，而不是 `AddDbContext` 在服務註冊期間：

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

`AddDbContextPool`使用時，在要求內容實例時，EF 會先檢查集區中是否有可用的實例。 要求處理完成之後，會重設執行個體上的任何狀態，並將執行個體本身傳回到集區。

這在概念上類似于連接共用在 ADO.NET 提供者中的運作方式，而且具有節省部分初始化內容實例成本的優點。

的 `poolSize` 參數會 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 設定集區保留的實例數目上限。 一旦 `poolSize` 超過此限制，就不會快取新的內容實例，而且 EF 會回到依需求建立實例的非共用行為。

## <a name="limitations"></a>限制

應用程式應該進行程式碼剖析和測試，以顯示內容初始化是相當大的成本。

`AddDbContextPool` 有幾項限制可在內容的方法中完成 `OnConfiguring` 。

> [!WARNING]  
> 避免在維護狀態的應用程式中使用內容共用。 例如，內容中不應該跨要求共用的私用欄位。 EF Core 只會在將內容實例新增至集區之前，重設其感知的狀態。

內容共用的運作方式是跨要求重複使用相同的內容實例。 這表示它實際上是以實例本身的 [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 來註冊，以便能夠保存。

內容共用適用于內容設定（包括已解決的服務）在要求之間固定的案例。 針對需要 [範圍](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 服務的情況，或需要變更設定時，請勿使用共用。 除了在高度優化的案例中，來自共用的效能提升通常是可忽略的。
