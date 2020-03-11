---
title: 設計工具實體分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: ba1895ae491cec909ff88a8784eea82f1876f595
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418459"
---
# <a name="designer-entity-splitting"></a>設計工具實體分割
本逐步解說示範如何使用 Entity Framework Designer （EF Designer）修改模型，將實體類型對應至兩個數據表。 當多個資料表共用共同的索引鍵時，您可以將實體對應至多個資料表。 將實體類型對應到兩個數據表的概念，可以輕鬆地擴充，以將實體類型對應至兩個以上的資料表。

下圖顯示使用 EF 設計工具時的主要視窗。

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Prerequisites

Visual Studio 2012 或 Visual Studio 2010、旗艦版、Premium、Professional 或 Web Express edition。

## <a name="create-the-database"></a>建立資料庫

隨 Visual Studio 安裝的資料庫伺服器會根據您安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2012，則您將會建立 LocalDB 資料庫。
-   如果您使用 Visual Studio 2010，您將會建立 SQL Express 資料庫。

首先，我們要建立一個資料庫，其中包含兩個數據表，我們將結合成單一實體。

-   開啟 Visual Studio
-   **View&gt; 伺服器總管**
-   以滑鼠右鍵按一下 **資料連線-&gt; 新增連接 ...**
-   如果您還沒有從伺服器總管連接到資料庫，則必須選取 [ **Microsoft SQL Server** ] 做為資料來源
-   連接到 LocalDB 或 SQL Express （視您安裝的版本而定）
-   輸入**EntitySplitting**做為資料庫名稱
-   選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，然後選取 **[是]**
-   新的資料庫現在會顯示在伺服器總管
-   如果您使用 Visual Studio 2012
    -   以滑鼠右鍵按一下伺服器總管中的資料庫，然後選取 [追加**查詢**]
    -   將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行**]。
-   如果您使用 Visual Studio 2010
    -   選取**資料&gt; Transact-sql 編輯器-&gt; 新的查詢連接**。
    -   輸入 **。\\SQLEXPRESS**做為伺服器名稱，然後按一下 **[確定]**
    -   從 [查詢編輯器] 頂端的下拉式選中選取 [ **EntitySplitting** ] 資料庫
    -   將下列 SQL 複製到新的查詢中，然後以滑鼠右鍵按一下查詢並選取 [**執行 SQL** ]。

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

-   在 **[檔案]** 功能表上，指向 **[開新檔案]** ，然後按一下 **[專案]** 。
-   在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台應用程式**] 範本。
-   輸入**MapEntityToTablesSample**做為專案的名稱，然後按一下 **[確定]** 。
-   如果系統提示您儲存在第一節中建立的 SQL 查詢，請按一下 [**否**]。

## <a name="create-a-model-based-on-the-database"></a>建立以資料庫為基礎的模型

-   以滑鼠右鍵按一下方案總管中的專案名稱，指向 [**加入**]，然後按一下 [**新增專案**]。
-   從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。
-   在 [檔案名] 中輸入**MapEntityToTablesModel** ，然後按一下 [**新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]。**
-   從下拉式選中選取 [ **EntitySplitting** ] 連接，然後按 **[下一步]** 。
-   在 [選擇您的資料庫物件] 對話方塊中，勾選 [**資料表** ] 節點旁的方塊。
    這會將**EntitySplitting**資料庫中的所有資料表加入至模型。
-   按一下 **[完成]** 。

會顯示 Entity Designer （提供編輯模型的設計介面）。

## <a name="map-an-entity-to-two-tables"></a>將實體對應至兩個數據表

在此步驟中，我們將更新**person**實體類型，以結合**person**和**PersonInfo**資料表中的資料。

-   選取 **PersonInfo **實體的 **電子郵件** 和**電話**屬性，然後按**Ctrl + X**鍵。
-   選取 **Person **實體，然後按**Ctrl + V**鍵。
-   在設計介面上，選取 [ **PersonInfo** ] 實體，然後按鍵盤上的 [**刪除**] 按鈕。
-   當系統詢問您是否要從模型中移除**PersonInfo**資料表時，請按一下 [**否**]，我們即將將其對應至**Person**實體。

    ![刪除資料表](~/ef6/media/deletetables.png)

後續步驟需要  視窗的  **對應詳細資料**。 如果您看不到這個視窗，請以滑鼠右鍵按一下設計介面，然後選取 [**對應詳細資料**]。

-   選取  **Person**  實體類型，然後按一下  **對應詳細資料**  視窗中的  **&lt;新增資料表或視圖&gt;**  。
-   從下拉式清單中選取 [ **PersonInfo ** ]。
     **對應詳細資料**  視窗會以預設資料行對應進行更新，這在我們的案例中是正常的。

 實體類型的 **person**現在會對應至 **Person** 並 **PersonInfo** 資料表。

![對應2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>使用模型

-   在 Main 方法中貼上下列程式碼。

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

執行此應用程式後，已對資料庫執行下列 T-sql 語句。 

-   執行內容時，會執行下列兩個**INSERT**語句。SaveChanges （）。 他們會從**person**實體接受資料，並將其分割在**person**和**PersonInfo**資料表之間。

    ![插入1](~/ef6/media/insert1.png)

    ![插入2](~/ef6/media/insert2.png)
-   下列**SELECT**是由於列舉資料庫中的人員而執行。 它會結合**Person**和**PersonInfo**資料表中的資料。

    ![Select](~/ef6/media/select.png)
