---
title: Entity Framework Core 工具參考 - EF Core
description: Entity Framework Core CLI 工具與 Visual Studio 套件管理員主控台的參考指南
author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 90e8d799de8fa9426de6ac3d286bcd5458a43f0d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061992"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="3763b-103">Entity Framework Core 工具參考</span><span class="sxs-lookup"><span data-stu-id="3763b-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="3763b-104">Entity Framework Core 工具有助於設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="3763b-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="3763b-105">它們主要用來管理「移轉」以及將資料庫結構描述反向工程以支援 `DbContext` 和實體類型。</span><span class="sxs-lookup"><span data-stu-id="3763b-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="3763b-106">[EF Core 套件管理員主控台工具](xref:core/miscellaneous/cli/powershell) 是在 Visual Studio 中的[套件管理器主控台](/nuget/tools/package-manager-console)執行。</span><span class="sxs-lookup"><span data-stu-id="3763b-106">The [EF Core Package Manager Console tools](xref:core/miscellaneous/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="3763b-107">[EF Core .NET 命令列介面 (CLI) 工具](xref:core/miscellaneous/cli/dotnet) 是跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的擴充功能。</span><span class="sxs-lookup"><span data-stu-id="3763b-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="3763b-108">這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。</span><span class="sxs-lookup"><span data-stu-id="3763b-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="3763b-109">這兩個工具都會公開相同的功能。</span><span class="sxs-lookup"><span data-stu-id="3763b-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="3763b-110">如果您要在 Visual Studio 中進行開發，則建議使用**套件管理員主控台**工具，因為它們提供更加整合的體驗。</span><span class="sxs-lookup"><span data-stu-id="3763b-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3763b-111">後續步驟</span><span class="sxs-lookup"><span data-stu-id="3763b-111">Next steps</span></span>

* [<span data-ttu-id="3763b-112">EF Core 套件管理員主控台工具參考</span><span class="sxs-lookup"><span data-stu-id="3763b-112">EF Core Package Manager Console tools reference</span></span>](xref:core/miscellaneous/cli/powershell)
* [<span data-ttu-id="3763b-113">EF Core .NET CLI 工具參考</span><span class="sxs-lookup"><span data-stu-id="3763b-113">EF Core .NET CLI tools reference</span></span>](xref:core/miscellaneous/cli/dotnet)
