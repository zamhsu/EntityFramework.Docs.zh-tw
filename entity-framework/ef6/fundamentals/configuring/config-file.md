---
title: 組態檔設定 EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
ms.openlocfilehash: 88c2439f3a89c9fb9ee22f828789df4decf39cc5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996499"
---
# <a name="configuration-file-settings"></a>組態檔設定
Entity Framework 可讓多個組態檔中指定的設定。 EF 遵循 'convention over configuration' 原則的一般情況下： 這篇文章中討論的所有設定的預設行為，您只需要擔心如何變更設定，當預設值不再符合您的需求。  

## <a name="a-code-based-alternative"></a>程式碼為基礎的替代方案  

所有這些設定也可以套用使用程式碼。 從開始，我們介紹了的 EF6[程式碼為基礎的組態](code-based.md)，以提供集中的方式套用組態從程式碼。 EF6，仍然可以從程式碼套用設定但您需要使用各種 Api 來設定不同的區域。 組態的 [檔案] 選項可讓這些設定，以輕鬆地在部署期間變更，而不需要更新您的程式碼。

## <a name="the-entity-framework-configuration-section"></a>Entity Framework 的組態區段  

從 EF4.1 您可以設定內容中使用的資料庫初始設定式**appSettings**組態檔區段。 在 EF 4.3 我們引進了自訂**entityFramework**一節，以處理新的設定。 Entity Framework 仍會辨識資料庫初始設定式將使用舊的格式，但建議您盡可能移動至新的格式。

**EntityFramework**安裝 EntityFramework NuGet 套件時自動區段新增至您的專案的組態檔。  

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

## <a name="connection-strings"></a>連接字串  

[此頁面](~/ef6/fundamentals/configuring/connection-strings.md)提供在 Entity Framework 如何判斷要使用的資料庫更多詳細資料，包括組態檔中的連接字串。  

在標準中的連接字串移**connectionStrings**項目，而且不需要**entityFramework**一節。  

第一次型的程式碼模型會使用一般的 ADO.NET 連接字串。 例如:   

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

EF 設計工具基礎模型使用特殊的 EF 連接字串。 例如:   

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

## <a name="code-based-configuration-type-ef6-onwards"></a>程式碼為基礎的組態類型 (EF6 之後版本)  

從 EF6 開始，您可以指定要用於 ef DbConfiguration[程式碼為基礎的組態](code-based.md)應用程式中。 在大部分情況下，您不必指定此設定，如 EF 會自動探索您 DbConfiguration。 如您可能需要在組態檔中指定 DbConfiguration 的詳細資訊，請參閱**移動 DbConfiguration**一節[程式碼為基礎的組態](code-based.md)。  

若要設定 DbConfiguration 類型，您可以指定中的組件限定的類型名稱**codeConfigurationType**項目。  

> [!NOTE]
> 組件限定的名稱是命名空間限定的名稱，後面接著逗號，再型別所在的組件。 您可以選擇性地也指定的組件版本、 文化特性和公開金鑰語彙基元。  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>EF 資料庫提供者 (EF6 之後版本)  

在 EF6 之前, 的資料庫提供者的實體架構特有組件必須是核心 ADO.NET 提供者的一部分。 從 EF6 開始，EF 的特定組件現在受管理，並個別註冊。  

通常您不需要將您自己註冊提供者。 當您在安裝時，這通常是由提供者完成。  

包含註冊提供者**提供者**下方的項目**提供者**子區段**entityFramework**一節。 有兩個必要的屬性提供者項目：  

- **{3&gt;invariantname&lt;3}** core ADO.NET 提供者會識別這個 EF 提供者目標  
- **型別**是 EF 提供者實作的組件限定的類型名稱  

> [!NOTE]
> 組件限定的名稱是命名空間限定的名稱，後面接著逗號，再型別所在的組件。 您可以選擇性地也指定的組件版本、 文化特性和公開金鑰語彙基元。  

