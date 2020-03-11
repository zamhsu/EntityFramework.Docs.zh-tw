---
title: 使用 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 989ea862-e936-4c85-926a-8cfbef5df5b8
ms.openlocfilehash: 34866ddbbf81f090a064af148a612dd354ae9401
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418956"
---
# <a name="using-migrateexe"></a>使用 debug.exe
Code First 移轉可用來從 visual studio 內部更新資料庫，但也可以透過命令列工具 [遷移] 來執行。 這個頁面會快速介紹如何使用 node.js 來執行資料庫的遷移。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，則必須先閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)，才能繼續進行。

## <a name="copy-migrateexe"></a>複製遷移 .exe

當您使用 NuGet [遷移] 安裝 Entity Framework 時，將會在下載之套件的 [tools] 資料夾內。 在 &lt;專案資料夾中&gt;\\套件\\EntityFramework。&lt;版本&gt;\\工具

遷移 .exe 之後，您必須將它複製到包含您的遷移之元件的位置。

如果您的應用程式是以 .NET 4 為目標，而不是4.5，則您也需要將重新**導向 .config**複製到該位置，並將它重新命名為 [**遷移 .exe**]。如此一來，遷移 .exe 就能取得正確的系結重新導向，以便找出 Entity Framework 元件。

| .NET 4.5                                      | .NET 4。0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![.NET 4.5 檔案](~/ef6/media/net45files.png) | ![.NET 4.0 檔案](~/ef6/media/net40files.png) |

> [!NOTE]
> 遷移 .exe 不支援 x64 元件。

將 node.js 移到正確的資料夾之後，您應該就可以使用它來對資料庫執行遷移。 所有公用程式的設計都是執行遷移。 它無法產生遷移或建立 SQL 腳本。

## <a name="see-options"></a>請參閱選項

``` console
Migrate.exe /?
```

上述會顯示與此公用程式相關聯的說明頁面，請注意，您必須在執行 node.js 的相同位置中有 EntityFramework，才能讓此作業正常運作。

## <a name="migrate-to-the-latest-migration"></a>遷移至最新的遷移

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

執行 msbuild.exe 時，唯一的必要參數是元件，這是包含您嘗試執行之遷移的元件，但如果未指定設定檔，則會使用所有慣例型設定。

## <a name="migrate-to-a-specific-migration"></a>遷移至特定的遷移

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

如果您想要執行至特定遷移的遷移，則可以指定遷移的名稱。 這會視需要執行所有先前的遷移，直到到達指定的遷移為止。

## <a name="specify-working-directory"></a>指定工作目錄

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

如果您的元件具有相依性，或讀取相對於工作目錄的檔案，則您將需要設定 startupDirectory。

## <a name="specify-migration-configuration-to-use"></a>指定要使用的遷移設定

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

如果您有多個遷移設定類別，類別繼承自 DbMigrationConfiguration，則您需要指定要用於此執行的。 這是藉由提供選用的第二個參數，而不需要像上述的交換器來指定。

## <a name="provide-connection-string"></a>提供連接字串

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

如果您想要在命令列指定連接字串，則您也必須提供提供者名稱。 未指定提供者名稱會造成例外狀況。

## <a name="common-problems"></a>常見問題

| 錯誤訊息                                                                                                                                                                                                                                                                                                                      | 解決方法                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 未處理的例外狀況： FileLoadException：無法載入檔案或元件 ' EntityFramework，Version = 5.0.0.0，Culture = 中性，PublicKeyToken = b77a5c561934e089 ' 或其相依性的其中之一。 找不到元件的資訊清單定義與元件參考不符。 （來自 HRESULT 的例外狀況：0x80131040）         | 這通常表示您執行的不是重新導向 .config 檔案的 .NET 4 應用程式。 您必須將重新導向複製到與 node.js 相同的位置，並將它重新命名為遷移 .exe。                                                                                       |
| 未處理的例外狀況： FileLoadException：無法載入檔案或元件 ' EntityFramework，Version = v4.4.0.0，Culture = 中性，PublicKeyToken = b77a5c561934e089 ' 或其相依性的其中之一。 找不到元件的資訊清單定義與元件參考不符。 （來自 HRESULT 的例外狀況：0x80131040）          | 這個例外狀況表示您正在執行 .NET 4.5 應用程式，並將重新導向複製到遷移 .exe 位置。 如果您的應用程式是 .NET 4.5，則您不需要具有內部重新導向的設定檔。 刪除遷移 .exe 檔案。                                    |
| 錯誤：無法更新資料庫以符合目前的模型，因為有暫止的變更，且已停用自動遷移。 將暫止的模型變更寫入以程式碼為基礎的遷移，或啟用自動遷移。 將 DbMigrationsConfiguration AutomaticMigrationsEnabled 設定為 true 以啟用自動遷移。 | 如果您未建立遷移來處理對模型所做的變更，且資料庫與模型不相符，就會發生這個錯誤。 將屬性加入至模型類別，然後在不建立遷移來升級資料庫的情況下執行 msbuild.exe，就是這種情況的範例。 |
| 錯誤：無法解析成員 ' ToolingFacade + UpdateRunner，EntityFramework，Version = 5.0.0.0，Culture = 中性，PublicKeyToken = b77a5c561934e089 ' 的類型。                                                                                                                                       | 這個錯誤可能是因為指定了不正確的啟動目錄所造成。 這必須是遷移 .exe 的位置                                                                                                                                                                                      |
| 未處理的例外狀況： NullReferenceException：物件參考未設定為物件的實例。 <br/>   在 System.web. node.js （String [] args）上。                                                                                                                                             | 這可能是因為未針對您所使用的案例指定必要的參數所造成。 例如，指定不指定提供者名稱的連接字串。                                                                                                                        |
| 錯誤：在元件 ' ClassLibrary1 ' 中找到一個以上的遷移設定類型。 指定要使用的名稱。                                                                                                                                                                                                  | 如錯誤狀態，指定元件中有一個以上的設定類別。 您必須使用/configurationType 參數來指定要使用哪一個。                                                                                                                                           |
| 錯誤：無法載入檔案或元件 '&lt;assemblyName&gt;' 或其相依性的其中之一。 指定的元件名稱或程式碼基底無效。 （來自 HRESULT 的例外狀況：0x80131047）                                                                                                                                                    | 這可能是因為指定元件名稱不正確或不具有                                                                                                                                                                                                                          |
| 錯誤：無法載入檔案或元件 '&lt;assemblyName&gt;' 或其相依性的其中之一。 嘗試載入了格式不正確的程式。                                                                                                                                                                          | 如果您嘗試對 x64 應用程式執行 node.js，就會發生這種情況。 EF 5.0 和以下僅適用于 x86。                                                                                                                                                                                |
