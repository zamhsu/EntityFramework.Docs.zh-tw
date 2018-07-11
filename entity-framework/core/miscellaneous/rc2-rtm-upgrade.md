---
title: 從 EF 升級核心 1.0 RTM-RC2 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
ms.technology: entity-framework-core
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 9561eac253517188251fece9a03f434482246051
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949053"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>從 EF Core 1.0 RC2 升級至 RTM

這篇文章提供移動至 1.0.0 RC2 封裝建置的應用程式的指引 RTM。

## <a name="package-versions"></a>套件版本

RC2 和 RTM 之間未變更的最上層封裝，您通常會安裝到應用程式名稱。

**您必須將已安裝的套件升級為 RTM 版本：**

* 執行階段套件 (例如`Microsoft.EntityFrameworkCore.SqlServer`) 從變更`1.0.0-rc2-final`至`1.0.0`。

* `Microsoft.EntityFrameworkCore.Tools`從變更封裝`1.0.0-preview1-final`至`1.0.0-preview2-final`。 請注意，工具仍然發行前版本。

## <a name="existing-migrations-may-need-maxlength-added"></a>現有的移轉可能需要新增的 maxLength

Rc2，在移轉的資料行定義看起來像`table.Column<string>(nullable: true)`和資料行的長度一些我們在移轉後的程式碼中儲存的中繼資料中查閱。 在 rtm 版，則長度現在已包含 scaffold 的程式碼在`table.Column<string>(maxLength: 450, nullable: true)`。

將不會有任何現有的移轉，才能使用 RTM 已包含 scaffold`maxLength`指定引數。 這表示將使用資料庫所支援的最大長度 (`nvarchar(max)` SQL Server 上)。 這可能是適合用於某些資料行，但屬於索引鍵，外部索引鍵資料行或索引需要更新，以包含最大長度。 依照慣例，450 是最大長度用於索引鍵，外部索引鍵和索引資料行。 如果您在模型中明確設定的長度，然後您應該改用該長度。

**ASP.NET 身分識別**

這項變更會影響使用 ASP.NET 身分識別，並從預先建立的專案-RTM 專案範本。 專案範本會包含用來建立資料庫的移轉。 此移轉必須編輯指定的最大長度`256`，下列資料行。

*  **AspNetRoles**

    * 名稱

    * NormalizedName

*  **AspNetUsers**

   * Email

   * NormalizedEmail

   * NormalizedUserName

   * 使用者名稱

初始移轉套用至資料庫時，無法進行這項變更會導致下列例外狀況。

    System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.

## <a name="net-core-remove-imports-in-projectjson"></a>.NET core： 在 project.json 中移除 「 匯入 」

如果您已針對.NET Core rc2，您需要新增`imports`加入 project.json 暫時的解決方法的其中一些不支援.NET 標準的 EF Core 相依性。 這些是可以立即移除。

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
> 自 1.0 版 RTM， [.NET Core SDK](https://www.microsoft.com/net/download/core)已不再支援`project.json`或開發使用 Visual Studio 2015 的.NET Core 應用程式。 建議您[從 project.json 移轉至 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。 如果您使用 Visual Studio，我們建議您升級到[Visual Studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="uwp-add-binding-redirects"></a>UWP： 新增繫結重新導向

嘗試執行 EF 命令在通用 Windows 平台 (UWP) 專案會導致下列錯誤：

    System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.

您需要以手動方式在 UWP 專案中加入繫結重新導向。 建立名為`App.config`在專案根資料夾，並將重新導向加入至正確的組件版本。

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
