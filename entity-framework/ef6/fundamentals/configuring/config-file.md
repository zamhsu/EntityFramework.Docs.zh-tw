---
title: 組態檔設定 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
ms.openlocfilehash: 949ad4f030205753c5fbf9b95f4d183d8c0d1fe7
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490868"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="e6d5a-102">組態檔設定</span><span class="sxs-lookup"><span data-stu-id="e6d5a-102">Configuration File Settings</span></span>
<span data-ttu-id="e6d5a-103">Entity Framework 可讓多個組態檔中指定的設定。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-103">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="e6d5a-104">EF 遵循 'convention over configuration' 原則的一般情況下： 這篇文章中討論的所有設定的預設行為，您只需要擔心如何變更設定，當預設值不再符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-104">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="e6d5a-105">程式碼為基礎的替代方案</span><span class="sxs-lookup"><span data-stu-id="e6d5a-105">A Code-Based Alternative</span></span>  

<span data-ttu-id="e6d5a-106">所有這些設定也可以套用使用程式碼。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-106">All of these settings can also be applied using code.</span></span> <span data-ttu-id="e6d5a-107">從開始，我們介紹了的 EF6[程式碼為基礎的組態](code-based.md)，以提供集中的方式套用組態從程式碼。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-107">Starting in EF6 we introduced [code-based configuration](code-based.md), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="e6d5a-108">EF6，仍然可以從程式碼套用設定但您需要使用各種 Api 來設定不同的區域。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-108">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="e6d5a-109">組態的 [檔案] 選項可讓這些設定，以輕鬆地在部署期間變更，而不需要更新您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-109">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="e6d5a-110">Entity Framework 的組態區段</span><span class="sxs-lookup"><span data-stu-id="e6d5a-110">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="e6d5a-111">從 EF4.1 您可以設定內容中使用的資料庫初始設定式**appSettings**組態檔區段。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-111">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="e6d5a-112">在 EF 4.3 我們引進了自訂**entityFramework**一節，以處理新的設定。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-112">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="e6d5a-113">Entity Framework 仍會辨識資料庫初始設定式將使用舊的格式，但建議您盡可能移動至新的格式。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-113">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="e6d5a-114">**EntityFramework**安裝 EntityFramework NuGet 套件時自動區段新增至您的專案的組態檔。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-114">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

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

## <a name="connection-strings"></a><span data-ttu-id="e6d5a-115">連接字串</span><span class="sxs-lookup"><span data-stu-id="e6d5a-115">Connection Strings</span></span>  

<span data-ttu-id="e6d5a-116">[此頁面](~/ef6/fundamentals/configuring/connection-strings.md)提供在 Entity Framework 如何判斷要使用的資料庫更多詳細資料，包括組態檔中的連接字串。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-116">[This page](~/ef6/fundamentals/configuring/connection-strings.md) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="e6d5a-117">在標準中的連接字串移**connectionStrings**項目，而且不需要**entityFramework**一節。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-117">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="e6d5a-118">第一次型的程式碼模型會使用一般的 ADO.NET 連接字串。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-118">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="e6d5a-119">例如: </span><span class="sxs-lookup"><span data-stu-id="e6d5a-119">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="e6d5a-120">EF 設計工具基礎模型使用特殊的 EF 連接字串。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-120">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="e6d5a-121">例如: </span><span class="sxs-lookup"><span data-stu-id="e6d5a-121">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="e6d5a-122">程式碼為基礎的組態類型 (EF6 之後版本)</span><span class="sxs-lookup"><span data-stu-id="e6d5a-122">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="e6d5a-123">從 EF6 開始，您可以指定要用於 ef DbConfiguration[程式碼為基礎的組態](code-based.md)應用程式中。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-123">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](code-based.md) in your application.</span></span> <span data-ttu-id="e6d5a-124">在大部分情況下，您不必指定此設定，如 EF 會自動探索您 DbConfiguration。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-124">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="e6d5a-125">如您可能需要在組態檔中指定 DbConfiguration 的詳細資訊，請參閱**移動 DbConfiguration**一節[程式碼為基礎的組態](code-based.md)。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-125">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](code-based.md).</span></span>  

