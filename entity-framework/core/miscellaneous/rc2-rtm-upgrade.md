---
title: 從 EF 升級核心 1.0 RTM-RC2 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
ms.technology: entity-framework-core
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 4bb4c5736708413f6581cad250b089b7bc22a559
ms.sourcegitcommit: 90139dbd6f485473afda0788a5a314c9aa601ea0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
ms.locfileid: "30151036"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="f5abd-102">從 EF Core 1.0 RC2 升級至 RTM</span><span class="sxs-lookup"><span data-stu-id="f5abd-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="f5abd-103">本文章提供移動 1.0.0 RC2 封裝建置的應用程式的指引 RTM。</span><span class="sxs-lookup"><span data-stu-id="f5abd-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="f5abd-104">封裝版本</span><span class="sxs-lookup"><span data-stu-id="f5abd-104">Package Versions</span></span>

<span data-ttu-id="f5abd-105">您通常會安裝到應用程式的最上層封裝名稱 RC2 及 RTM 之間並沒有變更。</span><span class="sxs-lookup"><span data-stu-id="f5abd-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="f5abd-106">**您需要在 RTM 版本升級已安裝的套件：**</span><span class="sxs-lookup"><span data-stu-id="f5abd-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="f5abd-107">執行階段封裝 (例如`Microsoft.EntityFrameworkCore.SqlServer`) 從變更`1.0.0-rc2-final`至`1.0.0`。</span><span class="sxs-lookup"><span data-stu-id="f5abd-107">Runtime packages (e.g. `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="f5abd-108">`Microsoft.EntityFrameworkCore.Tools`封裝變更從`1.0.0-preview1-final`至`1.0.0-preview2-final`。</span><span class="sxs-lookup"><span data-stu-id="f5abd-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="f5abd-109">請注意，工具仍然發行前版本。</span><span class="sxs-lookup"><span data-stu-id="f5abd-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="f5abd-110">現有的移轉可能需要加入 maxLength</span><span class="sxs-lookup"><span data-stu-id="f5abd-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="f5abd-111">Rc2，移轉的資料行定義看起來像`table.Column<string>(nullable: true)`，我們會儲存在移轉後的程式碼中的某些中繼資料中查閱資料行的長度。</span><span class="sxs-lookup"><span data-stu-id="f5abd-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="f5abd-112">在 RTM，長度現在會包含在程式碼 scaffold `table.Column<string>(maxLength: 450, nullable: true)`。</span><span class="sxs-lookup"><span data-stu-id="f5abd-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="f5abd-113">不會有任何現有的移轉作業的已使用 RTM 之前 scaffold`maxLength`指定引數。</span><span class="sxs-lookup"><span data-stu-id="f5abd-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="f5abd-114">這表示將使用之資料庫所支援的最大長度 (`nvarchar(max)` SQL Server 上)。</span><span class="sxs-lookup"><span data-stu-id="f5abd-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="f5abd-115">這可能是正常的某些資料行，但屬於索引鍵，外部索引鍵，資料行或索引需要更新成包含最大長度。</span><span class="sxs-lookup"><span data-stu-id="f5abd-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="f5abd-116">依照慣例，450 是最大長度用於索引鍵，外部索引鍵和索引資料行。</span><span class="sxs-lookup"><span data-stu-id="f5abd-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="f5abd-117">如果您在模型中明確設定的長度，然後您應該改用該長度。</span><span class="sxs-lookup"><span data-stu-id="f5abd-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

<span data-ttu-id="f5abd-118">**ASP.NET Identity**</span><span class="sxs-lookup"><span data-stu-id="f5abd-118">**ASP.NET Identity**</span></span>

<span data-ttu-id="f5abd-119">這項變更會影響使用 ASP.NET Identity，並且從預先建立的專案-RTM 專案範本。</span><span class="sxs-lookup"><span data-stu-id="f5abd-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="f5abd-120">專案範本包含用來建立資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="f5abd-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="f5abd-121">此移轉必須編輯成指定的最大長度`256`下列資料行。</span><span class="sxs-lookup"><span data-stu-id="f5abd-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

*  <span data-ttu-id="f5abd-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="f5abd-122">**AspNetRoles**</span></span>

    * <span data-ttu-id="f5abd-123">名稱</span><span class="sxs-lookup"><span data-stu-id="f5abd-123">Name</span></span>

    * <span data-ttu-id="f5abd-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="f5abd-124">NormalizedName</span></span>

*  <span data-ttu-id="f5abd-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="f5abd-125">**AspNetUsers**</span></span>

   * <span data-ttu-id="f5abd-126">Email</span><span class="sxs-lookup"><span data-stu-id="f5abd-126">Email</span></span>

   * <span data-ttu-id="f5abd-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="f5abd-127">NormalizedEmail</span></span>

   * <span data-ttu-id="f5abd-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="f5abd-128">NormalizedUserName</span></span>

   * <span data-ttu-id="f5abd-129">使用者名稱</span><span class="sxs-lookup"><span data-stu-id="f5abd-129">UserName</span></span>

<span data-ttu-id="f5abd-130">初始移轉套用至資料庫時，無法進行這項變更會導致下列的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="f5abd-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

    System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="f5abd-131">.NET core： 在 project.json 中移除 「 匯入 」</span><span class="sxs-lookup"><span data-stu-id="f5abd-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="f5abd-132">如果您已針對.NET Core rc2，您需要新增`imports`加入 project.json 暫時的解決方法的其中一些不支援.NET 標準的 EF Core相依性。</span><span class="sxs-lookup"><span data-stu-id="f5abd-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="f5abd-133">這些是可以立即移除。</span><span class="sxs-lookup"><span data-stu-id="f5abd-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> <span data-ttu-id="f5abd-134">自 1.0 版 RTM， [.NET Core SDK](https://www.microsoft.com/net/download/core)已不再支援`project.json`或開發使用 Visual Studio 2015 的.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="f5abd-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="f5abd-135">建議您[從 project.json 移轉至 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。</span><span class="sxs-lookup"><span data-stu-id="f5abd-135">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="f5abd-136">如果您使用 Visual Studio，我們建議您升級到[Visual Studio 2017](https://www.visualstudio.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="f5abd-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="f5abd-137">UWP： 新增繫結重新導向</span><span class="sxs-lookup"><span data-stu-id="f5abd-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="f5abd-138">嘗試執行 EF 命令在通用 Windows 平台 (UWP) 專案會導致下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="f5abd-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

    System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.

<span data-ttu-id="f5abd-139">您需要手動新增繫結重新導向至 UWP 專案。</span><span class="sxs-lookup"><span data-stu-id="f5abd-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="f5abd-140">建立名為`App.config`專案中的根資料夾和重新導向加入至正確的組件版本。</span><span class="sxs-lookup"><span data-stu-id="f5abd-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

``` xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
