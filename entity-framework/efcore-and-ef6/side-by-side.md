---
title: EF6 和 EF Core - 在相同的應用程式中使用它們
description: 在相同應用程式中同時使用 Entity Framework Core 和 Entity Framework 6 的指引
author: ajcvickers
ms.date: 01/23/2019
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: a9a8ab0ec77acf0fb1d1b1408d5711a6c8fa6664
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073522"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>在相同的應用程式中使用 EF Core 和 EF6

您可以同時安裝這兩個 NuGet 套件，以在相同的應用程式或程式庫中使用 EF Core 和 EF6。

某些類型在 EF Core 和 EF6 中有相同的名稱，僅命名空間不同，使得在相同程式碼檔案中同時使用 EF Core 和 EF6 變得有些複雜。 使用命名空間別名指示詞可輕鬆避免模稜兩可的指涉。 例如：

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

如果您要移植具有多個 EF 模型的現有應用程式，可以選擇只將其中一部分選擇性地移植到 EF Core，其他則繼續使用 EF6。
