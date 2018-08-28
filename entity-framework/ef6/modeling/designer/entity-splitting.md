---
title: 設計工具的實體分割-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: 214561f0a0381bced3ceae0b6acfcd45f5dd65c5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995615"
---
# <a name="designer-entity-splitting"></a>分割的實體設計工具
本逐步解說示範如何透過修改模型使用 Entity Framework Designer （EF 設計工具），將實體類型對應至兩個資料表。 當多個資料表共用共同的索引鍵時，您可以將實體對應至多個資料表。 適用於將實體類型對應至兩個資料表的概念，很容易擴大為將實體類型對應至兩個以上的資料表。

下圖顯示使用 EF 設計工具時所使用的主視窗。

![EFDesigner](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>必要條件

Visual Studio 2012 或 Visual Studio 2010、 Ultimate、 Premium、 Professional 或 Web Express edition。

## <a name="create-the-database"></a>建立資料庫

Visual Studio 隨附安裝的資料庫伺服器是您已安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2012，則您將會建立新的 LocalDB 資料庫。
-   如果您使用 Visual Studio 2010 將會建立 SQL Express 資料庫。

第一次我們將建立一個資料庫具有兩個資料表，我們要結合成單一的實體。

-   開啟 Visual Studio
-   **檢視-&gt;伺服器總管**
-   以滑鼠右鍵按一下**資料連線-&gt;新增連接...**
-   如果您尚未連線至資料庫從伺服器總管之前您必須選取**Microsoft SQL Server**做為資料來源
-   連接到 LocalDB 或 SQL Express，何者而定，您已安裝
-   請輸入**EntitySplitting**做為資料庫名稱
-   選取  **確定**而且會要求您想要建立新資料庫，請選取**是**
-   在 伺服器總管現在會顯示新的資料庫
-   如果您使用 Visual Studio 2012
    -   以滑鼠右鍵按一下伺服器總管 中的資料庫，然後選取**新查詢**
    -   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**Execute**
-   如果您使用 Visual Studio 2010
    -   選取 **資料集&gt;Transact SQL 編輯器-&gt;新增查詢連接...**
    -   請輸入 **。\\SQLEXPRESS**作為伺服器名稱，然後按一下 **[確定]**
    -   選取  **EntitySplitting**資料庫從下拉式清單頂端的查詢編輯器
    -   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取**執行 SQL**

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a>建立專案

-   在 [檔案]  功能表中，指向 [新增] ，然後按一下 [專案] 。
-   在左窗格中，按一下**Visual C\#**，然後選取**主控台應用程式**範本。
-   請輸入**MapEntityToTablesSample**做為名稱的專案，然後按一下**確定**。
-   按一下  **No**如果系統提示您儲存在第一節中建立的 SQL 查詢。

## <a name="create-a-model-based-on-the-database"></a>建立資料庫為基礎的模型

-   以滑鼠右鍵按一下方案總管 中的專案名稱，指向**新增**，然後按一下**新項目**。
-   選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。
-   請輸入**MapEntityToTablesModel.edmx**為檔案名稱，然後按一下**新增**。
-   在 [選擇模型內容] 對話方塊中，選取**從資料庫產生**，然後按一下 [**下一步]。**
-   選取 [ **EntitySplitting**連線，從下拉式清單，然後按一下**下一步]**。
-   在 [選擇您的資料庫物件] 對話方塊中，核取方塊旁**資料表**節點。
    這會新增中的所有資料表**EntitySplitting**模型的資料庫。
-   按一下 [ **完成**]。

Entity Designer 中，提供用於編輯模型的設計介面，會顯示。

## <a name="map-an-entity-to-two-tables"></a>將實體對應至兩個資料表

在此步驟中，我們將會更新**人員**將資料從實體型別**人員**並**PersonInfo**資料表。

-   選取**電子郵件**並**Phone**的屬性 * * PersonInfo * * 實體，然後按**Ctrl + X**索引鍵。
-   選取 * * 人員 * * 實體，然後按**Ctrl + V**索引鍵。
-   在設計介面上選取**PersonInfo**實體，然後按**刪除**鍵盤上的按鈕。
-   按一下 **否**詢問您是否要移除時**PersonInfo**資料表從模型中，我們即將將其對應至**人員**實體。

    ![DeleteTables](~/ef6/media/deletetables.png)

接下來的步驟需要**對應詳細資料**視窗。 如果看不到這個視窗，以滑鼠右鍵按一下設計介面，然後選取**對應詳細資料**。

-   選取  **Person**實體類型，然後按一下**&lt;將資料表或檢視&gt;** 中**對應詳細資料**視窗。
-   選取 * * PersonInfo * * 從下拉式清單。
    **對應詳細資料**視窗會更新成預設資料行對應，而是適合我們的案例。

**Person**實體類型現在已對應至**人員**並**PersonInfo**資料表。

![Mapping2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>使用模型

-   在 Main 方法中，貼上下列程式碼。

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   編譯並執行應用程式。

針對資料庫執行此應用程式最後執行下列 T-SQL 陳述式。 

-   下列兩個**插入**陳述式所執行的結果執行內容。Savechanges （)。 他們所採取的資料**Person**實體並將它之間分割**人員**並**PersonInfo**資料表。

    ![Insert1](~/ef6/media/insert1.png)

    ![Insert2](~/ef6/media/insert2.png)
-   下列**選取**執行結果列舉資料庫中的人員。 它結合了將資料從**Person**並**PersonInfo**資料表。

    ![選用版](~/ef6/media/select.png)
