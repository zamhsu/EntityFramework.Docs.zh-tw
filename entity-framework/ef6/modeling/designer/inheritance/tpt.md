---
title: 設計工具 TPT 繼承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
ms.openlocfilehash: 84330fba4807620aa242a70cd8ac76a60284416d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418207"
---
# <a name="designer-tpt-inheritance"></a>設計工具 TPT 繼承
此逐步解說會示範如何使用 Entity Framework Designer （EF Designer），在您的模型中執行每一類型的資料表（TPT）繼承。 一類一表 (Table-Per-Type) 繼承會在資料庫中使用個別資料表來維護繼承階層架構 (Inheritance Hierarchy) 中每一個類型的非繼承屬性和索引鍵屬性。

在此逐步解說中，我們會將**課程**（基底類型）、 **OnlineCourse** （衍生自課程）和 **OnsiteCourse** （衍生自「**課程**」）實體對應至具有相同名稱的資料表。 我們將從資料庫建立模型，然後改變模型以執行 TPT 繼承。

您也可以從 Model First 開始，然後從模型產生資料庫。 EF 設計工具預設會使用 TPT 策略，因此模型中的任何繼承都會對應到不同的資料表。

## <a name="other-inheritance-options"></a>其他繼承選項

每個階層的資料表（TPH）是另一種繼承類型，其中一個資料庫資料表用來維護繼承階層架構中所有實體類型的資料。  如需如何使用 Entity Designer 來對應每個階層的資料表繼承的詳細資訊，請參閱[EF DESIGNER TPH 繼承](~/ef6/modeling/designer/inheritance/tph.md)。 

請注意，Entity Framework 執行時間支援每個具體的資料表型別繼承（TPC）和混合繼承模型，但是 EF 設計工具不支援。 如果您想要使用 TPC 或混合繼承，您有兩個選項： [使用 Code First] 或 [手動編輯 EDMX 檔案]。 如果您選擇使用 EDMX 檔案，[對應詳細資料] 視窗將會進入「安全模式」，而且您將無法使用設計工具來變更對應。

## <a name="prerequisites"></a>Prerequisites

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取檔案 **&gt; 新&gt; 專案**
-   在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台**] 範本。
-   在 [名稱] 中輸入 **TPTDBFirstSample** 。
-   選取 [確定] **** 。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管中的專案，然後選取 [**新增-&gt; 新專案**]。
-   從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。
-   在 [檔案名] 中輸入**TPTModel** ，然後按一下 [**新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ ** 從資料庫產生**]，然後按 **[下一步]** 。
-   按一下 [ **新增連接**]。
    在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取 [ **部門**]、[**課程]、[OnlineCourse] 和 [OnsiteCourse** ] 資料表。
-   按一下 **[完成]** 。

會顯示 Entity Designer （提供編輯模型的設計介面）。 您在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都會加入至模型。

## <a name="implement-table-per-type-inheritance"></a>執行每一類型的資料表繼承

-   在設計介面上，以滑鼠右鍵按一下 [ **OnlineCourse** ] 實體類型，然後選取 [**屬性**]。
-   在 [**屬性**] 視窗中，將 [基底類型] 屬性設定為 [**課程**]。
-   以滑鼠右鍵按一下 [ **OnsiteCourse** ] 實體類型，然後選取 [**屬性**]。
-   在 [**屬性**] 視窗中，將 [基底類型] 屬性設定為 [**課程**]。
-   以滑鼠右鍵按一下 [ **OnlineCourse** ] 和 [**課程**] 實體類型之間的關聯（這一行）。
    選取 [**從模型刪除**]。
-   以滑鼠右鍵按一下 [ **OnsiteCourse** ] 和 [**課程**] 實體類型之間的關聯。
    選取 [**從模型刪除**]。

我們現在會從**OnlineCourse**和**OnsiteCourse**中刪除**CourseID**屬性，因為這些類別會從**課程**基底類型繼承**CourseID** 。

-   以滑鼠右鍵按一下**OnlineCourse**實體類型的 [ **CourseID** ] 屬性，然後選取 [**從模型刪除**]。
-   以滑鼠右鍵按一下**OnsiteCourse**實體類型的 [ **CourseID** ] 屬性，然後選取 [**從模型刪除**]
-   現已實作一類一表 (Table-Per-Type) 繼承。

![TPT](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼貼入**Main**函式中。 程式碼會執行三個查詢。 第一個查詢會傳回與指定之部門相關的所有**課程**。 第二個查詢會使用**OfType**方法來傳回與指定之部門相關的**OnlineCourses** 。 第三個查詢會傳回**OnsiteCourses**。

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
