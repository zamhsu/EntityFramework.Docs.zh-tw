---
title: 移轉概觀 - EF Core
description: 透過 Entity Framework Core 使用移轉來管理資料庫結構描述的概觀
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: f1197fb869c33c34672d20e9b727cd187c9c5601
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619472"
---
# <a name="migrations-overview"></a>移轉概觀

在實際專案中，資料模型會隨著功能的實作 (新增和移除新的實體或屬性) 而變更，而且必須據以變更資料庫結構描述，使其與應用程式保持同步。 EF Core 中的移轉功能讓您能夠以累加方式來更新資料庫結構描述，讓它與應用程式的資料模型保持同步，同時將現有的資料保留在資料庫中。

概括而言，移轉的運作方式如下：

* 引進資料模型變更時，開發人員會使用 EF Core 工具來加入對應的移轉，描述保持資料庫結構描述同步所需的更新。EF Core 將目前的模型與舊模型的快照集進行比較以判斷差異，並產生移轉來源檔案；您可以在專案的原始程式碼控制中追蹤檔案，就像任何其他原始程式檔一樣。
* 一旦產生新的移轉，就可以用各種方式將其套用到資料庫。 EF Core 會在特殊歷程記錄資料表中記錄所有套用的移轉，因此可知道已套用與尚未套用的移轉。

此頁面的其餘部分是使用移轉的逐步入門指南。 如需更深入的資訊，請參閱此節中的其他頁面。

## <a name="getting-started"></a>開始使用

假設您剛完成第一個 EF Core 應用程式，其中包含下列簡單的模型：

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

在開發期間，您可能已使用[建立及卸除 API](xref:core/managing-schemas/ensure-created) 來快速逐一查看，並視需要變更您的模型；但既然您的應用程式即將進入生產階段，您需要一種方法來安全地使您的結構描述進化，而不需卸載整個資料庫。

### <a name="install-the-tools"></a>安裝工具

首先，您必須安裝 [EF Core 命令列工具](xref:core/miscellaneous/cli/index)：

* 我們通常建議使用 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet)，適用於所有平台。
* 如果更熟悉 Visual Studio 或有 EF6 移轉的經驗，您也可以使用 [套件管理員主控台工具](xref:core/miscellaneous/cli/powershell)。

### <a name="create-your-first-migration"></a>建立第一個移轉

您現在已準備好加入第一個移轉！ 指示 EF Core 建立名為 **InitialCreate** 的移轉：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

EF Core 將會在專案中建立名為 **Migrations** 的目錄，並產生一些檔案。 最好的作法是檢查 EF Core 產生的確切項目，並視需要加以修改，但我們現在會跳過此程序。

### <a name="create-your-database-and-schema"></a>建立您的資料庫與結構描述

此時，您可以讓 EF 建立您的資料庫，並從移轉建立您的結構描述。 這可以透過下列項目來完成：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

就是這麼簡單，您的應用程式已準備好在新的資料庫上執行，而且您不需要撰寫任何一行 SQL。 請注意，這種套用移轉的方式適用於本機開發，但較不適合用於生產環境。如需詳細資訊，請參閱[套用移轉頁面](xref:core/managing-schemas/migrations/applying)。

### <a name="evolving-your-model"></a>發展您的模型

幾天過去了，而且有人要求您將建立時間戳記新增至您的部落格。 您已完成對應用程式進行的必要變更，而且您的模型現在看起來像這樣：

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

您的模型與生產資料庫現在已不同步，我們必須將新的資料行加入您的資料庫結構描述。 讓我們為此建立新的移轉：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

請注意，我們會為移轉提供描述性名稱，讓您稍後可以更輕鬆地了解專案歷程記錄。

由於這不是專案的第一次移轉，因此在加入資料行之前，EF Core 現在會將已更新的模型與舊模型的快照集進行比較；模型快照集是當您加入移轉時 EF Core 所產生的其中一個檔案，而且會簽入原始程式碼控制中。 根據該比較，EF Core 會偵測到已新增資料行，並新增適當的移轉。

您現在可以像之前一樣套用移轉：

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

請注意，這次 EF 會偵測到資料庫已經存在。 此外，第一次套用上面的移轉時，此事實會記錄在資料庫的特殊移轉歷程記錄資料表中；這可讓 EF 僅自動套用新的移轉。

### <a name="next-steps"></a>後續步驟

以上只是簡單的移轉簡介。 請參閱其他文件頁面，以深入了解[管理移轉](xref:core/managing-schemas/migrations/managing)、 [套用移轉](xref:core/managing-schemas/migrations/applying)與其他層面。 [.NET Core CLI 工具參考](xref:core/miscellaneous/cli/index)也包含不同命令的實用資訊
