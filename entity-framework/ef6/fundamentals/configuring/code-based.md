---
title: 以程式碼為基礎的設定-EF6
description: Entity Framework 6 中以程式碼為基礎的設定
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: ff86b542dead260190bbb0b0788a231f720fbeaf
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063275"
---
# <a name="code-based-configuration"></a><span data-ttu-id="9004c-103">以程式碼為基礎的設定</span><span class="sxs-lookup"><span data-stu-id="9004c-103">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="9004c-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="9004c-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9004c-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="9004c-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="9004c-106">Entity Framework 應用程式的設定可以在設定檔中指定 ( # B0/web.config) 或透過程式碼。</span><span class="sxs-lookup"><span data-stu-id="9004c-106">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="9004c-107">後者稱為以程式碼為基礎的設定。</span><span class="sxs-lookup"><span data-stu-id="9004c-107">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="9004c-108">設定檔中的設定會在個別的 [文章](xref:ef6/fundamentals/configuring/config-file)中說明。</span><span class="sxs-lookup"><span data-stu-id="9004c-108">Configuration in a config file is described in a [separate article](xref:ef6/fundamentals/configuring/config-file).</span></span> <span data-ttu-id="9004c-109">設定檔優先于以程式碼為基礎的設定。</span><span class="sxs-lookup"><span data-stu-id="9004c-109">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="9004c-110">換句話說，如果在程式碼和設定檔中設定了設定選項，則會使用設定檔中的設定。</span><span class="sxs-lookup"><span data-stu-id="9004c-110">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="9004c-111">使用 >dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="9004c-111">Using DbConfiguration</span></span>  

<span data-ttu-id="9004c-112">EF6 和更新版本中以程式碼為基礎的設定是藉由建立 System.Data.Entity.Config 的子類別來達成。>dbconfiguration.</span><span class="sxs-lookup"><span data-stu-id="9004c-112">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="9004c-113">子類別化 >dbconfiguration 時，應遵循下列指導方針：</span><span class="sxs-lookup"><span data-stu-id="9004c-113">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="9004c-114">為您的應用程式建立一個 >dbconfiguration 類別。</span><span class="sxs-lookup"><span data-stu-id="9004c-114">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="9004c-115">這個類別會指定整個應用程式網域的設定。</span><span class="sxs-lookup"><span data-stu-id="9004c-115">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="9004c-116">將您的 >dbconfiguration 類別放在與 DbCoNtext 類別相同的元件中。</span><span class="sxs-lookup"><span data-stu-id="9004c-116">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="9004c-117">如果您想要變更此 (，請參閱 *移動 >dbconfiguration* 一節。 ) </span><span class="sxs-lookup"><span data-stu-id="9004c-117">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="9004c-118">為您的 >dbconfiguration 類別提供公用無參數的函式。</span><span class="sxs-lookup"><span data-stu-id="9004c-118">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="9004c-119">從這個函式中呼叫受保護的 >dbconfiguration 方法，以設定設定選項。</span><span class="sxs-lookup"><span data-stu-id="9004c-119">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="9004c-120">遵循這些指導方針，可讓 EF 探索和使用您的設定，而這兩個工具都需要存取您的模型以及應用程式執行時。</span><span class="sxs-lookup"><span data-stu-id="9004c-120">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="9004c-121">範例</span><span class="sxs-lookup"><span data-stu-id="9004c-121">Example</span></span>  

<span data-ttu-id="9004c-122">衍生自 >dbconfiguration 的類別看起來可能像這樣：</span><span class="sxs-lookup"><span data-stu-id="9004c-122">A class derived from DbConfiguration might look like this:</span></span>  

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

<span data-ttu-id="9004c-123">此類別會將 EF 設定為使用 SQL Azure 執行策略-自動重試失敗的資料庫作業，並針對依慣例從 Code First 建立的資料庫使用本機 DB。</span><span class="sxs-lookup"><span data-stu-id="9004c-123">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="9004c-124">移動 >dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="9004c-124">Moving DbConfiguration</span></span>  

<span data-ttu-id="9004c-125">在某些情況下，不可能將您的 >dbconfiguration 類別放在與 DbCoNtext 類別相同的元件中。</span><span class="sxs-lookup"><span data-stu-id="9004c-125">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="9004c-126">例如，您可能在不同的元件中有兩個 DbCoNtext 類別。</span><span class="sxs-lookup"><span data-stu-id="9004c-126">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="9004c-127">有兩個選項可處理此情況。</span><span class="sxs-lookup"><span data-stu-id="9004c-127">There are two options for handling this.</span></span>  

