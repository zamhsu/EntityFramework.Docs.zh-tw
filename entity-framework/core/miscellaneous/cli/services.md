---
title: 設計階段服務-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
ms.openlocfilehash: e1cacdd4f40f9c395d8c88a91df4a92ef27001a8
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997527"
---
<a name="design-time-services"></a>設計階段服務
====================
某些工具所使用的服務才會在設計階段使用。 若要防止應用程式正在部署的 EF Core 執行階段服務會分開管理這些服務。 若要覆寫其中一個服務 （例如產生移轉檔案服務），將新增的實作`IDesignTimeServices`到您的啟始專案。

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
