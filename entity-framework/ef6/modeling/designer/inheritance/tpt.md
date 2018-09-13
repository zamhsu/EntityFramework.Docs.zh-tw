---
title: 設計工具的 TPT 繼承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
ms.openlocfilehash: 84330fba4807620aa242a70cd8ac76a60284416d
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489449"
---
# <a name="designer-tpt-inheritance"></a>設計工具的 TPT 繼承
此逐步解說示範如何使用 Entity Framework Designer （EF 設計工具） 在模型中實作每一類一表 (TPT) 繼承。 一類一表 (Table-Per-Type) 繼承會在資料庫中使用個別資料表來維護繼承階層架構 (Inheritance Hierarchy) 中每一個類型的非繼承屬性和索引鍵屬性。

我們將在本逐步解說對應**課程**（基底型別）， **OnlineCourse** （衍生自課程），並**OnsiteCourse** (衍生自**課程**)具有相同名稱的資料表實體。 我們將從資料庫建立模型，並再改變實作 TPT 繼承模型。

您可以也使用 Model First 啟動，並接著從模型產生資料庫。 EF 設計工具預設會使用 TPT 策略，因此任何繼承模型中的將會對應至個別資料表。

## <a name="other-inheritance-options"></a>其他的繼承選項

每個階層的資料表 (TPH) 是繼承的另一種類型中哪一個資料庫資料表繼承的用來維護繼承階層架構中的實體類型的所有資料。  如需如何將對應 Entity Designer 中的每個階層的資料表繼承資訊，請參閱[EF 設計工具 TPH 繼承](~/ef6/modeling/designer/inheritance/tph.md)。 

請注意，每個實體資料表類型 (TPC) 繼承和混合的繼承模型支援 Entity Framework 執行階段，但不是會受到 EF 設計工具。 如果您想要使用的 TPC 或混合的繼承，您會有兩個選項： 使用 Code First，或以手動方式編輯 EDMX 檔。 如果您選擇使用 EDMX 檔案，對應的 [詳細資料] 視窗將會進入 「 安全模式 」，您將無法使用設計工具來變更對應。

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- 新版的 Visual Studio。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取 **檔案&gt;新增-&gt;專案**
-   在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。
-   請輸入**TPTDBFirstSample**做為名稱。
-   選取 [確定]。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管 中的專案，然後選取**新增-&gt;新的項目**。
-   選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。
-   請輸入**TPTModel.edmx**為檔案名稱，然後按一下**新增**。
-   在 [選擇模型內容] 對話方塊中，選取 [產生資料庫 * * 從然後**下一步]**。
-   按一下 **新的連接**。
    在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。
    [選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。
-   在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取**部門**，**課程、 OnlineCourse 和 OnsiteCourse**資料表。
-   按一下 [ **完成**]。

Entity Designer 中，提供用於編輯模型的設計介面，會顯示。 您在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都加入至模型。

## <a name="implement-table-per-type-inheritance"></a>實作單表 (Table-per-type) 繼承

-   在設計介面中，以滑鼠右鍵按一下**OnlineCourse**實體類型，然後選取**屬性**。
-   在 [**屬性**] 視窗中，會將基底類型屬性設定為**課程**。
-   以滑鼠右鍵按一下**OnsiteCourse**實體類型，然後選取**屬性**。
-   在 [**屬性**] 視窗中，會將基底類型屬性設定為**課程**。
-   以滑鼠右鍵按一下之間的關聯 （列） **OnlineCourse**並**課程**實體類型。
    選取 **從模型刪除**。
-   以滑鼠右鍵按一下之間的關聯**OnsiteCourse**並**課程**實體類型。
    選取 **從模型刪除**。

我們現在將會刪除**CourseID**屬性從**OnlineCourse**並**OnsiteCourse**因為這些類別會繼承**CourseID**從**課程**基底型別。

-   以滑鼠右鍵按一下**CourseID**屬性**OnlineCourse**實體類型，然後選取**從模型刪除**。
-   以滑鼠右鍵按一下**CourseID**屬性**OnsiteCourse**實體類型，然後選取**從模型刪除**
-   現已實作一類一表 (Table-Per-Type) 繼承。

![TPT](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>使用模型

開啟**Program.cs**檔**Main**方法定義。 貼上下列程式碼插入**Main**函式。 三個查詢執行的程式碼。 第一個查詢會傳回所有**課程**指定部門產生關聯。 第二個查詢會使用**OfType**方法，以傳回**OnlineCourses**指定部門產生關聯。 第三個查詢會傳回**OnsiteCourses**。

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
