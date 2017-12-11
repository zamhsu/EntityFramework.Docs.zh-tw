---
title: "EF6 和 EF Core - 在相同的應用程式中使用它們"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: f6eb4bf7d99fbc61f8ffbd0dc7c6c17789395303
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>在相同的應用程式中使用 EF Core 和 EF6

如果同時安裝 EF Core 和 EF6 的 NuGet 套件，就可以在相同的 .NET Framework 應用程式或程式庫中使用 EF Core 和 EF6。 

某些類型在 EF Core 和 EF6 中有相同的名稱，僅命名空間不同，使得在相同程式碼檔案中同時使用 EF Core 和 EF6 變得有些複雜。 使用如下的命名空間別名指示詞，就可以輕鬆避免模稜兩可的指涉：

``` csharp
using Microsoft.EntityFrameworkCore;
using EF6 = System.Data.Entity; // e.g. EF6.DbContext
```

如果您要移植具有多個 EF 模型的現有應用程式，可以選擇只將其中一部分選擇性地移植到 EF Core，其他則繼續使用 EF6。
