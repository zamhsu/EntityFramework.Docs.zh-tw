---
title: 設計工具 CUD 預存程式-EF6
description: Entity Framework 6 中的設計工具 CUD 預存程式
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: f722cb3ac0b6ce21e685dbb7bffe571fa7b783d5
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620366"
---
# <a name="designer-cud-stored-procedures"></a>設計工具 CUD 預存程式

本逐步解說示範如何 \\ 使用 Entity Framework Designer (EF Designer) ，將實體類型的 create insert、update 和 delete (CUD) 作業對應到預存程式。 根據預設，Entity Framework 會自動產生 CUD 作業的 SQL 語句，但您也可以將預存程式對應至這些作業。  

請注意，Code First 不支援對應至預存程式或函數。 不過，您可以使用 DbSet 方法來呼叫預存程式或函數。 例如：

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>將 CUD 作業對應至預存程式時的考慮

將 CUD 作業對應至預存程式時，適用下列考慮事項：

- 如果您要將其中一個 CUD 作業對應至預存程式，請對應所有的作業。 如果您未對應全部三個，未對應的作業會在執行時失敗，而且會擲回 **UpdateException**   。
- 您必須將預存程式的每個參數對應至實體屬性。
- 如果伺服器為插入的資料列產生主鍵值，您必須將此值對應回實體的索引鍵屬性。 在接下來的範例中， **InsertPerson**預存程式會傳回   新建立的主要索引鍵，做為預存程式結果集的一部分。 主鍵會使用 EF Designer 的 [ ** &lt; &gt; 加入結果**系結] 功能， (**PersonID**) 對應到實體索引鍵   。
- 預存程序呼叫會與概念模型中的實體對應1:1。 例如，如果您在概念模型中執行繼承階層，然後對應 **父** (基底) 的 CUD 預存程式和 **子** (衍生的) 實體，儲存 **子** 變更將只會呼叫 **子**系的預存程式，而不會觸發 **父系**的預存程式呼叫。

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>設定專案

- 開啟 Visual Studio 2012。
- 選取檔案 **- &gt; 新增- &gt; 專案**
- 在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本。
- 輸入 **CUDSProcsSample**   作為名稱。
- 選取 [確定] ****。

## <a name="create-a-model"></a>建立模型

- 在方案總管中的專案名稱上按一下滑鼠右鍵，然後選取 [ **加入- &gt; 新增專案**]。
- 從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。
- 輸入 **CUDSProcs** 的檔案名，然後按一下 [ **新增**]。
- 在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]**。
- 按一下 [ **新增連接**]。 在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如** (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School**   作為資料庫名稱，然後按一下 **[確定]**。
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
- 在 [選擇您的資料庫物件] 對話方塊的 [ **資料表**]   節點底下，選取 [ **Person** ] 資料表。
- 此外，請在 [ **預存程式和函數** ] 節點底下選取下列預存程式： **DeletePerson**、 **InsertPerson**和 **UpdatePerson**。
- 從 Visual Studio 2012 開始，EF 設計工具支援大量匯入預存程式。 預設會核取 [將 **選取的預存程式和函式匯入至實體模型** ]。 由於在此範例中，我們有一個插入、更新和刪除實體類型的預存程式，因此我們不會想要將其匯入，並將取消核取此核取方塊。

    ![匯入 S 的過程](~/ef6/media/importsprocs.jpg)

- 按一下 **[完成]**。
    會顯示 EF 設計工具，其提供用於編輯模型的設計介面。

## <a name="map-the-person-entity-to-stored-procedures"></a>將 Person 實體對應至預存程式

- 以滑鼠右鍵按一下 [ **Person**]   實體類型，然後選取 [ **預存程式對應**]。
- 預存程式對應會出現在 [ **對應詳細資料**   ] 視窗中。
- 按一下 [ ** &lt; 選取插入 &gt; 函數**]。
    此欄位會變成儲存模型中預存程序的下拉式清單，可以對應到概念模型中的實體類型。
     **InsertPerson**   從下拉式清單中選取 [InsertPerson]。
- 預存程序參數與實體屬性之間的預設對應隨即出現。 請注意，箭頭表示對應方向：為預存程序參數提供屬性值。
- 按一下 [ ** &lt; 新增結果 &gt; **系結]。
- 輸入 **NewPersonID**，此為 **InsertPerson**預存程式所傳回的參數名稱   。 請確定不輸入前置或尾端空格。
- 按 **enter**鍵。
- 根據預設， **NewPersonID**   會對應到實體索引鍵 **PersonID**。 請注意，箭頭表示對應方向：為屬性提供結果資料行的值。

    ![對應詳細資料](~/ef6/media/mappingdetails.png)

- 按一下 [ ** &lt; Select Update &gt; Function**]   ，然後 **UpdatePerson**   從產生的下拉式清單中選取 [UpdatePerson]。
- 預存程序參數與實體屬性之間的預設對應隨即出現。
- 按一下 [ ** &lt; 選取刪除 &gt; 函數**]   ，然後 **DeletePerson**   從產生的下拉式清單中選取 [DeletePerson]。
- 預存程序參數與實體屬性之間的預設對應隨即出現。

Person 實體類型的插入、更新和刪除作業 **Person**   現在已對應至預存程式。

如果您想要在使用預存程式更新或刪除實體時啟用並行檢查，請使用下列其中一個選項：

- 使用**OUTPUT**參數，從預存程式傳回受影響的資料列數目，並檢查參數名稱旁邊的 [**受影響的資料列參數**   ] 核取方塊。 如果在呼叫作業時傳回的值為零，則會擲回  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)   。
- 核取您要用於平行存取檢查的屬性旁邊的 [ **使用原始值** ] 核取方塊。 當嘗試進行更新時，將資料寫回資料庫時，會使用原先從資料庫讀取的屬性值。 如果值不符合資料庫中的值，則會擲回**OptimisticConcurrencyException**   。

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼新增至 Main 函數。

程式碼會建立新的 **Person** 物件，然後更新物件，最後刪除該物件。

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

- 編譯並執行應用程式。 此程式會產生下列輸出 *

> [!NOTE]
> PersonID 由伺服器自動產生，因此您很可能會看到不同的數位 *

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

如果您正在使用 Visual Studio 的最終版本，您可以搭配偵錯工具使用 Intellitrace 來查看執行的 SQL 語句。

![使用 Intellitrace 進行 Debug](~/ef6/media/intellitrace.png)
