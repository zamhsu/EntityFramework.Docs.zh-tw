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
# <a name="configuration-file-settings"></a>設定檔設定
Entity Framework 允許從設定檔指定數個設定。 一般 EF 會遵循「設定慣例」原則：這篇文章中討論的所有設定都有預設行為，您只需要在預設不再滿足您的需求時，擔心變更設定。  

## <a name="a-code-based-alternative"></a>以程式碼為基礎的替代方案  

所有這些設定也可以使用程式碼來套用。 從 EF6 開始，我們引進了以[程式碼為基礎](code-based.md)的設定，它提供從程式碼套用設定的集中方式。 在 EF6 之前，設定仍然可以從程式碼套用，但您必須使用各種 Api 來設定不同的區域。 [設定檔] 選項可讓您在部署期間輕鬆地變更這些設定，而不需要更新您的程式碼。

## <a name="the-entity-framework-configuration-section"></a>Entity Framework 設定區段  

從 EF 4.1 開始，您可以使用設定檔的**appSettings**區段來設定內容的資料庫初始化運算式。 在 EF 4.3 中，我們引進了自訂**entityFramework**區段來處理新的設定。 Entity Framework 仍然會辨識使用舊格式設定的資料庫初始化運算式，但建議您盡可能移至新格式。

當您安裝 EntityFramework NuGet 套件時， **entityFramework**區段會自動新增至您專案的設定檔。  

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

[此頁面](~/ef6/fundamentals/configuring/connection-strings.md)提供有關 Entity Framework 如何判斷要使用之資料庫的詳細資訊，包括設定檔案中的連接字串。  

連接字串會在標準**connectionStrings**元素中，而且不需要**entityFramework**區段。  

以 Code First 為基礎的模型會使用標準的 ADO.NET 連接字串。 例如：  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

EF 設計工具型模型會使用特殊的 EF 連接字串。 例如：  

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

## <a name="code-based-configuration-type-ef6-onwards"></a>以程式碼為基礎的設定類型（EF6 和更新版本）  

從 EF6 開始，您可以指定要用於 EF 的 DbConfiguration，以便在應用程式中進行以程式[代碼為基礎](code-based.md)的設定。 在大部分情況下，您不需要指定此設定，因為 EF 會自動探索您的 DbConfiguration。 如需何時可能需要在設定檔中指定 DbConfiguration 的詳細資訊，請參閱程式[代碼型](code-based.md)設定的**移動 DbConfiguration**一節。  

若要設定 DbConfiguration 類型，請在**codeConfigurationType**元素中指定元件限定類型名稱。  

> [!NOTE]
> 元件限定名稱是命名空間限定名稱，後面接著逗號，再接著是該類型所在的元件。 您也可以選擇性地指定元件版本、文化特性和公開金鑰標記。  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>EF 資料庫提供者（EF6）  

在 EF6 之前，必須將資料庫提供者的 Entity Framework 特定部分納入核心 ADO.NET 提供者中。 從 EF6 開始，現在會分別管理和註冊 EF 特定元件。  

通常您不需要自行註冊提供者。 當您安裝時，提供者通常會完成這項作業。  

提供者的註冊方式是在**entityFramework** **區段的 provider 子區段**底下包含**provider**元素。 提供者專案有兩個必要屬性：  

- **invariantName**識別此 EF 提供者的目標核心 ADO.NET 提供者  
- **類型**是 EF 提供者執行的元件限定類型名稱  

> [!NOTE]
> 元件限定名稱是命名空間限定名稱，後面接著逗號，再接著是該類型所在的元件。 您也可以選擇性地指定元件版本、文化特性和公開金鑰標記。  

舉例來說，以下是當您安裝 Entity Framework 時，為了註冊預設 SQL Server 提供者所建立的專案。  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>攔截器（EF 6.1 和更新版本）  

從 EF 6.1 開始，您可以在設定檔中註冊攔截器。 攔截器可讓您在 EF 執行特定作業（例如執行資料庫查詢、開啟連接等）時執行其他邏輯。  

攔截器的註冊方式是在**entityFramework**區段的**攔截**器子區段底下包含**攔截**器元素。 例如，下列設定會註冊將所有資料庫作業記錄到主控台的內建**DatabaseLogger**攔截器。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>將資料庫作業記錄到檔案（EF 6.1 和更新版本）  

當您想要將記錄新增至現有的應用程式以協助偵測問題時，透過設定檔註冊攔截器會特別有用。 **DatabaseLogger**藉由提供檔案名做為「函式」參數，支援記錄至檔案。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

根據預設，這會在每次應用程式啟動時，以新的檔案覆寫記錄檔。 若要改為附加至記錄檔（如果已經存在），請使用類似下列的內容：  

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

如需**DatabaseLogger**和註冊攔截器的詳細資訊，請參閱[EF 6.1：開啟記錄而不重新編譯](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)。  

## <a name="code-first-default-connection-factory"></a>Code First 預設連接 Factory  

[設定] 區段可讓您指定預設的連接 factory，Code First 應該用來尋找要用於內容的資料庫。 只有在未將連接字串新增至內容的設定檔時，才會使用預設的連接處理站。  

當您安裝 EF NuGet 套件時，會根據您已安裝的預設連線處理站，註冊指向 SQL Express 或 LocalDB。  

若要設定連接處理站，您可以在**defaultConnectionFactory**元素中指定元件限定類型名稱。  

> [!NOTE]
> 元件限定名稱是命名空間限定名稱，後面接著逗號，再接著是該類型所在的元件。 您也可以選擇性地指定元件版本、文化特性和公開金鑰標記。  

以下是設定您自己的預設連線 factory 的範例：  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

上述範例要求自訂 factory 具有無參數的函式。 如有需要，您可以使用**parameters**元素指定函數參數。  

例如，包含在 Entity Framework 中的 SqlCeConnectionFactory，需要您將提供者不變名稱提供給此函式。 提供者不變名稱會識別您想要使用的 SQL Compact 版本。 下列設定會導致內容預設使用 SQL Compact 4.0 版。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

如果您未設定預設的連接 factory，Code First 會使用 SqlConnectionFactory，指向 `.\SQLEXPRESS`。 SqlConnectionFactory 也有一個可讓您覆寫連接字串部分的函式。 如果您想要使用 `.\SQLEXPRESS` 以外的 SQL Server 實例，您可以使用這個函數來設定伺服器。  

下列設定會導致 Code First 針對未設定明確連接字串的內容使用**MyDatabaseServer** 。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

根據預設，它會假設函式引數的類型為 string。 您可以使用 type 屬性來變更這個。  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>資料庫初始化運算式  

資料庫初始化運算式是根據每個內容來設定。 您可以使用**coNtext**元素，在設定檔中設定這些專案。 這個元素會使用元件限定名稱來識別要設定的內容。  

根據預設，Code First 內容會設定為使用 CreateDatabaseIfNotExists 初始化運算式。 **CoNtext**元素上有一個**disableDatabaseInitialization**屬性可用來停用資料庫初始化。  

例如，下列設定會針對 MyAssembly 中定義的 BlogCoNtext 內容停用資料庫初始化。  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

您可以使用**databaseInitializer**元素來設定自訂的初始化運算式。  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

函式參數使用與預設連接處理站相同的語法。  

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

您可以設定其中一個包含在 Entity Framework 中的泛型資料庫初始化運算式。 **Type**屬性會使用泛型型別的 .NET Framework 格式。  

例如，如果您使用 Code First 移轉，您可以使用 `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` 初始化運算式，將資料庫設定為自動遷移。  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
