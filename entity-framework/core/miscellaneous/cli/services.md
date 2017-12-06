---
title: "設計階段服務-EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
ms.technology: entity-framework-core
ms.openlocfilehash: f9c8208a59bfcefeaab01ea69e65fe809a0b3d89
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="design-time-services"></a><span data-ttu-id="53008-102">設計階段服務</span><span class="sxs-lookup"><span data-stu-id="53008-102">Design-time services</span></span>
====================
<span data-ttu-id="53008-103">某些工具使用的服務才會在設計階段使用。</span><span class="sxs-lookup"><span data-stu-id="53008-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="53008-104">若要防止應用程式正在部署的 EF 核心執行階段服務會分開管理這些服務。</span><span class="sxs-lookup"><span data-stu-id="53008-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="53008-105">若要覆寫其中一個服務 （例如產生移轉檔案服務），將加入的實作`IDesignTimeServices`至您的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="53008-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