<span data-ttu-id="e6d5a-126">若要設定 DbConfiguration 類型，您可以指定中的組件限定的類型名稱**codeConfigurationType**項目。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-126">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="e6d5a-127">組件限定的名稱是命名空間限定的名稱，後面接著逗號，再型別所在的組件。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-127">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="e6d5a-128">您可以選擇性地也指定的組件版本、 文化特性和公開金鑰語彙基元。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-128">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="e6d5a-129">EF 資料庫提供者 (EF6 之後版本)</span><span class="sxs-lookup"><span data-stu-id="e6d5a-129">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="e6d5a-130">在 EF6 之前, 的資料庫提供者的實體架構特有組件必須是核心 ADO.NET 提供者的一部分。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-130">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="e6d5a-131">從 EF6 開始，EF 的特定組件現在受管理，並個別註冊。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-131">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="e6d5a-132">通常您不需要將您自己註冊提供者。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-132">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="e6d5a-133">當您在安裝時，這通常是由提供者完成。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-133">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="e6d5a-134">包含註冊提供者**提供者**下方的項目**提供者**子區段**entityFramework**一節。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-134">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="e6d5a-135">有兩個必要的屬性提供者項目：</span><span class="sxs-lookup"><span data-stu-id="e6d5a-135">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="e6d5a-136">**{3&gt;invariantname&lt;3}** core ADO.NET 提供者會識別這個 EF 提供者目標</span><span class="sxs-lookup"><span data-stu-id="e6d5a-136">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="e6d5a-137">**型別**是 EF 提供者實作的組件限定的類型名稱</span><span class="sxs-lookup"><span data-stu-id="e6d5a-137">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="e6d5a-138">組件限定的名稱是命名空間限定的名稱，後面接著逗號，再型別所在的組件。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-138">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="e6d5a-139">您可以選擇性地也指定的組件版本、 文化特性和公開金鑰語彙基元。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-139">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="e6d5a-140">例如，以下是註冊的預設 SQL Server 提供者，當您安裝 Entity Framework 建立的項目。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-140">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="e6d5a-141">攔截器 (EF6.1 及更新版本)</span><span class="sxs-lookup"><span data-stu-id="e6d5a-141">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="e6d5a-142">從開始，EF6.1 您可以註冊攔截器組態檔中。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-142">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="e6d5a-143">攔截器可讓您執行額外的邏輯，當 EF 執行某些作業，例如執行資料庫查詢中，開啟連線，依此類推。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-143">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="e6d5a-144">攔截器會藉由註冊**攔截器**下方的項目**攔截器**子區段**entityFramework**一節。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-144">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="e6d5a-145">例如，下列組態會註冊內建**DatabaseLogger**會記錄至主控台的所有資料庫作業的攔截器。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-145">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="e6d5a-146">記錄至檔案 (EF6.1 及更新版本) 的資料庫作業</span><span class="sxs-lookup"><span data-stu-id="e6d5a-146">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="e6d5a-147">當您想要將記錄新增至現有的應用程式，以協助偵錯問題，則註冊攔截器，透過組態檔會特別有用。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-147">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="e6d5a-148">**DatabaseLogger**支援記錄到檔案，藉由提供檔案名稱做為建構函式參數。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-148">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="e6d5a-149">根據預設，這會每次應用程式啟動的時，新的檔案覆寫記錄檔。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-149">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="e6d5a-150">若要改為附加至記錄檔的檔案已經存在使用類似：</span><span class="sxs-lookup"><span data-stu-id="e6d5a-150">To instead append to the log file if it already exists use something like:</span></span>  

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

