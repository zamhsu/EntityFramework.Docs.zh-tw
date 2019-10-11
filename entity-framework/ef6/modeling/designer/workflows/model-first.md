---
title: Model First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: 1b37805beb3d33f0b6dad2577a8abb3ea8f7b1e4
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182434"
---
# <a name="model-first"></a>Model First
這段影片和逐步解說提供使用 Entity Framework Model First 開發的簡介。 Model First 可讓您使用 Entity Framework Designer 建立新的模型，然後從模型產生資料庫架構。 此模型會儲存在 EDMX 檔案（.edmx 副檔名）中，而且可以在 Entity Framework Designer 中查看和編輯。 您在應用程式中與互動的類別會從 EDMX 檔案自動產生。

## <a name="watch-the-video"></a>觀看影片
這段影片和逐步解說提供使用 Entity Framework Model First 開發的簡介。 Model First 可讓您使用 Entity Framework Designer 建立新的模型，然後從模型產生資料庫架構。 此模型會儲存在 EDMX 檔案（.edmx 副檔名）中，而且可以在 Entity Framework Designer 中查看和編輯。 您在應用程式中與互動的類別會從 EDMX 檔案自動產生。

**提供者**：[Rowan 莎莎](https://romiller.com/)

**影片**：[WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)@NO__T[-1 個](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>先決條件

您將需要安裝 Visual Studio 2010 或 Visual Studio 2012，才能完成此逐步解說。

如果您使用 Visual Studio 2010，您也必須安裝[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。

## <a name="1-create-the-application"></a>1.建立應用程式

為了簡單起見，我們將建立一個使用 Model First 來執行資料存取的基本主控台應用程式：

-   開啟 Visual Studio
-   **檔案 &gt; 個新 &gt; 個專案 。**
-   從左側功能表和**主控台應用程式**中選取 [ **Windows** ]
-   輸入**ModelFirstSample**作為名稱
-   選取 [確定]

## <a name="2-create-model"></a>2.建立模型

我們將使用 Entity Framework Designer （隨附于 Visual Studio 的一部分）來建立我們的模型。

-   **專案-&gt; 加入新專案 。**
-   從左側功能表中選取 [**資料**]，然後**ADO.NET 實體資料模型**
-   輸入**BloggingModel**作為名稱，然後按一下 **[確定]** ，這會啟動實體資料模型 Wizard
-   選取 [**空的模型**]，然後按一下 **[完成]**

    ![建立空的模型](~/ef6/media/createemptymodel.png)

Entity Framework Designer 會以空白模型開啟。 現在我們可以開始將實體、屬性和關聯新增至模型。

-   以滑鼠右鍵按一下設計介面，然後選取 [**屬性**]
-   在 屬性視窗將**實體容器名稱**變更為**BloggingCoNtext**
    *這是將為您產生的衍生內容名稱，內容代表資料庫的會話，讓我們能夠進行查詢和儲存資料*
-   以滑鼠右鍵按一下設計介面，然後選取 [**加入新的-&gt;** ] [實體]。
-   輸入**Blog**作為機構名稱，並**BlogId**作為索引鍵名稱，然後按一下 **[確定]** 。

    ![新增 Blog 實體](~/ef6/media/addblogentity.png)

-   以滑鼠右鍵按一下設計介面上的新實體，然後選取 [**新增-&gt;** 純量屬性]，輸入**name**作為屬性的名稱。
-   重複此程式以新增**Url**屬性。
-   以滑鼠右鍵按一下設計介面上的  **Url**  屬性，然後選取 **屬性**，在 屬性視窗將**可為 null**的設定變更為  **True** 
     *，這可讓我們將 Blog 儲存到資料庫，而不需為其指派 Url*
-   使用您剛才學會的技術，新增具有**PostId**索引鍵屬性的**Post**實體
-   將**標題**和**內容**純量屬性加入至**Post**實體

既然我們有幾個實體，就可以在兩者之間新增關聯（或關係）。

-   以滑鼠右鍵按一下設計介面，然後選取 [**加入新的-&gt; 關聯**]。
-   將關聯性點的一端**設為** **1**的多重性，另一個結束點則以**許多**
     的多重性**張貼**，*這表示 blog 有許多貼文，而且貼文屬於一個 blog*
-   確定已核取 [**將外鍵屬性加入至 ' Post ' 實體**] 方塊，然後按一下 **[確定]**

    ![新增關聯 MF](~/ef6/media/addassociationmf.png)

我們現在有一個簡單的模型，可讓我們從中產生資料庫，並使用來讀取和寫入資料。

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

## <a name="3-generating-the-database"></a>3.產生資料庫

假設我們的模型，Entity Framework 可以計算資料庫架構，讓我們使用模型來儲存和抓取資料。

隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。
-   如果您使用 Visual Studio 2012，則您將會建立[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)資料庫。

讓我們繼續產生資料庫。

-   以滑鼠右鍵按一下設計介面，然後選取 [**從模型產生資料庫**...]。
-   按一下 [**新增連接 ...** ] 並指定 LocalDB 或 SQL Express，視您使用的 Visual Studio 版本而定，輸入**ModelFirst**做為資料庫名稱。

    ![LocalDB 連接 MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 連接 MF](~/ef6/media/sqlexpressconnectionmf.png)

-   選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**
-   選取 **[下一步]** ，Entity Framework Designer 將會計算腳本以建立資料庫架構
-   顯示腳本之後，按一下 **[完成]** ，腳本就會新增至您的專案並開啟
-   以滑鼠右鍵按一下腳本，然後選取 [**執行**]，系統會提示您指定要連接的資料庫、指定 LocalDB 或 SQL Server Express，視您使用的 Visual Studio 版本而定

## <a name="4-reading--writing-data"></a>4.讀取 & 寫入資料

既然我們已經有模型，就可以使用它來存取一些資料。 我們即將用來存取資料的類別會根據 EDMX 檔案自動產生。

*這個螢幕擷取畫面是來自 Visual Studio 2012，如果您使用 Visual Studio 2010，BloggingModel.tt 和 BloggingModel.CoNtext.tt 檔案就會直接在您的專案底下，而不是在 EDMX 檔案底下。*

![產生的類別](~/ef6/media/generatedclasses.png)

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

## <a name="5-dealing-with-model-changes"></a>5.處理模型變更

現在是時候對我們的模型進行一些變更，當我們進行這些變更時，我們也需要更新資料庫架構。

首先，我們會將新的使用者實體加入至模型。

-   加入新的**使用者**機構名稱，並將**Username**當做索引鍵名稱，並使用**字串**做為金鑰的屬性類型

    ![新增使用者實體](~/ef6/media/adduserentity.png)

-   以滑鼠右鍵按一下設計介面上的 [使用者**名稱**] 屬性，然後選取 [**屬性**]，在屬性視窗將**MaxLength**設定變更為**50**
    ，這會將*可儲存在使用者名稱的資料限制為50字元*
-   將**DisplayName**純量屬性加入至**使用者**實體

我們現在已有更新的模型，而且我們已準備好更新資料庫，以容納新的使用者實體類型。

-   以滑鼠右鍵按一下設計介面，然後選取 [**從模型產生資料庫**...]，Entity Framework 將會計算腳本，以根據更新的模型重新建立架構。
-   按一下 **[完成]**
-   您可能會收到有關覆寫現有 DDL 腳本的警告，以及模型的對應和儲存部分，請在這兩個警告中按一下 **[是]**
-   系統會為您開啟用來建立資料庫的已更新 SQL 腳本  
    產生的 @no__t 0The 腳本將會卸載所有現有的資料表，然後從頭開始重新建立架構。這可能適用于本機開發，但無法將變更推送至已部署的資料庫。如果您需要將變更發行至已部署的資料庫，您必須編輯腳本，或使用架構比較工具來計算遷移腳本。 *
-   以滑鼠右鍵按一下腳本，然後選取 [**執行**]，系統會提示您指定要連接的資料庫、指定 LocalDB 或 SQL Server Express，視您使用的 Visual Studio 版本而定

## <a name="summary"></a>總結

在本逐步解說中，我們探討了 Model First 開發，這讓我們能夠在 EF 設計工具中建立模型，然後從該模型產生資料庫。 然後，我們會使用此模型來讀取和寫入資料庫中的一些資料。 最後，我們已更新模型，然後重新建立資料庫架構以符合模型。
