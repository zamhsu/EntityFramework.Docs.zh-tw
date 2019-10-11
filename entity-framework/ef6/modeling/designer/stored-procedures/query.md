---
title: 設計工具查詢預存程式-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 2e0092b526278597e8477d47eeb642598647bb91
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182474"
---
# <a name="designer-query-stored-procedures"></a>設計工具查詢預存程式
這個逐步解說會示範如何使用 Entity Framework Designer （EF 設計工具）將預存程式匯入模型中，然後呼叫匯入的預存程式來抓取結果。 

請注意，該 Code First 不支援對應至預存程式或函數。 不過，您可以使用 DbSet. SqlQuery 方法來呼叫預存程式或函數。 例如:
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取 [檔案 **-@no__t 1] [新 &gt;] 專案**
-   在左窗格中，按一下 [ **Visual C @ no__t-1**]，然後選取 [**主控台**] 範本。
-   在 [名稱] 中輸入 **EFwithSProcsSample** as。
-   選取 **[確定]** 。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管中的專案，然後選取 [**新增-&gt; 個新專案**]。
-   從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。
-   在 [檔案名] 中輸入**EFwithSProcsModel** ，然後按一下 [**新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。
-   按一下 [ **新增連接**]。  
    在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb） \\mssqllocaldb**），選取驗證方法，輸入 **School** for 資料庫名稱，然後按一下 **[確定]** 。  
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊中，檢查**資料表** checkbox，以選取所有資料表。  
    此外，請在 [**預存程式和函數**] 節點底下選取下列預存程式：**GetStudentGrades**和**GetDepartmentName**。 

    ![匯入](~/ef6/media/import.jpg)

    @no__t 0Starting 與 Visual Studio 2012，EF 設計工具支援大量匯入預存程式。預設會核取 [將**選取的預存程式和函數匯入 theentity 模型**]。 *
-   按一下 **[完成]** 。

根據預設，每個傳回多個資料行之匯入預存程式或函數的結果圖形都會自動成為新的複雜型別。 在此範例中，我們想要將 **GetStudentGrades**函數的結果對應至 **StudentGrade**實體，並將**GetDepartmentName**的結果對應至**none** （**none**是預設值）。

若要讓函式匯入傳回實體類型，對應的預存程式所傳回的資料行必須完全符合傳回之實體類型的純量屬性。 函式匯入也可以傳回簡單類型、複雜類型或無值的集合。

-   以滑鼠右鍵按一下設計介面，然後選取 [ **模型瀏覽器**]。
-   在 [**模型瀏覽器**] 中，選取 [函式匯 **入**]，然後按兩下 **GetStudentGrades**函數。
-   在 [編輯函式匯入] 對話方塊中，選取 [ **實體**]  and 選擇 [ **StudentGrade**]。  
    [函式匯入 **] 對話方塊頂端**的 [0The 函數匯**入可組合**] 核取方塊可讓您對應至可組合的函式。 @no__t如果您核取此方塊，[**預存程式/函數名稱**] 下拉式清單中只會顯示可組合的函式（資料表值函數）。如果未核取此方塊，清單中只會顯示不可組合的函式。 *

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼新增至 Main 函式中。

程式碼會呼叫兩個預存程式：**GetStudentGrades** （傳回指定*StudentId*的**StudentGrades** ）和**GetDepartmentName** （傳回輸出參數中的部門名稱）。  

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

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a>輸出參數
-----------------

如果使用輸出參數，則在完全讀取結果之前，其值將無法使用。 這是因為 DbDataReader 的基礎行為，請參閱[使用 DataReader 抓取資料](https://go.microsoft.com/fwlink/?LinkID=398589)以取得詳細資訊。
