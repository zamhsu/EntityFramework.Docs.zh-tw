---
title: ASP.NET Core 使用者入門 - 現有的資料庫 - EF Core
author: rowanmiller
ms.date: 08/02/2018
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: bba2742c3f3b6da93dd4b4f170a3878fc0473bc8
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022193"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a>在 ASP.NET Core 上使用 EF Core 搭配現有資料庫的使用者入門

在本教學課程中，您將會建置 ASP.NET Core MVC 應用程式，而其使用 Entity Framework Core 執行基本的資料存取。 您將會對現有的資料庫進行還原工程，建立 Entity Framework 模型。

[在 GitHub 上檢視此文章的範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)。

## <a name="prerequisites"></a>必要條件

安裝下列軟體：

* [Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) 與這些工作負載：
  * **ASP.NET 與網頁程式開發** (位在 [Web & Cloud] \(Web 與雲端) 下)
  * **.NET Core 跨平台開發** (位在 [其他工具組] 下)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。

## <a name="create-blogging-database"></a>建立部落格資料庫

本教學課程使用您 LocalDb 執行個體上的**部落格**資料庫作為現有的資料庫。 如果您在另一個教學課程中已建立了**部落格**資料庫，則可跳過這些步驟。

* 開啟 Visual Studio
* [工具] > [連線到資料庫]
* 選取 [Microsoft SQL Server]，並按一下 [繼續]
* 輸入 **(localdb)\mssqllocaldb** 作為**伺服器名稱**
* 輸入 **master** 作為**資料庫名稱**，並按一下 [確定]
* master 資料庫現在會顯示在 [伺服器總管] 中的 [資料連線] 下
* 以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]
* 將以下列出的指令碼，複製到查詢編輯器中
* 以滑鼠右鍵按一下查詢編輯器，然後選取 [執行]

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>建立新專案

* 開啟 Visual Studio 2017
* [檔案] > [新增] > [專案...]
* 從左側功能表中，選取 **[已安裝] > [Visual C#] > [Web]**
* 選取 [ASP.NET Core Web 應用程式] 專案範本
* 輸入 **EFGetStarted.AspNetCore.ExistingDb** 作為名稱，然後按一下 [確定]
* 等候 [新增 ASP.NET Core Web 應用程式] 對話方塊出現
* 請確定目標架構下拉式清單設定為 **.NET Core**，然後版本下拉式清單設定為 **ASP.NET Core 2.1**
* 選取 [Web 應用程式 (模型-檢視-控制器)] 範本
* 確認 [驗證] 已設為 [無驗證]
* 按一下 [確定] 

## <a name="install-entity-framework-core"></a>安裝 Entity Framework Core

若要安裝 EF Core，請為希望作為目標的 EF Core 資料庫提供者，安裝此套件。 如需可用的提供者清單，請參閱[資料庫提供者](../../providers/index.md)。 

在本教學課程中，因為使用 SQL Server，所以無需安裝提供者套件。 [Microsoft.AspnetCore.App 中繼套件](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)包含 SQL Server 提供者套件。

## <a name="reverse-engineer-your-model"></a>針對您的模型進行反向工程

現在就可以根據您現有的資料庫來建立 EF 模型。

* [工具] –> [NuGet 套件管理員] –> [套件管理員主控台]
* 執行下列命令，以便從現有的資料庫來建立模型：

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

如果您收到 `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` 的錯誤，請關閉再重新開啟 Visual Studio。

> [!TIP]  
> 您可以將 `-Tables` 引數加入上述命令，以指定要為哪些資料表產生實體。 例如，`-Tables Blog,Post`。

反向工程程序會根據現有資料庫的結構描述，建立實體類別 (`Blog.cs` & `Post.cs`) 和衍生的內容 (`BloggingContext.cs`)。

 實體類別是簡單的 C# 物件，代表您要查詢和儲存的資料。 以下為 `Blog` 與 `Post` 實體類別：

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Post.cs)]

> [!TIP]  
> 若要啟用延遲載入，可以設定瀏覽屬性 `virtual` (Blog.Post 與 Post.Blog)。

 內容代表資料庫的工作階段，並可讓您查詢和儲存實體類別的執行個體。

<!-- Static code listing, rather than a linked file, because the tutorial modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public BloggingContext()
    {
    }

    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    {
    }

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

### <a name="register-and-configure-your-context-in-startupcs"></a>在 Startup.cs 中註冊並設定內容

若要為 MVC 控制器提供 `BloggingContext`，請將其註冊為服務。

* 開啟 **Startup.cs**
* 在檔案開頭加入下列 `using` 陳述式

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

您現已可使用 `AddDbContext(...)` 方法，將其註冊為服務。
* 找出 `ConfigureServices(...)` 方法
* 新增下列反白顯示的程式碼，將內容註冊為服務

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=14-15)]

> [!TIP]  
> 在實際的應用程式中，一般會將連接字串置於組態檔或環境變數中。 為了簡單起見，本教學課程將會請您將其定義在程式碼中。 如需詳細資訊，請參閱[連接字串](../../miscellaneous/connection-strings.md)。

## <a name="create-a-controller-and-views"></a>建立控制器和檢視

* 在 [方案總管] 中以滑鼠右鍵按一下 [Controllers] 資料夾，然後選取 [新增] > [控制器]
* 選取 [使用 Entity Framework 執行檢視的 MVC 控制器] 並按一下 [確定]
* 將 [模型類別] 設為 [Blog]，並將 [資料內容類別] 設為 [BloggingContext]
* 按一下 [新增]

## <a name="run-the-application"></a>執行應用程式

您現在可以執行應用程式來查看運作狀況。

* [偵錯] > [啟動但不偵錯]
* 應用程式會在網頁瀏覽器中建置及開啟
* 巡覽至 `/Blogs`
* 按一下 [新建]
* 輸入新部落格的 **URL**，然後按一下 [建立]

  ![建立頁面](_static/create.png)

  ![索引頁面](_static/index-existing-db.png)

## <a name="next-steps"></a>後續步驟

如需如何建立內容和實體類別的詳細資訊，請參閱下列文章：
* [Entity Framework Core 工具參考 - .NET CLI](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [Entity Framework Core 工具參考 - 套件管理員主控台](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)