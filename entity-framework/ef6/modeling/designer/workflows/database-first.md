---
title: Database First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: d40cff4ddccf43a394ef4f244653372a5a89b05a
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182455"
---
# <a name="database-first"></a>Database First
這段影片和逐步解說提供使用 Entity Framework Database First 開發的簡介。 Database First 可讓您從現有的資料庫對模型進行反向工程。 此模型會儲存在 EDMX 檔案（.edmx 副檔名）中，而且可以在 Entity Framework Designer 中查看和編輯。 您在應用程式中與互動的類別會從 EDMX 檔案自動產生。

## <a name="watch-the-video"></a>觀看影片
這段影片提供使用 Entity Framework 進行 Database First 開發的介紹。 Database First 可讓您從現有的資料庫對模型進行反向工程。 此模型會儲存在 EDMX 檔案（.edmx 副檔名）中，而且可以在 Entity Framework Designer 中查看和編輯。 您在應用程式中與互動的類別會從 EDMX 檔案自動產生。

**提供者**：[Rowan 莎莎](https://romiller.com/)

**影片**：[WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)@NO__T[-1 個](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>先決條件

您至少必須安裝 Visual Studio 2010 或 Visual Studio 2012，才能完成此逐步解說。

如果您使用 Visual Studio 2010，您也必須安裝[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。

 

## <a name="1-create-an-existing-database"></a>1.建立現有的資料庫

通常當您將目標設為現有的資料庫時，就會建立它，但在此逐步解說中，我們需要建立要存取的資料庫。

隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。
-   如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)資料庫。

 

讓我們繼續產生資料庫。

-   開啟 Visual Studio
-   **View-&gt; 伺服器總管**
-   以滑鼠右鍵按一下 [**資料連線-&gt; 新增連接 ...** ]
-   如果您還沒有從伺服器總管連接到資料庫，則必須選取 [Microsoft SQL Server] 做為資料來源

    ![選取資料來源](~/ef6/media/selectdatasource.png)

-   連接到 LocalDB 或 SQL Express （視您安裝的版本而定），然後輸入**DatabaseFirst**做為資料庫名稱

    ![Sql Express 連接 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 連接 DF](~/ef6/media/localdbconnectiondf.png)

-   選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**

    ![建立資料庫對話方塊](~/ef6/media/createdatabasedialog.png)

-   新的資料庫現在會出現在伺服器總管中，以滑鼠右鍵按一下它，然後選取 [追加**查詢**]
-   將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。

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

為了簡單起見，我們將建立一個使用 Database First 來執行資料存取的基本主控台應用程式：

-   開啟 Visual Studio
-   **檔案 &gt; 個新 &gt; 個專案 。**
-   從左側功能表和**主控台應用程式**中選取 [ **Windows** ]
-   輸入**DatabaseFirstSample**作為名稱
-   選取 [確定]

 

## <a name="3-reverse-engineer-model"></a>3.反向工程模型

我們將使用 Entity Framework Designer （隨附于 Visual Studio 的一部分）來建立我們的模型。

-   **專案-&gt; 加入新專案 。**
-   從左側功能表中選取 [**資料**]，然後**ADO.NET 實體資料模型**
-   輸入**BloggingModel**作為名稱，然後按一下 **[確定]**
-   這會啟動**實體資料模型 Wizard**
-   選取 [**從資料庫產生**]，然後按 **[下一步]**

    ![Wizard 步驟1](~/ef6/media/wizardstep1.png)

-   選取您在第一個區段中建立之資料庫的連接，並輸入**BloggingCoNtext**做為連接字串的名稱，然後按 **[下一步]**

    ![Wizard 步驟2](~/ef6/media/wizardstep2.png)

-   按一下 [資料表] 旁的核取方塊以匯入所有資料表，然後按一下 [完成]

    ![Wizard 步驟3](~/ef6/media/wizardstep3.png)

 

反向工程程式完成之後，新的模型就會新增至您的專案，並開啟以供您在 Entity Framework Designer 中觀看。 App.config 檔案也已新增至您的專案，其中包含資料庫的連接詳細資料。

![模型初始](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步驟

如果您使用 Visual Studio 2010，則需要遵循一些額外的步驟，才能升級至最新版本的 Entity Framework。 升級很重要，因為它可讓您存取改良的 API 介面、更容易使用的應用程式，以及最新的錯誤修正。

首先，我們需要從 NuGet 取得最新版本的 Entity Framework。

-   **專案– &gt; 管理 NuGet 套件 ...** 
    *如果您沒有 [**管理 NuGet 套件 ...** ] 選項，您應該安裝[最新版本的 nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) *
-   選取 [**線上**] 索引標籤
-   選取**EntityFramework**套件
-   按一下 [安裝]。

接下來，我們需要交換我們的模型，以產生會使用 DbCoNtext API 的程式碼，這是在 Entity Framework 的較新版本中引進。

-   在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [**新增程式碼產生專案**...]。
-   從左側功能表中選取 [**線上範本**]，然後搜尋**DbCoNtext**
-   選取**C @ no__t-1**的 EF 5.x DbCoNtext 產生器，輸入**BloggingModel**做為名稱，然後按一下 [**新增**]

    ![DbCoNtext 範本](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4.讀取 & 寫入資料

既然我們已經有模型，就可以使用它來存取一些資料。 我們即將用來存取資料的類別會根據 EDMX 檔案自動產生。

*這個螢幕擷取畫面是來自 Visual Studio 2012，如果您使用 Visual Studio 2010，BloggingModel.tt 和 BloggingModel.CoNtext.tt 檔案就會直接在您的專案底下，而不是在 EDMX 檔案底下。*

![產生的類別 DF](~/ef6/media/generatedclassesdf.png)

 

在 Program.cs 中執行 Main 方法，如下所示。 此程式碼會建立內容的新實例，然後使用它來插入新的 Blog。 然後，它會使用 LINQ 查詢來抓取資料庫中依標題字母順序排序的所有 Blog。

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

您現在可以執行應用程式並加以測試。

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a>5.處理資料庫變更

現在可以對資料庫架構進行一些變更，當我們進行這些變更時，我們也需要更新我們的模型來反映這些變更。

第一個步驟是對資料庫架構進行一些變更。 我們要將 Users 資料表加入至架構。

-   以滑鼠右鍵按一下伺服器總管中的 [ **DatabaseFirst** ] 資料庫，然後選取 [追加**查詢**]
-   將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

現在已更新架構，就可以使用這些變更來更新模型。

-   在 EF 設計工具中，以滑鼠右鍵按一下模型的空白位置，然後選取 [從資料庫更新模型]，這將會啟動更新嚮導
-   在 [更新嚮導] 的 [新增] 索引標籤上，核取 [資料表] 旁的方塊，這表示我們想要從架構新增任何新的資料表。
    @no__t 0The 重新整理 索引標籤會顯示模型中任何現有的資料表，並會在更新期間檢查是否有變更。刪除 索引標籤會顯示已從架構移除的任何資料表，而且也會在更新過程中從模型中移除。系統會自動偵測這兩個索引標籤上的資訊，並提供僅供參考之用，您無法變更任何設定。 *

    ![重新整理嚮導](~/ef6/media/refreshwizard.png)

-   按一下 [更新嚮導] 上的 [完成]

 

模型現在已更新為包含新的使用者實體，其對應至我們新增至資料庫的使用者資料表。

![已更新模型](~/ef6/media/modelupdated.png)

## <a name="summary"></a>總結

在本逐步解說中，我們探討了 Database First 開發，這讓我們能夠在以現有資料庫為基礎的 EF 設計工具中建立模型。 然後，我們會使用該模型來讀取及寫入資料庫中的某些資料。 最後，我們更新了模型，以反映我們對資料庫架構所做的變更。
