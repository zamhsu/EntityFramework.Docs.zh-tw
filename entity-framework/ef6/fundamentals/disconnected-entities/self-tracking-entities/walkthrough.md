---
title: 自我追蹤實體的逐步解說-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
ms.openlocfilehash: d89c452410d34bea71e8220aae141c3bfca3e1ce
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490268"
---
# <a name="self-tracking-entities-walkthrough"></a>自我追蹤實體的逐步解說
> [!IMPORTANT]
> 我們不再建議使用自我追蹤實體範本。 繼續提供該範本只是為了支援現有應用程式。 如果您的應用程式需要使用已中斷連線的實體圖形，請考慮使用 [Trackable Entities](http://trackableentities.github.io/) (可追蹤的實體) 之類的其他替代項目，這是一項類似於自我追蹤實體的技術，可由社群更積極地進行開發或使用低層級變更追蹤 API 來撰寫自訂程式碼。

本逐步解說將示範 Windows Communication Foundation (WCF) 服務會公開傳回實體圖形作業的案例。 接著，用戶端應用程式會操作該圖形並提交修改至服務作業的驗證，並將更新儲存至資料庫，使用 Entity Framework。

完成本逐步解說之前請確定您閱讀[自我追蹤實體](index.md)頁面。

這個逐步解說會完成下列動作：

-   建立可存取的資料庫。
-   建立包含模型的類別庫。
-   交換 Self-Tracking Entity Generator 範本。
-   將實體類別移至個別的專案中。
-   建立 WCF 服務公開查詢和儲存實體的作業。
-   建立用戶端應用程式 （主控台和 WPF） 取用服務。

我們將使用第一個資料庫在此逐步解說中，但相同的技巧同樣適用於第一個模型。

## <a name="pre-requisites"></a>必要條件

若要完成此逐步解說，您必須 Visual Studio 的最新版本。

## <a name="create-a-database"></a>建立資料庫

Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2012，則您將會建立新的 LocalDB 資料庫。
-   如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。

接下來產生的資料庫。

-   開啟 Visual Studio
-   **檢視-&gt;伺服器總管**
-   以滑鼠右鍵按一下**資料連線-&gt;新增連接...**
-   如果您尚未連線至資料庫從伺服器總管之前您必須選取**Microsoft SQL Server**做為資料來源
-   連接到 LocalDB 或 SQL Express，何者而定，您已安裝
-   請輸入**STESample**做為資料庫名稱
-   選取  **確定**而且會要求您想要建立新資料庫，請選取**是**
-   在 伺服器總管現在會顯示新的資料庫
-   如果您使用 Visual Studio 2012
    -   以滑鼠右鍵按一下伺服器總管 中的資料庫，然後選取**新查詢**
    -   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**
-   如果您使用 Visual Studio 2010
    -   選取 **資料集&gt;Transact SQL 編輯器-&gt;新增查詢連接...**
    -   請輸入 **。\\SQLEXPRESS**作為伺服器名稱，然後按一下 **[確定]**
    -   選取  **STESample**資料庫從下拉式清單頂端的查詢編輯器
    -   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**執行 SQL**

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

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a>建立模型

首先，我們必須要將此模型的專案。

-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Visual C\#** 從左窗格中，然後**類別庫**
-   請輸入**STESample**作為名稱，然後按一下 **[確定]**

現在我們將建立簡單的模型來存取資料庫的 EF 設計工具中：

-   **專案-&gt;加入新項目...**
-   選取 **資料**從左窗格中，然後**ADO.NET 實體資料模型**
-   請輸入**BloggingModel**作為名稱，然後按一下 **[確定]**
-   選取 **從資料庫產生**，按一下 **下一步**
-   輸入您在上一節中建立資料庫的連接資訊
-   請輸入**為 [bloggingcontext]** 做為連接字串，然後按一下 [名稱**下一步]**
-   核取方塊旁**資料表**，按一下 **完成**

## <a name="swap-to-ste-code-generation"></a>切換至 STE 程式碼產生

現在，我們需要停用的預設程式碼產生和自我追蹤實體的交換。

### <a name="if-you-are-using-visual-studio-2012"></a>如果您使用 Visual Studio 2012

-   依序展開**BloggingModel.edmx**中**方案總管**並刪除**BloggingModel.tt**並**BloggingModel.Context.tt** 
    *這會停用的預設程式碼產生*
-   以滑鼠右鍵按一下介面，然後選取 EF 設計工具上的空白區域**加入的程式碼產生項目...**
-   選取 **線上**左的窗格中搜尋**STE 產生器**
-   選取  **STE 適用於 C 的產生器\#** 範本，輸入**STETemplate**做為名稱，然後按一下**新增**
-   **STETemplate.tt**並**STETemplate.Context.tt**檔案會新增 BloggingModel.edmx 檔案巢狀

### <a name="if-you-are-using-visual-studio-2010"></a>如果您使用 Visual Studio 2010

-   以滑鼠右鍵按一下介面，然後選取 EF 設計工具上的空白區域**加入的程式碼產生項目...**
-   選取 **程式碼**從左窗格中，然後**ADO.NET Self-Tracking Entity Generator**
-   請輸入**STETemplate**作為名稱，然後按一下**新增**
-   **STETemplate.tt**並**STETemplate.Context.tt**檔案會直接新增至您的專案

## <a name="move-entity-types-into-separate-project"></a>將實體類型移至不同的專案

若要使用自我追蹤實體用戶端應用程式需要從我們的模型產生實體類別的存取權。 因為我們不想要公開 （expose） 用戶端應用程式的整個模型，所以我們要進入個別的專案中的實體類別。

第一步是停止現有的專案中產生實體類別：

-   以滑鼠右鍵按一下**STETemplate.tt**中**方案總管**，然後選取**屬性**
-   在 **屬性**視窗中清除**TextTemplatingFileGenerator**從**CustomTool**屬性
-   依序展開**STETemplate.tt**中**方案總管 中**刪除其下的巢狀的所有檔案

接下來，我們即將加入新的專案，並在其中產生實體類別

-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Visual C\#** 從左窗格中，然後**類別庫**
-   請輸入**STESample.Entities**作為名稱，然後按一下 **[確定]**
-   **專案-&gt;加入現有項目...**
-   瀏覽至**STESample**專案資料夾
-   若要檢視選取**的所有檔案 (\*。\*)**
-   選取  **STETemplate.tt**檔案
-   按一下下拉式箭號旁**新增**按鈕，然後選取**加入做為連結**

    ![新增連結的範本](~/ef6/media/addlinkedtemplate.png)

我們也要確定內容相同的命名空間中產生實體類別。 這只會減少 using 陳述式，我們需要新增在我們的應用程式的數目。

-   以滑鼠右鍵按一下連結**STETemplate.tt**中**方案總管**，然後選取**屬性**
-   在 [**屬性**] 視窗中設定**自訂工具命名空間**到**STESample**

STE 範本所產生的程式碼將會需要的參考**System.Runtime.Serialization**以編譯。 此程式庫所需的 WCF **DataContract**並**DataMember**可序列化的實體類型所使用的屬性。

-   以滑鼠右鍵按一下**STESample.Entities**專案中**方案總管**，然後選取**加入參考...**
    -   在 Visual Studio 2012-核取方塊旁**System.Runtime.Serialization** ，按一下  **確定**
    -   在 Visual Studio 2010-選取**System.Runtime.Serialization** ，按一下  **確定**

最後，我們的內容中的專案將會需要的實體類型的參考。

-   以滑鼠右鍵按一下**STESample**專案中**方案總管**，然後選取**加入參考...**
    -   在 Visual Studio 2012-選取**解決方案**從左窗格中，核取方塊旁**STESample.Entities**按一下 **[確定]**
    -   在 Visual Studio 2010-選取**專案**索引標籤上，選取**STESample.Entities**按一下 **[確定]**

>[!NOTE]
> 將實體類型移到個別的專案的另一個選項是將範本檔案，而不是將其連結從其預設位置。 如果您這麼做，您必須更新**inputFile**變數的範本，以提供 edmx 檔案的相對路徑 (在此範例中會 **...\\BloggingModel.edmx**)。

## <a name="create-a-wcf-service"></a>建立 WCF 服務

現在就可以開始新增 WCF 服務，以公開我們的資料，我們先建立專案。

-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Visual C\#** 從左窗格中，然後**WCF 服務應用程式**
-   請輸入**STESample.Service**作為名稱，然後按一下 **[確定]**
-   將參考加入**System.Data.Entity**組件
-   將參考加入**STESample**並**STESample.Entities**專案

我們需要將 EF 連接字串複製到此專案，以便在執行階段找到。

-   開啟**App.Config**適用於檔案 * * STESample * * 專案和複製**connectionStrings**項目
-   貼上**connectionStrings**元素的子元素當做**configuration**項目**Web.Config**中的檔案**STESample.Service**專案

現在就可以開始實作實際的服務。

-   開啟**IService1.cs**並取代為下列程式碼的內容

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   開啟**Service1.svc**並取代為下列程式碼的內容

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a>取用的主控台應用程式的服務

讓我們建立使用我們的服務的主控台應用程式。

-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Visual C\#** 從左窗格中，然後**主控台應用程式**
-   請輸入**STESample.ConsoleTest**作為名稱，然後按一下 **[確定]**
-   將參考加入**STESample.Entities**專案

我們需要我們的 WCF 服務的服務參考

-   以滑鼠右鍵按一下**STESample.ConsoleTest**專案中**方案總管**，然後選取**加入服務參考...**
-   按一下 **探索**
-   請輸入**BloggingService**作為命名空間，然後按一下 **[確定]**

現在我們可以撰寫一些程式碼來取用服務。

-   開啟**Program.cs**並取代為下列程式碼的內容。

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

您現在可以執行應用程式來查看運作狀況。

-   以滑鼠右鍵按一下**STESample.ConsoleTest**專案中**方案總管**，然後選取**偵錯-&gt;開始新執行個體**

當應用程式執行時，您會看到下列輸出。

```
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a>使用 WPF 應用程式服務

讓我們建立使用我們的服務的 WPF 應用程式。

-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Visual C\#** 從左窗格中，然後**WPF 應用程式**
-   請輸入**STESample.WPFTest**作為名稱，然後按一下 **[確定]**
-   將參考加入**STESample.Entities**專案

我們需要我們的 WCF 服務的服務參考

-   以滑鼠右鍵按一下**STESample.WPFTest**專案中**方案總管**，然後選取**加入服務參考...**
-   按一下 **探索**
-   請輸入**BloggingService**作為命名空間，然後按一下 **[確定]**

現在我們可以撰寫一些程式碼來取用服務。

-   開啟**MainWindow.xaml**並取代為下列程式碼的內容。

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   開啟 MainWindow 的程式碼後置 (**MainWindow.xaml.cs**)，並取代為下列程式碼的內容

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

您現在可以執行應用程式來查看運作狀況。

-   以滑鼠右鍵按一下**STESample.WPFTest**專案中**方案總管**，然後選取**偵錯-&gt;開始新執行個體**
-   您可以管理使用螢幕的資料，並將它儲存服務會使用透過**儲存**按鈕

![WPF 主視窗](~/ef6/media/wpf.png)
