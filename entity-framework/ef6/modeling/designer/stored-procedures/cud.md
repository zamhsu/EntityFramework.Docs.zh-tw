---
title: 設計工具的 CUD 預存程序-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
ms.openlocfilehash: 35a00aa817c8643352c517c233977efd49e3baac
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489553"
---
# <a name="designer-cud-stored-procedures"></a>設計工具的 CUD 預存程序
此逐步解說顯示如何將對應建立\\插入、 更新和刪除 (CUD) 作業的實體類型使用 Entity Framework Designer （EF 設計工具） 的預存程序。  根據預設，Entity Framework 會自動產生 CUD 作業的 SQL 陳述式，但您也可以將預存程序對應至這些作業。  

請注意，Code First 不支援對應至預存程序或函式。 不過，您可以使用 System.Data.Entity.DbSet.SqlQuery 方法呼叫預存程序或函式。 例如: 
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>預存程序的對應 CUD 作業時的考量

當將 CUD 作業對應至預存程序中，適用下列考量： 

-   如果您要對應 CUD 作業的其中一個預存程序，將對應所有人都。 如果您不會對應所有三個，對應的作業將會失敗，如果執行，而且**UpdateException**就會擲回。
-   您必須將預存程序的每個參數對應至實體屬性。
-   如果伺服器就會產生主索引鍵值插入的資料列，您必須將此值對應回實體的索引鍵屬性。 在範例中，如下所示**InsertPerson**預存程序會傳回新建立的主索引鍵做為預存程序的結果集的一部分。 主索引鍵對應到實體索引鍵 (**PersonID**) 使用**&lt;加入結果繫結&gt;** EF 設計工具的功能。
-   預存程序呼叫是對應與 1:1 概念模型中的實體。 例如，如果您實作在概念模型，然後對應中的繼承階層架構 CUD 預存程序**父代**（基本） 和**子**（衍生） 的實體，儲存**子系**變更都只會呼叫**子**的預存程序，就不會觸發**父**的預存程序呼叫。

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- 新版的 Visual Studio。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取 **檔案&gt;新增-&gt;專案**
-   在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。
-   請輸入**CUDSProcsSample**做為名稱。
-   選取 [確定]。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管 中的專案名稱，然後選取**新增-&gt;新的項目**。
-   選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。
-   請輸入**CUDSProcs.edmx**為檔案名稱，然後按一下**新增**。
-   在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。
-   按一下 **新的連接**。 在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。
    [選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。
-   在 選擇您的資料庫物件對話方塊的 **資料表**節點中，選取**人員**資料表。
-   此外，請選取下的下列預存程序**預存程序和函式**節點： **DeletePerson**， **InsertPerson**，和**UpdatePerson**. 
-   從 Visual Studio 2012 EF 設計工具支援大量匯入預存程序。 **匯入選取的預存程序和函式的實體模型到**依預設會勾選。 在此範例中，我們有預存程序插入、 更新和刪除實體類型，因為我們不想要匯入它們，並將取消核取此核取方塊。 

    ![匯入 S 程序](~/ef6/media/importsprocs.jpg)

-   按一下 [ **完成**]。
    EF 設計工具中，可提供用於編輯模型的設計介面，會顯示。

## <a name="map-the-person-entity-to-stored-procedures"></a>將 Person 實體對應至預存程序

-   以滑鼠右鍵按一下**Person**實體類型，然後選取**預存程序對應**。
-   預存程序對應會出現在**對應詳細資料**視窗。
-   按一下  **&lt;選取 插入函數&gt;**。
    此欄位會變成儲存模型中預存程序的下拉式清單，可以對應到概念模型中的實體類型。
    選取  **InsertPerson**從下拉式清單。
-   預存程序參數與實體屬性之間的預設對應隨即出現。 請注意，箭頭表示對應方向：為預存程序參數提供屬性值。
-   按一下  **&lt;加入結果繫結&gt;**。
-   型別**NewPersonID**，所傳回的參數名稱**InsertPerson**預存程序。 請確定輸入開頭或尾端空格。
-   按 **Enter** 鍵。
-   根據預設， **NewPersonID**對應到實體索引鍵**PersonID**。 請注意，箭頭表示對應方向：為屬性提供結果資料行的值。

    ![對應詳細資料](~/ef6/media/mappingdetails.png)

-   按一下  **&lt;選取更新函式&gt;** ，然後選取**UpdatePerson**從產生的下拉式清單。
-   預存程序參數與實體屬性之間的預設對應隨即出現。
-   按一下  **&lt;選取刪除函式&gt;** ，然後選取**DeletePerson**從產生的下拉式清單。
-   預存程序參數與實體屬性之間的預設對應隨即出現。

插入、 更新和刪除作業**人員**實體類型現在會對應至預存程序。

如果您想要啟用並行檢查更新或刪除實體的預存程序時，請使用下列選項之一：

-   使用**輸出**參數來傳回的數目受影響的資料列的預存程序和核取**資料列受影響的參數**參數名稱旁的核取方塊。 如果傳回的值為零，呼叫作業時， [ **OptimisticConcurrencyException** ](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)就會擲回。
-   請檢查**使用原始值**您想要用於並行存取檢查屬性旁的核取方塊。 當嘗試更新時，資料寫回至資料庫時將使用原本從資料庫讀取屬性的值。 如果值不符合資料庫中的值**OptimisticConcurrencyException**就會擲回。

## <a name="use-the-model"></a>使用模型

開啟**Program.cs**檔**Main**方法定義。 將下列程式碼新增至 Main 函式。

程式碼會建立新**人員**物件，然後更新物件，而最後會刪除物件。         

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

-   編譯並執行應用程式。 此程式會產生下列輸出 *
    >[!NOTE]
> PersonID 是自動產生的伺服器，因此您很可能會看到不同的號碼 *

```
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

如果您正在使用的 Visual Studio Ultimate 的版本，您可以偵錯工具使用 Intellitrace，查看執行的 SQL 陳述式。

![Intellitrace](~/ef6/media/intellitrace.png)
