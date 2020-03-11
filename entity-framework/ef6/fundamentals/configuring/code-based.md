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
# <a name="code-based-configuration"></a><span data-ttu-id="29ea0-102">以程式碼為基礎的設定</span><span class="sxs-lookup"><span data-stu-id="29ea0-102">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="29ea0-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="29ea0-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="29ea0-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="29ea0-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="29ea0-105">您可以在設定檔（app.config/web.config）中或透過程式碼來指定 Entity Framework 應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="29ea0-105">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="29ea0-106">後者又稱為以程式碼為基礎的設定。</span><span class="sxs-lookup"><span data-stu-id="29ea0-106">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="29ea0-107">設定檔中的設定會在個別的[文章](config-file.md)中加以說明。</span><span class="sxs-lookup"><span data-stu-id="29ea0-107">Configuration in a config file is described in a [separate article](config-file.md).</span></span> <span data-ttu-id="29ea0-108">設定檔的優先順序高於以程式碼為基礎的設定。</span><span class="sxs-lookup"><span data-stu-id="29ea0-108">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="29ea0-109">換句話說，如果同時在程式碼和設定檔中設定了設定選項，就會使用設定檔中的設定。</span><span class="sxs-lookup"><span data-stu-id="29ea0-109">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="29ea0-110">使用 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="29ea0-110">Using DbConfiguration</span></span>  

<span data-ttu-id="29ea0-111">EF6 和更新版本中的程式碼型設定，是藉由建立 DbConfiguration 的子類別來達成。</span><span class="sxs-lookup"><span data-stu-id="29ea0-111">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="29ea0-112">子類別化 DbConfiguration 時，應遵循下列指導方針：</span><span class="sxs-lookup"><span data-stu-id="29ea0-112">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="29ea0-113">為您的應用程式只建立一個 DbConfiguration 類別。</span><span class="sxs-lookup"><span data-stu-id="29ea0-113">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="29ea0-114">此類別會指定應用程式網域的範圍設定。</span><span class="sxs-lookup"><span data-stu-id="29ea0-114">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="29ea0-115">將您的 DbConfiguration 類別放在與 DbCoNtext 類別相同的元件中。</span><span class="sxs-lookup"><span data-stu-id="29ea0-115">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="29ea0-116">（如果您想要變更此，請參閱*移動 DbConfiguration*一節）。</span><span class="sxs-lookup"><span data-stu-id="29ea0-116">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="29ea0-117">為您的 DbConfiguration 類別提供公用無參數的函式。</span><span class="sxs-lookup"><span data-stu-id="29ea0-117">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="29ea0-118">從這個函式內呼叫受保護的 DbConfiguration 方法，以設定設定選項。</span><span class="sxs-lookup"><span data-stu-id="29ea0-118">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="29ea0-119">遵循這些指導方針讓 EF 能夠自動探索和使用您的設定，這兩個工具都需要存取您的模型，以及當您的應用程式執行時。</span><span class="sxs-lookup"><span data-stu-id="29ea0-119">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="29ea0-120">範例</span><span class="sxs-lookup"><span data-stu-id="29ea0-120">Example</span></span>  

<span data-ttu-id="29ea0-121">衍生自 DbConfiguration 的類別可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="29ea0-121">A class derived from DbConfiguration might look like this:</span></span>  

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

<span data-ttu-id="29ea0-122">此類別會將 EF 設定為使用 SQL Azure 執行策略，以自動重試失敗的資料庫作業，並針對慣例從 Code First 所建立的資料庫使用 Local DB。</span><span class="sxs-lookup"><span data-stu-id="29ea0-122">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="29ea0-123">移動 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="29ea0-123">Moving DbConfiguration</span></span>  

<span data-ttu-id="29ea0-124">在某些情況下，無法將您的 DbConfiguration 類別放在與 DbCoNtext 類別相同的元件中。</span><span class="sxs-lookup"><span data-stu-id="29ea0-124">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="29ea0-125">例如，您可能會在不同的元件中有兩個 DbCoNtext 類別。</span><span class="sxs-lookup"><span data-stu-id="29ea0-125">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="29ea0-126">有兩個選項可處理此情況。</span><span class="sxs-lookup"><span data-stu-id="29ea0-126">There are two options for handling this.</span></span>  

<span data-ttu-id="29ea0-127">第一個選項是使用設定檔來指定要使用的 DbConfiguration 實例。</span><span class="sxs-lookup"><span data-stu-id="29ea0-127">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="29ea0-128">若要這麼做，請設定 entityFramework 區段的 codeConfigurationType 屬性。</span><span class="sxs-lookup"><span data-stu-id="29ea0-128">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="29ea0-129">例如：</span><span class="sxs-lookup"><span data-stu-id="29ea0-129">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="29ea0-130">CodeConfigurationType 的值必須是 DbConfiguration 類別的元件和命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="29ea0-130">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="29ea0-131">第二個選項是將 DbConfigurationTypeAttribute 放在您的內容類別上。</span><span class="sxs-lookup"><span data-stu-id="29ea0-131">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="29ea0-132">例如：</span><span class="sxs-lookup"><span data-stu-id="29ea0-132">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="29ea0-133">傳遞給屬性的值可以是您的 DbConfiguration 類型（如上所示），或是元件和命名空間限定的類型名稱字串。</span><span class="sxs-lookup"><span data-stu-id="29ea0-133">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="29ea0-134">例如：</span><span class="sxs-lookup"><span data-stu-id="29ea0-134">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="29ea0-135">明確設定 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="29ea0-135">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="29ea0-136">在某些情況下，您可能需要在使用任何 DbCoNtext 類型之前進行設定。</span><span class="sxs-lookup"><span data-stu-id="29ea0-136">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="29ea0-137">其中的範例包括：</span><span class="sxs-lookup"><span data-stu-id="29ea0-137">Examples of this include:</span></span>  

