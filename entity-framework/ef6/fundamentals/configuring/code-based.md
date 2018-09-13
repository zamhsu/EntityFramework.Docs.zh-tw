---
title: 程式碼為基礎的組態-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
ms.openlocfilehash: 7c7da8992fd1b29f998e08c13d445c1d2d8cc2ce
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490814"
---
# <a name="code-based-configuration"></a><span data-ttu-id="0cf01-102">程式碼為基礎的組態</span><span class="sxs-lookup"><span data-stu-id="0cf01-102">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="0cf01-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="0cf01-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="0cf01-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="0cf01-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="0cf01-105">在組態檔 (app.config/web.config) 或透過程式碼，可以指定之 Entity Framework 應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="0cf01-105">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="0cf01-106">後者稱為程式碼為基礎的組態。</span><span class="sxs-lookup"><span data-stu-id="0cf01-106">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="0cf01-107">組態設定檔中的所述[其他文章另](config-file.md)。</span><span class="sxs-lookup"><span data-stu-id="0cf01-107">Configuration in a config file is described in a [separate article](config-file.md).</span></span> <span data-ttu-id="0cf01-108">組態檔的優先順序高於程式碼為基礎的組態。</span><span class="sxs-lookup"><span data-stu-id="0cf01-108">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="0cf01-109">換句話說，如果這兩個程式碼和組態檔設定組態選項，然後在組態檔中會使用設定。</span><span class="sxs-lookup"><span data-stu-id="0cf01-109">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="0cf01-110">使用 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="0cf01-110">Using DbConfiguration</span></span>  

<span data-ttu-id="0cf01-111">程式碼為基礎的組態在 EF6 中和更新版本之後，即可建立 System.Data.Entity.Config.DbConfiguration 的子類別。</span><span class="sxs-lookup"><span data-stu-id="0cf01-111">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="0cf01-112">DbConfiguration 子類別化時，應遵循下列指導方針：</span><span class="sxs-lookup"><span data-stu-id="0cf01-112">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="0cf01-113">建立您的應用程式只有一個 DbConfiguration 類別。</span><span class="sxs-lookup"><span data-stu-id="0cf01-113">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="0cf01-114">這個類別會指定應用程式定義域的各種設定。</span><span class="sxs-lookup"><span data-stu-id="0cf01-114">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="0cf01-115">將 DbConfiguration 類別放在與您的 DbContext 類別相同的組件中。</span><span class="sxs-lookup"><span data-stu-id="0cf01-115">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="0cf01-116">(請參閱*移動 DbConfiguration*如果您想要變更這一節。)</span><span class="sxs-lookup"><span data-stu-id="0cf01-116">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="0cf01-117">提供給 DbConfiguration 類別公用的無參數建構函式。</span><span class="sxs-lookup"><span data-stu-id="0cf01-117">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="0cf01-118">藉由呼叫從受保護的 DbConfiguration 方法，這個建構函式中設定組態選項。</span><span class="sxs-lookup"><span data-stu-id="0cf01-118">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="0cf01-119">遵循這些指導方針，可讓 EF 來探索並需要存取您的模型，並執行您的應用程式時，這兩個工具會自動使用您的組態。</span><span class="sxs-lookup"><span data-stu-id="0cf01-119">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="0cf01-120">範例</span><span class="sxs-lookup"><span data-stu-id="0cf01-120">Example</span></span>  

<span data-ttu-id="0cf01-121">類別，衍生自 DbConfiguration 可能看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="0cf01-121">A class derived from DbConfiguration might look like this:</span></span>  

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

<span data-ttu-id="0cf01-122">這個類別會設定使用 SQL Azure 執行策略-，會自動重試失敗的資料庫作業-，以及用於從 Code First 慣例所建立的資料庫中的本機資料庫的 EF。</span><span class="sxs-lookup"><span data-stu-id="0cf01-122">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="0cf01-123">移動 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="0cf01-123">Moving DbConfiguration</span></span>  

<span data-ttu-id="0cf01-124">有一些在不可能將 DbConfiguration 類別放在與您的 DbContext 類別相同的組件的情況。</span><span class="sxs-lookup"><span data-stu-id="0cf01-124">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="0cf01-125">例如，您可能會有兩個 DbContext 類別每個不同的組件中。</span><span class="sxs-lookup"><span data-stu-id="0cf01-125">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="0cf01-126">有兩個選項可處理此。</span><span class="sxs-lookup"><span data-stu-id="0cf01-126">There are two options for handling this.</span></span>  

