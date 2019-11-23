---
title: 設計工具 CUD 預存程式-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
ms.openlocfilehash: bdb0df969c33d5ad3f103bfa9af6002c9c2bb9b3
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813554"
---
# <a name="designer-cud-stored-procedures"></a>設計工具 CUD 預存程式

此逐步解說示範如何使用 Entity Framework Designer （EF Designer），將實體類型的 create\\insert、update 和 delete （CUD）作業對應至預存程式。  根據預設，Entity Framework 會自動產生 CUD 作業的 SQL 語句，但您也可以將預存程式對應至這些作業。  

請注意，該 Code First 不支援對應至預存程式或函數。 不過，您可以使用 DbSet. SqlQuery 方法來呼叫預存程式或函數。 例如：

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>將 CUD 作業對應至預存程式時的考慮

將 CUD 作業對應至預存程式時，適用下列考慮事項：

- 如果您要將其中一個 CUD 作業對應至預存程式，請將其對應。 如果您未對應這三個，未對應的作業會在執行時失敗，而且會擲回 **UpdateException** 。
- 您必須將預存程式的每個參數對應至實體屬性。
- 如果伺服器為插入的資料列產生主要金鑰值，您必須將此值對應回實體的索引鍵屬性。 在接下來的範例中， **InsertPerson** 預存程式會傳回新建立的主鍵做為預存程式之結果集的一部分。 主鍵會使用 EF Designer 的 **&lt;加入結果**系結&gt;  功能對應至實體索引鍵（**PersonID**）。
- 預存程序呼叫會對應1:1 和概念模型中的實體。 例如，如果您在概念模型中執行繼承階層，然後對應**父**（基底）和**子**系（衍生）實體的 CUD 預存程式，則儲存**子**變更只會呼叫**子**系的預存程式，而不會觸發**父系**的預存程序呼叫。

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

- 開啟 Visual Studio 2012。
- 選取檔案 **&gt; 新&gt; 專案**
- 在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台**] 範本。
- 在 [名稱] 中輸入 **CUDSProcsSample** 。
- 選取 **[確定]** 。

## <a name="create-a-model"></a>建立模型

- 以滑鼠右鍵按一下方案總管中的專案名稱，然後選取 [**新增-&gt; 新專案**]。
- 從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。
- 在 [檔案名] 中輸入**CUDSProcs** ，然後按一下 [**新增**]。
- 在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。
- 按一下 [ **新增連接**]。 在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
- 在 [選擇您的資料庫物件] 對話方塊的 [ **資料表** ] 節點底下，選取 [ **Person** ] 資料表。
- 此外，請在 [**預存程式和函數**] 節點底下選取下列預存程式： **DeletePerson**、 **InsertPerson**和**UpdatePerson**。
- 從 Visual Studio 2012 開始，EF 設計工具支援大量匯入預存程式。 預設會核取 [將**選取的預存程式和函數匯入至實體模型**]。 因為在此範例中，我們有一個插入、更新和刪除實體類型的預存程式，所以我們不想要匯入它們，而且會取消核取此核取方塊。

    ![匯入 S 的過程](~/ef6/media/importsprocs.jpg)

- 按一下 **[完成]** 。
    [EF Designer] （提供編輯模型的設計介面）隨即顯示。

## <a name="map-the-person-entity-to-stored-procedures"></a>將 Person 實體對應至預存程式

- 以滑鼠右鍵按一下 [ **Person** ] 實體類型，然後選取 [ **預存程式對應**]。
- 預存程式對應會顯示在  **對應詳細資料**  視窗中。
- 按一下  **&lt;選取 插入函數&gt;** 。
    此欄位會變成儲存模型中預存程序的下拉式清單，可以對應到概念模型中的實體類型。
    從下拉式清單中選取 [ **InsertPerson** ]。
- 預存程序參數與實體屬性之間的預設對應隨即出現。 請注意，箭頭表示對應方向：為預存程序參數提供屬性值。
- 按一下 [ **&lt;加入結果**系結]&gt;。
- 輸入 **NewPersonID**，這是 **InsertPerson** 預存程式所傳回的參數名稱。 請確定不輸入前置或尾端空格。
- 按 **enter**鍵。
- 根據預設， **NewPersonID** 會對應至實體索引鍵 **PersonID**。 請注意，箭頭表示對應方向：為屬性提供結果資料行的值。

    ![對應詳細資料](~/ef6/media/mappingdetails.png)

- 按一下  **&lt;選取 更新**函式&gt; ，然後從產生的下拉式清單中選取  **UpdatePerson** 。
- 預存程序參數與實體屬性之間的預設對應隨即出現。
- 按一下  **&lt;選取 刪除**函式&gt; 然後從產生的下拉式清單中選取  **DeletePerson** 。
- 預存程序參數與實體屬性之間的預設對應隨即出現。

 **Person** 實體類型的插入、更新和刪除作業現在會對應至預存程式。

如果您想要在使用預存程式來更新或刪除實體時啟用並行檢查，請使用下列其中一個選項：

- 使用**OUTPUT**參數從預存程式傳回受影響的資料列數目，然後檢查參數名稱旁邊的 **受影響**的資料列 參數  核取方塊。 如果在呼叫作業時傳回的值為零，則會擲回  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) 。
- 核取您要用於並行檢查的屬性旁的 [**使用原始值**] 核取方塊。 嘗試進行更新時，將資料寫回資料庫時，會使用原本從資料庫讀取的屬性值。 如果值不符合資料庫中的值，則會擲回**OptimisticConcurrencyException** 。

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼新增至 Main 函式中。

此程式碼會建立新的**Person**物件，然後更新物件，最後刪除物件。

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- 編譯並執行應用程式。 程式會產生下列輸出 *

> [!NOTE]
> PersonID 是由伺服器自動產生的，因此您很有可能會看到不同的數位 *

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

如果您使用的是 Visual Studio 的旗艦版，您可以使用 Intellitrace 搭配偵錯工具來查看執行的 SQL 語句。

![Intellitrace](~/ef6/media/intellitrace.png)