- <span data-ttu-id="29ea0-138">使用 DbModelBuilder 來建立不含內容的模型</span><span class="sxs-lookup"><span data-stu-id="29ea0-138">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="29ea0-139">使用在使用應用程式內容之前使用該內容之 DbCoNtext 的其他架構/公用程式代碼</span><span class="sxs-lookup"><span data-stu-id="29ea0-139">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="29ea0-140">在這種情況下，EF 無法自動探索設定，而您必須執行下列其中一項動作：</span><span class="sxs-lookup"><span data-stu-id="29ea0-140">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="29ea0-141">在設定檔中設定 DbConfiguration 類型，如上面的*移動 DbConfiguration*一節中所述</span><span class="sxs-lookup"><span data-stu-id="29ea0-141">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="29ea0-142">在應用程式啟動期間呼叫靜態 DbConfiguration SetConfiguration 方法</span><span class="sxs-lookup"><span data-stu-id="29ea0-142">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="29ea0-143">覆寫 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="29ea0-143">Overriding DbConfiguration</span></span>  

<span data-ttu-id="29ea0-144">在某些情況下，您必須覆寫 DbConfiguration 中的設定集。</span><span class="sxs-lookup"><span data-stu-id="29ea0-144">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="29ea0-145">這通常不是由應用程式開發人員執行，而是由協力廠商提供者和無法使用衍生 DbConfiguration 類別的外掛程式來完成。</span><span class="sxs-lookup"><span data-stu-id="29ea0-145">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="29ea0-146">在此情況下，EntityFramework 可讓事件處理常式註冊，以便在鎖定之前立即修改現有的設定。</span><span class="sxs-lookup"><span data-stu-id="29ea0-146">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="29ea0-147">它也會提供一個方便的方法，用來取代 EF 服務定位器所傳回的任何服務。</span><span class="sxs-lookup"><span data-stu-id="29ea0-147">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="29ea0-148">這就是使用它的目的：</span><span class="sxs-lookup"><span data-stu-id="29ea0-148">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="29ea0-149">在應用程式啟動時（使用 EF 之前）外掛程式或提供者應該註冊此事件的事件處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="29ea0-149">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="29ea0-150">（請注意，這必須在應用程式使用 EF 之前發生）。</span><span class="sxs-lookup"><span data-stu-id="29ea0-150">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="29ea0-151">事件處理常式會對每個需要取代的服務呼叫 ReplaceService。</span><span class="sxs-lookup"><span data-stu-id="29ea0-151">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="29ea0-152">例如，若要取代 IDbConnectionFactory 和 DbProviderService，您可以註冊處理常式，如下所示：</span><span class="sxs-lookup"><span data-stu-id="29ea0-152">For example, to replace IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="29ea0-153">在上述程式碼中，MyProviderServices 和 MyConnectionFactory 代表您的服務的執行。</span><span class="sxs-lookup"><span data-stu-id="29ea0-153">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="29ea0-154">您也可以加入其他相依性處理常式，以取得相同的效果。</span><span class="sxs-lookup"><span data-stu-id="29ea0-154">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="29ea0-155">請注意，您也可以用這種方式包裝 DbProviderFactory，但是這麼做只會影響 EF，而不會對 EF 以外的 DbProviderFactory 使用。</span><span class="sxs-lookup"><span data-stu-id="29ea0-155">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="29ea0-156">基於這個理由，您可能會想要像之前一樣繼續包裝 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="29ea0-156">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="29ea0-157">您也應該記住您在應用程式外部執行的服務，例如，從 [套件管理員主控台] 執行遷移時。</span><span class="sxs-lookup"><span data-stu-id="29ea0-157">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="29ea0-158">當您從主控台執行 [遷移] 時，將會嘗試尋找您的 DbConfiguration。</span><span class="sxs-lookup"><span data-stu-id="29ea0-158">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="29ea0-159">不過，它是否會取得包裝的服務取決於它所註冊的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="29ea0-159">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="29ea0-160">如果它是在 DbConfiguration 的結構中註冊，則程式碼應該會執行，而服務應該會被包裝。</span><span class="sxs-lookup"><span data-stu-id="29ea0-160">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="29ea0-161">通常這不會是這種情況，這表示工具不會取得包裝的服務。</span><span class="sxs-lookup"><span data-stu-id="29ea0-161">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
