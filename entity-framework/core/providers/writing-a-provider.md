---
title: 撰寫資料庫提供者-EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 9d52a8581772cc5405e94966fa7ebdff4128c252
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654769"
---
# <a name="writing-a-database-provider"></a>撰寫資料庫提供者

如需有關撰寫 Entity Framework Core 資料庫提供者的詳細資訊，請參閱，以[Arthur Vickers](https://github.com/ajcvickers)[撰寫 EF Core 提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)。

> [!NOTE]
> 這些文章在 EF Core 1.1 之後尚未更新，而且已經大幅變更，因為該時間[問題 681](https://github.com/aspnet/EntityFramework.Docs/issues/681)正在追蹤此檔的更新。

EF Core 程式碼基底是開放原始碼，包含數個可做為參考的資料庫提供者。 您可以在 <https://github.com/aspnet/EntityFrameworkCore>找到原始程式碼。 查看常用協力廠商提供者的程式碼，例如[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)和[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)，可能也會很有説明。 特別是，這些專案會進行設定，以擴充並執行我們在 NuGet 上發行的功能測試。 強烈建議使用這種設定。

## <a name="keeping-up-to-date-with-provider-changes"></a>保持最新的提供者變更

從2.1 版後的工作開始，我們已建立可能需要提供者程式碼中對應變更的[變更記錄](provider-log.md)檔。 這是為了在更新現有提供者以使用新版本的 EF Core 時提供協助。

在2.1 之前，我們使用了 GitHub 問題的[`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)和[`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)標籤，以及類似用途的提取要求。 我們會 continiue 在問題上使用這些 lables，以指出給定版本中的工作專案可能也需要在提供者中完成工作。 `providers-beware` 標籤通常表示工作專案的執行可能會中斷提供者，而 `providers-fyi` 標籤通常表示提供者不會中斷，但程式碼可能需要變更，例如啟用新功能。

## <a name="suggested-naming-of-third-party-providers"></a>協力廠商提供者的建議命名

我們建議使用 NuGet 套件的下列命名。 這與 EF Core 小組所提供的封裝名稱一致。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

例如:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