<span data-ttu-id="0cf01-127">第一個選項是使用組態檔來指定要使用 DbConfiguration 執行個體。</span><span class="sxs-lookup"><span data-stu-id="0cf01-127">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="0cf01-128">若要這樣做，請設定 entityFramework 區段的 codeConfigurationType 屬性。</span><span class="sxs-lookup"><span data-stu-id="0cf01-128">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="0cf01-129">例如: </span><span class="sxs-lookup"><span data-stu-id="0cf01-129">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="0cf01-130">CodeConfigurationType 的值必須是組件和 DbConfiguration 類別的命名空間限定的名稱。</span><span class="sxs-lookup"><span data-stu-id="0cf01-130">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="0cf01-131">第二個選項是將 DbConfigurationTypeAttribute 放在您的內容類別。</span><span class="sxs-lookup"><span data-stu-id="0cf01-131">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="0cf01-132">例如: </span><span class="sxs-lookup"><span data-stu-id="0cf01-132">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="0cf01-133">此值傳遞至屬性可以是您 DbConfiguration 類型--如上所示，或組件和命名空間限定類型名稱字串。</span><span class="sxs-lookup"><span data-stu-id="0cf01-133">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="0cf01-134">例如: </span><span class="sxs-lookup"><span data-stu-id="0cf01-134">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="0cf01-135">明確地設定 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="0cf01-135">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="0cf01-136">有某些情況下，組態可能需要之前已使用任何 DbContext 類型。</span><span class="sxs-lookup"><span data-stu-id="0cf01-136">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="0cf01-137">範例包括：</span><span class="sxs-lookup"><span data-stu-id="0cf01-137">Examples of this include:</span></span>  

- <span data-ttu-id="0cf01-138">使用 DbModelBuilder 建置的模型沒有內容</span><span class="sxs-lookup"><span data-stu-id="0cf01-138">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="0cf01-139">使用一些其他 framework/公用程式的程式碼之前會使用您的應用程式內容會使用該內容的地方 DbContext</span><span class="sxs-lookup"><span data-stu-id="0cf01-139">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="0cf01-140">在此情況下 EF 無法自動探索設定，您必須改為執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="0cf01-140">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="0cf01-141">在組態檔中，設定 DbConfiguration 型別，如中所述*移動 DbConfiguration*上一節</span><span class="sxs-lookup"><span data-stu-id="0cf01-141">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="0cf01-142">在 應用程式啟動期間呼叫靜態 DbConfiguration.SetConfiguration 方法</span><span class="sxs-lookup"><span data-stu-id="0cf01-142">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="0cf01-143">覆寫 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="0cf01-143">Overriding DbConfiguration</span></span>  

<span data-ttu-id="0cf01-144">有一些情況下，您要覆寫在 DbConfiguration 中設定的組態。</span><span class="sxs-lookup"><span data-stu-id="0cf01-144">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="0cf01-145">不這麼做通常由應用程式開發人員，而是只要協力廠商提供者和無法使用衍生的 DbConfiguration 類別的外掛程式。</span><span class="sxs-lookup"><span data-stu-id="0cf01-145">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="0cf01-146">為此，EntityFramework 可讓它鎖定之前可以修改現有的組態的事件處理常式註冊。</span><span class="sxs-lookup"><span data-stu-id="0cf01-146">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="0cf01-147">它也提供 sugar 方法專為取代 EF 服務定位程式所傳回的任何服務。</span><span class="sxs-lookup"><span data-stu-id="0cf01-147">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="0cf01-148">這是它要使用的方式：</span><span class="sxs-lookup"><span data-stu-id="0cf01-148">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="0cf01-149">在應用程式啟動 （之前使用 EF） 外掛程式或提供者應該註冊這個事件的事件處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="0cf01-149">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="0cf01-150">（請注意，這必須進行應用程式使用 EF 之前）。</span><span class="sxs-lookup"><span data-stu-id="0cf01-150">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="0cf01-151">事件處理常式會針對每個服務，需要更換 ReplaceService 呼叫。</span><span class="sxs-lookup"><span data-stu-id="0cf01-151">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="0cf01-152">比方說，repalce IDbConnectionFactory 和 DbProviderService 您會註冊一個處理常式，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0cf01-152">For example, to repalce IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="0cf01-153">MyProviderServices 和 MyConnectionFactory 上方的程式碼代表您實作的服務。</span><span class="sxs-lookup"><span data-stu-id="0cf01-153">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="0cf01-154">您也可以新增額外的相依性的處理常式，來取得相同的效果。</span><span class="sxs-lookup"><span data-stu-id="0cf01-154">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="0cf01-155">請注意，您也可以包裝 DbProviderFactory，如此一來，但這麼做因此只會影響 EF 和不使用 EF 外部的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="0cf01-155">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="0cf01-156">因此您可能要包裝 DbProviderFactory，因為您之前會繼續。</span><span class="sxs-lookup"><span data-stu-id="0cf01-156">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="0cf01-157">您應該也請記住您執行外部以您的應用程式-例如，從套件管理員主控台中執行移轉時的服務。</span><span class="sxs-lookup"><span data-stu-id="0cf01-157">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="0cf01-158">當您執行移轉從它會嘗試尋找您 DbConfiguration 主控台。</span><span class="sxs-lookup"><span data-stu-id="0cf01-158">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="0cf01-159">不過，是否會取得已包裝的服務取決於 where 它所註冊的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="0cf01-159">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="0cf01-160">如果它註冊為您 DbConfiguration 建構的一部分，應該執行的程式碼，並應該取得包裝的服務。</span><span class="sxs-lookup"><span data-stu-id="0cf01-160">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="0cf01-161">通常這不會發生這個情況，這表示工具無法取得已包裝的服務。</span><span class="sxs-lookup"><span data-stu-id="0cf01-161">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
