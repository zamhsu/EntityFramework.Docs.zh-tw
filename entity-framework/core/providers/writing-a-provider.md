---
title: 撰寫資料庫提供者的 EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: c7130b0d104cd26584d298da98eb3e7080ee7f3c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993662"
---
# <a name="writing-a-database-provider"></a>撰寫資料庫提供者

撰寫 Entity Framework Core 資料庫提供者的相關資訊，請參閱[因此您想要撰寫的 EF Core 提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)由[Arthur Vickers](https://github.com/ajcvickers)。

> [!NOTE]
> 這些文章有尚未更新從 EF Core 1.1，而且已有重大變更自那時起[問題 681](https://github.com/aspnet/EntityFramework.Docs/issues/681)正在追蹤更新這份文件。

EF Core 程式碼基底是開放原始碼，並包含數個可做為參考資料庫提供者。 您可以找到原始程式碼https://github.com/aspnet/EntityFrameworkCore。 它也可能有助於查看程式碼常用的協力廠商提供者，例如[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)， [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)，並[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)。 特別是，這些專案已設定，以從擴充，並在 NuGet 上執行我們所發行的功能測試。 強烈建議這種安裝程式。

## <a name="keeping-up-to-date-with-provider-changes"></a>保持最新提供者變更

在 2.1 版之後，以啟動工作，我們建立了[之變更的記錄](provider-log.md)，可能需要在提供者程式碼中對應的變更。 這被為了協助更新現有的提供者時，才能使用 EF Core 的新版本。

在 2.1 之前, 我們會使用[ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)並[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)我們的 GitHub 問題和類似的目的的提取要求中的標籤。 我們將 continiue 問題上使用這些標籤，來指出在指定的版本中的工作項目可能也需要在提供者中的工作。 A`providers-beware`標籤通常表示工作項目的實作可能會中斷提供者，雖然`providers-fyi`標籤通常會表示提供者並不會中斷，但程式碼可能需要變更，例如，若要啟用新功能。

## <a name="suggested-naming-of-third-party-providers"></a>建議的命名的協力廠商提供者

我們建議使用 NuGet 套件的下列命名。 這是與 EF Core 小組所傳遞的封裝的名稱一致。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

例如: 
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
