---
title: 設計階段服務-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
ms.technology: entity-framework-core
ms.openlocfilehash: f9c8208a59bfcefeaab01ea69e65fe809a0b3d89
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
ms.locfileid: "26053688"
---
<a name="design-time-services"></a>設計階段服務
====================
某些工具使用的服務才會在設計階段使用。 若要防止應用程式正在部署的 EF Core 執行階段服務會分開管理這些服務。 若要覆寫其中一個服務 （例如產生移轉檔案服務），將加入的實作`IDesignTimeServices`至您的啟始專案。

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
