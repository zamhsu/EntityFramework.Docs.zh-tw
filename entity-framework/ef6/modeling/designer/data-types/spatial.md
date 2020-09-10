---
title: 空間-EF Designer-EF6
description: 空間-Entity Framework 6 中的 EF 設計工具
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 10430d6a043182e5536dfad148dc4158cf4c96c9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620484"
---
# <a name="spatial---ef-designer"></a>空間-EF 設計工具
> [!NOTE]
> **EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

影片和逐步解說會示範如何使用 Entity Framework Designer 來對應空間類型。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

本逐步解說會使用 Model First 來建立新的資料庫，但 EF 設計工具也可以與 [Database First](xref:ef6/modeling/designer/workflows/database-first) 工作流程搭配使用，以對應至現有的資料庫。

Entity Framework 5 引進了空間類型支援。 請注意，若要使用空間類型、列舉和資料表值函式之類的新功能，您必須將目標設為 .NET Framework 4.5。 Visual Studio 2012 預設會以 .NET 4.5 為目標。

若要使用空間資料類型，您也必須使用具有空間支援的 Entity Framework 提供者。 如需詳細資訊，請參閱 [提供者的空間類型支援](xref:ef6/fundamentals/providers/spatial-support) 。

有兩個主要的空間資料類型： geography 和 geometry。 Geography 資料類型會儲存橢圓體資料 (例如，GPS 緯度和經度座標) 。 Geometry 資料類型代表 Euclidean (的平面) 座標系統。

## <a name="watch-the-video"></a>觀賞影片
這段影片示範如何使用 Entity Framework Designer 來對應空間類型。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

**提供者**： Julia Kornich

**影片**： [wmv](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)的  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>必要條件

您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio 2012
2.  **在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。
3.  在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本
4.  輸入 **SpatialEFDesigner** 做為專案的名稱，然後按一下 **[確定]**

## <a name="create-a-new-model-using-the-ef-designer"></a>使用 EF 設計工具建立新的模型

1.  在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [**加入**]，然後按一下 [**新增專案**]。
2.  從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]
3.  輸入**UniversityModel**的檔案名，然後按一下 [**新增**]。
4.  在實體資料模型 Wizard] 頁面的 [選擇模型內容] 對話方塊中，選取 [ **空的模型** ]。
5.  按一下 [完成]

隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。

精靈會執行下列動作：

-   產生定義概念模型、儲存體模型和兩者之間對應的 EnumTestModel .edmx 檔。 設定 .edmx 檔的中繼資料成品處理屬性，以內嵌在輸出元件中，以便將產生的中繼資料檔案內嵌至元件。
-   將參考加入至下列元件： EntityFramework、ComponentModel DataAnnotations 和 System.object。
-   建立 UniversityModel.tt 和 UniversityModel.CoNtext.tt 檔案，並將它們新增至 .edmx 檔案底下。 這些 T4 範本檔產生的程式碼會定義 DbCoNtext 衍生型別，以及對應至 .edmx 模型中之實體的 POCO 類型。

## <a name="add-a-new-entity-type"></a>加入新的實體類型

1.  以滑鼠右鍵按一下設計介面的空白區域，然後選取 [ **加入 &gt; 實體**]，[新增實體] 對話方塊隨即出現。
2.  指定類型名稱的 **大學** ，並為索引鍵屬性名稱指定 **UniversityID** ，將類型保留為 **Int32**
3.  按一下 [檔案] &gt; [新增] &gt; [專案] 
4.  以滑鼠右鍵按一下實體，然後選取 [**加入 &gt; 新的-** 純量屬性]
5.  將新屬性重新命名為 **Name**
6.  新增另一個純量屬性，並將它重新命名為 **Location** 開啟屬性視窗，並將新屬性的類型變更為 **Geography**
7.  儲存模型並建立專案
    > [!NOTE]
    > 當您建立時，有關未對應實體和關聯的警告可能會出現在 [錯誤清單] 中。 您可以忽略這些警告，因為在我們選擇從模型產生資料庫之後，錯誤就會消失。

## <a name="generate-database-from-model"></a>從模型產生資料庫

現在我們可以產生以模型為基礎的資料庫。

1.  以滑鼠右鍵按一下 Entity Designer 介面上的空白處，然後選取 [**從模型產生資料庫**]
2.  [產生資料庫嚮導] 的 [選擇您的資料連線] 對話方塊隨即顯示，請按一下 [**新增連接**] 按鈕，為資料庫的伺服器名稱和**大學**指定** (localdb) \\ Mssqllocaldb** ，然後按一下 **[確定]** 。
3.  對話方塊會詢問您是否要建立新的資料庫，請按一下 **[是**]。
4.  按一下 **[下一步** ]，[建立資料庫嚮導] 會產生資料定義語言 (DDL) 若要建立資料庫，則會在 [摘要和設定] 對話方塊中顯示產生的 DDL，請注意，ddl 不包含對應至列舉類型之資料表的定義
5.  按一下 **[** 完成]，然後按一下 [完成]，就不會執行 DDL 腳本。
6.  Create Database Wizard 會執行下列動作：在 T-sql 編輯器中開啟 **UniversityModel** 會產生儲存架構，而 edmx 檔案的對應區段會將連接字串資訊加入 App.config 檔案
7.  在 T-sql 編輯器中按一下滑鼠右鍵，然後選取 [**執行**連接至伺服器] 對話方塊，輸入步驟2中的連接資訊，然後按一下 **[連接]** 。
8.  若要查看產生的架構，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵，**然後選取 [** 重新整理]。

## <a name="persist-and-retrieve-data"></a>保存和取出資料

開啟定義 Main 方法的 Program.cs 檔案。 將下列程式碼新增至 Main 函數。

此程式碼會將兩個新的大學物件新增至內容中。 空間屬性會使用 DbGeography. Value.fromtext 方法來初始化。 以 WellKnownText 表示的地理位置點會傳遞給方法。 然後，程式碼會儲存資料。 然後，會建立並執行 LINQ 查詢，該查詢會傳回其位置最接近指定位置的大學物件。

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

```console
The closest University to you is: School of Fine Art.
```

若要在資料庫中查看資料，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵， **然後選取 [** 重新整理]。 然後，按一下資料表上的滑鼠右鍵，然後選取 [ **View Data**]。

## <a name="summary"></a>摘要

在這個逐步解說中，我們探討了如何使用 Entity Framework Designer 來對應空間類型，以及如何在程式碼中使用空間類型。 
