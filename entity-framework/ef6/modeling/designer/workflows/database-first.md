---
title: Database First-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
caps.latest.revision: 3
ms.openlocfilehash: 17bba5fe9883a1bee0f8b9624dfa35da889e6005
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120244"
---
# <a name="database-first"></a>第一次資料庫
本影片以及逐步說明的逐步解說提供使用 Entity Framework 資料庫優先開發的簡介。 資料庫第一次可讓您進行反向工程，從現有的資料庫模型。 模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。 從 EDMX 檔案，會自動產生應用程式中與您互動的類別。

## <a name="watch-the-video"></a>觀看影片
這段影片中提供使用 Entity Framework 資料庫優先開發的簡介。 資料庫第一次可讓您進行反向工程，從現有的資料庫模型。 模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。 從 EDMX 檔案，會自動產生應用程式中與您互動的類別。

**主講人**[Rowan Miller](http://romiller.com/)

**視訊**: [WMV](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>必要條件

您必須至少是 Visual studio 2010 或 Visual Studio 2012 安裝完成本逐步解說。

如果您使用 Visual Studio 2010，您也必須有[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安裝。

 

## <a name="1-create-an-existing-database"></a>1.建立現有的資料庫

通常當您的目標將已建立，現有的資料庫，但在此逐步解說，我們需要建立可存取的資料庫。

Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。
-   如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)資料庫。

 

接下來產生的資料庫。

-   開啟 Visual Studio
-   **檢視-&gt;伺服器總管**
-   以滑鼠右鍵按一下**資料連線-&gt;新增連接...**
-   如果您尚未連線至資料庫從伺服器總管之前您必須選取 Microsoft SQL Server 做為資料來源

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   連接到 LocalDB 或 SQL Express，何者而定，您已安裝，然後輸入**DatabaseFirst.Blogging**做為資料庫名稱

    ![SqlExpressConnectionDF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDBConnectionDF](~/ef6/media/localdbconnectiondf.png)

-   選取  **確定**而且會要求您想要建立新資料庫，請選取**是**

    ![CreateDatabaseDialog](~/ef6/media/createdatabasedialog.png)

-   新資料庫現在會出現在 [伺服器總管] 中，按一下滑鼠右鍵，然後選取**新查詢**
-   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);
```

## <a name="2-create-the-application"></a>2.建立應用程式

為了簡單起見，我們要建置基本的主控台應用程式，會使用 Database First 來執行資料存取：

-   開啟 Visual Studio
-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Windows**從左側的功能表和**主控台應用程式**
-   請輸入**DatabaseFirstSample**做為名稱
-   選取 [確定]

 

## <a name="3-reverse-engineer-model"></a>3.反向工程模型

我們將利用 Entity Framework Designer 中，也就是 Visual Studio 的一部分，以建立我們的模型。

-   **專案-&gt;加入新項目...**
-   選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**
-   請輸入**BloggingModel**作為名稱，然後按一下 **[確定]**
-   這會啟動**Entity Data Model 精靈**
-   選取 **從資料庫產生**，按一下 **下一步**

    ![WizardStep1](~/ef6/media/wizardstep1.png)

-   選取您建立第一個區段中的資料庫連接中，輸入**為 [bloggingcontext]** 做為連接字串，然後按一下 [名稱**下一步]**

    ![WizardStep2](~/ef6/media/wizardstep2.png)

-   按一下 匯入的所有資料表，並按一下 完成 '資料表旁的核取方塊

    ![WizardStep3](~/ef6/media/wizardstep3.png)

 

反向工程程序完成後新模型加入您的專案，並讓您檢視在 Entity Framework Designer 中開啟。 App.config 檔案也已新增至您的專案與資料庫的連線詳細資料。

![ModelInitial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步驟

如果您正在 Visual Studio 2010 中有您要升級至最新版的 Entity Framework 遵循一些額外的步驟。 升級很重要，因為它可讓您存取改良的 API 介面，也就是容易使用，以及最新的 bug 修正。

首先，我們要從 NuGet 取得最新版的 Entity Framework。

-   **專案 –&gt;管理 NuGet 封裝...** 
    *如果您沒有**管理 NuGet 套件...** 選項，您應該安裝[最新版的 NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*
-   選取 [ **Online** ] 索引標籤
-   選取  **EntityFramework**封裝
-   按一下 **安裝**

接下來，我們需要交換我們的模型來產生程式碼會使用所導入新版的 Entity Framework DbContext API。

-   以滑鼠右鍵按一下您的模型，在 EF 設計工具中的非空點，然後選取**加入的程式碼產生項目...**
-   選取 **線上範本**從左側的功能表，並搜尋**DbContext**
-   選取 EF **5.x 適用於 C 的 DbContext Generator\#**，輸入**BloggingModel**做為名稱，然後按一下**新增**

    ![DbContextTemplate](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4.讀取和寫入資料

現在，我們就可以開始使用它來存取部分資料的模型。 我們將類別，用以存取資料自動產生根據 EDMX 檔。

*這個螢幕擷取畫面是來自 Visual Studio 2012 中，如果您使用 Visual Studio 2010 BloggingModel.tt 且 BloggingModel.Context.tt 檔案將會直接在您的專案之下，而非巢狀於 EDMX 檔之下。*

![GeneratedClassesDF](~/ef6/media/generatedclassesdf.png)

 

在 Program.cs 中實作的 Main 方法，如下所示。 此程式碼會建立我們的內容的新執行個體，然後用它來插入新的部落格 然後它會使用 LINQ 查詢來擷取依字母順序排序項目所使用的資料庫中的所有部落格。

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

您現在可以執行應用程式，並加以測試。

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a>5.因應資料庫變更

現在就可以開始對資料庫結構描述，進行一些變更，當我們做出這些變更，我們也需要更新我們的模型，以反映這些變更。

第一個步驟是對資料庫結構描述進行一些變更。 我們要加入使用者資料表的結構描述。

-   以滑鼠右鍵按一下**DatabaseFirst.Blogging**伺服器總管 中的資料庫，並選取**新查詢**
-   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

現在，更新結構描述時，就可以使用這些變更來更新模型。

-   以滑鼠右鍵按一下空白的模型中的 EF 設計工具和選取 ' 從資料庫更新模型...' 的位置，這會啟動 更新精靈
-   在 [更新精靈] 核取方塊中，資料表旁的 [新增] 索引標籤中，這表示我們想要從結構描述中加入任何新的資料表。
    *[重新整理] 索引標籤會顯示任何現有的資料表，將會檢查變更期間更新模型中。刪除索引標籤會顯示已移除從結構描述，也會移除從模型更新一部分的任何資料表。這些兩個索引標籤上的資訊會自動偵測，並提供僅供參考之用，您無法變更任何設定。*

    ![RefreshWizard](~/ef6/media/refreshwizard.png)

-   按一下 [完成]，在 [更新精靈]

 

模型現在會更新以包含新的使用者實體會對應到使用者資料表，我們新增至資料庫。

![ModelUpdated](~/ef6/media/modelupdated.png)

## <a name="summary"></a>總結

在本逐步解說，我們探討了第一個資料庫的開發，這可讓我們來根據現有資料庫的 EF 設計工具中建立模型。 然後，我們會使用該模型來讀取和寫入資料庫中的一些資料。 最後，我們已更新模型，以反映資料庫結構描述我們所做的變更。