例如，以下是註冊的預設 SQL Server 提供者，當您安裝 Entity Framework 建立的項目。  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>攔截器 (EF6.1 及更新版本)  

從開始，EF6.1 您可以註冊攔截器組態檔中。 攔截器可讓您執行額外的邏輯，當 EF 執行某些作業，例如執行資料庫查詢中，開啟連線，依此類推。  

攔截器會藉由註冊**攔截器**下方的項目**攔截器**子區段**entityFramework**一節。 例如，下列組態會註冊內建**DatabaseLogger**會記錄至主控台的所有資料庫作業的攔截器。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>記錄至檔案 (EF6.1 及更新版本) 的資料庫作業  

當您想要將記錄新增至現有的應用程式，以協助偵錯問題，則註冊攔截器，透過組態檔會特別有用。 **DatabaseLogger**支援記錄到檔案，藉由提供檔案名稱做為建構函式參數。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

根據預設，這會每次應用程式啟動的時，新的檔案覆寫記錄檔。 若要改為附加至記錄檔的檔案已經存在使用類似：  

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

如需詳細資訊**DatabaseLogger**並註冊攔截器，請參閱部落格文章[EF 6.1： 開啟記錄功能不需要重新編譯](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)。  

## <a name="code-first-default-connection-factory"></a>程式碼的第一個預設連接 Factory  

[設定] 區段可讓您指定預設的連接 factory Code First 應該用來找出要使用內容的資料庫。 任何連接字串新增至內容的組態檔之後，才會使用預設連接 factory。  

安裝 EF NuGet 套件時的預設連線處理站已註冊指向 SQL Express 或 LocalDB，取決於哪一個已安裝。  

若要設定的連線處理站，您可以指定中的組件限定的類型名稱**deafultConnectionFactory**項目。  

> [!NOTE]
> 組件限定的名稱是命名空間限定的名稱，後面接著逗號，再型別所在的組件。 您可以選擇性地也指定的組件版本、 文化特性和公開金鑰語彙基元。  

以下是設定您自己的預設連接 factory 的範例：  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

上述範例中需要自訂的處理站有無參數建構函式。 如有需要您可以指定使用的建構函式參數**參數**項目。  

比方說，SqlCeConnectionFactory，包含在 Entity Framework 中，需要您提供給建構函式的提供者非變異名稱。 提供者非變異名稱識別的 SQL Compact 您想要使用的版本。 下列組態會導致要使用 SQL Compact 4.0 版預設的內容。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

如果您未設定的預設連線處理站，Code First 會使用 SqlConnectionFactory，指向`.\SQLEXPRESS`。 SqlConnectionFactory 也有可讓您覆寫連接字串的組件的建構函式。 如果您想要使用的 SQL Server 執行個體以外的其他`.\SQLEXPRESS`您可以使用這個建構函式來設定伺服器。  

下列組態會導致 Code First 至使用**MyDatabaseServer**沒有設定明確的連接字串的內容。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

根據預設，它會假設建構函式引數是字串類型。 若要變更此，您可以使用的型別屬性。  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>資料庫初始設定式  

資料庫初始設定式會設定每個內容為基礎。 他們可以設定在組態檔中使用**內容**項目。 這個項目會使用組件限定的名稱來識別正在設定的內容。  

根據預設，第一個程式碼的內容會設定為使用 CreateDatabaseIfNotExists 初始設定式。 沒有**disableDatabaseInitialization**屬性上**內容**可以用來停用資料庫初始化的項目。  

例如，下列組態會停用 Blogging.BlogContext 內容 MyAssembly.dll 中所定義的資料庫初始化。  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

您可以使用**databaseInitializer**来設定的自訂初始設定式項目。  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

建構函式參數會作為預設連線處理站中的相同的語法。  

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

您可以設定 Entity Framework 所隨附的一般資料庫初始設定式的其中一個。 **型別**屬性會用於泛型型別中的.NET Framework 格式。  

例如，如果您使用 Code First 移轉，您可以設定要使用自動移轉的資料庫`MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>`初始設定式。  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
