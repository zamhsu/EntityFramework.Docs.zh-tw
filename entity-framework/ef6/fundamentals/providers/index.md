---
title: Entity Framework 提供者 - EF6
author: divega
ms.date: 06/27/2018
ms.assetid: 7BFB7763-CD6C-4520-93A2-7B265F5FA586
ms.openlocfilehash: bf07296503e4bb5d1e13f5f6f29e7118cbbde61d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181680"
---
# <a name="entity-framework-6-providers"></a>Entity Framework 6 提供者
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

Entity Framework 目前正在開放原始碼授權下進行開發，EF6 和更新版本將不會隨附於 .NET Framework。 這有許多優點，但也需要針對 EF6 組件重建 EF 提供者。 這表示 EF5 和先前版本的 EF 提供者在重建之前將不適用於 EF6。

## <a name="which-providers-are-available-for-ef6"></a>哪些提供者可供 EF6 使用？

我們知道已針對 EF6 重建的提供者包括：

*   **Microsoft SQL Server 提供者**
    *   從 [Entity Framework 開放原始碼程式碼基底](https://github.com/aspnet/EntityFramework6)建置
    *   隨附於 [EntityFramework NuGet 套件](https://nuget.org/packages/EntityFramework)
*   **Microsoft SQL Server Compact Edition 提供者**
    *   從 [Entity Framework 開放原始碼程式碼基底](https://github.com/aspnet/EntityFramework6)建置
    *   隨附於 [EntityFramework.SqlServerCompact NuGet 套件](https://nuget.org/packages/EntityFramework.SqlServerCompact)
*   [**Devart dotConnect 資料提供者**](https://www.devart.com/dotconnect/)
    *   有來自 [Devart](https://www.devart.com/) 且適用於各種資料庫的協力廠商提供者，包括 Oracle、MySQL、PostgreSQL、SQLite、Salesforce、DB2 和 SQL Server
*   [**CData Software 提供者**](https://www.cdata.com/ado/)
    *   有來自 [CData Software](https://www.cdata.com/ado/) 且適用於各種資料存放區的協力廠商提供者，包括 Salesforce、 Azure 表格儲存體、MySql 等等
*   **Firebird 提供者**
    *   以 [NuGet 套件](https://www.nuget.org/packages/EntityFramework.Firebird/)的形式提供給您
*   **Visual Fox Pro 提供者**
    *   以 [NuGet 套件](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)的形式提供給您
*   **MySQL**
    *   [MySQL Connector/NET for Entity Framework](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   **PostgreSQL**
    *   Npgsql 是以 [NuGet 套件](https://www.nuget.org/packages/EntityFramework6.Npgsql/)的形式提供給您
*   **Oracle**
    *   ODP.NET 是以 [NuGet 套件](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)的形式提供給您

請注意，這份清單中的包含項目並不表示指定提供者的功能或支援層級，而只是表示已提供適用於 EF6 的組建。

## <a name="registering-ef-providers"></a>註冊 EF 提供者

從 Entity Framework 6 開始，EF 提供者可以使用程式碼架構組態或應用程式的組態檔進行註冊。

### <a name="config-file-registration"></a>組態檔註冊

app.config 或 web.config 中的 EF 提供者註冊具有下列格式：


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

請注意，通常如果從 NuGet 安裝 EF 提供者，則 NuGet 套件會自動將此註冊新增至設定檔。 如果您將 NuGet 套件安裝到不是應用程式啟始專案的專案中，則可能需要將註冊複製到啟始專案的組態檔。

此註冊中的 “invariantName” 是用來識別 ADO.NET 提供者的相同非變異名稱。 這可視為 DbProviderFactories 註冊中的 “invariant” 屬性，以及連接字串註冊中的 “providerName” 屬性。 要使用的非變異名稱也應該包含在提供者的文件中。 非變異名稱的範例是 “System.Data.SqlClient” (適用於 SQL Server) 和 “System.Data.SqlServerCe.4.0” (適用於 SQL Server Compact)。

此註冊中的 “type” 是衍生自 “System.Data.Entity.Core.Common.DbProviderServices” 之提供者類型的組件限定名稱。 例如，要用於 SQL Compact 的字串會是 “System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact”。 要在此處使用的類型應該包含在提供者的文件中。

### <a name="code-based-registration"></a>程式碼架構註冊

從 Entity Framework 6 開始，可以在程式碼中指定 EF 的整個應用程式組態。 如需完整的詳細料，請參閱 _[Entity Framework 程式碼架構組態](https://msdn.microsoft.com/data/jj680699)_ 。 使用程式碼架構組態註冊 EF 提供者的一般方式是建立衍生自 System.Data.Entity.DbConfiguration 的新類別，並將它放在與您的 DbContext 類別相同的組件中。 DbConfiguratio 類別接著應該在其建構函式中註冊提供者。 例如，若要註冊 SQL Compact 提供者，DbConfiguration 類別看起來像這樣：

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

在此程式碼中，“SqlCeProviderServices.ProviderInvariantName” 可讓您方便使用 SQL Server Compact 提供者非變異名稱字串 (“System.Data.SqlServerCe.4.0”)，而 SqlCeProviderServices.Instance 會傳回 SQL Compact EF 提供者的單一執行個體。

## <a name="what-if-the-provider-i-need-isnt-available"></a>如果我需要的提供者無法使用，該怎麼辦？

如果該提供者適用於舊版的 EF，則建議您連絡提供者的擁有者，並要求他們建立 EF6 版本。 您應該併入 [EF6 提供者模型的文件](~/ef6/fundamentals/providers/provider-model.md)參考。

## <a name="can-i-write-a-provider-myself"></a>我可以自行撰寫提供者嗎？

您當然可以自行建立 EF 提供者，但這絕非易事。 有關 EF6 提供者模型的上方連結是一個不錯的起點。 您還可能會發現使用 SQL Server 和 SQL CE 提供者的程式碼作為起點或參考很有用，而這些程式碼包含在 [EF 開放原始碼程式碼基底](https://github.com/aspnet/EntityFramework6)中。

請注意，從 EF6 開始，EF 提供者不再與基礎 ADO.NET 提供者緊密結合。 這可讓您更輕鬆地撰寫 EF 提供者，而不必撰寫或包裝 ADO.NET 類別。
