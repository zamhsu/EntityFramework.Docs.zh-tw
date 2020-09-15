---
title: 設計工具 TPH 繼承-EF6
description: Entity Framework 6 中的設計工具 TPH 繼承
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: 39675f9533dfef0ddad1867e3b70cd13b30708ea
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073392"
---
# <a name="designer-tph-inheritance"></a>設計工具 TPH 繼承
本逐步解說將示範如何使用 Entity Framework Designer (EF Designer) ，在概念模型中執行每個階層的資料表 (TPH) 繼承。 TPH 繼承使用一個資料庫資料表來維護繼承階層中所有實體類型的資料。

在這個逐步解說中，我們會將 Person 資料表對應至三個實體類型： Person (基底類型) 、Student (衍生自 Person) ，而講師 (衍生自 Person) 。 我們會從資料庫 (Database First) 建立概念模型，然後使用 EF 設計工具來改變模型，以實行 TPH 繼承。

您可以使用 Model First 對應至 TPH 繼承，但您必須撰寫自己的資料庫產生工作流程，這是很複雜的工作流程。 然後，您會將此工作流程指派給 EF 設計工具中的 **資料庫產生工作流程** 屬性。 更簡單的替代方法是使用 Code First。

## <a name="other-inheritance-options"></a>其他繼承選項

每一類型的資料表 (TPT) 是另一種繼承類型，也就是資料庫中的個別資料表對應至參與繼承的實體。 如需有關如何使用 EF 設計工具對應每個類型的資料表繼承的詳細資訊，請參閱 [Ef 設計工具 TPT 繼承](xref:ef6/modeling/designer/inheritance/tpt)。

Entity Framework 執行時間支援 (TPC) 和混合式繼承模型的資料表個別具象類型繼承，但 EF 設計工具並不支援。 如果您想要使用 TPC 或混合的繼承，您有兩個選項： [使用 Code First] 或 [手動編輯 EDMX 檔案]。 如果您選擇使用 EDMX 檔案，[對應詳細資料] 視窗將會進入「安全模式」，而您將無法使用設計工具來變更對應。

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取檔案 **- &gt; 新增- &gt; 專案**
-   在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本。
-   輸入 **TPHDBFirstSample**   作為名稱。
-   選取 [確定] ****。

## <a name="create-a-model"></a>建立模型

-   在方案總管中的專案名稱上按一下滑鼠右鍵，然後選取 [ **加入- &gt; 新增專案**]。
-   從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。
-   輸入 **TPHModel** 的檔案名，然後按一下 [ **新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]**。
-   按一下 [ **新增連接**]。
    在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如** (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School**   作為資料庫名稱，然後按一下 **[確定]**。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取 [ **Person** ] 資料表。
-   按一下 **[完成]**。

隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。 在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都會加入至模型中。

這就是 **Person** 資料表在資料庫中的外觀。

![Person 資料表](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>執行每個階層的資料表繼承

 **Person**資料表有**鑒別**子資料行，它可以有兩個值的其中一個： "Student" 和 "講師"。 取決於 **Person** 資料表將對應至 **Student** 實體或 **講師** 實體的值。  **Person**資料表也有兩個數據行 **：日期**時間   和 **EnrollmentDate**，這些資料行必須是可**為 null**的，因為某位人員不能同時是學生和講師， (至少不是本逐步解說) 。

### <a name="add-new-entities"></a>新增實體

-   加入新的實體。
    若要這樣做，請以滑鼠右鍵按一下 Entity Framework Designer 之設計介面的空白空間，然後選取 [ **新增 &gt; 實體**]。
-   輸入 [ **講師**]   作為 [ **機構名稱**]   ，然後從 [ ** **    **基底類型**] 的下拉式清單中選取 [人員]。
-   按一下 **[確定]**。
-   新增另一個新的實體。 輸入 **Student**   作為 [ **機構名稱**]   ， **Person**然後   從 [ **基底類型**] 的下拉式清單中選取 [人員]。

有兩個新的實體類型加入至設計介面。 箭號會從新的實體類型指向 **Person**   實體類型，這表示 **person**   是新實體類型的基底類型。

-   以滑鼠右鍵按一下 Person 實體的 [ **雇用**日期]   屬性 **Person**   。 選取 [ **剪** 下] (或使用 Ctrl + X 鍵) 。
-   以滑鼠右鍵按一下 **講師**   實體，然後選取 [ **貼**上 (或使用 Ctrl + V 鍵) 。
-   以滑鼠右鍵按一下 [ **雇用**日期]   屬性，然後選取 [ **屬性**]。
-   在 [ **屬性**   ] 視窗中，將 [ **可為 null**]   屬性設定為 **false**。
-   以滑鼠右鍵按一下 [Person] 實體的 [ **EnrollmentDate**]   屬性 **Person**   。 選取 [ **剪** 下] (或使用 Ctrl + X 鍵) 。
-   以滑鼠右鍵按一下 **Student** 實體，然後選取 **[貼上 (或使用 Ctrl + V 鍵) 。**
-   選取 **EnrollmentDate**   屬性，並將 **可為 null**的   屬性設定為 **false**。
-   選取 [ **Person**]   實體類型。 在 [ **屬性**   ] 視窗中，將其 [ **抽象**]   屬性設定為 [ **true**]。
-   刪除**Person**的**鑒別**子屬性。 下一節將說明應該刪除它的原因。

### <a name="map-the-entities"></a>對應實體

-   以滑鼠右鍵按一下 **講師** ，然後選取 [ **資料表對應]。**
    在 [對應詳細資料] 視窗中選取了講師實體。
-   在 [對應詳細資料] 視窗中，按一下 [ ** &lt; 加入資料表或視圖 &gt; **    **Mapping Details**   ]。
    [ ** &lt; 加入資料表或 &gt; 視圖**   ] 欄位會成為選取的實體可以對應之資料表或視圖的下拉式清單。
-    **Person**   從下拉式清單中選取 [人員]。
-   [ **對應詳細資料**   ] 視窗會更新成預設資料行對應，以及新增條件的選項。
-   按一下 [ ** &lt; 新增條件 &gt; **]。
    [ ** &lt; 加入條件 &gt; **]   欄位會變成可設定條件之資料行的下拉式清單。
-    **Discriminator**   從下拉式清單中選取 [鑒別子]。
-   在 **Operator**   [ **對應詳細資料**] 視窗的 [運算子] 資料行中   ，從下拉式清單中選取 [=]。
-   在 [ **值/屬性** ] 資料行中，輸入「 **講師**」。 最終結果看起來應該像這樣：

    ![對應詳細資料](~/ef6/media/mappingdetails2.png)

-   針對 **student**   實體類型重複這些步驟，但讓條件等於**學生**的價值。  
    *我們想要移除**鑒別**子屬性的原因是因為您無法將資料表資料行對應到一次以上。這個資料行會用於條件式對應，因此也無法用於屬性對應。如果條件使用的 **是 null**   或 **不是 null 的**比較，則這兩種方法都可以使用它   。*

現已實作每個階層的資料表繼承。

![最終的 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼貼入 **Main** 函數中。 程式碼會執行三個查詢。 第一個查詢會傳回所有 **Person** 物件。 第二個查詢會使用 **OfType** 方法來傳回 **講師** 物件。 第三個查詢會使用 **OfType** 方法來傳回 **Student** 物件。

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
