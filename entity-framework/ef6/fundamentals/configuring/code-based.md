---
title: 程式碼為基礎的組態-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
ms.openlocfilehash: 58acb7e74fee66cc70f78ef2c3474524d19264de
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993329"
---
# <a name="code-based-configuration"></a>程式碼為基礎的組態
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

在組態檔 (app.config/web.config) 或透過程式碼，可以指定之 Entity Framework 應用程式設定。 後者稱為程式碼為基礎的組態。  

組態設定檔中的所述[其他文章另](config-file.md)。 組態檔的優先順序高於程式碼為基礎的組態。 換句話說，如果這兩個程式碼和組態檔設定組態選項，然後在組態檔中會使用設定。  

## <a name="using-dbconfiguration"></a>使用 DbConfiguration  

程式碼為基礎的組態在 EF6 中和更新版本之後，即可建立 System.Data.Entity.Config.DbConfiguration 的子類別。 DbConfiguration 子類別化時，應遵循下列指導方針：  

- 建立您的應用程式只有一個 DbConfiguration 類別。 這個類別會指定應用程式定義域的各種設定。  
- 將 DbConfiguration 類別放在與您的 DbContext 類別相同的組件中。 (請參閱*移動 DbConfiguration*如果您想要變更這一節。)  
- 提供給 DbConfiguration 類別公用的無參數建構函式。  
- 藉由呼叫從受保護的 DbConfiguration 方法，這個建構函式中設定組態選項。  

遵循這些指導方針，可讓 EF 來探索並需要存取您的模型，並執行您的應用程式時，這兩個工具會自動使用您的組態。  

## <a name="example"></a>範例  

類別，衍生自 DbConfiguration 可能看起來像這樣：  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDBConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

這個類別會設定使用 SQL Azure 執行策略-，會自動重試失敗的資料庫作業-，以及用於從 Code First 慣例所建立的資料庫中的本機資料庫的 EF。  

## <a name="moving-dbconfiguration"></a>移動 DbConfiguration  

有一些在不可能將 DbConfiguration 類別放在與您的 DbContext 類別相同的組件的情況。 例如，您可能會有兩個 DbContext 類別每個不同的組件中。 有兩個選項可處理此。  

第一個選項是使用組態檔來指定要使用 DbConfiguration 執行個體。 若要這樣做，請設定 entityFramework 區段的 codeConfigurationType 屬性。 例如:   

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType 的值必須是組件和 DbConfiguration 類別的命名空間限定的名稱。  

第二個選項是將 DbConfigurationTypeAttribute 放在您的內容類別。 例如:   

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

此值傳遞至屬性可以是您 DbConfiguration 類型--如上所示，或組件和命名空間限定類型名稱字串。 例如:   

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>明確地設定 DbConfiguration  

有某些情況下，組態可能需要之前已使用任何 DbContext 類型。 範例包括：  

- 使用 DbModelBuilder 建置的模型沒有內容  
- 使用一些其他 framework/公用程式的程式碼之前會使用您的應用程式內容會使用該內容的地方 DbContext  

在此情況下 EF 無法自動探索設定，您必須改為執行下列其中一項：  

- 在組態檔中，設定 DbConfiguration 型別，如中所述*移動 DbConfiguration*上一節
- 在 應用程式啟動期間呼叫靜態 DbConfiguration.SetConfiguration 方法  

## <a name="overriding-dbconfiguration"></a>覆寫 DbConfiguration  

有一些情況下，您要覆寫在 DbConfiguration 中設定的組態。 不這麼做通常由應用程式開發人員，而是只要協力廠商提供者和無法使用衍生的 DbConfiguration 類別的外掛程式。  

為此，EntityFramework 可讓它鎖定之前可以修改現有的組態的事件處理常式註冊。  它也提供 sugar 方法專為取代 EF 服務定位程式所傳回的任何服務。 這是它要使用的方式：  

- 在應用程式啟動 （之前使用 EF） 外掛程式或提供者應該註冊這個事件的事件處理常式方法。 （請注意，這必須進行應用程式使用 EF 之前）。  
- 事件處理常式會針對每個服務，需要更換 ReplaceService 呼叫。  

比方說，repalce IDbConnectionFactory 和 DbProviderService 您會註冊一個處理常式，如下所示：  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

MyProviderServices 和 MyConnectionFactory 上方的程式碼代表您實作的服務。  

您也可以新增額外的相依性的處理常式，來取得相同的效果。  

請注意，您也可以包裝 DbProviderFactory，如此一來，但這麼做因此只會影響 EF 和不使用 EF 外部的 DbProviderFactory。 因此您可能要包裝 DbProviderFactory，因為您之前會繼續。  

您應該也請記住您執行外部以您的應用程式-例如，從套件管理員主控台中執行移轉時的服務。 當您執行移轉從它會嘗試尋找您 DbConfiguration 主控台。 不過，是否會取得已包裝的服務取決於 where 它所註冊的事件處理常式。 如果它註冊為您 DbConfiguration 建構的一部分，應該執行的程式碼，並應該取得包裝的服務。 通常這不會發生這個情況，這表示工具無法取得已包裝的服務。  
