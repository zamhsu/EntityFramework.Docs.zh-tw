---
title: 遷移總覽-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238225"
---
# <a name="migrations-overview"></a>遷移總覽

在實際的專案中，資料模型會隨著功能的執行而變更：新增和移除新的實體或屬性，而且必須據此變更資料庫架構，使其與應用程式保持同步。 EF Core 中的移轉功能讓您能夠以累加方式來更新資料庫結構描述，讓它與應用程式的資料模型保持同步，同時將現有的資料保留在資料庫中。

概括而言，遷移的運作方式如下：

* 引進資料模型變更時，開發人員會使用 EF Core 工具來加入對應的遷移，以描述保持資料庫架構同步所需的更新。EF Core 會比較目前的模型與舊模型的快照集，以判斷差異，並產生遷移來源檔案;您可以在專案的原始檔控制中追蹤檔案，就像任何其他原始程式檔一樣。
* 一旦產生新的遷移之後，就可以用各種方式將它套用到資料庫。 EF Core 會記錄特殊歷程記錄資料表中所有套用的遷移，讓 it 知道已套用但尚未套用的遷移。

本頁面的其餘部分是使用遷移的逐步入門指南。 如需更深入的資訊，請參閱本節中的其他頁面。

## <a name="getting-started"></a>開始使用

假設您剛完成第一個 EF Core 應用程式，其中包含下列簡單的模型：

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

在開發期間，您可能已使用[建立和卸載 api](xref:core/managing-schemas/ensure-created)來快速反復執行，並視需要變更您的模型;但現在您的應用程式即將進入生產階段，您需要一種方法來安全地演進架構，而不需卸載整個資料庫。

### <a name="install-the-tools"></a>安裝工具

首先，您必須安裝[EF Core 命令列工具](xref:core/miscellaneous/cli/index)：

* 我們通常建議使用可在所有平臺上運作的[.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet)。
* 如果您更熟悉 Visual Studio 或擁有 EF6 遷移的經驗，您也可以使用[套件管理員主控台工具](xref:core/miscellaneous/cli/powershell)。

### <a name="create-your-first-migration"></a>建立您的第一次遷移

您現在已準備好加入您的第一次遷移！ 指示 EF Core 建立名為**InitialCreate**的遷移：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

EF Core 會在專案中建立名為「**遷移**」的目錄，並產生一些檔案。 最好的做法是檢查產生的 EF Core，而且可能會修改它，但我們現在會跳過該程式。

### <a name="create-your-database-and-schema"></a>建立您的資料庫和架構

此時，您可以讓 EF 建立您的資料庫，並從遷移建立您的架構。 這可以透過下列方式來完成：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

就是這麼簡單-您的應用程式已準備好在新的資料庫上執行，而且您不需要撰寫任何一行的 SQL。 請注意，這種套用遷移的方式適用于本機開發，但較不適合用于生產環境-如需詳細資訊，請參閱[應用程式遷移頁面](xref:core/managing-schemas/migrations/applying)。

### <a name="evolving-your-model"></a>發展您的模型

經過幾天後，系統會要求您將建立時間戳記新增至您的 blog。 您已完成對應用程式所做的必要變更，而您的模型現在看起來像這樣：

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

您的模型和生產資料庫現在已不同步-我們必須將新的資料行加入至您的資料庫架構。 讓我們為此建立新的遷移：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

請注意，我們會為遷移提供描述性的名稱，讓您稍後可以更輕鬆地瞭解專案歷程記錄。

由於這不是專案的第一次遷移，因此在加入資料行之前，EF Core 現在會比較已更新的模型與舊模型的快照集;模型快照集是當您加入遷移時 EF Core 所產生的其中一個檔案，而且會簽入原始檔控制中。 根據該比較，EF Core 會偵測到已新增資料行，並新增適當的遷移。

您現在可以像之前一樣套用您的遷移：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

請注意，這次 EF 會偵測到資料庫已經存在。 此外，當我們的第一次遷移已套用時，此事實會記錄在資料庫的特殊遷移歷程記錄資料表中;這可讓 EF 自動套用新的遷移。

### <a name="next-steps"></a>後續步驟

以上只是簡單的遷移簡介。 請參閱其他檔頁面，以深入瞭解如何[管理遷移](xref:core/managing-schemas/migrations/managing)、套用[它們](xref:core/managing-schemas/migrations/applying)和其他層面。 [.NET Core CLI 工具參考](xref:core/miscellaneous/cli/index)也包含不同命令的實用資訊
