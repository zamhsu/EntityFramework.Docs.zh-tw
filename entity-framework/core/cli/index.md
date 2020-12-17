---
title: Entity Framework Core 工具參考 - EF Core
description: Entity Framework Core CLI 工具與 Visual Studio 套件管理員主控台的參考指南
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 1ffc773cb8ed30516d682b90bbd9accef634ae6a
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635376"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core 工具參考

Entity Framework Core 工具有助於設計階段開發工作。 它們主要用來管理「移轉」以及將資料庫結構描述反向工程以支援 `DbContext` 和實體類型。

您可以安裝下列其中一項工具，因為這兩個工具都會公開相同的功能：

* [EF Core 套件管理員主控台工具](xref:core/cli/powershell) 是在 Visual Studio 中的[套件管理器主控台](/nuget/tools/package-manager-console)執行。 如果您要在 Visual Studio 中進行開發，我們建議使用這些工具，因為它們提供更整合的體驗。

* [EF Core .NET 命令列介面 (CLI) 工具](xref:core/cli/dotnet) 是跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的擴充功能。 這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。

## <a name="next-steps"></a>後續步驟

* [EF Core 套件管理員主控台工具參考](xref:core/cli/powershell)
* [EF Core .NET CLI 工具參考](xref:core/cli/dotnet)
