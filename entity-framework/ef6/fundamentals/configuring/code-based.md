---
title: 以程式碼為基礎的設定-EF6
description: Entity Framework 6 中以程式碼為基礎的設定
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 67bb7ebd620c90ebe80983cc5baa6cab032907f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070727"
---
# <a name="code-based-configuration"></a>以程式碼為基礎的設定
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

Entity Framework 應用程式的設定可以在設定檔中指定 ( # A0/web.config) 或透過程式碼。 後者稱為以程式碼為基礎的設定。  

設定檔中的設定會在個別的 [文章](xref:ef6/fundamentals/configuring/config-file)中說明。 設定檔優先于以程式碼為基礎的設定。 換句話說，如果在程式碼和設定檔中設定了設定選項，則會使用設定檔中的設定。  

## <a name="using-dbconfiguration"></a>使用 >dbconfiguration  

EF6 和更新版本中以程式碼為基礎的設定是藉由建立 System.Data.Entity.Config 的子類別來達成。>dbconfiguration. 子類別化 >dbconfiguration 時，應遵循下列指導方針：  

- 為您的應用程式建立一個 >dbconfiguration 類別。 這個類別會指定整個應用程式網域的設定。  
- 將您的 >dbconfiguration 類別放在與 DbCoNtext 類別相同的元件中。 如果您想要變更此 (，請參閱 *移動 >dbconfiguration* 一節。 )   
- 為您的 >dbconfiguration 類別提供公用無參數的函式。  
- 從這個函式中呼叫受保護的 >dbconfiguration 方法，以設定設定選項。  

遵循這些指導方針，可讓 EF 探索和使用您的設定，而這兩個工具都需要存取您的模型以及應用程式執行時。  

## <a name="example"></a>範例  

衍生自 >dbconfiguration 的類別看起來可能像這樣：  

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

## <a name="moving-dbconfiguration"></a>移動 >dbconfiguration  

在某些情況下，不可能將您的 >dbconfiguration 類別放在與 DbCoNtext 類別相同的元件中。 例如，您可能在不同的元件中有兩個 DbCoNtext 類別。 有兩個選項可處理此情況。  

第一個選項是使用設定檔來指定要使用的 >dbconfiguration 實例。 若要這樣做，請設定 entityFramework 區段的 codeConfigurationType 屬性。 例如：  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType 的值必須是 >dbconfiguration 類別的元件和命名空間限定名稱。  

第二個選項是將 DbConfigurationTypeAttribute 放在您的內容類別上。 例如：  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

傳遞給屬性的值可以是您的 >dbconfiguration 類型（如上所示），或元件和命名空間限定類型名稱字串。 例如：  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>明確設定 >dbconfiguration  

在某些情況下，您可能需要進行一些設定，才能使用任何 DbCoNtext 型別。 其中的範例包括：  

- 使用 DbModelBuilder 建立沒有內容的模型  
- 使用一些其他架構/公用程式程式碼，以使用在使用您的應用程式內容之前使用該內容的 DbCoNtext  

在這種情況下，EF 無法自動探索設定，您必須改為執行下列其中一項：  

- 在設定檔中設定 >dbconfiguration 類型，如上述的 *移動 >dbconfiguration* 一節所述
- 在應用程式啟動期間呼叫靜態 >dbconfiguration. SetConfiguration 方法  

## <a name="overriding-dbconfiguration"></a>覆寫 >dbconfiguration  

在某些情況下，您需要覆寫 >dbconfiguration 中的設定集。 這通常不是由應用程式開發人員完成，而是由無法使用衍生 >dbconfiguration 類別的協力廠商提供者和外掛程式所完成。  

如此一來，EntityFramework 就可以註冊事件處理常式，以便在鎖定之前修改現有的設定。  它也會提供一個方便的方法，專門用來取代 EF 服務定位器所傳回的任何服務。 這就是要使用它的方式：  

- 在應用程式啟動時 (使用 EF 之前) 外掛程式或提供者應該為此事件註冊事件處理常式方法。  (請注意，這必須在應用程式使用 EF 之前發生。 )   
- 事件處理常式會對每個需要取代的服務呼叫 ReplaceService。  

例如，若要取代 IDbConnectionFactory 和 DbProviderService，您會註冊處理常式，如下所示：  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

在上述程式碼中，MyProviderServices 和 MyConnectionFactory 代表您的服務的執行。  

您也可以新增其他相依性處理常式來獲得相同的效果。  

請注意，您也可以使用這種方式來包裝 DbProviderFactory，但這樣做只會影響 EF，而不會在 EF 之外使用 DbProviderFactory。 基於這個理由，您可能會想要像之前一樣繼續包裝 DbProviderFactory。  

您也應該記住，您在應用程式外部執行的服務-例如，從封裝管理員主控台執行遷移時。 當您從主控台執行 [遷移] 時，將會嘗試尋找您的 >dbconfiguration。 但是，它是否會取得包裝的服務，取決於它所註冊的事件處理常式。 如果它在 >dbconfiguration 的結構中註冊，則程式碼應該會執行，而且應該會包裝服務。 通常不會發生這種情況，這表示工具不會取得包裝的服務。  
