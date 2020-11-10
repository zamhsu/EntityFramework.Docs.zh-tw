---
title: 從 EF6 移植至 EF Core-移植 EDMX-Based 模型-EF
description: 將 Entity Framework 6 以 EDMX 為基礎的模型應用程式移植到 Entity Framework Core 的特定資訊
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 9c1b308318c9bf0325ece0b60ace646becc8de39
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429217"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>將 EF6 EDMX-Based 模型移植到 EF Core

EF Core 不支援模型的 EDMX 檔案格式。 移植這些模型的最佳選項，是為您的應用程式從資料庫產生新的以程式碼為基礎的模型。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF Core NuGet 套件

安裝 `Microsoft.EntityFrameworkCore.Tools` NuGet 套件。

## <a name="regenerate-the-model"></a>重新產生模型

您現在可以使用反向工程功能，根據您現有的資料庫建立模型。

在封裝管理員主控台中執行下列命令 (工具-> NuGet 封裝管理員– > 封裝管理員主控台) 。 請參閱 [封裝管理員主控台 (Visual Studio) ](xref:core/cli/powershell) 的命令選項，以 scaffold 資料表的子集等等。

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

例如，以下命令可讓您從 SQL Server LocalDB 實例上的 [日誌] 資料庫 scaffold 模型。

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>移除 EF6 模型

您現在可以從應用程式中移除 EF6 模型。

您可以將 EF6 NuGet 套件安裝 (EntityFramework) ，因為 EF Core 和 EF6 可在相同的應用程式中並存使用。 但是，如果您不打算在應用程式的任何區域中使用 EF6，則卸載套件將有助於在需要注意的程式碼片段上提供編譯錯誤。

## <a name="update-your-code"></a>更新您的程式碼

此時，請務必解決編譯錯誤並檢查程式碼，以查看 EF6 與 EF Core 之間的行為變更是否會影響您。

## <a name="test-the-port"></a>測試埠

這是因為您的應用程式會進行編譯，並不表示它已成功移植到 EF Core。 您必須測試應用程式的所有區域，以確保任何行為變更都不會對您的應用程式造成負面影響。
