---
title: 從 EF6 移植到 EF Core-移植以 EDMX 為基礎的模型-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: f0bb06dc687aaa774981d97daadc55f00fbd527e
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182069"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>移植到 EF Core EF6 EDMX 型模型

EF Core 不支援模型的 EDMX 檔案格式。 移植這些模型的最佳選項是，從資料庫為您的應用程式產生新的以程式碼為基礎的模型。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF Core NuGet 封裝

安裝 `Microsoft.EntityFrameworkCore.Tools` NuGet 套件。

## <a name="regenerate-the-model"></a>重新產生模型

您現在可以使用反向工程功能，根據現有的資料庫建立模型。

在 [套件管理員主控台] 中執行下列命令（[工具] – > NuGet 套件管理員-> 套件管理員主控台）。 如需 scaffold 資料表子集等的命令選項，請參閱[套件管理員主控台（Visual Studio）](../../core/miscellaneous/cli/powershell.md) 。

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

例如，下列命令會從 SQL Server LocalDB 實例上的 [日誌] 資料庫中 scaffold 模型。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>移除 EF6 模型

您現在會從應用程式中移除 EF6 模型。

它，就可以保留 EF6 NuGet 封裝 (EntityFramework) 安裝，因為 EF Core 和 EF6 可以使用-並存相同的應用程式中。 不過，如果您不打算在應用程式的任何區域中使用 EF6，則卸載封裝將有助於在需要注意的程式碼片段上提供編譯錯誤。

## <a name="update-your-code"></a>更新您的程式碼

此時，它是定址的編譯錯誤，並檢閱看 EF6 和 EF Core 之間的行為變更會影響您的程式碼。

## <a name="test-the-port"></a>測試埠

只在您的應用程式會編譯，因為並不表示它成功匯出至 EF Core。 您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。
