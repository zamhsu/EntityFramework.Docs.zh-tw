---
title: 設計工具 TPH 繼承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
ms.openlocfilehash: 43ba34a98c3960a7a3052a00e2ed2751c2f2b121
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418424"
---
# <a name="designer-tph-inheritance"></a>設計工具 TPH 繼承
此逐步解說會示範如何使用 Entity Framework Designer （EF Designer），在概念模型中執行每個階層的資料表（TPH）繼承。 TPH 繼承會使用一個資料庫資料表來維護繼承階層架構中所有實體類型的資料。

在此逐步解說中，我們會將 Person 資料表對應至三個實體類型： Person （基底類型）、Student （衍生自 Person）和講師（衍生自 Person）。 我們將從資料庫建立概念模型（Database First），然後使用 EF 設計工具來改變模型，以執行 TPH 繼承。

您可以使用 Model First 對應到 TPH 繼承，但是您必須撰寫自己的資料庫產生工作流程，這是很複雜的。 接著，您會將此工作流程指派給 EF 設計工具中的 [**資料庫產生工作流程**] 屬性。 較簡單的替代方法是使用 Code First。

## <a name="other-inheritance-options"></a>其他繼承選項

每一類型的資料表（TPT）是另一種繼承類型，在其中，資料庫中的個別資料表會對應至參與繼承的實體。  如需如何使用 EF Designer 來對應每一類型的資料表繼承的相關資訊，請參閱[EF DESIGNER TPT 繼承](~/ef6/modeling/designer/inheritance/tpt.md)。

Entity Framework 執行時間支援單表類型繼承（TPC）和混合繼承模型，但 EF 設計工具並不支援。 如果您想要使用 TPC 或混合繼承，您有兩個選項： [使用 Code First] 或 [手動編輯 EDMX 檔案]。 如果您選擇使用 EDMX 檔案，[對應詳細資料] 視窗將會進入「安全模式」，而且您將無法使用設計工具來變更對應。

## <a name="prerequisites"></a>Prerequisites

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取檔案 **&gt; 新&gt; 專案**
-   在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台**] 範本。
-   在 [名稱] 中輸入 **TPHDBFirstSample** 。
-   選取 [確定] **** 。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管中的專案名稱，然後選取 [**新增-&gt; 新專案**]。
-   從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。
-   在 [檔案名] 中輸入**TPHModel** ，然後按一下 [**新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。
-   按一下 [ **新增連接**]。
    在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取 [ **Person** ] 資料表。
-   按一下 **[完成]** 。

會顯示 Entity Designer （提供編輯模型的設計介面）。 您在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都會加入至模型。

這就是**Person**資料表在資料庫中的外觀。

![Person 資料表](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>執行每個階層的資料表繼承

 **Person**資料表具有**鑒別**子資料行，它可以有兩個值的其中一個： "Student" 和 "講師"。 根據值， **Person**資料表會對應到**Student**實體或**講師**實體。  **Person**資料表也有兩個數據行，也就是 **雇用** 和 **EnrollmentDate**，這必須是可**為 null** ，因為一個人不能同時是學生和講師（至少在本逐步解說中）。

### <a name="add-new-entities"></a>加入新的實體

-   加入新的實體。
    若要這麼做，請以滑鼠右鍵按一下 Entity Framework Designer 設計介面的空白空間，然後選取 [**加入&gt;實體**]。
-   在  **機構名稱** 輸入 **講師** ，然後從  **基底類型** 的下拉式清單中選取  **Person** 。
-   按一下 **[確定]** 。
-   新增另一個新實體。 輸入 **Student** 作為 [ **機構名稱**] 然後從 [ **基底類型**] 的下拉式清單中選取 [ **Person** ]。

在設計介面中加入了兩個新的實體類型。 箭號會從新的實體類型指向 **Person** 實體類型;這表示 **Person** 是新實體類型的基底類型。

-   以滑鼠右鍵按一下 Person 實體的 [ **雇用** **者** ] 屬性。 選取 [ **剪**下] （或使用 Ctrl X 鍵）。
-   以滑鼠右鍵按一下 [ **講師** ] 實體，然後選取 [ **貼**上] （或使用 Ctrl + V 鍵）。
-   以滑鼠右鍵按一下 [ **雇用**日期 ] 屬性，然後選取 [ **屬性**]。
-   在 [ **屬性** ] 視窗中，將 **可為 null**的 屬性設定為 **false**。
-   以滑鼠右鍵按一下 **Person** 實體的 [ **EnrollmentDate** ] 屬性。 選取 [ **剪**下] （或使用 Ctrl X 鍵）。
-   以滑鼠右鍵按一下 [ **Student** ] 實體，然後選取 [貼上] **（或使用 Ctrl + V 鍵）。**
-   選取 [ **EnrollmentDate** ] 屬性，並將 **可為 null**的 屬性設定為 **false**。
-   選取 [ **Person** ] 實體類型。 在 [ **屬性** ] 視窗中，將其 [ **抽象** ] 屬性設為 [ **true**]。
-   刪除**Person**的**鑒別**子屬性。 下一節將說明應刪除的原因。

### <a name="map-the-entities"></a>對應實體

-   以滑鼠右鍵按一下 **講師**，然後選取 [**資料表對應]。**
    [對應詳細資料] 視窗中已選取講師實體。
-   在  **對應詳細資料** 視窗中，按一下  **&lt;新增資料表或 View&gt;**  。
     **&lt;加入資料表或視圖&gt;**   欄位會變成可對應所選取實體之資料表或視圖的下拉式清單。
-   從下拉式清單中選取 [ **Person** ]。
-    **對應詳細資料**  視窗會以預設資料行對應進行更新，以及新增條件的選項。
-   按一下 [ **&lt;新增條件&gt;** ]。
     **&lt;新增條件&gt;**   欄位會變成可設定條件的下拉式資料行清單。
-   從下拉式清單中選取 [ **鑒別**子 ]。
-   在 [ **對應詳細資料** ] 視窗的 [ **運算子** ] 欄位中，從下拉式清單中選取 [=]。
-   在 [ **值/屬性**] 資料行中，輸入 **講師**。 最後的結果應該如下所示：

    ![對應詳細資料](~/ef6/media/mappingdetails2.png)

-   針對 **student** 實體類型重複上述步驟，但讓條件等於**student**值。  
    *我們想要移除**鑒別**子屬性的原因，是因為您無法多次對應資料表資料行。此資料行會用於條件式對應，因此也無法用於屬性對應。如果條件使用 **Is null** 或 **Not null** 比較，則可用於兩者的唯一方法。*

現已實作每個階層的資料表繼承。

![最終的 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼貼入**Main**函式中。 程式碼會執行三個查詢。 第一個查詢會傳回所有**Person**物件。 第二個查詢會使用**OfType**方法來傳回**講師**物件。 第三個查詢使用**OfType**方法來傳回**Student**物件。

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
