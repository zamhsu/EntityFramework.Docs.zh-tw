---
title: 空間-Code First-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
caps.latest.revision: 3
ms.openlocfilehash: 03557820bc689d8e7389a1f84121b7eeaa904df1
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120478"
---
# <a name="spatial---code-first"></a>空間-Code First
> [!NOTE]
> **EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。 如果您使用的是較早版本，則不適用部分或全部的資訊。

影片以及逐步說明的逐步解說示範如何將使用 Entity Framework Code First 的空間型別對應。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

本逐步解說將使用 Code First 來建立新的資料庫，但您也可以使用[Code First 至現有的資料庫](~/ef6/modeling/code-first/workflows/existing-database.md)。

在 Entity Framework 5 中引進的空間型別支援。 請注意，若要使用的空間型別、 列舉、 等資料表值函式的新功能，您必須為目標.NET Framework 4.5。 Visual Studio 2012 的預設值為目標.NET 4.5。

若要使用的空間資料類型也必須使用有空間支援 Entity Framework 提供者。 請參閱[空間類型的提供者支援](~/ef6/fundamentals/providers/spatial-support.md)如需詳細資訊。

有兩種主要的空間資料類型： geography 和 geometry。 Geography 資料類型會儲存橢圓體資料 （例如 GPS 經緯度座標）。 Geometry 資料類型代表 Euclidean （平面） 座標系統。

## <a name="watch-the-video"></a>觀看影片
這段影片示範如何將使用 Entity Framework Code First 的空間型別對應。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

**由**: Julia Kornich

**視訊**: [WMV](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>必要條件

您必須擁有 Visual Studio 2012、 Ultimate、 Premium、 Professional 或 Web Express edition 安裝才能完成此逐步解說。

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio 2012
2.  在 **檔案**功能表上，指向**新增**，然後按一下 **專案**
3.  在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本
4.  請輸入**SpatialCodeFirst**做為名稱的專案，然後按一下 **[確定]**

## <a name="define-a-new-model-using-code-first"></a>定義新的模型使用 Code First

使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。 下列程式碼定義 University 類別。

該大學有 DbGeography 類型的 Location 屬性。 若要使用的 DbGeography 型別，您必須加入 System.Data.Entity 組件的參考，而且也加入 System.Data.Spatial using 陳述式。

開啟 Program.cs 檔案並貼上下列 using 陳述式在檔案頂端：

``` csharp
using System.Data.Spatial;
```

Program.cs 檔案中加入下列的大學類別定義。

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a>定義 DbContext 衍生型別

除了定義實體，您需要定義類別，衍生自 DbContext，並公開 DbSet&lt;TEntity&gt;屬性。 DbSet&lt;TEntity&gt;屬性可讓知道您想要在模型中包含哪些的類型的內容。

DbContext 衍生型別的執行個體在執行階段，其中包含填入資料庫的資料的物件期間同時管理實體物件、 變更追蹤，和保存資料至資料庫。

EntityFramework 組件中定義的 DbContext 和 DbSet 型別。 我們將使用 EntityFramework NuGet 套件，以新增此 DLL 的參考。

1.  在 [方案總管] 中，以滑鼠右鍵按一下專案名稱。
2.  選取**管理 NuGet 封裝...**
3.  在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝。
4.  按一下 **安裝**

請注意，除了 EntityFramework 組件 System.ComponentModel.DataAnnotations 組件的參考也會加入。

在 Program.cs 檔案頂端，新增下列 using 陳述式：

``` csharp
using System.Data.Entity;
```

在 Program.cs 中新增內容定義。 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>持續儲存並擷取資料

開啟 Program.cs 檔案的 Main 方法定義的位置。 將下列程式碼新增至 Main 函式。

程式碼會新增兩個新的大學物件內容。 空間的屬性會初始化使用 DbGeography.FromText 方法。 表示如 WellKnownText 傳遞給方法的地理點。 程式碼接著會將資料儲存。 然後，在 LINQ 查詢中傳回最接近指定的位置，其位置所在的大學物件建構及執行。

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
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

## <a name="view-the-generated-database"></a>檢視所產生的資料庫

當您執行應用程式第一次時，Entity Framework 會為您建立資料庫。 因為我們沒有安裝 Visual Studio 2012，則會在 LocalDB 執行個體上建立資料庫。 根據預設，Entity Framework 資料庫的名稱衍生內容的完整名稱 (在此範例中是**SpatialCodeFirst.UniversityContext**)。 後續的時間，將會使用現有的資料庫。  

請注意，是否您的模型建立資料庫之後，進行任何變更，您應該使用 Code First 移轉來更新資料庫結構描述。 請參閱[Code First 至新的資料庫](~/ef6/modeling/code-first/workflows/new-database.md)如使用移轉的範例。

若要檢視的資料庫和資料，請執行下列作業：

1.  在 Visual Studio 2012 主功能表中，選取**檢視** - &gt; **SQL Server 物件總管**。
2.  如果 LocalDB 不在伺服器清單中，按一下滑鼠右鍵**SQL Server** ，然後選取**加入 SQL Server**使用預設值**Windows 驗證**連接到LocalDB 執行個體
3.  展開 [LocalDB] 節點
4.  Unfold**資料庫**查看新的資料庫，並瀏覽至資料夾**大學**資料表
5.  若要檢視資料，以滑鼠右鍵按一下資料表並選取**檢視資料**

## <a name="summary"></a>總結

在此逐步解說中，我們討論過如何使用 Entity Framework Code First 使用空間的類型。 
