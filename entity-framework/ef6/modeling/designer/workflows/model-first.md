---
title: 第一次-模型 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: 8e010f95db40261073b4af80a3c0e3225a2cd1cf
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490476"
---
# <a name="model-first"></a>第一次建立模型
本影片以及逐步說明的逐步解說提供使用 Entity Framework 模型優先開發的簡介。 第一次模型可讓您建立新的模型使用 Entity Framework Designer，然後透過模型產生資料庫結構描述。 模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。 從 EDMX 檔案，會自動產生應用程式中與您互動的類別。

## <a name="watch-the-video"></a>觀看影片
本影片以及逐步說明的逐步解說提供使用 Entity Framework 模型優先開發的簡介。 第一次模型可讓您建立新的模型使用 Entity Framework Designer，然後透過模型產生資料庫結構描述。 模型會儲存在 EDMX 檔案 （副檔名為.edmx），及可以檢視和編輯 Entity Framework 設計工具中。 從 EDMX 檔案，會自動產生應用程式中與您互動的類別。

**主講人**[Rowan Miller](http://romiller.com/)

**視訊**: [WMV](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>必要條件

您必須擁有 Visual Studio 2010 或 Visual Studio 2012 安裝完成本逐步解說。

如果您使用 Visual Studio 2010，您也必須有[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安裝。

## <a name="1-create-the-application"></a>1.建立應用程式

為了簡單起見，我們要建置基本的主控台應用程式，會使用 Model First 來執行資料存取：

-   開啟 Visual Studio
-   **檔案-&gt;新增-&gt;專案...**
-   選取  **Windows**從左側的功能表和**主控台應用程式**
-   請輸入**ModelFirstSample**做為名稱
-   選取 [確定]

## <a name="2-create-model"></a>2.建立模型

我們將利用 Entity Framework Designer 中，也就是 Visual Studio 的一部分，以建立我們的模型。

-   **專案-&gt;加入新項目...**
-   選取 **資料**從左側功能表，然後**ADO.NET 實體資料模型**
-   請輸入**BloggingModel**作為名稱，然後按一下**確定**，這會啟動 Entity Data Model 精靈
-   選取 **空的模型**，按一下 **完成**

    ![建立空的模型](~/ef6/media/createemptymodel.png)

Entity Framework 設計工具會開啟空白的模型。 現在我們可以開始將實體、 屬性和關聯加入至模型。

-   以滑鼠右鍵按一下設計介面，並選取**屬性**
-   在 [屬性] 視窗變更**實體容器名稱**要**為 [bloggingcontext]**
    *這是將會為您的內容產生衍生內容的名稱代表資料庫中，讓我們可以查詢和儲存資料的工作階段*
-   以滑鼠右鍵按一下設計介面，然後選取**加入新-&gt;實體...**
-   輸入**部落格**做為實體名稱並**BlogId**做為索引鍵的名稱，然後按一下 **[確定]**

    ![加入實體部落格](~/ef6/media/addblogentity.png)

-   以滑鼠右鍵按一下設計介面，然後選取新的實體**加入新-&gt;純量屬性**，輸入**名稱**做為屬性的名稱。
-   重複此程序，新增**Url**屬性。
-   以滑鼠右鍵按一下**Url**屬性在設計介面，然後選取**屬性**，在 屬性 視窗變更**Nullable**設為  **True**
    *這可讓我們的部落格儲存至資料庫，但未指派其 Url*
-   使用您剛學會的技術加入**Post**實體**PostId**索引鍵屬性
-   新增**標題**並**內容**純量屬性**Post**實體

現在，我們有幾個實體時，就可以新增關聯 （或關聯性） 之間。

-   以滑鼠右鍵按一下設計介面，然後選取**加入新-&gt;關聯...**
-   請指向的關聯性某一端**部落格**的多重性**一個**和其他端點來**Post**多重性為**許多** 
    *這表示，部落格有許多文章和文章屬於一個部落格*
-   請確定**外部索引鍵屬性加入 'Post' 實體**核取方塊，然後按一下  **確定**

    ![新增關聯 MF](~/ef6/media/addassociationmf.png)

我們現在有簡單的模型，我們可以產生的資料庫，並使用來讀取和寫入資料。

![建立初始的模型](~/ef6/media/modelinitial.png)

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

    ![DbContext 範本](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a>3.產生資料庫

我們的模型，Entity Framework 可以計算資料庫結構描述，讓我們來儲存和擷取使用模型資料。

Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。
-   如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)資料庫。

接下來產生的資料庫。

-   以滑鼠右鍵按一下設計介面，並選取**從模型產生資料庫...**
-   按一下 **新增連接...** 指定 LocalDB 或 SQL Express，Visual studio 版本而定，您會使用，請輸入**ModelFirst.Blogging**做為資料庫名稱。

    ![LocalDB 連接 MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 連接 MF](~/ef6/media/sqlexpressconnectionmf.png)

-   選取  **確定**而且會要求您想要建立新資料庫，請選取**是**
-   選取 [**下一步]** 和 Entity Framework 設計工具會在計算指令碼來建立資料庫結構描述
-   一旦指令碼會顯示，按一下**完成**指令碼會加入至專案，並且開啟
-   以滑鼠右鍵按一下指令碼，然後選取**Execute**，系統會提示您指定要連接的資料庫指定 LocalDB 或 SQL Server Express，您使用 Visual studio 版本而定

## <a name="4-reading--writing-data"></a>4.讀取和寫入資料

現在，我們就可以開始使用它來存取部分資料的模型。 我們將類別，用以存取資料自動產生根據 EDMX 檔。

*這個螢幕擷取畫面是來自 Visual Studio 2012 中，如果您使用 Visual Studio 2010 BloggingModel.tt 且 BloggingModel.Context.tt 檔案將會直接在您的專案之下，而非巢狀於 EDMX 檔之下。*

![產生的類別](~/ef6/media/generatedclasses.png)

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

## <a name="5-dealing-with-model-changes"></a>5.處理模型變更

現在就可以開始對我們的模型進行一些變更，當我們做出這些變更，我們也需要更新資料庫結構描述。

我們一開始先將新的 「 使用者 」 實體加入至我們的模型。

-   加入新**使用者**實體名稱取代**Username**做為索引鍵的名稱和**字串**做為索引鍵的屬性類型

    ![新增使用者實體](~/ef6/media/adduserentity.png)

-   以滑鼠右鍵按一下**使用者名稱**屬性，在設計介面，然後選取**屬性**，在 屬性 視窗變更**MaxLength**設為  **50** 
    *這會限制可以儲存為 50 個字元的使用者名稱中的資料*
-   新增**DisplayName**純量屬性**使用者**實體

我們現在有更新的模型，我們已準備好更新資料庫以容納我們新的使用者實體類型。

-   以滑鼠右鍵按一下設計介面，並選取**從模型產生資料庫...**，entity Framework 會計算指令碼來重新建立已更新的模型為基礎的結構描述。
-   按一下 **完成**
-   您可能會收到有關覆寫現有的 DDL 指令碼和模型的對應和儲存體組件的警告，請按一下**是**這些兩個警告
-   若要建立的資料庫更新的 SQL 指令碼會開啟  
    *產生指令碼會卸除所有現有的資料表，並再重新建立全新的結構描述。這可能適用於本機開發，但並不可行，將變更推送到已部署的資料庫。如果您需要將變更發佈至已部署的資料庫，您必須編輯指令碼，或使用結構描述比較工具來計算移轉指令碼。*
-   以滑鼠右鍵按一下指令碼，然後選取**Execute**，系統會提示您指定要連接的資料庫指定 LocalDB 或 SQL Server Express，您使用 Visual studio 版本而定

## <a name="summary"></a>總結

在本逐步解說，我們探討了第一個模型的開發，這可讓我們在 EF 設計工具中建立模型，並再從該模型產生資料庫。 然後，我們會使用模型來讀取和寫入資料庫中的一些資料。 最後，我們會更新模型，然後再重新建立資料庫結構描述，以符合模型。
