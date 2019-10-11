---
title: 空間 Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
ms.openlocfilehash: 018f480c1f0f1e74fc9f7a8950a6880e96f1facc
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182655"
---
# <a name="spatial---code-first"></a>空間-Code First
> [!NOTE]
> **EF5 僅限**，此頁面中所討論的功能、api 等內容是在 Entity Framework 5 中引進。 如果您使用的是較早版本，則不適用部分或全部的資訊。

影片和逐步解說會顯示如何使用 Entity Framework Code First 來對應空間類型。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

這個逐步解說將使用 Code First 建立新的資料庫，但是您也可以使用[現有資料庫的 Code First](~/ef6/modeling/code-first/workflows/existing-database.md)。

空間類型支援已于 Entity Framework 5 中引進。 請注意，若要使用空間類型、列舉和資料表值函式等新功能，您必須以 .NET Framework 4.5 為目標。 Visual Studio 2012 預設會以 .NET 4.5 為目標。

若要使用空間資料類型，您也必須使用具有空間支援的 Entity Framework 提供者。 如需詳細資訊，請參閱[提供者支援的空間類型](~/ef6/fundamentals/providers/spatial-support.md)。

主要的空間資料類型有兩種： geography 和 geometry。 Geography 資料類型會儲存橢圓體資料（例如，GPS 緯度和經度座標）。 Geometry 資料類型代表 Euclidean （平面）座標系統。

## <a name="watch-the-video"></a>觀看影片
這段影片示範如何使用 Entity Framework Code First 來對應空間類型。 它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。

**提供者**：Julia Kornich

**影片**：[WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)@NO__T[-1 個](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>先決條件

您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio 2012
2.  **在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]
3.  在左窗格中，按一下 [ **Visual C @ no__t-1**]，然後選取**主控台**範本
4.  輸入**SpatialCodeFirst**做為專案的名稱，然後按一下 **[確定]** 。

## <a name="define-a-new-model-using-code-first"></a>使用 Code First 定義新的模型

使用 Code First 開發時，您通常會從撰寫定義概念（網域）模型的 .NET Framework 類別開始著手。 下列程式碼會定義大學類別。

大學具有 DbGeography 類型的 Location 屬性。 若要使用 DbGeography 類型，您必須加入 system.string 元件的參考，並使用語句來新增 System.web 空間。

開啟 Program.cs 檔案，並在檔案頂端貼上下列 using 語句：

``` csharp
using System.Data.Spatial;
```

將下列大學類別定義新增至 Program.cs 檔案。

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a>定義 DbCoNtext 衍生類型

除了定義實體以外，您還需要定義衍生自 DbCoNtext 的類別，並公開 DbSet @ no__t-0TEntity @ no__t-1 屬性。 DbSet @ no__t-0TEntity @ no__t-1 屬性可讓內容知道您想要包含在模型中的類型。

DbCoNtext 衍生類型的實例會在運行時間管理實體物件，其中包括以資料庫的資料填入物件、變更追蹤，以及將資料保存至資料庫。

DbCoNtext 和 DbSet 類型定義于 EntityFramework 元件中。 我們會使用 EntityFramework NuGet 套件來新增對此 DLL 的參考。

1.  在方案總管中，以滑鼠右鍵按一下專案名稱。
2.  選取 [**管理 NuGet 套件 ...** ]
3.  在 [管理 NuGet 套件] 對話方塊中，選取 [**線上**] 索引標籤，然後選擇 [ **EntityFramework** ] 套件。
4.  按一下 [安裝]。

請注意，除了 EntityFramework 元件之外，也會新增 System.workflow.componentmodel.activity. DataAnnotations 元件的參考。

在 Program.cs 檔案的頂端，新增下列 using 語句：

``` csharp
using System.Data.Entity;
```

在 Program.cs 中，新增內容定義。 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>保存和取出資料

開啟定義 Main 方法的 Program.cs 檔案。 將下列程式碼新增至 Main 函式中。

程式碼會在內容中加入兩個新的大學物件。 空間屬性會使用 DbGeography. FromText 方法進行初始化。 以 WellKnownText 表示的 geography 點會傳遞至方法。 然後，程式碼會儲存資料。 然後，會建立並執行 LINQ 查詢，以傳回其位置最接近指定位置的大學物件。

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

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a>查看產生的資料庫

當您第一次執行應用程式時，Entity Framework 會為您建立資料庫。 因為我們已安裝 Visual Studio 2012，所以會在 LocalDB 實例上建立資料庫。 根據預設，Entity Framework 會在衍生內容的完整名稱後面加上資料庫名稱（在此範例中為**SpatialCodeFirst. UniversityCoNtext**）。 將使用現有資料庫的後續時間。  

請注意，如果您在建立資料庫之後對模型進行任何變更，您應該使用 Code First 移轉來更新資料庫架構。 如需使用遷移的範例，請參閱[Code First 至新的資料庫](~/ef6/modeling/code-first/workflows/new-database.md)。

若要查看資料庫和資料，請執行下列動作：

1.  在 [Visual Studio 2012] 主功能表中，選取 [ **View** - @ no__t-2 **SQL Server 物件總管**]。
2.  如果 LocalDB 不在伺服器清單中，請按一下**SQL Server**上的滑鼠右鍵，然後選取 [**新增] SQL Server**使用預設的 [ **Windows 驗證**] 來連接到 LocalDB 實例
3.  展開 LocalDB 節點
4.  展開 [**資料庫**] 資料夾以查看新的資料庫，並流覽至 [**大學**] 資料表
5.  若要查看資料，請以滑鼠右鍵按一下資料表，然後選取 [**查看資料**]

## <a name="summary"></a>總結

在本逐步解說中，我們探討了如何搭配使用空間類型與 Entity Framework Code First。 