<span data-ttu-id="e6d5a-151">如需詳細資訊**DatabaseLogger**並註冊攔截器，請參閱部落格文章[EF 6.1： 開啟記錄功能不需要重新編譯](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-151">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="e6d5a-152">程式碼的第一個預設連接 Factory</span><span class="sxs-lookup"><span data-stu-id="e6d5a-152">Code First Default Connection Factory</span></span>  

<span data-ttu-id="e6d5a-153">[設定] 區段可讓您指定預設的連接 factory Code First 應該用來找出要使用內容的資料庫。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-153">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="e6d5a-154">任何連接字串新增至內容的組態檔之後，才會使用預設連接 factory。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-154">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="e6d5a-155">安裝 EF NuGet 套件時的預設連線處理站已註冊指向 SQL Express 或 LocalDB，取決於哪一個已安裝。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-155">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="e6d5a-156">若要設定的連線處理站，您可以指定中的組件限定的類型名稱**deafultConnectionFactory**項目。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-156">To set a connection factory, you specify the assembly qualified type name in the **deafultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="e6d5a-157">組件限定的名稱是命名空間限定的名稱，後面接著逗號，再型別所在的組件。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-157">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="e6d5a-158">您可以選擇性地也指定的組件版本、 文化特性和公開金鑰語彙基元。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-158">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="e6d5a-159">以下是設定您自己的預設連接 factory 的範例：</span><span class="sxs-lookup"><span data-stu-id="e6d5a-159">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="e6d5a-160">上述範例中需要自訂的處理站有無參數建構函式。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-160">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="e6d5a-161">如有需要您可以指定使用的建構函式參數**參數**項目。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-161">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="e6d5a-162">比方說，SqlCeConnectionFactory，包含在 Entity Framework 中，需要您提供給建構函式的提供者非變異名稱。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-162">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="e6d5a-163">提供者非變異名稱識別的 SQL Compact 您想要使用的版本。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-163">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="e6d5a-164">下列組態會導致要使用 SQL Compact 4.0 版預設的內容。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-164">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="e6d5a-165">如果您未設定的預設連線處理站，Code First 會使用 SqlConnectionFactory，指向`.\SQLEXPRESS`。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-165">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="e6d5a-166">SqlConnectionFactory 也有可讓您覆寫連接字串的組件的建構函式。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-166">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="e6d5a-167">如果您想要使用的 SQL Server 執行個體以外的其他`.\SQLEXPRESS`您可以使用這個建構函式來設定伺服器。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-167">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="e6d5a-168">下列組態會導致 Code First 至使用**MyDatabaseServer**沒有設定明確的連接字串的內容。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-168">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="e6d5a-169">根據預設，它會假設建構函式引數是字串類型。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-169">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="e6d5a-170">若要變更此，您可以使用的型別屬性。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-170">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="e6d5a-171">資料庫初始設定式</span><span class="sxs-lookup"><span data-stu-id="e6d5a-171">Database Initializers</span></span>  

<span data-ttu-id="e6d5a-172">資料庫初始設定式會設定每個內容為基礎。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-172">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="e6d5a-173">他們可以設定在組態檔中使用**內容**項目。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-173">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="e6d5a-174">這個項目會使用組件限定的名稱來識別正在設定的內容。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-174">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="e6d5a-175">根據預設，第一個程式碼的內容會設定為使用 CreateDatabaseIfNotExists 初始設定式。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-175">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="e6d5a-176">沒有**disableDatabaseInitialization**屬性上**內容**可以用來停用資料庫初始化的項目。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-176">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="e6d5a-177">例如，下列組態會停用 Blogging.BlogContext 內容 MyAssembly.dll 中所定義的資料庫初始化。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-177">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="e6d5a-178">您可以使用**databaseInitializer**来設定的自訂初始設定式項目。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-178">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="e6d5a-179">建構函式參數會作為預設連線處理站中的相同的語法。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-179">Constructor parameters use the same syntax as default connection factories.</span></span>  

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

<span data-ttu-id="e6d5a-180">您可以設定 Entity Framework 所隨附的一般資料庫初始設定式的其中一個。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-180">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="e6d5a-181">**型別**屬性會用於泛型型別中的.NET Framework 格式。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-181">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="e6d5a-182">例如，如果您使用 Code First 移轉，您可以設定要使用自動移轉的資料庫`MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>`初始設定式。</span><span class="sxs-lookup"><span data-stu-id="e6d5a-182">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
