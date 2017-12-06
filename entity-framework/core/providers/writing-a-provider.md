---
title: "撰寫資料庫提供者的 EF 核心"
author: anmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
ms.technology: entity-framework-core
uid: core/providers/writing-a-provider
ms.openlocfilehash: 9d6d3748a9097b3b8eeee2a8a516c53f3b2afa58
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="writing-a-database-provider"></a>撰寫資料庫提供者

撰寫 Entity Framework Core 資料庫提供者的相關資訊，請參閱[因此您想要撰寫的 EF 核心提供者](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)由[Arthur Vickers](https://github.com/ajcvickers)。

EF 核心程式碼基底是開放原始碼，並包含數個可做為參考資料庫提供者。 您可以在 https://github.com/aspnet/EntityFramework 尋找原始碼。

## <a name="the-providers-beware-label"></a>提供者注意標籤

當您開始在提供者上時，監看[ `providers-beware` ](https://github.com/aspnet/EntityFramework/labels/providers-beware)索引標籤上，我們的 GitHub 問題和提取要求。 我們可以使用這個標籤以識別可能會影響提供者撰寫人員的變更。

## <a name="suggested-naming-of-third-party-providers"></a>建議的協力廠商提供者命名

我們建議使用下列命名的 NuGet 封裝。 這是與 EF 核心小組所傳遞的封裝的名稱一致。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

例如：
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
