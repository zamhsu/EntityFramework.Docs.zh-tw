---
title: EF6 和 EF Core - 在相同的應用程式中使用它們
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: bcf0a26535c4ec880a9ac25478c987fb683f6d26
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78419641"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="55cad-102">在相同的應用程式中使用 EF Core 和 EF6</span><span class="sxs-lookup"><span data-stu-id="55cad-102">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="55cad-103">可以通過同時安裝 NuGet 包在同一應用程式或庫中使用 EF Core 和 EF6。</span><span class="sxs-lookup"><span data-stu-id="55cad-103">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="55cad-104">某些類型在 EF Core 和 EF6 中有相同的名稱，僅命名空間不同，使得在相同程式碼檔案中同時使用 EF Core 和 EF6 變得有些複雜。</span><span class="sxs-lookup"><span data-stu-id="55cad-104">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="55cad-105">使用命名空間別名指示詞可輕鬆避免模稜兩可的指涉。</span><span class="sxs-lookup"><span data-stu-id="55cad-105">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="55cad-106">例如：</span><span class="sxs-lookup"><span data-stu-id="55cad-106">For example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="55cad-107">如果您要移植具有多個 EF 模型的現有應用程式，可以選擇只將其中一部分選擇性地移植到 EF Core，其他則繼續使用 EF6。</span><span class="sxs-lookup"><span data-stu-id="55cad-107">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
