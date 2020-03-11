---
title: 設計工具資料表分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: f5e7532e6c0b473d8ce77cbd11e3e673b0af6cbe
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418165"
---
# <a name="designer-table-splitting"></a>設計工具資料表分割
本逐步解說示範如何使用 Entity Framework Designer （EF Designer）修改模型，將多個實體類型對應至單一資料表。

當您使用消極式載入來載入您的物件時，您可能會想要使用資料表分割來延遲載入部分屬性的其中一個原因。 您可以將可能包含非常大量資料的屬性區分為不同的實體，並只在需要時才載入。

下圖顯示使用 EF 設計工具時的主要視窗。

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Prerequisites

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

本逐步解說使用 Visual Studio 2012。

-   開啟 Visual Studio 2012。
-   在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。
-   在左窗格中，按一下 [Visual C\#]，然後選取 [主控台應用程式] 範本。
-   輸入**TableSplittingSample**做為專案的名稱，然後按一下 **[確定]** 。

## <a name="create-a-model-based-on-the-school-database"></a>建立以 School 資料庫為基礎的模型

-   以滑鼠右鍵按一下方案總管中的專案名稱，指向 [**加入**]，然後按一下 [**新增專案**]。
-   從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。
-   在 [檔案名] 中輸入**TableSplittingModel** ，然後按一下 [**新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]。**
-   按一下 [新增連接]。 在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊中，展開 [資料表 ] 節點，並檢查 [ **Person** **]** 資料表。 這會將指定的資料表新增至**School**模型。
-   按一下 **[完成]** 。

會顯示 Entity Designer （提供編輯模型的設計介面）。 您在 [ **選擇您的資料庫物件** ] 對話方塊中選取的所有物件都會加入至模型。

## <a name="map-two-entities-to-a-single-table"></a>將兩個實體對應至單一資料表

在本節中，您會將**Person**實體分割成兩個實體，然後將它們對應至單一資料表。

> [!NOTE]
> **Person**實體未包含任何可能包含大量資料的屬性;它只是用來作為範例。

-   以滑鼠右鍵按一下設計介面的空白區域，指向 [ **加入新**的]，然後按一下 [ **實體**]。
    [ **新增實體** ] 對話方塊隨即出現。
-   針對 [ **機構名稱**] 輸入 **HireInfo** ，然後針對索引**鍵屬性**名稱鍵入**PersonID** 。
-   按一下 **[確定]** 。
-   在設計介面上便會建立並顯示新的實體類型。
-   選取 **Person** 實體類型的 [ **雇用**者 ] 屬性，然後按**Ctrl + X**鍵。
-   選取 [ **HireInfo** ] 實體，然後按**Ctrl + V**鍵。
-   建立**Person**和**HireInfo**之間的關聯。 若要這樣做，請在設計介面的空白區域上按一下滑鼠右鍵，指向 [ **加入新**的]，然後按一下 [ **關聯**]。
-   [ **新增關聯** ] 對話方塊隨即出現。 預設會提供**PersonHireInfo**名稱。
-   在關聯性的兩端指定多重性**1 （一）** 。
-   按 [確定]。

下一個步驟需要  視窗的  **對應詳細資料**。 如果您看不到這個視窗，請以滑鼠右鍵按一下設計介面，然後選取 [**對應詳細資料**]。

-   選取 [ **HireInfo** ] 實體類型，然後按一下 [ **對應詳細資料** ] 視窗中的 [ **&lt;新增資料表或視圖]&gt;**  。
-   從  **&lt;新增資料表或視圖&gt;**  欄位 下拉式清單中，選取 **人員**。 此清單包含所選取實體可對應的資料表或視圖。
    根據預設，應該會對應適當的屬性。

    ![對應](~/ef6/media/mapping.png)

-   在設計介面上選取 [ **PersonHireInfo** ] 關聯。
-   在設計介面上以滑鼠右鍵按一下關聯，然後選取 [**屬性**]。
-   在 [**屬性**] 視窗中，選取 [**參考條件約束**] 屬性，然後按一下省略號按鈕。
-   從 [**主體**] 下拉式清單中選取 [**人員**]。
-   按 [確定]。

 

## <a name="use-the-model"></a>使用模型

-   在 Main 方法中貼上下列程式碼。

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

執行此應用程式後，會針對**School**資料庫執行下列 t-sql 語句。 

-   執行內容時，會執行下列**INSERT** 。SaveChanges （）和結合**Person**和**HireInfo**實體的資料

    ![插入](~/ef6/media/insert.png)

-   執行內容時，會執行下列**SELECT** 。FirstOrDefault （），並只選取對應到**Person**的資料行

    ![選取1](~/ef6/media/select1.png)

-   下列**SELECT**是由於存取導覽屬性 existingPerson 而執行，而且只會選取對應至**HireInfo**的資料行。

    ![選取2](~/ef6/media/select2.png)
