---
title: 空間-EF 設計工具-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
ms.openlocfilehash: 04ba6e8d4a59816ca31e831b8e466cb1152a3d1b
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490186"
---
# <a name="spatial---ef-designer"></a>空間-EF 設計工具
> [!NOTE]
> **EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。 如果您使用的是較早版本，則不適用部分或全部的資訊。

影片以及逐步說明的逐步解說示範如何將使用 Entity Framework Designer 的空間型別對應。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

本逐步解說將使用 Model First 來建立新的資料庫，但 EF 設計工具也可搭配[Database First](~/ef6/modeling/designer/workflows/database-first.md)的工作流程，對應到現有的資料庫。

在 Entity Framework 5 中引進的空間型別支援。 請注意，若要使用的空間型別、 列舉、 等資料表值函式的新功能，您必須為目標.NET Framework 4.5。 Visual Studio 2012 的預設值為目標.NET 4.5。

若要使用的空間資料類型也必須使用有空間支援 Entity Framework 提供者。 請參閱[空間類型的提供者支援](~/ef6/fundamentals/providers/spatial-support.md)如需詳細資訊。

有兩種主要的空間資料類型： geography 和 geometry。 Geography 資料類型會儲存橢圓體資料 （例如 GPS 經緯度座標）。 Geometry 資料類型代表 Euclidean （平面） 座標系統。

## <a name="watch-the-video"></a>觀看影片
這段影片示範如何將使用 Entity Framework Designer 的空間型別對應。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

**由**: Julia Kornich

**視訊**: [WMV](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>必要條件

您必須擁有 Visual Studio 2012、 Ultimate、 Premium、 Professional 或 Web Express edition 安裝才能完成此逐步解說。

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio 2012
2.  在 **檔案**功能表上，指向**新增**，然後按一下 **專案**
3.  在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本
4.  請輸入**SpatialEFDesigner**做為名稱的專案，然後按一下 **[確定]**

## <a name="create-a-new-model-using-the-ef-designer"></a>建立新的模型使用 EF 設計工具

1.  以滑鼠右鍵按一下方案總管] 中的專案名稱，指向**新增**，然後按一下 [**新項目**
2.  選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中
3.  請輸入**UniversityModel.edmx**為檔案名稱，然後按一下**新增**
4.  在 Entity Data Model 精靈 頁面上，選取**空的模型**在選擇模型內容 對話方塊
5.  按一下 **完成**

Entity Designer 中，提供用於編輯模型的設計介面，會顯示。

精靈會執行下列動作：

-   產生 EnumTestModel.edmx 檔案定義概念模型、 儲存體模型和兩者之間的對應。 讓產生的中繼資料檔案取得內嵌於組件，請將.edmx 檔案的 Metadata Artifact Processing 屬性設定為內嵌於輸出組件中。
-   加入下列組件的參考： EntityFramework、 System.ComponentModel.DataAnnotations 和 System.Data.Entity。
-   建立 UniversityModel.tt 和 UniversityModel.Context.tt 檔案，並將它們加入在.edmx 檔案。 這些 T4 範本檔案產生的衍生 DbContext 類型和對應至.edmx 模型中實體的 POCO 型別定義的程式碼

## <a name="add-a-new-entity-type"></a>加入新的實體類型

1.  以滑鼠右鍵按一下設計介面上選取的空白處**新增-&gt;實體**，新的實體 對話方塊隨即出現
2.  指定**大學**類型的名稱，並指定**UniversityID**做為索引鍵屬性名稱，將保留的類型為**Int32**
3.  按一下 [確定] 
4.  以滑鼠右鍵按一下實體，然後選取**加入新-&gt;純量屬性**
5.  重新命名新的屬性到**名稱**
6.  新增另一個純量屬性，並重新命名為**位置**開啟 [屬性] 視窗，並變更新屬性的型別**地理位置**
7.  儲存模型，並建置專案
    > [!NOTE]
    > 當您建置時，未對應的實體和關聯有關的警告可能會出現在 錯誤清單。 您可以忽略這些警告，因為錯誤，我們選擇將透過模型產生資料庫之後，就會消失。

## <a name="generate-database-from-model"></a>從模型產生資料庫

現在我們可以產生以模型為基礎的資料庫。

1.  以滑鼠右鍵按一下 Entity designer 介面，然後選取空白空間**從模型產生資料庫**
2.  選擇您的資料連接對話方塊的 產生資料庫精靈 會顯示按一下**新的連線**按鈕指定 **(localdb)\\mssqllocaldb**伺服器名稱 和  **大學**資料庫，然後按一下  **確定**
3.  將快顯對話方塊，詢問您是否要建立新的資料庫中，按一下**是**。
4.  按一下 [**下一步]** 並建立資料庫產生的 DDL 會顯示在摘要和設定對話方塊方塊中請注意，DDL 不包含的定義，建立資料庫精靈會產生資料定義語言 (DDL)對應至列舉類型的資料表
5.  按一下 **完成**按一下 完成 將不會執行 DDL 指令碼。
6.  建立資料庫精靈會進行下列作業： 開啟**UniversityModel.edmx.sql** T-SQL 編輯器產生之 EDMX 的存放區結構描述和對應區段檔案中加入連接字串資訊加入 App.config 檔案
7.  按一下滑鼠右鍵在 T-SQL 編輯器中，然後選取**Execute** [連接到伺服器] 對話方塊出現時，請從步驟 2 輸入的連接資訊，然後按一下**Connect**
8.  若要檢視產生的結構描述，以滑鼠右鍵按一下 SQL Server 物件總管 中的資料庫名稱，然後選取**重新整理**

## <a name="persist-and-retrieve-data"></a>持續儲存並擷取資料

開啟 Program.cs 檔案的 Main 方法定義的位置。 將下列程式碼新增至 Main 函式。

程式碼會新增兩個新的大學物件內容。 空間的屬性會初始化使用 DbGeography.FromText 方法。 表示如 WellKnownText 傳遞給方法的地理點。 程式碼接著會將資料儲存。 然後，在 LINQ 查詢中傳回最接近指定的位置，其位置所在的大學物件建構及執行。

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

編譯並執行應用程式。 此程式會產生下列輸出：

```
The closest University to you is: School of Fine Art.
```

若要檢視資料庫中的資料，以滑鼠右鍵按一下 SQL Server 物件總管 中的資料庫名稱，然後選取**重新整理**。 然後，按一下資料表，然後選取滑鼠右鍵**檢視資料**。

## <a name="summary"></a>總結

在此逐步解說中，我們討論過如何將使用 Entity Framework 設計工具的空間型別對應，以及如何在程式碼中使用的空間類型。 
