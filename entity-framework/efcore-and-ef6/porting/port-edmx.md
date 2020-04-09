---
title: 從 EF6 移植到 EF 核心 - 移植基於 EDMX 的型號 - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: f0bb06dc687aaa774981d97daadc55f00fbd527e
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "78416920"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>將基於 EF6 EDMX 的型號移植到 EF 核心

EF Core 不支援模型的 EDMX 檔案格式。 移植這些模型的最佳選擇是從應用程式的資料庫中生成一個新的基於代碼的模型。

## <a name="install-ef-core-nuget-packages"></a>安裝 EF 核心 NuGet 套件

安裝 `Microsoft.EntityFrameworkCore.Tools` NuGet 套件。

## <a name="regenerate-the-model"></a>重新產生模型

現在,您可以使用反向工程功能基於現有資料庫創建模型。

在包管理器控制台中運行以下命令(工具 +> NuGet 包管理員 [>包管理器控制台)。 有關設置表子集的命令選項等,請參閱[包管理器主控台(可視化工作室)。](../../core/miscellaneous/cli/powershell.md)

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

例如,下面是在 SQL Server LocalDB 實例上從部落格資料庫設置模型的命令。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>移除 EF6 型號

現在,您將從應用程式中刪除 EF6 模型。

保留 EF6 NuGet 套件 (EntityFramework) 安裝是可以的,因為 EF Core 和 EF6 可以並行用於同一應用程式。 但是,如果您不打算在應用程式的任何區域使用 EF6,則卸載包將有助於在需要注意的代碼片段上出現編譯錯誤。

## <a name="update-your-code"></a>更新代碼

此時,需要解決編譯錯誤並查看代碼,以查看 EF6 和 EF Core 之間的行為更改是否會影響您。

## <a name="test-the-port"></a>測試連接埠

僅僅因為您的應用程式編譯,並不意味著它已成功移植到 EF Core。 您需要測試應用程式的所有區域,以確保沒有任何行為更改對應用程式造成負面影響。
