---
title: 設定檔設定-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
ms.openlocfilehash: 86389e4a3a3bac46e2a4cf2da648a4b19e29f3c3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417970"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="4c0ef-102">設定檔設定</span><span class="sxs-lookup"><span data-stu-id="4c0ef-102">Configuration File Settings</span></span>
<span data-ttu-id="4c0ef-103">Entity Framework 允許從設定檔指定數個設定。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-103">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="4c0ef-104">一般 EF 會遵循「設定慣例」原則：這篇文章中討論的所有設定都有預設行為，您只需要在預設不再滿足您的需求時，擔心變更設定。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-104">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="4c0ef-105">以程式碼為基礎的替代方案</span><span class="sxs-lookup"><span data-stu-id="4c0ef-105">A Code-Based Alternative</span></span>  

<span data-ttu-id="4c0ef-106">所有這些設定也可以使用程式碼來套用。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-106">All of these settings can also be applied using code.</span></span> <span data-ttu-id="4c0ef-107">從 EF6 開始，我們引進了以[程式碼為基礎](code-based.md)的設定，它提供從程式碼套用設定的集中方式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-107">Starting in EF6 we introduced [code-based configuration](code-based.md), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="4c0ef-108">在 EF6 之前，設定仍然可以從程式碼套用，但您必須使用各種 Api 來設定不同的區域。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-108">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="4c0ef-109">[設定檔] 選項可讓您在部署期間輕鬆地變更這些設定，而不需要更新您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-109">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="4c0ef-110">Entity Framework 設定區段</span><span class="sxs-lookup"><span data-stu-id="4c0ef-110">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="4c0ef-111">從 EF 4.1 開始，您可以使用設定檔的**appSettings**區段來設定內容的資料庫初始化運算式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-111">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="4c0ef-112">在 EF 4.3 中，我們引進了自訂**entityFramework**區段來處理新的設定。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-112">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="4c0ef-113">Entity Framework 仍然會辨識使用舊格式設定的資料庫初始化運算式，但建議您盡可能移至新格式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-113">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="4c0ef-114">當您安裝 EntityFramework NuGet 套件時， **entityFramework**區段會自動新增至您專案的設定檔。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-114">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a><span data-ttu-id="4c0ef-115">連接字串</span><span class="sxs-lookup"><span data-stu-id="4c0ef-115">Connection Strings</span></span>  

<span data-ttu-id="4c0ef-116">[此頁面](~/ef6/fundamentals/configuring/connection-strings.md)提供有關 Entity Framework 如何判斷要使用之資料庫的詳細資訊，包括設定檔案中的連接字串。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-116">[This page](~/ef6/fundamentals/configuring/connection-strings.md) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="4c0ef-117">連接字串會在標準**connectionStrings**元素中，而且不需要**entityFramework**區段。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-117">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="4c0ef-118">以 Code First 為基礎的模型會使用標準的 ADO.NET 連接字串。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-118">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="4c0ef-119">例如：</span><span class="sxs-lookup"><span data-stu-id="4c0ef-119">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="4c0ef-120">EF 設計工具型模型會使用特殊的 EF 連接字串。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-120">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="4c0ef-121">例如：</span><span class="sxs-lookup"><span data-stu-id="4c0ef-121">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="4c0ef-122">以程式碼為基礎的設定類型（EF6 和更新版本）</span><span class="sxs-lookup"><span data-stu-id="4c0ef-122">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="4c0ef-123">從 EF6 開始，您可以指定要用於 EF 的 DbConfiguration，以便在應用程式中進行以程式[代碼為基礎](code-based.md)的設定。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-123">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](code-based.md) in your application.</span></span> <span data-ttu-id="4c0ef-124">在大部分情況下，您不需要指定此設定，因為 EF 會自動探索您的 DbConfiguration。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-124">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="4c0ef-125">如需何時可能需要在設定檔中指定 DbConfiguration 的詳細資訊，請參閱程式[代碼型](code-based.md)設定的**移動 DbConfiguration**一節。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-125">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](code-based.md).</span></span>  

