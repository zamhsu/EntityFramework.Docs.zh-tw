---
title: ASP.NET Core 使用者入門 - 現有的資料庫 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: db2469d0badd428734425c1f568667f00bef2f4f
ms.sourcegitcommit: 90139dbd6f485473afda0788a5a314c9aa601ea0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a>在 ASP.NET Core 上使用 EF Core 搭配現有資料庫的使用者入門

在本逐步解說中，您將建置 ASP.NET Core MVC 應用程式，該應用程式將使用 Entity Framework 執行基本的資料存取。 您將會使用反向工程，根據現有的資料庫來建立 Entity Framework 模型。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb) \(英文\)。

## <a name="prerequisites"></a>必要條件

若要完成此逐步解說，必須符合下列必要條件：

* [Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) 與這些工作負載：
  * **ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)
  * **.NET Core 跨平台開發** (位在 [其他工具組] 下)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。
* [部落格資料庫](#blogging-database)

### <a name="blogging-database"></a>部落格資料庫

本教學課程使用您 LocalDb 執行個體上的**部落格**資料庫作為現有的資料庫。

> [!TIP]  
> 如果您在另一個教學課程中已經建立**部落格**資料庫，則可以跳過這些步驟。

* 開啟 Visual Studio
* [工具] > [連線到資料庫]
* 選取 [Microsoft SQL Server]，並按一下 [繼續]
* 輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**
* 輸入 **master** 作為**資料庫名稱**，並按一下 [確定]
* master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下
* 以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]
* 將下面列出的指令碼複製到查詢編輯器中
* 以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>建立新專案

* 開啟 Visual Studio 2017
* [檔案] > [新增] > [專案]
* 從左側功能表選取 [已安裝] > [範本] > [Visual C#] > [Web]
* 選取 [ASP.NET Core Web 應用程式 (.NET Core)] 專案範本
* 輸入 **EFGetStarted.AspNetCore.ExistingDb** 作為名稱，然後按一下 [確定]
* 等候 [新增 ASP.NET Core Web 應用程式] 對話方塊出現
* 在 [ASP.NET Core 範本 2.0] 下，選取 [Web 應用程式 (模型-檢視-控制器)]
* 確認 [驗證] 已設為 [無驗證]
* 按一下 [確定] 

## <a name="install-entity-framework"></a>安裝 Entity Framework

若要使用 EF Core，請針對您要作為目標的資料庫提供者來安裝套件。 本逐步解說會使用 SQL Server。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。

* [工具] > [NuGet 套件管理員] > [套件管理員主控台]

* 執行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`

我們將使用一些 Entity Framework Core 工具，以便從資料庫來建立模型。 因此，我們也將安裝工具套件：

* 執行 `Install-Package Microsoft.EntityFrameworkCore.Tools`

我們將使用一些 ASP.NET Core Scaffolding 工具，於稍後建立控制器與檢視。 因此，我們也將安裝此設計套件：

* 執行 `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`

## <a name="reverse-engineer-your-model"></a>針對您的模型進行反向工程

現在就可以根據您現有的資料庫來建立 EF 模型。

* [工具] –> [NuGet 套件管理員] –> [套件管理員主控台]
* 執行下列命令，以便從現有的資料庫來建立模型：

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

如果您收到 `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。

> [!TIP]  
> 您可以將 `-Tables` 引數加入上述命令，以指定要為哪些資料表產生實體。 例如， `-Tables Blog,Post`.

反向工程程序會根據現有資料庫的結構描述，建立實體類別 (`Blog.cs` & `Post.cs`) 和衍生的內容 (`BloggingContext.cs`)。

 實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

 內容代表資料庫的工作階段，並可讓您查詢和儲存實體類別的執行個體。

<!-- Static code listing, rather than a linked file, because the walkthrough modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public virtual DbSet<Blog> Blog { get; set; }
    public virtual DbSet<Post> Post { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>(entity =>
        {
            entity.Property(e => e.Url).IsRequired();
        });

        modelBuilder.Entity<Post>(entity =>
        {
            entity.HasOne(d => d.Blog)
                .WithMany(p => p.Post)
                .HasForeignKey(d => d.BlogId);
        });
    }
}
```

## <a name="register-your-context-with-dependency-injection"></a>使用相依性插入來註冊內容

相依性插入的概念是 ASP.NET Core 的核心。 服務 (例如 `BloggingContext`) 是在應用程式啟動期間使用相依性插入來註冊。 接著，會透過建構函式參數或屬性，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。 如需相依性插入的詳細資訊，請參閱 ASP.NET 網站上的[相依性插入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)文章。

### <a name="remove-inline-context-configuration"></a>移除內嵌內容設定

在 ASP.NET Core 中，通常是在 **Startup.cs** 中執行設定。 為了遵循此模式，我們要將資料庫提供者的設定移至 **Startup.cs**。

* 開啟 `Models\BloggingContext.cs`
* 刪除 `OnConfiguring(...)` 方法

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
    optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
}
```

* 新增下列建構函式，以藉由相依性插入將設定傳遞到內容中

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/BloggingContext.cs#Constructor)]

### <a name="register-and-configure-your-context-in-startupcs"></a>在 Startup.cs 中註冊並設定內容

為了讓 MVC 控制器能使用 `BloggingContext`，我們要將它註冊為服務。

* 開啟 **Startup.cs**
* 在檔案開頭加入下列 `using` 陳述式

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

現在，我們可以使用 `AddDbContext(...)` 方法以將它註冊為服務。
* 找出 `ConfigureServices(...)` 方法
* 加入下列程式碼以將內容註冊為服務

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

> [!TIP]  
> 在實際的應用程式中，您通常會將連接字串放到設定檔中。 為了簡單起見，我們在程式碼中加以定義。 如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。

## <a name="create-a-controller"></a>建立控制器

接下來，我們將在專案中啟用 Scaffolding。

* 在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]
* 選取 [完整相依性]，然後按一下 [新增]
* 在開啟的 `ScaffoldingReadMe.txt` 檔案中，您可以忽略其中的指示

啟用 Scaffolding 之後，就可以建立 `Blog` 實體的控制器結構。

* 在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]
* 選取 [使用 Entity Framework 執行檢視的 MVC 控制器] 並按一下 [確定]
* 將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]
* 按一下 [新增]

## <a name="run-the-application"></a>執行應用程式

您現在可以執行應用程式來查看運作狀況。

* [偵錯] > [啟動但不偵錯]
* 系統將會建置應用程式並在網頁瀏覽器中開啟
* 巡覽至 `/Blogs`
* 按一下 [新建]
* 輸入新部落格的 **URL**，然後按一下 [建立]

![影像](_static/create.png)

![影像](_static/index-existing-db.png)
