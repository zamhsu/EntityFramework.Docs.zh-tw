---
title: Entity Framework Core 工具參考 - EF Core
description: Entity Framework Core CLI 工具與 Visual Studio 套件管理員主控台的參考指南
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 9a6ae8f945e92453ddfaa089ae1d606d142f725a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431471"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core 工具參考

Entity Framework Core 工具有助於設計階段開發工作。 它們主要用來管理「移轉」以及將資料庫結構描述反向工程以支援 `DbContext` 和實體類型。

* [EF Core 套件管理員主控台工具](xref:core/cli/powershell) 是在 Visual Studio 中的[套件管理器主控台](/nuget/tools/package-manager-console)執行。

* [EF Core .NET 命令列介面 (CLI) 工具](xref:core/cli/dotnet) 是跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的擴充功能。 這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。

這兩個工具都會公開相同的功能。 如果您要在 Visual Studio 中進行開發，則建議使用 **套件管理員主控台** 工具，因為它們提供更加整合的體驗。

## <a name="next-steps"></a>後續步驟

* [EF Core 套件管理員主控台工具參考](xref:core/cli/powershell)
* [EF Core .NET CLI 工具參考](xref:core/cli/dotnet)
