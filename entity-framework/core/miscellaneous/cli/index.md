---
title: Entity Framework Core 工具參考 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 237192c55ea3542521a7a292ac8550d72e4ef82c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412813"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core 工具參考

Entity Framework Core 工具有助於設計階段開發工作。 它們主要用來管理「移轉」以及將資料庫結構描述反向工程以支援 `DbContext` 和實體類型。

* [EF Core 套件管理員主控台工具](powershell.md) 是在 Visual Studio 中的[套件管理器主控台](https://docs.microsoft.com/nuget/tools/package-manager-console)執行。

* [EF Core .NET 命令列介面 (CLI) 工具](dotnet.md) 是跨平台 [.NET Core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/)的擴充功能。 這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。

這兩個工具都會公開相同的功能。 如果您要在 Visual Studio 中進行開發，則建議使用**套件管理員主控台**工具，因為它們提供更加整合的體驗。

## <a name="next-steps"></a>後續步驟

* [EF Core 套件管理員主控台工具參考](powershell.md)
* [EF Core .NET CLI 工具參考](dotnet.md)
