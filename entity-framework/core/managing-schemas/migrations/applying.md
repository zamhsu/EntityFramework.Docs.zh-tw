---
title: 套用遷移-EF Core
description: 使用 Entity Framework Core 將架構遷移套用至生產和開發資料庫的策略
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 7ff84636fb0999941b832c6a2d65d77b0ad368c5
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429843"
---
# <a name="applying-migrations"></a>套用遷移

新增您的遷移之後，您必須將其部署並套用至您的資料庫。 有各種不同的策略可執行此操作，而某些策略更適合生產環境，其他則適用于開發週期。

> [!NOTE]
> 無論您的部署策略為何，一律先檢查產生的遷移，並測試它們，再套用至生產環境資料庫。 當意圖要重新命名時，遷移可能會卸載資料行，或可能會因為套用至資料庫的各種原因而失敗。

## <a name="sql-scripts"></a>SQL 腳本

部署到生產環境資料庫的建議方式是產生 SQL 腳本。 這項策略的優點包括下列各項：

* 您可以檢查 SQL 腳本的精確度;這點很重要，因為將架構變更套用到生產資料庫是可能會造成資料遺失的潛在危險作業。
* 在某些情況下，可以調整腳本以符合生產資料庫的特定需求。
* SQL 腳本可以與部署技術搭配使用，甚至可以在您的 CI 程式中產生。
* 您可以將 SQL 腳本提供給 DBA，並且可以個別管理和封存。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>基本使用方式

下列會從空白資料庫產生 SQL 腳本至最新的遷移：

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>使用 From (隱含)

下列會從指定的遷移產生 SQL 腳本至最新的遷移。

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>使用 From 與 To

下列會從指定的遷移產生 SQL 腳本 `from` 至指定的 `to` 遷移。

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

您可以使用比 `to` 新的 `from` 來產生復原指令碼。

> [!WARNING]
> 請注意，可能會發生資料遺失的狀況。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>基本使用方式

下列會從空白資料庫產生 SQL 腳本至最新的遷移：

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>使用 From (隱含)

下列會從指定的遷移產生 SQL 腳本至最新的遷移。

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>使用 From 與 To

下列會從指定的遷移產生 SQL 腳本 `from` 至指定的 `to` 遷移。

```powershell
Script-Migration AddNewTables AddAuditTable
```

您可以使用比 `to` 新的 `from` 來產生復原指令碼。 *請注意，可能會發生資料遺失的狀況。*

***

腳本產生會接受下列兩個引數，指出應產生的遷移範圍：

* 執行指令碼之前， **from** 移轉應該是套用到資料庫的最後一個移轉。 若未套用任何移轉，請指定 `0` (此為預設)。
* 執行指令碼之後， **to** 移轉是套用到資料庫的最後一個移轉。 預設為您專案中的最後一個移轉。

## <a name="idempotent-sql-scripts"></a>等冪 SQL 腳本

上述產生的 SQL 腳本只能套用至將架構從一個遷移變更為另一個：您必須負責適當地套用腳本，而且只能在資料庫中正確的遷移狀態。 EF Core 也支援產生 **等冪** 腳本，其會在內部檢查哪些遷移已透過) 的「遷移歷程記錄」資料表 (套用，而且只會套用遺漏的遷移。 如果您不完全知道套用至資料庫的最後一次遷移，或您要部署到多個資料庫，且每個資料庫都有不同的遷移方式，這會很有用。

下列會產生等冪的遷移：

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Script-Migration -Idempotent
```

**_

## <a name="command-line-tools"></a>命令列工具

EF 命令列工具可以用來將遷移套用至資料庫。 雖然能在本機開發和測試遷移時保持生產力，但這種方法並不適合用來管理生產資料庫：

_ SQL 命令會直接套用到工具，而不會讓開發人員有機會檢查或修改它們。 這在生產環境中可能會有危險。
* 實際執行伺服器上必須安裝 .NET SDK 和 EF 工具。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

下列程式會將您的資料庫更新至最新的遷移：

```dotnetcli
dotnet ef database update
```

下列程式會將您的資料庫更新為指定的遷移：

```dotnetcli
dotnet ef database update AddNewTables
```

請注意，這也可以用來復原至先前的遷移。

> [!WARNING]
> 請注意，可能會發生資料遺失的狀況。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

下列程式會將您的資料庫更新至最新的遷移：

```powershell
Update-Database
```

下列程式會將您的資料庫更新為指定的遷移：

```powershell
Update-Database AddNewTables
```

請注意，這也可以用來復原至先前的遷移。

> [!WARNING]
> 請注意，可能會發生資料遺失的狀況。

***

如需透過命令列工具套用遷移的詳細資訊，請參閱 [EF Core 工具參考](xref:core/cli/index)。

## <a name="apply-migrations-at-runtime"></a>在執行階段套用移轉

應用程式本身可能會以程式設計方式套用遷移，通常是在啟動期間進行。 雖然在本機開發和測試遷移方面都有生產力，但這種方法並不適合用來管理生產資料庫，原因如下：

* 如果您的應用程式有多個實例正在執行，這兩個應用程式都可以嘗試同時套用遷移，而 (或更糟的情況下，會造成資料損毀) 。
* 同樣地，如果應用程式在另一個應用程式遷移時存取資料庫，這可能會造成嚴重的問題。
* 應用程式必須具有較高的存取權，才能修改資料庫架構。 一般來說，限制應用程式在生產環境中的資料庫許可權是很好的作法。
* 在發生問題時，必須能夠復原已套用的遷移。 其他策略則可輕鬆且現成地提供。
* SQL 命令會直接套用到程式，而不會讓開發人員有機會檢查或修改它們。 這在生產環境中可能會有危險。

若要以程式設計方式套用遷移，請呼叫 `context.Database.Migrate()` 。 例如，一般的 ASP.NET 應用程式可以執行下列作業：

```csharp
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

請注意， `Migrate()` 建置於服務之上 `IMigrator` ，可用於更先進的案例。 您可以使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 來加以存取。

> [!WARNING]
>
> * 在生產環境中使用這種方法之前，請先仔細考慮。 經驗證明，這項部署策略的簡潔性是由它所建立的問題所遠遠超過。 請考慮改為從遷移產生 SQL 腳本。
> * 請勿在 `Migrate()` 之前呼叫 `EnsureCreated()`。 `EnsureCreated()` 會略過移轉而建立結構描述，因此造成 `Migrate()` 失敗。
