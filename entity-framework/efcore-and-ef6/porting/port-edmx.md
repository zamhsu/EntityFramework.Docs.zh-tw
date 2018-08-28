---
title: 從 EF6 移植到 EF Core-移植 EDMX 為基礎的模型
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 2c3336ac675a830566001a0ddb3777839f52db18
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997407"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>移植到 EF Core EF6 EDMX 型模型

EF Core 不支援模型的 EDMX 檔案格式。 連接埠這些模型的最佳選項是從您的應用程式的資料庫中產生新的程式碼為基礎的模型。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF Core NuGet 封裝

安裝`Microsoft.EntityFrameworkCore.Tools`NuGet 套件。

## <a name="regenerate-the-model"></a>重新產生模型

您現在可以使用反向工程功能來建立模型，根據您現有的資料庫。

在 Package Manager Console 執行下列命令 (工具 –> NuGet 套件管理員 –> Package Manager Console)。 請參閱[套件管理員主控台 (Visual Studio)](../../core/miscellaneous/cli/powershell.md)命令選項，以建立結構的資料表等子集。

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

例如，以下是命令來建立模型，以從您的 SQL Server LocalDB 執行個體上的部落格資料庫。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>移除 EF6 模型

您現在會從您的應用程式移除 EF6 模型。

它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF Core 和 EF6 可以使用-並存相同的應用程式中。 不過，如果您不想要使用 EF6 應用程式的任何區域中，然後解除安裝封裝有助於讓需要注意的程式碼片段上的編譯錯誤。

## <a name="update-your-code"></a>更新您的程式碼

此時，它是定址的編譯錯誤，並檢閱看 EF6 和 EF Core 之間的行為變更會影響您的程式碼。

## <a name="test-the-port"></a>測試連接埠

只在您的應用程式會編譯，因為並不表示它成功匯出至 EF Core。 您必須測試您的應用程式，以確保沒有任何行為變更造成不良影響您的應用程式的所有區域。

> [!TIP]
> 請參閱[開始使用 ASP.NET Core 與現有的資料庫在 EF Core](xref:core/get-started/aspnetcore/existing-db)對於如何使用現有的資料庫，其他參考 
