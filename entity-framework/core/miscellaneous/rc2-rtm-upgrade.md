---
title: 從 EF 升級核心 1.0 RTM-RC2 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: e7f121d18931e26e7b5d11842da6da4a9b789efe
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181355"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>從 EF Core 1.0 RC2 升級至 RTM

本文提供將以 RC2 套件建立的應用程式移至 1.0.0 RTM 的指引。

## <a name="package-versions"></a>套件版本

您通常會安裝在應用程式中的最上層套件名稱，在 RC2 和 RTM 之間並沒有變更。

**您需要將已安裝的套件升級為 RTM 版本：**

* 執行時間封裝（例如 `Microsoft.EntityFrameworkCore.SqlServer`）已從 `1.0.0-rc2-final` 變更為 `1.0.0`。

* @No__t-0 套件從 `1.0.0-preview1-final` 變更為 `1.0.0-preview2-final`。 請注意，工具仍然是發行前版本。

## <a name="existing-migrations-may-need-maxlength-added"></a>現有的遷移可能需要加入 maxLength

在 RC2 中，遷移中的資料行定義看起來像 `table.Column<string>(nullable: true)`，而資料行的長度是在我們儲存于遷移後程式碼中的某些中繼資料中查閱。 在 RTM 中，長度現在已包含在 scaffold 程式碼中 `table.Column<string>(maxLength: 450, nullable: true)`。

在使用 RTM 之前 scaffold 的任何現有遷移都不會指定 `maxLength` 引數。 這表示將會使用資料庫支援的最大長度（SQL Server 上 `nvarchar(max)`）。 這可能適用于某些資料行，但屬於索引鍵、外鍵或索引之一部分的資料行則需要更新為包含最大長度。 依照慣例，450是用於金鑰、外鍵和索引資料行的最大長度。 如果您已在模型中明確設定長度，則應改用該長度。

**ASP.NET Identity**

這種變更會影響使用 ASP.NET Identity 的專案，而且是從 RTM 之前的專案範本建立的。 專案範本包含用來建立資料庫的遷移。 您必須編輯此遷移，才能為下列資料行指定 `256` 的最大長度。

*  **AspNetRoles**

    * Name

    * NormalizedName

*  **AspNetUsers**

   * Email

   * NormalizedEmail

   * NormalizedUserName

   * 使用者名稱

若無法進行這項變更，將會在初始遷移套用至資料庫時產生下列例外狀況。

```console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core：在專案 json 中移除 "imports"

如果您已針對.NET Core rc2，您需要新增`imports`加入 project.json 暫時的解決方法的其中一些不支援.NET 標準的 EF Core 相依性。 現在可以移除這些。

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
> 自 1.0 版 RTM， [.NET Core SDK](https://www.microsoft.com/net/download/core)已不再支援`project.json`或開發使用 Visual Studio 2015 的.NET Core 應用程式。 建議您[從 project.json 移轉至 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。 如果您使用 Visual Studio，建議您升級至[Visual Studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="uwp-add-binding-redirects"></a>UWP新增系結重新導向

嘗試在通用 Windows 平臺（UWP）專案上執行 EF 命令會產生下列錯誤：

```console
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

您必須手動將系結重新導向新增至 UWP 專案。 在專案根資料夾中建立名為 `App.config` 的檔案，並將重新導向新增至正確的元件版本。

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
