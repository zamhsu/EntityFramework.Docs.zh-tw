---
title: 相關資料和序列化-EF Core
description: 有關如何使用 Entity Framework Core 的相關資料迴圈的資訊，可能會影響序列化架構
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 6b28a1fc79a8c23e5249d36386c8ed06805dbe30
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078914"
---
# <a name="related-data-and-serialization"></a>相關資料和序列化

因為 EF Core 會自動執行導覽屬性的修正，所以您的物件圖形中會有迴圈。 例如，載入部落格與其相關文章將會產生參考文章集合的部落格物件。 那些文章都會具有針對該部落格的參考。

某些序列化架構不允許這種迴圈。 例如，如果找到迴圈，Json.NET 將會擲回下列例外狀況。

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog' (針對具有類型 'MyApplication.Models.Blog' 的屬性 'Blog' 偵測到自我參考迴圈)。

如果您使用 ASP.NET Core，可以將 Json.NET 設定為忽略它在物件圖形中找到的迴圈。 這項設定是在的 `ConfigureServices(...)` 方法中完成 `Startup.cs` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

另一個替代方式為使用 `[JsonIgnore]` 屬性裝飾導覽屬性的其中一個，它會指示 Json.NET 序列化時不要周遊該導覽屬性。
