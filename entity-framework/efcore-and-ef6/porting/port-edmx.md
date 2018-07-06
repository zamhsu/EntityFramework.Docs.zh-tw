---
title: 從 EF6 移植到 EF Core-移植 EDMX 為基礎的模型
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: c999d2114c76ee3a7615ae897b42ee3376cff14e
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812686"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>移植到 EF Core EF6 EDMX 型模型

EF Core 不支援模型的 EDMX 檔案格式。 最佳的選項，這些模型中，連接埠是從應用程式資料庫中產生新的程式碼為基礎的模型。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF Core NuGet 封裝

安裝`Microsoft.EntityFrameworkCore.Tools`NuGet 封裝。

## <a name="regenerate-the-model"></a>重新產生模型

您現在可以使用反向工程功能來建立模型，根據您現有的資料庫。

Package Manager Console 中，執行下列命令 (工具 –> NuGet 套件管理員 –> Package Manager Console)。 請參閱[Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) scaffold 等資料表子集的命令選項。

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

例如，以下是 scaffold 模型，以從您的 SQL Server LocalDB 執行個體上的部落格資料庫命令。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>移除 EF6 模型

您現在會從您的應用程式移除 EF6 模型。

它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF Core 和 EF6 可以使用-並存相同的應用程式中。 不過，如果您不打算使用 EF6 應用程式的任何區域中，然後解除安裝封裝有助於讓上需要注意的程式碼片段的編譯錯誤。

## <a name="update-your-code"></a>更新您的程式碼

此時，它是定址的編譯錯誤，並檢閱看 EF6 和 EF Core 之間的行為變更會影響您的程式碼。

## <a name="test-the-port"></a>測試連接埠

只在您的應用程式會編譯，因為並不表示它成功匯出至 EF Core。 您必須測試您的應用程式，以確保沒有任何行為變更已經造成不利影響您的應用程式的所有區域。

> [!TIP]
> 請參閱[開始使用 ASP.NET Core 與現有的資料庫在 EF Core](xref:core/get-started/aspnetcore/existing-db)對於如何使用現有的資料庫，其他參考 
