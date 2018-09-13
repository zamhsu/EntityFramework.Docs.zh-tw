---
title: 使用 migrate.exe-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 989ea862-e936-4c85-926a-8cfbef5df5b8
ms.openlocfilehash: 6e9880523bbcf2fe55390a447241e59723a0967f
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490210"
---
# <a name="using-migrateexe"></a>使用 migrate.exe
Code First 移轉可以用來更新資料庫，以從在 visual studio，但也可以透過命令列工具 migrate.exe 執行。 此頁面會提供如何使用 migrate.exe 執行對資料庫的移轉的快速概觀。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果不這麼做，則您將需要閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)再繼續進行。

## <a name="copy-migrateexe"></a>複製 migrate.exe

當您安裝 Entity Framework 使用 NuGet migrate.exe 會下載的封裝的 [工具] 資料夾內。 在 &lt;專案資料夾&gt;\\封裝\\EntityFramework。&lt;版本&gt;\\工具

一旦您擁有 migrate.exe 您需要將它複製到包含移轉之組件的位置。

如果您的應用程式的目標.NET 4 中，而且不 4.5，則您必須複製**Redirect.config**做為位置以及與將它重新命名**migrate.exe.config**。這是使 migrate.exe 取得正確的繫結重新導向，若要能夠找出 Entity Framework 組件。

| .NET 4.5                                   | .NET 4.0                                   |
|:-------------------------------------------|:-------------------------------------------|
| ![.NET 4.5 檔案](~/ef6/media/net45files.png)  | ![.NET 4.0 檔案](~/ef6/media/net40files.png)  |

> [!NOTE]
> migrate.exe 不支援 x64 的組件。

一旦您已經移動 migrate.exe 到正確的資料夾，則您應該能夠使用它來執行對資料庫的移轉。 此公用程式設計來執行的只是執行移轉。 它無法產生遷移，或建立的 SQL 指令碼。

## <a name="see-options"></a>請參閱選項

``` console
Migrate.exe /?
```

上述會顯示與此公用程式，請注意，您必須在相同的位置，您在此工作順序執行 migrate.exe EntityFramework.dll 相關聯的 [說明] 頁面。

## <a name="migrate-to-the-latest-migration"></a>移轉至最新的移轉

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile=”..\\web.config”
```

當執行 migrate.exe 只有必要的參數組件，也就是包含您嘗試執行的移轉作業的組件，但它會使用所有的慣例基礎設定值，如果您未指定組態檔。

## <a name="migrate-to-a-specific-migration"></a>移轉至特定的移轉

``` console
Migrate.exe MyApp.exe /startupConfigurationFile=”MyApp.exe.config” /targetMigration=”AddTitle”
```

如果您想要執行至特定移轉的移轉，您可以指定移轉的名稱。 這會視需要執行所有先前移轉作業之前取得與指定的移轉。

## <a name="specify-working-directory"></a>指定工作目錄

``` console
Migrate.exe MyApp.exe /startupConfigurationFile=”MyApp.exe.config” /startupDirectory=”c:\\MyApp”
```

如果您的組件有相依性，或讀取之工作目錄相對的檔案，則您必須設定 startupDirectory。

## <a name="specify-migration-configuration-to-use"></a>指定要使用的移轉設定

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile=”..\\web.config”
```

如果您有多個移轉組態類別，類別繼承自 DbMigrationConfiguration，然後您要指定這被要用於此執行。 這是藉由提供選擇性的第二個參數，不含上述做為參數指定。

## <a name="provide-connection-string"></a>提供連接字串

``` console
Migrate.exe BlogDemo.dll /connectionString=”Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI” /connectionProviderName=”System.Data.SqlClient”
```

如果您想要指定連接字串，在命令列，則您也必須提供的提供者名稱。 未指定的提供者名稱，將會導致例外狀況。

## <a name="common-problems"></a>常見問題

| 錯誤訊息                                                                                                                                                                                                                                                                                                                      | 方案                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 未處理的例外狀況： System.IO.FileLoadException： 無法載入檔案或組件 'EntityFramework，version=5.0.0.0，Culture = neutral，publickeytoken=b77a5c561934e089' 或其中一個相依性。 找到的組件資訊清單定義不符合組件參考。 (來自 HRESULT 的例外狀況： 0x80131040)         | 這通常表示您沒有 Redirect.config 檔案執行.NET 4 應用程式。 您需要將 Redirect.config 複製到與 migrate.exe 相同的位置，並將它重新命名為 migrate.exe.config。                                                                                       |
| 未處理的例外狀況： System.IO.FileLoadException： 無法載入檔案或組件 'EntityFramework，版本 = 4.4.0.0，Culture = neutral，publickeytoken=b77a5c561934e089' 或其中一個相依性。 找到的組件資訊清單定義不符合組件參考。 (來自 HRESULT 的例外狀況： 0x80131040)          | 這個例外狀況表示您正在.NET 4.5 Redirect.config 應用程式複製到 migrate.exe 位置。 如果您的應用程式是.NET 4.5 則您不需要有組態檔內的重新導向使用。 刪除 migrate.exe.config 檔案。                                    |
| 錯誤： 無法更新以符合目前的模型，因為有暫止的變更和已停用自動移轉的資料庫。 寫入程式碼為基礎的移轉暫止的模型變更，或啟用自動移轉。 True 會啟用自動移轉設定至 DbMigrationsConfiguration.AutomaticMigrationsEnabled。 | 如果執行移轉時您並未建立移轉至應付加入模型中，所做的變更和資料庫與模型不符，就會發生此錯誤。 將屬性加入至模型類別，則不需要建立移轉至升級的資料庫執行 migrate.exe 是這個範例。 |
| 錯誤： 成員未解析的類型 ' System.Data.Entity.Migrations.Design.ToolingFacade+UpdateRunner,EntityFramework，version=5.0.0.0，Culture = neutral，publickeytoken=b77a5c561934e089 '。                                                                                                                                       | 此錯誤可能因指定不正確的啟動目錄。 這必須是 migrate.exe 的位置                                                                                                                                                                                      |
| 未處理的例外狀況： System.NullReferenceException： 物件參考未設定為物件的執行個體。 <br/>   在 System.Data.Entity.Migrations.Console.Program.Main (String [] args)                                                                                                                                             | 這可能被因未指定必要的參數，如您所使用的案例。 不需要指定提供者名稱，例如指定的連接字串。                                                                                                                        |
| 錯誤: 'ClassLibrary1' 組件中找到一個以上的移轉組態類型。 指定要使用一個名稱。                                                                                                                                                                                                  | 如錯誤所述，一個以上的組態類別中沒有指定的組件。 您必須使用 /configurationType 參數來指定要使用哪一個。                                                                                                                                           |
| 錯誤： 無法載入檔案或組件 '&lt;assemblyName&gt;' 或其中一個相依性。 指定的組件名稱，或程式碼基底無效。 (來自 HRESULT 的例外狀況： 0x80131047)                                                                                                                                                    | 這可能被因未正確指定組件名稱，或不需要                                                                                                                                                                                                                          |
| 錯誤： 無法載入檔案或組件 '&lt;assemblyName&gt;' 或其中一個相依性。 嘗試載入了格式不正確的程式。                                                                                                                                                                          | 如果您嘗試執行 migrate.exe 對 x64 應用程式。 EF 5.0 和以下則只能在 x86 上。                                                                                                                                                                                |
