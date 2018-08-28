---
title: 設計工具的資料表分割-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: 87b6e1bd0374f77dfffab342c659cf4e16c8a337
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994499"
---
# <a name="designer-table-splitting"></a>分割的資料表設計工具
本逐步解說示範如何將多個實體類型對應至單一資料表中，透過修改模型使用 Entity Framework Designer （EF 設計工具）。

使用消極式載入來載入您的物件時，您可能想要使用資料表分割的其中一個原因延遲載入的某些屬性。 您可以分隔可能包含非常大量的資料分成個別的實體，並只載入時所需的屬性。

下圖顯示使用 EF 設計工具時所使用的主視窗。

![EFDesigner](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- 新版的 Visual Studio。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

本逐步解說使用 Visual Studio 2012。

-   開啟 Visual Studio 2012。
-   在 [檔案]  功能表中，指向 [新增] ，然後按一下 [專案] 。
-   在左窗格中，按一下 Visual C\#，然後選取 主控台應用程式範本。
-   請輸入**TableSplittingSample**做為名稱的專案，然後按一下**確定**。

## <a name="create-a-model-based-on-the-school-database"></a>建立 School 資料庫為基礎的模型

-   以滑鼠右鍵按一下方案總管 中的專案名稱，指向**新增**，然後按一下**新項目**。
-   選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。
-   請輸入**TableSplittingModel.edmx**為檔案名稱，然後按一下**新增**。
-   在 [選擇模型內容] 對話方塊中，選取**從資料庫產生**，然後按一下 [**下一步]。**
-   按一下 新增連接。 在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。
    [選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。
-   在 [選擇您的資料庫物件] 對話方塊中，展開**資料表**節點，並檢查**人員**資料表。 這會新增至指定的資料表**學校**模型。
-   按一下 [ **完成**]。

Entity Designer 中，提供用於編輯模型的設計介面，會顯示。 您在選取的所有物件**選擇您的資料庫物件**對話方塊加入至模型。

## <a name="map-two-entities-to-a-single-table"></a>將兩個實體對應至單一資料表

在本節中，您將分割**人員**成兩個實體的實體，然後將它們對應至單一資料表。

> [!NOTE]
> **人員**實體不包含任何可能包含大量資料的屬性; 它只會使用做為範例。

-   以滑鼠右鍵按一下設計介面的空白區域，指向**加入新**，然後按一下**實體**。
    **新的實體** 對話方塊隨即出現。
-   型別**HireInfo**的**實體名稱**並**PersonID**如**Key 屬性**名稱。
-   按一下 [確定 **Deploying Office Solutions**]。
-   在設計介面上便會建立並顯示新的實體類型。
-   選取  **HireDate**屬性**人員**實體類型，然後按**Ctrl + X**索引鍵。
-   選取  **HireInfo**實體，然後按**Ctrl + V**索引鍵。
-   建立之間的關聯**Person**並**HireInfo**。 若要這樣做，請以滑鼠右鍵按一下設計介面的空白區域，指向**加入新**，然後按一下**關聯**。
-   **新增關聯** 對話方塊隨即出現。 **PersonHireInfo**預設指定名稱。
-   指定多重性**1(One)** 關聯性的兩端。
-   按下**確定**。

下一個步驟需要**對應詳細資料**視窗。 如果看不到這個視窗，以滑鼠右鍵按一下設計介面，然後選取**對應詳細資料**。

-   選取  **HireInfo**實體類型，然後按一下**&lt;將資料表或檢視&gt;** 中**對應詳細資料**視窗。
-   選取  **Person**從**&lt;將資料表或檢視&gt;** 欄位下拉式清單。 清單包含資料表或檢視選取的實體可以對應。
    根據預設值，就應該對應適當的屬性。

    ![對應](~/ef6/media/mapping.png)

-   選取  **PersonHireInfo**關聯的設計介面上。
-   以滑鼠右鍵按一下設計介面，然後選取的關聯**屬性**。
-   在 **屬性**視窗中，選取**參考條件約束**屬性，然後按一下省略符號按鈕。
-   選取  **Person**從**主體**下拉式清單。
-   按下**確定**。

 

## <a name="use-the-model"></a>使用模型

-   在 Main 方法中，貼上下列程式碼。

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   編譯並執行應用程式。

針對執行下列 T-SQL 陳述式**學校**執行此應用程式最後的資料庫。 

-   下列**插入**由於執行內容而執行。Savechanges （） 及結合資料是從**Person**並**HireInfo**實體

    ![Insert](~/ef6/media/insert.png)

-   下列**選取**由於執行內容而執行。People.FirstOrDefault() 和選取的資料行對應至**人員**

    ![Select1](~/ef6/media/select1.png)

-   下列**選取 **因為存取導覽屬性 existingPerson.Instructor 執行，並選取 只是資料行對應至**HireInfo**

    ![Select2](~/ef6/media/select2.png)
