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
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="8ac63-103">Entity Framework Core 工具參考</span><span class="sxs-lookup"><span data-stu-id="8ac63-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="8ac63-104">Entity Framework Core 工具有助於設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="8ac63-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="8ac63-105">它們主要用來管理「移轉」以及將資料庫結構描述反向工程以支援 `DbContext` 和實體類型。</span><span class="sxs-lookup"><span data-stu-id="8ac63-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

<span data-ttu-id="8ac63-106">您可以安裝下列其中一項工具，因為這兩個工具都會公開相同的功能：</span><span class="sxs-lookup"><span data-stu-id="8ac63-106">Either of the following tools can be installed, as both tools expose the same functionality:</span></span>

* <span data-ttu-id="8ac63-107">[EF Core 套件管理員主控台工具](xref:core/cli/powershell) 是在 Visual Studio 中的[套件管理器主控台](/nuget/tools/package-manager-console)執行。</span><span class="sxs-lookup"><span data-stu-id="8ac63-107">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span> <span data-ttu-id="8ac63-108">如果您要在 Visual Studio 中進行開發，我們建議使用這些工具，因為它們提供更整合的體驗。</span><span class="sxs-lookup"><span data-stu-id="8ac63-108">We recommend using these tools if you are developing in Visual Studio as they provide a more integrated experience.</span></span>

* <span data-ttu-id="8ac63-109">[EF Core .NET 命令列介面 (CLI) 工具](xref:core/cli/dotnet) 是跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的擴充功能。</span><span class="sxs-lookup"><span data-stu-id="8ac63-109">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="8ac63-110">這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。</span><span class="sxs-lookup"><span data-stu-id="8ac63-110">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ac63-111">後續步驟</span><span class="sxs-lookup"><span data-stu-id="8ac63-111">Next steps</span></span>

* [<span data-ttu-id="8ac63-112">EF Core 套件管理員主控台工具參考</span><span class="sxs-lookup"><span data-stu-id="8ac63-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="8ac63-113">EF Core .NET CLI 工具參考</span><span class="sxs-lookup"><span data-stu-id="8ac63-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
