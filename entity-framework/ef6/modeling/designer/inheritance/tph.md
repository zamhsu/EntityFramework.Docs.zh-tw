---
title: 設計工具的 TPH 繼承-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
caps.latest.revision: 3
ms.openlocfilehash: 0a017d3b97808cede3134119940b2e5839d0f282
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120334"
---
# <a name="designer-tph-inheritance"></a>設計工具的 TPH 繼承
此逐步解說示範如何實作 Entity Framework Designer （EF 設計工具） 的概念模型中的每個階層的資料表 (TPH) 繼承。 TPH 繼承會使用一個資料庫資料表來維護繼承階層架構中的實體類型的所有資料。

在本逐步解說我們會將 Person 資料表對應至三個實體類型： Person （基底類型）、 （衍生自 Person） 的學生和講師 （衍生自 Person）。 我們將從資料庫 (Database First) 建立概念模型，並再來改變模型來實作 TPH 繼承的使用 EF 設計工具。

它是無法對應到使用 Model First TPH 繼承，但您必須撰寫您自己也就是複雜的資料庫產生工作流程。 然後，您就會指派此工作流程**資料庫產生工作流程**EF 設計工具中的屬性。 簡單的替代方法是使用 Code First。

## <a name="other-inheritance-options"></a>其他的繼承選項

每一類一表 (TPT) 是繼承的另一種類型所在資料庫中的個別資料表會對應到參與繼承的實體。  如需如何將對應表 (Table-per-type) 繼承與 EF 設計工具的資訊，請參閱[EF 設計工具 TPT 繼承](~/ef6/modeling/designer/inheritance/tpt.md)。

資料表每個實體類型的繼承 (TPC) 和混合的繼承模型支援 Entity Framework 執行階段，但不是會受到 EF 設計工具。 如果您想要使用的 TPC 或混合的繼承，您會有兩個選項： 使用 Code First，或以手動方式編輯 EDMX 檔。 如果您選擇使用 EDMX 檔案，對應的 [詳細資料] 視窗將會進入 「 安全模式 」，您將無法使用設計工具來變更對應。

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- 新版的 Visual Studio。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取 **檔案&gt;新增-&gt;專案**
-   在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。
-   請輸入**TPHDBFirstSample**做為名稱。
-   選取 [確定]。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管 中的專案名稱，然後選取**新增-&gt;新的項目**。
-   選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。
-   請輸入**TPHModel.edmx**為檔案名稱，然後按一下**新增**。
-   在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。
-   按一下 **新的連接**。
    在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。
    [選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。
-   在 [選擇您的資料庫物件] 對話方塊的 [資料表] 節點底下，選取**人員**資料表。
-   按一下 [ **完成**]。

Entity Designer 中，提供用於編輯模型的設計介面，會顯示。 您在 [選擇您的資料庫物件] 對話方塊中選取的所有物件都加入至模型。

也就是如何**人員**資料表會尋找資料庫中。

![PersonTable](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>實作每個階層的資料表繼承

**Person**資料表有**鑑別子**資料行，它可以有兩個值之一:"Student"和"Instructor"。 值而定**Person**資料表將會對應到**學生**實體或**講師**實體。 **Person**資料表也有兩個資料行， **HireDate**並**EnrollmentDate**，這必須是**可為 null**因為人員不能是student 和 instructor 一次 （至少不在本逐步解說）。

### <a name="add-new-entities"></a>加入新的實體

-   加入新的實體。
    若要這樣做，請在 Entity Framework Designer 中，設計介面的空白處上按一下滑鼠右鍵，然後選取**Add-&gt;實體**。
-   型別**講師**for**實體名稱**，然後選取**人員**從下拉式清單**基底型別**。
-   按一下 [確定 **Deploying Office Solutions**]。
-   加入另一個新的實體。 型別**學生**如**實體名稱**，然後選取**人員**的下拉式清單從**基底型別**。

兩個新的實體類型已新增至設計介面。 箭號指向新的實體類型，以從**Person**實體類型，這表示**人員**是新的實體類型的基底類型。

-   以滑鼠右鍵按一下**HireDate**屬性**人員**實體。 選取 **剪下**（或使用 Ctrl-X 鍵）。
-   以滑鼠右鍵按一下**講師**實體，然後選取**貼上**（或使用 Ctrl-V 鍵）。
-   以滑鼠右鍵按一下**HireDate**屬性，並選取**屬性**。
-   在 **屬性**視窗中，將**Nullable**屬性設**false**。
-   以滑鼠右鍵按一下**EnrollmentDate**屬性**人員**實體。 選取 **剪下**（或使用 Ctrl-X 鍵）。
-   以滑鼠右鍵按一下**學生**實體，然後選取**貼上 （或使用 Ctrl-V 鍵）。**
-   選取  **EnrollmentDate**屬性並設定**Nullable**屬性設**false**。
-   選取 **人員**實體類型。 在 **屬性**視窗中，將其**抽象**屬性設 **，則為 true**。
-   刪除**鑑別子**屬性從**人員**。 應刪除它的原因是下列一節所述。

### <a name="map-the-entities"></a>對應實體

-   以滑鼠右鍵按一下**講師**，然後選取**資料表對應。**
    對應詳細資料視窗中選取 [Instructor] 實體。
-   按一下  **&lt;新增資料表或檢視表&gt;** 中**對應詳細資料**視窗。
    **&lt;新增資料表或檢視表&gt;** 欄位會變成下拉式清單的資料表或檢視選取的實體可以對應。
-   選取 **人員**從下拉式清單。
-   **對應詳細資料**視窗會更新成預設資料行對應及加入條件的選項。
-   按一下  **&lt;新增條件&gt;**。
    **&lt;新增條件&gt;** 欄位會變成可以設定條件的資料行的下拉式清單。
-   選取 **鑑別子**從下拉式清單。
-   在 **運算子**資料行**對應詳細資料**視窗中，選取 = 從下拉式清單。
-   在 **值/屬性**資料行中輸入**講師**。 最後的結果應該如下所示：

    ![MappingDetails2](~/ef6/media/mappingdetails2.png)

-   重複這些步驟**學生**實體類型，但請相等條件**學生**值。  
    *我們想要移除的原因**鑑別子**屬性，是因為您不能超過一次對應資料表資料行。本專欄將用於條件式對應，因此無法用於屬性以及對應。它可以用於兩者，如果條件使用的唯一方式**Is Null**或是**Is Not Null**比較。*

現已實作每個階層的資料表繼承。

![FinalTPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>使用模型

開啟**Program.cs**檔**Main**方法定義。 貼上下列程式碼插入**Main**函式。 三個查詢執行的程式碼。 第一個查詢會傳回所有**人員**物件。 第二個查詢會使用**OfType**方法，以傳回**講師**物件。 第三個查詢會使用**OfType**方法，以傳回**學生**物件。

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
