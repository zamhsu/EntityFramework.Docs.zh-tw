---
title: 設計工具的查詢預存程序-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 04478ea1c8cd43a7ba4ee788e464992af3de7f64
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283897"
---
# <a name="designer-query-stored-procedures"></a>設計工具的查詢預存程序
此逐步解說示範如何使用 Entity Framework Designer （EF 設計工具） 載入模型匯入預存程序，然後呼叫 匯入的預存程序來擷取結果。 

請注意，Code First 不支援對應至預存程序或函式。 不過，您可以使用 System.Data.Entity.DbSet.SqlQuery 方法呼叫預存程序或函式。 例如: 
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- 新版的 Visual Studio。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取 **檔案&gt;新增-&gt;專案**
-   在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。
-   請輸入**EFwithSProcsSample**做為名稱。
-   選取 [確定]。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管 中的專案，然後選取**新增-&gt;新的項目**。
-   選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。
-   請輸入**EFwithSProcsModel.edmx**為檔案名稱，然後按一下**新增**。
-   在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。
-   按一下 **新的連接**。  
    在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。  
    [選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。
-   在 [選擇您的資料庫物件] 對話方塊中，檢查**資料表**核取方塊以選取所有資料表。  
    此外，請選取下的下列預存程序**預存程序和函式**節點： **GetStudentGrades**並**GetDepartmentName**。 

    ![匯入](~/ef6/media/import.jpg)

    *從 Visual Studio 2012 EF 設計工具支援大量匯入預存程序。**匯入選取的預存程序和函式 theentity 模型**依預設會勾選。*
-   按一下 [ **完成**]。

根據預設，每個匯入的預存程序或函式會傳回多個資料行的結果圖案會自動成為新的複雜型別。 在此範例中我們想要對應的結果**GetStudentGrades**函式**StudentGrade**實體和結果**GetDepartmentName**至**無**(**無**做為預設值)。

函式匯入傳回實體類型，對應的預存程序所傳回的資料行必須完全符合傳回的實體類型的純量屬性。 函式匯入也可以傳回簡單型別、 複雜型別或沒有值的集合。

-   以滑鼠右鍵按一下設計介面，然後選取**模型瀏覽器**。
-   在 **模型瀏覽器**，選取**函式匯入**，然後按兩下**GetStudentGrades**函式。
-   在 [編輯函式匯入] 對話方塊中，選取**實體**，然後選擇**StudentGrade**。  
    ***是可組合的函式匯入**頂端的核取方塊**函式匯入**對話方塊可讓您對應至可組合函式。如果您不要核取此方塊，只可組合函式 （資料表值函式） 會出現在**預存程序 / 函式名稱**下拉式清單。如果您不核取此方塊，只有非可組合函式會顯示在清單中。*

## <a name="use-the-model"></a>使用模型

開啟**Program.cs**檔**Main**方法定義。 將下列程式碼新增至 Main 函式。

程式碼會呼叫兩個預存程序： **GetStudentGrades** (會傳回**StudentGrades**指定*StudentId*) 和**GetDepartmentName**（在輸出參數傳回的部門名稱）。  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

編譯並執行應用程式。 此程式會產生下列輸出：

```
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a>輸出參數
-----------------

如果 output 參數使用，直到已完全讀取結果，將無法使用它們的值。 這是因為基礎行為的 DbDataReader，請參閱 <<c0> [ 擷取的資料使用 DataReader](https://go.microsoft.com/fwlink/?LinkID=398589)如需詳細資訊。