<span data-ttu-id="9004c-128">第一個選項是使用設定檔來指定要使用的 >dbconfiguration 實例。</span><span class="sxs-lookup"><span data-stu-id="9004c-128">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="9004c-129">若要這樣做，請設定 entityFramework 區段的 codeConfigurationType 屬性。</span><span class="sxs-lookup"><span data-stu-id="9004c-129">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="9004c-130">例如︰</span><span class="sxs-lookup"><span data-stu-id="9004c-130">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="9004c-131">CodeConfigurationType 的值必須是 >dbconfiguration 類別的元件和命名空間限定名稱。</span><span class="sxs-lookup"><span data-stu-id="9004c-131">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="9004c-132">第二個選項是將 DbConfigurationTypeAttribute 放在您的內容類別上。</span><span class="sxs-lookup"><span data-stu-id="9004c-132">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="9004c-133">例如︰</span><span class="sxs-lookup"><span data-stu-id="9004c-133">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="9004c-134">傳遞給屬性的值可以是您的 >dbconfiguration 類型（如上所示），或元件和命名空間限定類型名稱字串。</span><span class="sxs-lookup"><span data-stu-id="9004c-134">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="9004c-135">例如︰</span><span class="sxs-lookup"><span data-stu-id="9004c-135">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="9004c-136">明確設定 >dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="9004c-136">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="9004c-137">在某些情況下，您可能需要進行一些設定，才能使用任何 DbCoNtext 型別。</span><span class="sxs-lookup"><span data-stu-id="9004c-137">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="9004c-138">其中的範例包括：</span><span class="sxs-lookup"><span data-stu-id="9004c-138">Examples of this include:</span></span>  

- <span data-ttu-id="9004c-139">使用 DbModelBuilder 建立沒有內容的模型</span><span class="sxs-lookup"><span data-stu-id="9004c-139">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="9004c-140">使用一些其他架構/公用程式程式碼，以使用在使用您的應用程式內容之前使用該內容的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="9004c-140">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="9004c-141">在這種情況下，EF 無法自動探索設定，您必須改為執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="9004c-141">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="9004c-142">在設定檔中設定 >dbconfiguration 類型，如上述的 *移動 >dbconfiguration* 一節所述</span><span class="sxs-lookup"><span data-stu-id="9004c-142">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="9004c-143">在應用程式啟動期間呼叫靜態 >dbconfiguration. SetConfiguration 方法</span><span class="sxs-lookup"><span data-stu-id="9004c-143">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="9004c-144">覆寫 >dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="9004c-144">Overriding DbConfiguration</span></span>  

<span data-ttu-id="9004c-145">在某些情況下，您需要覆寫 >dbconfiguration 中的設定集。</span><span class="sxs-lookup"><span data-stu-id="9004c-145">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="9004c-146">這通常不是由應用程式開發人員完成，而是由無法使用衍生 >dbconfiguration 類別的協力廠商提供者和外掛程式所完成。</span><span class="sxs-lookup"><span data-stu-id="9004c-146">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="9004c-147">如此一來，EntityFramework 就可以註冊事件處理常式，以便在鎖定之前修改現有的設定。</span><span class="sxs-lookup"><span data-stu-id="9004c-147">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="9004c-148">它也會提供一個方便的方法，專門用來取代 EF 服務定位器所傳回的任何服務。</span><span class="sxs-lookup"><span data-stu-id="9004c-148">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="9004c-149">這就是要使用它的方式：</span><span class="sxs-lookup"><span data-stu-id="9004c-149">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="9004c-150">在應用程式啟動時 (使用 EF 之前) 外掛程式或提供者應該為此事件註冊事件處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="9004c-150">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="9004c-151"> (請注意，這必須在應用程式使用 EF 之前發生。 ) </span><span class="sxs-lookup"><span data-stu-id="9004c-151">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="9004c-152">事件處理常式會對每個需要取代的服務呼叫 ReplaceService。</span><span class="sxs-lookup"><span data-stu-id="9004c-152">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="9004c-153">例如，若要取代 IDbConnectionFactory 和 DbProviderService，您會註冊處理常式，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9004c-153">For example, to replace IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="9004c-154">在上述程式碼中，MyProviderServices 和 MyConnectionFactory 代表您的服務的執行。</span><span class="sxs-lookup"><span data-stu-id="9004c-154">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="9004c-155">您也可以新增其他相依性處理常式來獲得相同的效果。</span><span class="sxs-lookup"><span data-stu-id="9004c-155">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="9004c-156">請注意，您也可以使用這種方式來包裝 DbProviderFactory，但這樣做只會影響 EF，而不會在 EF 之外使用 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="9004c-156">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="9004c-157">基於這個理由，您可能會想要像之前一樣繼續包裝 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="9004c-157">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="9004c-158">您也應該記住，您在應用程式外部執行的服務-例如，從封裝管理員主控台執行遷移時。</span><span class="sxs-lookup"><span data-stu-id="9004c-158">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="9004c-159">當您從主控台執行 [遷移] 時，將會嘗試尋找您的 >dbconfiguration。</span><span class="sxs-lookup"><span data-stu-id="9004c-159">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="9004c-160">但是，它是否會取得包裝的服務，取決於它所註冊的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="9004c-160">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="9004c-161">如果它在 >dbconfiguration 的結構中註冊，則程式碼應該會執行，而且應該會包裝服務。</span><span class="sxs-lookup"><span data-stu-id="9004c-161">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="9004c-162">通常不會發生這種情況，這表示工具不會取得包裝的服務。</span><span class="sxs-lookup"><span data-stu-id="9004c-162">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
