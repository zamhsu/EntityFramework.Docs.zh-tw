---
title: EF6 和 EF Core - 在相同的應用程式中使用它們
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: ead251c5454473738c2f2bfdac6557aa3e1c5591
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949069"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>在相同的應用程式中使用 EF Core 和 EF6

如果同時安裝 EF Core 和 EF6 的 NuGet 套件，就可以在相同的 .NET Framework 應用程式或程式庫中使用 EF Core 和 EF6。

某些類型在 EF Core 和 EF6 中有相同的名稱，僅命名空間不同，使得在相同程式碼檔案中同時使用 EF Core 和 EF6 變得有些複雜。 使用命名空間別名指示詞可輕鬆避免模稜兩可的指涉。 例如: 

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

如果您要移植具有多個 EF 模型的現有應用程式，可以選擇只將其中一部分選擇性地移植到 EF Core，其他則繼續使用 EF6。
