---
title: 從 EF Core 1.0 RC2 升級至 RTM EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526767"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="54e70-102">從 EF Core 1.0 RC2 升級至 RTM</span><span class="sxs-lookup"><span data-stu-id="54e70-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="54e70-103">本文提供將以 RC2 套件建立的應用程式移至 1.0.0 RTM 的指引。</span><span class="sxs-lookup"><span data-stu-id="54e70-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="54e70-104">套件版本</span><span class="sxs-lookup"><span data-stu-id="54e70-104">Package Versions</span></span>

<span data-ttu-id="54e70-105">您通常會安裝在應用程式中的最上層套件名稱，在 RC2 和 RTM 之間並沒有變更。</span><span class="sxs-lookup"><span data-stu-id="54e70-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="54e70-106">**您需要將已安裝的套件升級為 RTM 版本：**</span><span class="sxs-lookup"><span data-stu-id="54e70-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="54e70-107">執行時間套件（例如 `Microsoft.EntityFrameworkCore.SqlServer` ）已從變更 `1.0.0-rc2-final` 為 `1.0.0` 。</span><span class="sxs-lookup"><span data-stu-id="54e70-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="54e70-108">`Microsoft.EntityFrameworkCore.Tools`封裝已從變更 `1.0.0-preview1-final` 為 `1.0.0-preview2-final` 。</span><span class="sxs-lookup"><span data-stu-id="54e70-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="54e70-109">請注意，工具仍然是發行前版本。</span><span class="sxs-lookup"><span data-stu-id="54e70-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="54e70-110">現有的遷移可能需要加入 maxLength</span><span class="sxs-lookup"><span data-stu-id="54e70-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="54e70-111">在 RC2 中，遷移中的資料行定義看起來像 `table.Column<string>(nullable: true)` ，而資料行的長度是在我們儲存于遷移後程式碼中的某些中繼資料中查閱。</span><span class="sxs-lookup"><span data-stu-id="54e70-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="54e70-112">在 RTM 中，長度現在包含在 scaffold 程式碼中 `table.Column<string>(maxLength: 450, nullable: true)` 。</span><span class="sxs-lookup"><span data-stu-id="54e70-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="54e70-113">在使用 RTM 之前 scaffold 的任何現有遷移都不會 `maxLength` 指定引數。</span><span class="sxs-lookup"><span data-stu-id="54e70-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="54e70-114">這表示將會使用資料庫支援的最大長度（ `nvarchar(max)` SQL Server）。</span><span class="sxs-lookup"><span data-stu-id="54e70-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="54e70-115">這可能適用于某些資料行，但屬於索引鍵、外鍵或索引之一部分的資料行則需要更新為包含最大長度。</span><span class="sxs-lookup"><span data-stu-id="54e70-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="54e70-116">依照慣例，450是用於金鑰、外鍵和索引資料行的最大長度。</span><span class="sxs-lookup"><span data-stu-id="54e70-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="54e70-117">如果您已在模型中明確設定長度，則應改用該長度。</span><span class="sxs-lookup"><span data-stu-id="54e70-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="54e70-118">ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="54e70-118">ASP.NET Identity</span></span>

<span data-ttu-id="54e70-119">這種變更會影響使用 ASP.NET Identity 的專案，而且是從 RTM 之前的專案範本建立的。</span><span class="sxs-lookup"><span data-stu-id="54e70-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="54e70-120">專案範本包含用來建立資料庫的遷移。</span><span class="sxs-lookup"><span data-stu-id="54e70-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="54e70-121">您必須編輯此遷移，才能針對下列資料行指定的最大長度 `256` 。</span><span class="sxs-lookup"><span data-stu-id="54e70-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="54e70-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="54e70-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="54e70-123">Name</span><span class="sxs-lookup"><span data-stu-id="54e70-123">Name</span></span>
  * <span data-ttu-id="54e70-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="54e70-124">NormalizedName</span></span>
* <span data-ttu-id="54e70-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="54e70-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="54e70-126">電子郵件</span><span class="sxs-lookup"><span data-stu-id="54e70-126">Email</span></span>
  * <span data-ttu-id="54e70-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="54e70-127">NormalizedEmail</span></span>
  * <span data-ttu-id="54e70-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="54e70-128">NormalizedUserName</span></span>
  * <span data-ttu-id="54e70-129">UserName</span><span class="sxs-lookup"><span data-stu-id="54e70-129">UserName</span></span>

<span data-ttu-id="54e70-130">若無法進行這項變更，將會在初始遷移套用至資料庫時產生下列例外狀況。</span><span class="sxs-lookup"><span data-stu-id="54e70-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="54e70-131">.NET Core：在 project.js中移除 [匯入]</span><span class="sxs-lookup"><span data-stu-id="54e70-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="54e70-132">如果您的目標是使用 RC2 的 .NET Core，您需要將新增 `imports` 至 project.js，做為部分 EF Core 不支援 .NET Standard 之相依性的暫時因應措施。</span><span class="sxs-lookup"><span data-stu-id="54e70-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="54e70-133">現在可以移除這些。</span><span class="sxs-lookup"><span data-stu-id="54e70-133">These can now be removed.</span></span>

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
> <span data-ttu-id="54e70-134">從版本 1.0 RTM， [.NET Core SDK](https://www.microsoft.com/net/download/core)不再支援 `project.json` 或使用 Visual Studio 2015 開發 .net Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="54e70-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="54e70-135">建議您[從 project.json 移轉至 csproj](/dotnet/articles/core/migration/)。</span><span class="sxs-lookup"><span data-stu-id="54e70-135">We recommend you [migrate from project.json to csproj](/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="54e70-136">如果您使用 Visual Studio，建議您升級至[Visual Studio 2017](https://www.visualstudio.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="54e70-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="54e70-137">UWP：新增系結重新導向</span><span class="sxs-lookup"><span data-stu-id="54e70-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="54e70-138">嘗試在通用 Windows 平臺（UWP）專案上執行 EF 命令會產生下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="54e70-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="54e70-139">您必須手動將系結重新導向新增至 UWP 專案。</span><span class="sxs-lookup"><span data-stu-id="54e70-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="54e70-140">`App.config`在專案根資料夾中建立名為的檔案，並將重新導向新增至正確的元件版本。</span><span class="sxs-lookup"><span data-stu-id="54e70-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

```xml
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
