---
title: 記錄和攔截的總覽-EF Core
description: EF Core 的記錄、事件、攔截器和診斷總覽
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066596"
---
# <a name="overview-of-logging-and-interception"></a>記錄和攔截的總覽

Entity Framework Core (EF Core) 包含數個產生記錄、回應事件和取得診斷的機制。 這些都是針對不同的情況而量身訂做的，因此請務必為手邊的工作選取最佳的機制，即使有多個機制可以運作也是一樣。 例如，資料庫攔截器可以用來記錄 SQL，但是這種方式較適合用來處理其中一個記錄特定機制。 此頁面提供每個機制的總覽，並說明每個機制的使用時機。

## <a name="simple-logging"></a>簡單記錄

> [!NOTE]
> 這項功能已在 EF Core 5.0 中新增。

您可以使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)從任何類型的應用程式存取 EF Core 記錄 <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> 設定 [DbCoNtext 實例](xref:core/miscellaneous/configuring-dbcontext)時。 這項設定通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。 例如︰

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

此概念類似于 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6。

如需詳細資訊，請參閱 [簡單記錄](xref:core/miscellaneous/events/simple-logging) 。
