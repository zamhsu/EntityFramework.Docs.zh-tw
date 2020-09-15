---
title: Entity Framework Core 工具參考 - EF Core
description: Entity Framework Core CLI 工具與 Visual Studio 套件管理員主控台的參考指南
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 7d5fb984aafed2cf45efa9e5b83bf4bc6c18a44a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619426"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core 工具參考

Entity Framework Core 工具有助於設計階段開發工作。 它們主要用來管理「移轉」以及將資料庫結構描述反向工程以支援 `DbContext` 和實體類型。

* [EF Core 套件管理員主控台工具](xref:core/miscellaneous/cli/powershell) 是在 Visual Studio 中的[套件管理器主控台](/nuget/tools/package-manager-console)執行。

* [EF Core .NET 命令列介面 (CLI) 工具](xref:core/miscellaneous/cli/dotnet) 是跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的擴充功能。 這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。

這兩個工具都會公開相同的功能。 如果您要在 Visual Studio 中進行開發，則建議使用**套件管理員主控台**工具，因為它們提供更加整合的體驗。

## <a name="next-steps"></a>後續步驟

* [EF Core 套件管理員主控台工具參考](xref:core/miscellaneous/cli/powershell)
* [EF Core .NET CLI 工具參考](xref:core/miscellaneous/cli/dotnet)
