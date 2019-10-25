---
title: 設計階段服務-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: ff0243a588d5e957aed89fcf1ce7462b5b9a747a
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811946"
---
# <a name="design-time-services"></a><span data-ttu-id="966ac-102">設計階段服務</span><span class="sxs-lookup"><span data-stu-id="966ac-102">Design-time services</span></span>

<span data-ttu-id="966ac-103">工具所使用的某些服務只會在設計階段使用。</span><span class="sxs-lookup"><span data-stu-id="966ac-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="966ac-104">這些服務會與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="966ac-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="966ac-105">若要覆寫其中一個服務（例如，用來產生遷移檔案的服務），請將 `IDesignTimeServices` 的執行新增至您的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="966ac-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
