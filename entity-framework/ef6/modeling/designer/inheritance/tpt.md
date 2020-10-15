---
title: 設計工具 TPT 繼承-EF6
description: Entity Framework 6 中的設計工具 TPT 繼承
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: f8e4198278fb44309916d57b44fb9af41d572108
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066170"
---
# <a name="designer-tpt-inheritance"></a>設計工具 TPT 繼承
本逐步解說將示範如何使用 Entity Framework Designer (EF Designer) ，在您的模型中執行每一類型的資料表 (TPT) 繼承。 一類一表 (Table-Per-Type) 繼承會在資料庫中使用個別資料表來維護繼承階層架構 (Inheritance Hierarchy) 中每一個類型的非繼承屬性和索引鍵屬性。

在這個逐步解說中，我們會將**課程** (基底類型) 、 **OnlineCourse** (衍生自課程) ，而 **OnsiteCourse**   (衍生自**課程**) 實體到具有相同名稱的資料表。 我們會從資料庫建立模型，然後改變模型以執行 TPT 繼承。

您也可以從 Model First 開始，然後從模型產生資料庫。 EF Designer 預設會使用 TPT 策略，因此模型中的任何繼承都會對應到不同的資料表。

## <a name="other-inheritance-options"></a>其他繼承選項

每個階層的資料表 (TPH) 是另一種繼承類型，也就是使用一個資料庫資料表來維護繼承階層中所有實體類型的資料。如需有關如何使用 Entity Designer 來對應每個階層的資料表繼承的詳細資訊，請參閱 [EF DESIGNER TPH 繼承](xref:ef6/modeling/designer/inheritance/tph)。 

請注意， (TPC) 和混合式繼承模型的資料表個別具象型別繼承會受到 Entity Framework 執行時間支援，但 EF Designer 並不支援。 如果您想要使用 TPC 或混合的繼承，您有兩個選項： [使用 Code First] 或 [手動編輯 EDMX 檔案]。 如果您選擇使用 EDMX 檔案，[對應詳細資料] 視窗將會進入「安全模式」，而您將無法使用設計工具來變更對應。

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取檔案 **- &gt; 新增- &gt; 專案**
-   在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本。
-   輸入 **TPTDBFirstSample**   作為名稱。
-   選取 [確定] ****。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管中的專案，然後選取 [ **加入- &gt; 新增專案**]。
-   從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。
-   輸入 **TPTModel** 的檔案名，然後按一下 [ **新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [**   從資料庫產生**]，然後按 **[下一步]**。
-   按一下 [ **新增連接**]。
    在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如** (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School**   作為資料庫名稱，然後按一下 **[確定]**。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取 [ **部門**]、[ **課程]、[OnlineCourse] 和 [OnsiteCourse** ] 資料表。
-   按一下 **[完成]**。

隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。 在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都會加入至模型中。

## <a name="implement-table-per-type-inheritance"></a>執行每一類型的資料表繼承

-   在設計介面上，以滑鼠右鍵按一下 [ **OnlineCourse** ] 實體類型，然後選取 [ **屬性**]。
-   在 [ **屬性** ] 視窗中，將 [基底類型] 屬性設定為 [ **課程**]。
-   以滑鼠右鍵按一下 [ **OnsiteCourse** ] 實體類型，然後選取 [ **屬性**]。
-   在 [ **屬性** ] 視窗中，將 [基底類型] 屬性設定為 [ **課程**]。
-   以滑鼠右鍵按一下 [ **OnlineCourse** ] 和 [ **課程** ] 實體類型之間的關聯 (行) 。
    選取 [ **從模型刪除**]。
-   以滑鼠右鍵按一下 [ **OnsiteCourse** ] 和 [ **課程** ] 實體類型之間的關聯。
    選取 [ **從模型刪除**]。

我們現在將刪除**OnlineCourse**和**OnsiteCourse**中的**CourseID**屬性，因為這些類別會從**課程**基底類型繼承**CourseID** 。

-   以滑鼠右鍵按一下 [ **OnlineCourse** ] 實體類型的 [ **CourseID** ] 屬性，然後選取 [**從模型刪除**]。
-   以滑鼠右鍵按一下 [ **OnsiteCourse** ] 實體類型的 [ **CourseID** ] 屬性，然後選取 [**從模型刪除**]。
-   現已實作一類一表 (Table-Per-Type) 繼承。

![每一類型的資料表](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼貼入 **Main** 函數中。 程式碼會執行三個查詢。 第一個查詢會傳回與指定部門相關的所有 **課程** 。 第二個查詢會使用 **OfType** 方法來傳回與指定部門相關的 **OnlineCourses** 。 第三個查詢會傳回 **OnsiteCourses**。

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
