---
title: 撰寫資料庫提供者-EF Core
description: 撰寫新的 Entity Framework Core 提供者的資訊
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 6d7a8a03c612eeda4d65917a2713e4570c671002
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616513"
---
# <a name="writing-a-database-provider"></a>撰寫資料庫提供者

如需撰寫 Entity Framework Core 資料庫提供者的詳細資訊，請參閱，您想要透過[Arthur Vickers](https://github.com/ajcvickers)[來撰寫 EF Core 提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)。

> [!NOTE]
> 這些貼文自 EF Core 1.1 以來尚未更新，並且自這段時間起已經有重大變更。  
[問題 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) 正在追蹤此檔的更新。

EF Core 程式碼基底是開放原始碼，並且包含數個可當做參考使用的資料庫提供者。 您可以在中找到原始程式碼 <https://github.com/aspnet/EntityFrameworkCore> 。 查看常用協力廠商提供者（例如 [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)和 [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)）的程式碼可能也會很有説明。 尤其是，這些專案會設定為從 NuGet 上發佈的功能測試進行擴充和執行。 強烈建議使用這種設定。

## <a name="keeping-up-to-date-with-provider-changes"></a>透過提供者變更保持最新狀態

從2.1 版之後的工作開始，我們已建立可能需要在提供者程式碼中對應變更的 [變更記錄](xref:core/providers/provider-log) 檔。 這是為了在更新現有的提供者以使用新版 EF Core 時提供協助。

在2.1 之前，我們 [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) 在 GitHub 問題上使用和標籤，並以類似的目的提取要求。 我們將 continiue 使用這些 lables 來解決問題，以指出指定版本中的哪些工作專案可能也需要在提供者中完成工作。 `providers-beware`標籤通常表示工作專案的執行可能會中斷提供者，而 `providers-fyi` 標籤通常表示提供者不會中斷，但是程式碼可能需要變更，例如啟用新的功能。

## <a name="suggested-naming-of-third-party-providers"></a>協力廠商提供者的建議命名

建議您針對 NuGet 套件使用下列命名。 這與 EF Core 小組提供的封裝名稱一致。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

例如：

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
