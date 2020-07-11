---
title: 套用遷移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238747"
---
# <a name="applying-migrations"></a>套用遷移

新增您的遷移之後，必須將其部署並套用至您的資料庫。 執行此作業的策略有很多種，有些則更適合用於生產環境，而其他則適用于開發週期。

> [!NOTE]
> 無論您的部署策略為何，在套用至實際執行資料庫之前，一律會檢查產生的遷移並加以測試。 當目的是要將資料行重新命名時，遷移可能會卸載它，或在套用至資料庫時，可能會因為各種原因而失敗。

## <a name="sql-scripts"></a>SQL 腳本

將遷移部署到實際執行資料庫的建議方式是產生 SQL 腳本。 此策略的優點包括下列各項：

* 可以檢查 SQL 腳本的正確性;這很重要，因為將架構變更套用至生產環境資料庫是可能會造成資料遺失的潛在危險作業。
* 在某些情況下，可以微調腳本以符合生產資料庫的特定需求。
* SQL 腳本可與部署技術搭配使用，甚至可以在 CI 程式中產生。
* SQL 腳本可以提供給 DBA，而且可以分開管理和封存。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>基本使用方式

下列會從空白資料庫產生 SQL 腳本至最新的遷移：

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>使用 From (隱含)

下列會產生從給定的遷移到最新的遷移的 SQL 腳本。

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>使用 From 與 To

下列會從指定的遷移產生 SQL 腳本 `from` 到指定的 `to` 遷移。

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

您可以使用比 `to` 新的 `from` 來產生復原指令碼。

> [!WARNING]
> 請注意，可能會發生資料遺失的狀況。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>基本使用方式

下列會從空白資料庫產生 SQL 腳本至最新的遷移：

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>使用 From (隱含)

下列會產生從給定的遷移到最新的遷移的 SQL 腳本。

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>使用 From 與 To

下列會從指定的遷移產生 SQL 腳本 `from` 到指定的 `to` 遷移。

```powershell
Script-Migration AddNewTables AddAuditTable
```
您可以使用比 `to` 新的 `from` 來產生復原指令碼。 *請注意，可能會發生資料遺失的狀況。*

***

腳本產生會接受下列兩個引數，指出應該產生的遷移範圍：

* 執行指令碼之前，**from** 移轉應該是套用到資料庫的最後一個移轉。 若未套用任何移轉，請指定 `0` (此為預設)。
* 執行指令碼之後，**to** 移轉是套用到資料庫的最後一個移轉。 預設為您專案中的最後一個移轉。

## <a name="idempotent-sql-scripts"></a>等冪 SQL 腳本

上述產生的 SQL 腳本僅適用于將您的架構從一個遷移變更為另一個。您必須負責適當地套用腳本，而僅限於正確遷移狀態下的資料庫。 EF Core 也支援產生**等冪**腳本，這會在內部檢查哪些遷移已透過「遷移記錄」資料表)  (套用，而且只會套用遺漏的。 如果您不完全知道套用到資料庫的最後一個遷移是什麼，或者您要部署到多個資料庫，而每一個都是在不同的遷移上，這就很有用。

以下會產生等冪遷移：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a>命令列工具

EF 命令列工具可以用來將遷移套用至資料庫。 雖然在本機開發和測試遷移的生產力，但這種方法並不適合用來管理生產資料庫：

* 此工具會直接套用 SQL 命令，而不會提供開發人員變更來檢查或修改它們。 這在生產環境中可能會有危險。
* .NET SDK 和 EF 工具必須安裝在實際執行伺服器上。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

以下會將您的資料庫更新為最新的遷移：

```dotnetcli
dotnet ef database update
```

以下會將您的資料庫更新為指定的遷移：

```dotnetcli
dotnet ef database update AddNewTables
```

請注意，這也可以用來回複到先前的遷移。

> [!WARNING]
> 請注意，可能會發生資料遺失的狀況。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

以下會將您的資料庫更新為最新的遷移：

``` powershell
Update-Database
```

以下會將您的資料庫更新為指定的遷移：

``` powershell
Update-Database AddNewTables
```

請注意，這也可以用來回複到先前的遷移。

> [!WARNING]
> 請注意，可能會發生資料遺失的狀況。

***

如需透過命令列工具套用遷移的詳細資訊，請參閱[EF Core 工具參考](xref:core/miscellaneous/cli/index)。

## <a name="apply-migrations-at-runtime"></a>在執行階段套用移轉

應用程式本身有可能以程式設計方式套用遷移，通常是在啟動期間執行。 雖然在本機開發和測試遷移方面有生產力，但這種方法不適合用于管理生產資料庫，原因如下：

* 如果您的應用程式有多個實例正在執行，這兩個應用程式可能會嘗試同時套用遷移，而且 (或更糟的情況下，會造成資料損毀) 。
* 同樣地，如果應用程式在另一個應用程式進行遷移時存取資料庫，這可能會造成嚴重的問題。
* 應用程式必須具有更高的存取權，才能修改資料庫架構。 在生產環境中限制應用程式的資料庫許可權通常是很好的作法。
* 請務必能夠在發生問題時復原已套用的遷移。 其他策略則提供這種簡單且現成的。
* SQL 命令是直接由程式套用，而不會讓開發人員進行變更來檢查或修改它們。 這在生產環境中可能會有危險。

若要以程式設計方式套用遷移，請呼叫 `context.Database.Migrate()` 。 例如，典型的 ASP.NET 應用程式可以執行下列動作：

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

請注意， `Migrate()` 建置於服務的最上層 `IMigrator` ，可用於更先進的案例。 您可以使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 來加以存取。

> [!WARNING]
>
> * 在生產環境中使用此方法之前，請先仔細考慮。 經驗顯示，此部署策略的簡單性是由它所建立的問題所遠遠超過。 請考慮改為使用 SQL 腳本。
> * 請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。 `EnsureCreated()` 會略過移轉而建立結構描述，因此造成 `Migrate()` 失敗。
