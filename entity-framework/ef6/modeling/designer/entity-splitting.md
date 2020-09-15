---
title: 設計工具實體分割-EF6
description: Entity Framework 6 中的設計工具實體分割
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: fa25629bae1a35b2d792e2b890e87db6c99c37ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073132"
---
# <a name="designer-entity-splitting"></a>設計工具實體分割
本逐步解說示範如何使用 Entity Framework Designer (EF Designer) 修改模型，將實體類型對應至兩個數據表。 當多個資料表共用共同的索引鍵時，您可以將實體對應至多個資料表。 適用於將實體類型對應至兩個資料表的概念，很容易擴大為將實體類型對應至兩個以上的資料表。

下圖顯示使用 EF 設計工具時使用的主視窗。

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>必要條件

Visual Studio 2012 或 Visual Studio 2010、旗艦版、Premium、Professional 或 Web Express 版。

## <a name="create-the-database"></a>建立資料庫

隨 Visual Studio 安裝的資料庫伺服器會根據您所安裝的 Visual Studio 版本而有所不同：

-   如果您使用 Visual Studio 2012，則會建立 LocalDB 資料庫。
-   如果您使用 Visual Studio 2010，將會建立 SQL Express 資料庫。

首先我們要建立一個資料庫，其中包含兩個數據表，我們會將它們合併成單一實體。

-   開啟 Visual Studio
-   **View- &gt; 伺服器總管**
-   以滑鼠右鍵按一下 [**資料連線- &gt; 新增連接 ...** ]
-   如果您還沒有從伺服器總管連接到資料庫，則必須選取 **Microsoft SQL Server** 作為資料來源
-   連接至 LocalDB 或 SQL Express （視您已安裝的版本而定）
-   輸入 **EntitySplitting** 做為資料庫名稱
-   選取 **[確定]** ，系統會詢問您是否要建立新的資料庫，請選取 **[是]** 。
-   新資料庫現在會出現在伺服器總管
-   如果您使用 Visual Studio 2012
    -   以滑鼠右鍵按一下 [伺服器總管] 中的資料庫，並選取 [新增查詢]****
    -   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行**]
-   如果您使用 Visual Studio 2010
    -   選取 **資料- &gt; 交易 SQL 編輯器- &gt; 新增查詢連接 ...**
    -   輸入 **。 \\SQLEXPRESS** 作為伺服器名稱，然後按一下 **[確定]**
    -   從查詢編輯器頂端的下拉式清單中選取 **EntitySplitting** 資料庫
    -   將下列 SQL 複製到新的查詢，然後以滑鼠右鍵按一下查詢並選取 [**執行 SQL** ]。

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
-   在左窗格中，按一下 [ **Visual \# C**]，然後選取 [**主控台應用程式**] 範本。
-   輸入 **MapEntityToTablesSample** 做為專案的名稱，然後按一下 **[確定]**。
-   如果系統提示您儲存在第一節中建立的 SQL 查詢，請按一下 [ **否** ]。

## <a name="create-a-model-based-on-the-database"></a>建立以資料庫為基礎的模型

-   在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [ **加入**]，然後按一下 [ **新增專案**]。
-   從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。
-   輸入 **MapEntityToTablesModel** 的檔案名，然後按一下 [ **新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]。**
-   從下拉式清單中選取 **EntitySplitting** 連接，然後按 **[下一步]**。
-   在 [選擇您的資料庫物件] 對話方塊中，核取 [**資料表**] 節點旁的核取方塊   。
    這會將 **EntitySplitting** 資料庫中的所有資料表加入至模型。
-   按一下 **[完成]**。

隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。

## <a name="map-an-entity-to-two-tables"></a>將實體對應至兩個數據表

在此步驟中，我們將更新 **person** 實體類型，以結合 **person** 和 **PersonInfo** 資料表中的資料。

-   選取 PersonInfo 實體的 **電子郵件**   和**電話**屬性，然後按**Ctrl + X**鍵。 **  **
-   選取 **Person **實體，然後按 **Ctrl + V** 鍵。
-   在設計介面上，選取 [ **PersonInfo**]   實體，然後按鍵盤上的 [**刪除**] 按鈕。
-   當系統詢問您是否要從模型中移除**PersonInfo**資料表時，請按一下 [**否**]，我們即將將其對應至**Person**實體。

    ![刪除資料表](~/ef6/media/deletetables.png)

接下來的步驟需要 [ **對應詳細資料**   ] 視窗。 如果您看不到這個視窗，請以滑鼠右鍵按一下設計介面，然後選取 [ **對應詳細資料**]。

-   選取 [ **Person**   ] 實體類型，然後按一下 [對應詳細資料] 視窗中的 [ ** &lt; 加入資料表或視圖 &gt; **    **Mapping Details**   ]。
-    **PersonInfo **   從下拉式清單中選取 [PersonInfo]。
    [ **對應詳細資料**   ] 視窗會更新成預設資料行對應，在我們的案例中，這些都沒問題。

 **Person**   實體類型現在會對應到 **person**   和 **PersonInfo**   資料表。

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

執行此應用程式後，會對資料庫執行下列 T-sql 語句。 

-   執行內容的結果會執行下列兩個 **INSERT** 語句。SaveChanges ( # A1。 他們會取用 **person** 實體的資料，並在 **person** 和 **PersonInfo** 資料表之間分割資料。

    ![插入1](~/ef6/media/insert1.png)

    ![插入2](~/ef6/media/insert2.png)
-   下列 **SELECT** 是由於列舉資料庫中的人員而執行。 它會結合 **Person** 和 **PersonInfo** 資料表中的資料。

    ![選取結合 Person 和 PersonInfo 資料](~/ef6/media/select.png)
