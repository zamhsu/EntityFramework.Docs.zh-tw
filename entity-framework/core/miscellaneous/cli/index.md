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
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="7b6cb-102">Entity Framework Core 工具參考</span><span class="sxs-lookup"><span data-stu-id="7b6cb-102">Entity Framework Core tools reference</span></span>

<span data-ttu-id="7b6cb-103">Entity Framework Core 工具有助於設計階段開發工作。</span><span class="sxs-lookup"><span data-stu-id="7b6cb-103">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="7b6cb-104">它們主要用來管理「移轉」以及將資料庫結構描述反向工程以支援 `DbContext` 和實體類型。</span><span class="sxs-lookup"><span data-stu-id="7b6cb-104">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="7b6cb-105">[EF Core 套件管理員主控台工具](powershell.md) 是在 Visual Studio 中的[套件管理器主控台](https://docs.microsoft.com/nuget/tools/package-manager-console)執行。</span><span class="sxs-lookup"><span data-stu-id="7b6cb-105">The [EF Core Package Manager Console tools](powershell.md) run in the [Package Manager Console](https://docs.microsoft.com/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="7b6cb-106">[EF Core .NET 命令列介面 (CLI) 工具](dotnet.md) 是跨平台 [.NET Core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/)的擴充功能。</span><span class="sxs-lookup"><span data-stu-id="7b6cb-106">The [EF Core .NET command-line interface (CLI) tools](dotnet.md) are an extension to the cross-platform [.NET Core CLI tools](https://docs.microsoft.com/dotnet/core/tools/).</span></span> <span data-ttu-id="7b6cb-107">這些工具需要 .NET Core SDK 專案 (專案檔中含 `Sdk="Microsoft.NET.Sdk"` 或類似項目的專案)。</span><span class="sxs-lookup"><span data-stu-id="7b6cb-107">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="7b6cb-108">這兩個工具都會公開相同的功能。</span><span class="sxs-lookup"><span data-stu-id="7b6cb-108">Both tools expose the same functionality.</span></span> <span data-ttu-id="7b6cb-109">如果您要在 Visual Studio 中進行開發，則建議使用**套件管理員主控台**工具，因為它們提供更加整合的體驗。</span><span class="sxs-lookup"><span data-stu-id="7b6cb-109">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7b6cb-110">後續步驟</span><span class="sxs-lookup"><span data-stu-id="7b6cb-110">Next steps</span></span>

* [<span data-ttu-id="7b6cb-111">EF Core 套件管理員主控台工具參考</span><span class="sxs-lookup"><span data-stu-id="7b6cb-111">EF Core Package Manager Console tools reference</span></span>](powershell.md)
* [<span data-ttu-id="7b6cb-112">EF Core .NET CLI 工具參考</span><span class="sxs-lookup"><span data-stu-id="7b6cb-112">EF Core .NET CLI tools reference</span></span>](dotnet.md)
