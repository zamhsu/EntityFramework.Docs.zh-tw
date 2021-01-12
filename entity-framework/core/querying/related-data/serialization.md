---
title: 相關資料和序列化-EF Core
description: 有關如何使用 Entity Framework Core 的相關資料迴圈的資訊，可能會影響序列化架構
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 3e1152f09493d039032d9d88493b52a5ef425eb9
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129131"
---
# <a name="related-data-and-serialization"></a><span data-ttu-id="79a3a-103">相關資料和序列化</span><span class="sxs-lookup"><span data-stu-id="79a3a-103">Related data and serialization</span></span>

<span data-ttu-id="79a3a-104">因為 EF Core 會自動執行導覽屬性的修正，所以您的物件圖形中會有迴圈。</span><span class="sxs-lookup"><span data-stu-id="79a3a-104">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="79a3a-105">例如，載入部落格與其相關文章將會產生參考文章集合的部落格物件。</span><span class="sxs-lookup"><span data-stu-id="79a3a-105">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="79a3a-106">那些文章都會具有針對該部落格的參考。</span><span class="sxs-lookup"><span data-stu-id="79a3a-106">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="79a3a-107">某些序列化架構不允許這種迴圈。</span><span class="sxs-lookup"><span data-stu-id="79a3a-107">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="79a3a-108">例如，如果找到迴圈，Json.NET 將會擲回下列例外狀況。</span><span class="sxs-lookup"><span data-stu-id="79a3a-108">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="79a3a-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog' (針對具有類型 'MyApplication.Models.Blog' 的屬性 'Blog' 偵測到自我參考迴圈)。</span><span class="sxs-lookup"><span data-stu-id="79a3a-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="79a3a-110">如果您使用 ASP.NET Core，可以將 Json.NET 設定為忽略它在物件圖形中找到的迴圈。</span><span class="sxs-lookup"><span data-stu-id="79a3a-110">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="79a3a-111">這項設定是在的 `ConfigureServices(...)` 方法中完成 `Startup.cs` 。</span><span class="sxs-lookup"><span data-stu-id="79a3a-111">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

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

<span data-ttu-id="79a3a-112">另一個替代方式為使用 `[JsonIgnore]` 屬性裝飾導覽屬性的其中一個，它會指示 Json.NET 序列化時不要周遊該導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="79a3a-112">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
