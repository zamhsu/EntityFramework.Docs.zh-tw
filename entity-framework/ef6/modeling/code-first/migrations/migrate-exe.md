---
title: 使用 migrate.exe-EF6
description: 使用 Entity Framework 6 中的 migrate.exe
author: divega
ms.date: 10/23/2016
ms.assetid: 989ea862-e936-4c85-926a-8cfbef5df5b8
uid: ef6/modeling/code-first/migrations/migrate-exe
ms.openlocfilehash: e35907ed421d95111ca956aa928d65ece2c9a116
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616991"
---
# <a name="using-migrateexe"></a>使用 migrate.exe
Code First 移轉可以用來從 visual studio 內更新資料庫，但也可以透過命令列工具 migrate.exe 來執行。 此頁面將快速瞭解如何使用 migrate.exe 來執行對資料庫的遷移。

> [!NOTE]
> 本文假設您知道如何在基本案例中使用 Code First 移轉。 如果您沒有這麼做，就必須先閱讀 [Code First 移轉](xref:ef6/modeling/code-first/migrations/index) ，再繼續進行操作。

## <a name="copy-migrateexe"></a>複製 migrate.exe

當您使用 NuGet migrate.exe 安裝 Entity Framework 時，將會位於所下載套件的 [工具] 資料夾內。 在 [ &lt; 專案資料夾 &gt; \\ 封裝 \\ EntityFramework &lt; ] 中。版本 &gt; \\ 工具

migrate.exe 之後，您必須將它複製到包含您的遷移之元件的位置。

如果您的應用程式是以 .NET 4 為目標，而不是4.5，則您也必須將 **Redirect.config** 複製到位置，然後將它重新命名 **migrate.exe.config**。這是為了讓 migrate.exe 取得正確的系結重新導向，以找出 Entity Framework 元件。

| .NET 4.5                                      | .NET 4。0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![.NET 4.5 檔案](~/ef6/media/net45files.png) | ![.NET 4.0 檔案](~/ef6/media/net40files.png) |

> [!NOTE]
> migrate.exe 不支援 x64 元件。

一旦您將 migrate.exe 移至正確的資料夾，就應該能夠使用它來執行資料庫的遷移。 所有公用程式的設計都是執行遷移。 它無法產生遷移或建立 SQL 腳本。

## <a name="see-options"></a>查看選項

``` console
Migrate.exe /?
```

上述程式會顯示與此公用程式相關聯的 [說明] 頁面，請注意，您必須在執行 migrate.exe 的相同位置中有 EntityFramework.dll，才能讓此作業正常運作。

## <a name="migrate-to-the-latest-migration"></a>遷移至最新的遷移

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

當 migrate.exe 執行時，唯一的必要參數是元件，也就是包含您要執行之遷移的元件，但如果未指定設定檔，則會使用所有以慣例為基礎的設定。

## <a name="migrate-to-a-specific-migration"></a>遷移至特定的遷移

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

如果您想要執行遷移至特定的遷移，您可以指定遷移的名稱。 這將會依需要執行所有先前的遷移，直到您進入指定的遷移為止。

## <a name="specify-working-directory"></a>指定工作目錄

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

如果您的元件具有相依性或讀取相對於工作目錄的檔案，則您必須設定 startupDirectory。

## <a name="specify-migration-configuration-to-use"></a>指定要使用的遷移設定

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

如果您有多個遷移設定類別，繼承自 DbMigrationConfiguration 的類別，則您必須指定要用於此執行的類別。 這是藉由提供選用的第二個參數，而不是上面的參數來指定。

## <a name="provide-connection-string"></a>提供連接字串

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

如果您想要在命令列指定連接字串，則也必須提供提供者名稱。 未指定提供者名稱將會造成例外狀況。

## <a name="common-problems"></a>常見問題

| 錯誤訊息                                                                                                                                                                                                                                                                                                                      | 解決方案                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 未處理的例外狀況： FileLoadException：無法載入檔案或元件 ' EntityFramework，Version = 5.0.0.0，Culture = 中性，PublicKeyToken = b77a5c561934e089 ' 或其相依性的其中之一。 找到元件的資訊清單定義與元件參考不符。 HRESULT 的 (例外狀況： 0x80131040)          | 這通常表示您執行的是沒有 Redirect.config 檔案的 .NET 4 應用程式。 您必須將 Redirect.config 複製到 migrate.exe 的相同位置，並將它重新命名為 migrate.exe.config。                                                                                       |
| 未處理的例外狀況： FileLoadException：無法載入檔案或元件 ' EntityFramework，Version = v4.4.0.0，Culture = 中性，PublicKeyToken = b77a5c561934e089 ' 或其相依性的其中之一。 找到元件的資訊清單定義與元件參考不符。 HRESULT 的 (例外狀況： 0x80131040)           | 這個例外狀況表示您正在執行 .NET 4.5 應用程式，並將 Redirect.config 複製到 migrate.exe 位置。 如果您的應用程式是 .NET 4.5，則您不需要在內部重新導向至設定檔。 刪除 migrate.exe.config 檔案。                                    |
| 錯誤：無法更新資料庫以符合目前的模型，因為有暫止的變更，且已停用自動遷移。 將暫止的模型變更寫入至以程式碼為基礎的遷移，或啟用自動遷移。 將 DbMigrationsConfiguration AutomaticMigrationsEnabled 設定為 true 以啟用自動遷移。 | 如果您未建立遷移來處理對模型所做的變更，而且資料庫與模型不相符，就會發生這個錯誤。 將屬性新增至模型類別，然後在不建立遷移來升級資料庫的情況下執行 migrate.exe，就是這種情況的範例。 |
| 錯誤：無法解析成員 ' ToolingFacade + UpdateRunner，EntityFramework，Version = 5.0.0.0，Culture = 中性，PublicKeyToken = b77a5c561934e089 ' 的類型。                                                                                                                                       | 這個錯誤可能是因為指定的啟動目錄不正確所造成。 這必須是 migrate.exe 的位置                                                                                                                                                                                      |
| 未處理的例外狀況： NullReferenceException：物件參考未設定為物件的實例。 <br/>   在 System.string....... Main (String [] args)                                                                                                                                              | 這可能是因為沒有針對您所使用的情節指定必要參數所造成。 例如，指定連接字串，但不指定提供者名稱。                                                                                                                        |
| 錯誤：在元件 ' ClassLibrary1 ' 中找到一個以上的遷移設定類型。 指定要使用的名稱。                                                                                                                                                                                                  | 在錯誤狀態中，指定的元件中有一個以上的設定類別。 您必須使用/configurationType 參數來指定要使用的參數。                                                                                                                                           |
| 錯誤：無法載入檔案或元件 ' &lt; assemblyName &gt; ' 或其相依性的其中一個。 指定的元件名稱或程式碼基底無效。 HRESULT 的 (例外狀況： 0x80131047)                                                                                                                                                     | 這可能是因為指定的元件名稱不正確或沒有                                                                                                                                                                                                                          |
| 錯誤：無法載入檔案或元件 ' &lt; assemblyName &gt; ' 或其相依性的其中一個。 嘗試載入了格式不正確的程式。                                                                                                                                                                          | 如果您嘗試對 x64 應用程式執行 migrate.exe，就會發生這種情況。 EF 5.0 和以下僅適用于 x86。                                                                                                                                                                                |