<span data-ttu-id="4c0ef-126">若要設定 DbConfiguration 類型，請在**codeConfigurationType**元素中指定元件限定類型名稱。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-126">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="4c0ef-127">元件限定名稱是命名空間限定名稱，後面接著逗號，再接著是該類型所在的元件。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-127">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="4c0ef-128">您也可以選擇性地指定元件版本、文化特性和公開金鑰標記。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-128">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="4c0ef-129">EF 資料庫提供者（EF6）</span><span class="sxs-lookup"><span data-stu-id="4c0ef-129">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="4c0ef-130">在 EF6 之前，必須將資料庫提供者的 Entity Framework 特定部分納入核心 ADO.NET 提供者中。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-130">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="4c0ef-131">從 EF6 開始，現在會分別管理和註冊 EF 特定元件。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-131">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="4c0ef-132">通常您不需要自行註冊提供者。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-132">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="4c0ef-133">當您安裝時，提供者通常會完成這項作業。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-133">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="4c0ef-134">提供者的註冊方式是在**entityFramework** **區段的 provider 子區段**底下包含**provider**元素。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-134">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="4c0ef-135">提供者專案有兩個必要屬性：</span><span class="sxs-lookup"><span data-stu-id="4c0ef-135">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="4c0ef-136">**invariantName**識別此 EF 提供者的目標核心 ADO.NET 提供者</span><span class="sxs-lookup"><span data-stu-id="4c0ef-136">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="4c0ef-137">**類型**是 EF 提供者執行的元件限定類型名稱</span><span class="sxs-lookup"><span data-stu-id="4c0ef-137">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="4c0ef-138">元件限定名稱是命名空間限定名稱，後面接著逗號，再接著是該類型所在的元件。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-138">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="4c0ef-139">您也可以選擇性地指定元件版本、文化特性和公開金鑰標記。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-139">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="4c0ef-140">舉例來說，以下是當您安裝 Entity Framework 時，為了註冊預設 SQL Server 提供者所建立的專案。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-140">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="4c0ef-141">攔截器（EF 6.1 和更新版本）</span><span class="sxs-lookup"><span data-stu-id="4c0ef-141">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="4c0ef-142">從 EF 6.1 開始，您可以在設定檔中註冊攔截器。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-142">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="4c0ef-143">攔截器可讓您在 EF 執行特定作業（例如執行資料庫查詢、開啟連接等）時執行其他邏輯。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-143">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="4c0ef-144">攔截器的註冊方式是在**entityFramework**區段的**攔截**器子區段底下包含**攔截**器元素。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-144">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="4c0ef-145">例如，下列設定會註冊將所有資料庫作業記錄到主控台的內建**DatabaseLogger**攔截器。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-145">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="4c0ef-146">將資料庫作業記錄到檔案（EF 6.1 和更新版本）</span><span class="sxs-lookup"><span data-stu-id="4c0ef-146">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="4c0ef-147">當您想要將記錄新增至現有的應用程式以協助偵測問題時，透過設定檔註冊攔截器會特別有用。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-147">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="4c0ef-148">**DatabaseLogger**藉由提供檔案名做為「函式」參數，支援記錄至檔案。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-148">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="4c0ef-149">根據預設，這會在每次應用程式啟動時，以新的檔案覆寫記錄檔。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-149">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="4c0ef-150">若要改為附加至記錄檔（如果已經存在），請使用類似下列的內容：</span><span class="sxs-lookup"><span data-stu-id="4c0ef-150">To instead append to the log file if it already exists use something like:</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="4c0ef-151">如需**DatabaseLogger**和註冊攔截器的詳細資訊，請參閱[EF 6.1：開啟記錄而不重新編譯](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-151">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="4c0ef-152">Code First 預設連接 Factory</span><span class="sxs-lookup"><span data-stu-id="4c0ef-152">Code First Default Connection Factory</span></span>  

<span data-ttu-id="4c0ef-153">[設定] 區段可讓您指定預設的連接 factory，Code First 應該用來尋找要用於內容的資料庫。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-153">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="4c0ef-154">只有在未將連接字串新增至內容的設定檔時，才會使用預設的連接處理站。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-154">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="4c0ef-155">當您安裝 EF NuGet 套件時，會根據您已安裝的預設連線處理站，註冊指向 SQL Express 或 LocalDB。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-155">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="4c0ef-156">若要設定連接處理站，您可以在**defaultConnectionFactory**元素中指定元件限定類型名稱。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-156">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="4c0ef-157">元件限定名稱是命名空間限定名稱，後面接著逗號，再接著是該類型所在的元件。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-157">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="4c0ef-158">您也可以選擇性地指定元件版本、文化特性和公開金鑰標記。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-158">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="4c0ef-159">以下是設定您自己的預設連線 factory 的範例：</span><span class="sxs-lookup"><span data-stu-id="4c0ef-159">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="4c0ef-160">上述範例要求自訂 factory 具有無參數的函式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-160">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="4c0ef-161">如有需要，您可以使用**parameters**元素指定函數參數。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-161">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="4c0ef-162">例如，包含在 Entity Framework 中的 SqlCeConnectionFactory，需要您將提供者不變名稱提供給此函式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-162">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="4c0ef-163">提供者不變名稱會識別您想要使用的 SQL Compact 版本。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-163">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="4c0ef-164">下列設定會導致內容預設使用 SQL Compact 4.0 版。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-164">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="4c0ef-165">如果您未設定預設的連接 factory，Code First 會使用 SqlConnectionFactory，指向 `.\SQLEXPRESS`。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-165">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="4c0ef-166">SqlConnectionFactory 也有一個可讓您覆寫連接字串部分的函式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-166">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="4c0ef-167">如果您想要使用 `.\SQLEXPRESS` 以外的 SQL Server 實例，您可以使用這個函數來設定伺服器。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-167">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="4c0ef-168">下列設定會導致 Code First 針對未設定明確連接字串的內容使用**MyDatabaseServer** 。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-168">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="4c0ef-169">根據預設，它會假設函式引數的類型為 string。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-169">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="4c0ef-170">您可以使用 type 屬性來變更這個。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-170">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="4c0ef-171">資料庫初始化運算式</span><span class="sxs-lookup"><span data-stu-id="4c0ef-171">Database Initializers</span></span>  

<span data-ttu-id="4c0ef-172">資料庫初始化運算式是根據每個內容來設定。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-172">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="4c0ef-173">您可以使用**coNtext**元素，在設定檔中設定這些專案。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-173">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="4c0ef-174">這個元素會使用元件限定名稱來識別要設定的內容。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-174">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="4c0ef-175">根據預設，Code First 內容會設定為使用 CreateDatabaseIfNotExists 初始化運算式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-175">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="4c0ef-176">**CoNtext**元素上有一個**disableDatabaseInitialization**屬性可用來停用資料庫初始化。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-176">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="4c0ef-177">例如，下列設定會針對 MyAssembly 中定義的 BlogCoNtext 內容停用資料庫初始化。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-177">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="4c0ef-178">您可以使用**databaseInitializer**元素來設定自訂的初始化運算式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-178">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="4c0ef-179">函式參數使用與預設連接處理站相同的語法。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-179">Constructor parameters use the same syntax as default connection factories.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

<span data-ttu-id="4c0ef-180">您可以設定其中一個包含在 Entity Framework 中的泛型資料庫初始化運算式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-180">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="4c0ef-181">**Type**屬性會使用泛型型別的 .NET Framework 格式。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-181">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="4c0ef-182">例如，如果您使用 Code First 移轉，您可以使用 `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` 初始化運算式，將資料庫設定為自動遷移。</span><span class="sxs-lookup"><span data-stu-id="4c0ef-182">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
