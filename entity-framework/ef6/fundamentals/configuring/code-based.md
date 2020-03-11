---
title: 以程式碼為基礎的設定-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
ms.openlocfilehash: 079a4ab30af74eac8b1f51ece5801ff40a867a29
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417866"
---
# <a name="code-based-configuration"></a>以程式碼為基礎的設定
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

您可以在設定檔（app.config/web.config）中或透過程式碼來指定 Entity Framework 應用程式的設定。 後者又稱為以程式碼為基礎的設定。  

設定檔中的設定會在個別的[文章](config-file.md)中加以說明。 設定檔的優先順序高於以程式碼為基礎的設定。 換句話說，如果同時在程式碼和設定檔中設定了設定選項，就會使用設定檔中的設定。  

## <a name="using-dbconfiguration"></a>使用 DbConfiguration  

EF6 和更新版本中的程式碼型設定，是藉由建立 DbConfiguration 的子類別來達成。 子類別化 DbConfiguration 時，應遵循下列指導方針：  

- 為您的應用程式只建立一個 DbConfiguration 類別。 此類別會指定應用程式網域的範圍設定。  
- 將您的 DbConfiguration 類別放在與 DbCoNtext 類別相同的元件中。 （如果您想要變更此，請參閱*移動 DbConfiguration*一節）。  
- 為您的 DbConfiguration 類別提供公用無參數的函式。  
- 從這個函式內呼叫受保護的 DbConfiguration 方法，以設定設定選項。  

遵循這些指導方針讓 EF 能夠自動探索和使用您的設定，這兩個工具都需要存取您的模型，以及當您的應用程式執行時。  

## <a name="example"></a>範例  

衍生自 DbConfiguration 的類別可能如下所示：  

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

此類別會將 EF 設定為使用 SQL Azure 執行策略，以自動重試失敗的資料庫作業，並針對慣例從 Code First 所建立的資料庫使用 Local DB。  

## <a name="moving-dbconfiguration"></a>移動 DbConfiguration  

在某些情況下，無法將您的 DbConfiguration 類別放在與 DbCoNtext 類別相同的元件中。 例如，您可能會在不同的元件中有兩個 DbCoNtext 類別。 有兩個選項可處理此情況。  

第一個選項是使用設定檔來指定要使用的 DbConfiguration 實例。 若要這麼做，請設定 entityFramework 區段的 codeConfigurationType 屬性。 例如：  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType 的值必須是 DbConfiguration 類別的元件和命名空間限定名稱。  

第二個選項是將 DbConfigurationTypeAttribute 放在您的內容類別上。 例如：  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

傳遞給屬性的值可以是您的 DbConfiguration 類型（如上所示），或是元件和命名空間限定的類型名稱字串。 例如：  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>明確設定 DbConfiguration  

在某些情況下，您可能需要在使用任何 DbCoNtext 類型之前進行設定。 其中的範例包括：  

- 使用 DbModelBuilder 來建立不含內容的模型  
- 使用在使用應用程式內容之前使用該內容之 DbCoNtext 的其他架構/公用程式代碼  

在這種情況下，EF 無法自動探索設定，而您必須執行下列其中一項動作：  

- 在設定檔中設定 DbConfiguration 類型，如上面的*移動 DbConfiguration*一節中所述
- 在應用程式啟動期間呼叫靜態 DbConfiguration SetConfiguration 方法  

## <a name="overriding-dbconfiguration"></a>覆寫 DbConfiguration  

在某些情況下，您必須覆寫 DbConfiguration 中的設定集。 這通常不是由應用程式開發人員執行，而是由協力廠商提供者和無法使用衍生 DbConfiguration 類別的外掛程式來完成。  

在此情況下，EntityFramework 可讓事件處理常式註冊，以便在鎖定之前立即修改現有的設定。  它也會提供一個方便的方法，用來取代 EF 服務定位器所傳回的任何服務。 這就是使用它的目的：  

- 在應用程式啟動時（使用 EF 之前）外掛程式或提供者應該註冊此事件的事件處理常式方法。 （請注意，這必須在應用程式使用 EF 之前發生）。  
- 事件處理常式會對每個需要取代的服務呼叫 ReplaceService。  

例如，若要取代 IDbConnectionFactory 和 DbProviderService，您可以註冊處理常式，如下所示：  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

在上述程式碼中，MyProviderServices 和 MyConnectionFactory 代表您的服務的執行。  

您也可以加入其他相依性處理常式，以取得相同的效果。  

請注意，您也可以用這種方式包裝 DbProviderFactory，但是這麼做只會影響 EF，而不會對 EF 以外的 DbProviderFactory 使用。 基於這個理由，您可能會想要像之前一樣繼續包裝 DbProviderFactory。  

您也應該記住您在應用程式外部執行的服務，例如，從 [套件管理員主控台] 執行遷移時。 當您從主控台執行 [遷移] 時，將會嘗試尋找您的 DbConfiguration。 不過，它是否會取得包裝的服務取決於它所註冊的事件處理常式。 如果它是在 DbConfiguration 的結構中註冊，則程式碼應該會執行，而服務應該會被包裝。 通常這不會是這種情況，這表示工具不會取得包裝的服務。  
