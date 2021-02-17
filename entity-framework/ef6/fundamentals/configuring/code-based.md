---
title: 以程式碼為基礎的設定-EF6
description: Entity Framework 6 中以程式碼為基礎的設定
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: b16cbcef85708730dcc6b82a38635cc60cb2206a
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543168"
---
# <a name="code-based-configuration"></a>以程式碼為基礎的設定
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

Entity Framework 應用程式的設定可以在設定檔中指定 (app.config/web.config) 或透過程式碼。 後者稱為以程式碼為基礎的設定。  

設定檔中的設定會在個別的 [文章](xref:ef6/fundamentals/configuring/config-file)中說明。 設定檔優先于以程式碼為基礎的設定。 換句話說，如果在程式碼和設定檔中設定了設定選項，則會使用設定檔中的設定。  

## <a name="using-dbconfiguration"></a>使用 `DbConfiguration`

EF6 和更新版本中以程式碼為基礎的設定是藉由建立的子類別來達成 `System.Data.Entity.Config.DbConfiguration` 。 子類別化時，應遵循下列指導方針 `DbConfiguration` ：  

- `DbConfiguration`為您的應用程式建立一個類別。 這個類別會指定整個應用程式網域的設定。  
- 將您 `DbConfiguration` 的類別放置在與您類別相同的元件中 `DbContext` 。 如果您想要變更此 (，請參閱 *移動 `DbConfiguration`* 區段。 )   
- 為您 `DbConfiguration` 的類別提供公用無參數的函式。  
- 從這個函式中呼叫受保護的方法，以設定設定選項 `DbConfiguration` 。  

遵循這些指導方針，可讓 EF 探索和使用您的設定，而這兩個工具都需要存取您的模型以及應用程式執行時。  

## <a name="example"></a>範例  

衍生自的類別 `DbConfiguration` 看起來可能像這樣：  

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
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

此類別會將 EF 設定為使用 SQL Azure 執行策略-自動重試失敗的資料庫作業，並針對依慣例從 Code First 建立的資料庫使用本機 DB。  

## <a name="moving-dbconfiguration"></a>移動 `DbConfiguration`  

在某些情況下，無法將您 `DbConfiguration` 的類別放置在與您類別相同的元件中 `DbContext` 。 例如，您可能會 `DbContext` 在不同的元件中有兩個類別。 有兩個選項可處理此情況。  

第一個選項是使用設定檔來指定 `DbConfiguration` 要使用的實例。 若要這樣做，請設定 entityFramework 區段的 codeConfigurationType 屬性。 例如：  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType 的值必須是您類別的元件和命名空間限定名稱 `DbConfiguration` 。  

第二個選項是放置 `DbConfigurationTypeAttribute` 在您的內容類別上。 例如：  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

傳遞給屬性的值可以是您的 `DbConfiguration` 類型（如上所示），或元件和命名空間限定類型名稱字串。 例如：  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>`DbConfiguration`明確設定  

在某些情況下，您可能需要進行一些設定，才能 `DbContext` 使用任何類型。 其中的範例包括：  

- 使用 `DbModelBuilder` 建立沒有內容的模型  
- 使用一些其他架構/公用程式程式碼，以使用在 `DbContext` 應用程式內容使用之前所使用的內容  

在這種情況下，EF 無法自動探索設定，您必須改為執行下列其中一項：  

- 在 `DbConfiguration` 設定檔中設定類型，如上述 *移動 `DbConfiguration`* 一節中所述
- 呼叫靜態 `DbConfiguration` 。應用程式啟動期間的 SetConfiguration 方法  

## <a name="overriding-dbconfiguration"></a>重寫 `DbConfiguration`  

在某些情況下，您需要覆寫中的設定集 `DbConfiguration` 。 這通常不是由應用程式開發人員完成，而是由無法使用衍生類別的協力廠商提供者和外掛程式所完成 `DbConfiguration` 。  

如此一來，EntityFramework 就可以註冊事件處理常式，以便在鎖定之前修改現有的設定。  它也會提供一個方便的方法，專門用來取代 EF 服務定位器所傳回的任何服務。 這就是要使用它的方式：  

- 在應用程式啟動時 (使用 EF 之前) 外掛程式或提供者應該為此事件註冊事件處理常式方法。  (請注意，這必須在應用程式使用 EF 之前發生。 )   
- 事件處理常式會對每個需要取代的服務呼叫 ReplaceService。  

例如，若要取代 `IDbConnectionFactory` ， `DbProviderService` 您會註冊處理常式，如下所示：  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

在上述程式碼中，表示您的服務的實作為 `MyProviderServices` `MyConnectionFactory` 。  

您也可以新增其他相依性處理常式來獲得相同的效果。  

請注意，您也可以 `DbProviderFactory` 使用這種方式來換行，但這樣做只會影響 ef，而不會在 `DbProviderFactory` ef 之外使用。 基於這個理由，您可能會想要繼續換行 `DbProviderFactory` 。  

您也應該記住，您在應用程式外部執行的服務-例如，從封裝管理員主控台執行遷移時。 當您從主控台執行 [遷移] 時，將會嘗試尋找您的 `DbConfiguration` 。 但是，它是否會取得包裝的服務，取決於它所註冊的事件處理常式。 如果是在的結構中註冊，則程式 `DbConfiguration` 代碼應該會執行，而且應該會包裝服務。 通常不會發生這種情況，這表示工具不會取得包裝的服務。  
