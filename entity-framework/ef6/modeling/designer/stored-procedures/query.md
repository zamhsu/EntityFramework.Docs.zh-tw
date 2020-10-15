---
title: 設計工具查詢預存程式-EF6
description: Entity Framework 6 中的設計工具查詢預存程式
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: d88bf70e232f5245023ab6683290503822d07463
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066014"
---
# <a name="designer-query-stored-procedures"></a>設計工具查詢預存程式
本逐步解說將示範如何使用 Entity Framework Designer (EF) 設計工具，將預存程式匯入至模型，然後呼叫匯入的預存程式來取得結果。 

請注意，Code First 不支援對應至預存程式或函數。 不過，您可以使用 DbSet 方法來呼叫預存程式或函數。 例如︰
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>必要條件

若要完成這個逐步解說，您將需要：

- Visual Studio 的最新版本。
- [School 範例資料庫](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>設定專案

-   開啟 Visual Studio 2012。
-   選取檔案 **- &gt; 新增- &gt; 專案**
-   在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本。
-   輸入 **EFwithSProcsSample**   作為名稱。
-   選取 [確定] ****。

## <a name="create-a-model"></a>建立模型

-   以滑鼠右鍵按一下方案總管中的專案，然後選取 [ **加入 &gt; 新專案**]。
-   從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。
-   輸入 **EFwithSProcsModel** 的檔案名，然後按一下 [ **新增**]。
-   在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]**。
-   按一下 [ **新增連接**]。  
    在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如** (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School**   作為資料庫名稱，然後按一下 **[確定]**。  
    [選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。
-   在 [選擇您的資料庫物件] 對話方塊中，選取 [**資料表**   ] 核取方塊以選取所有資料表。  
    此外，請在 [ **預存程式和函數** ] 節點底下選取下列預存程式： **GetStudentGrades** 和 **GetDepartmentName**。 

    ![匯入預存程式](~/ef6/media/import.jpg)

    *從 Visual Studio 2012 開始，EF 設計工具支援大量匯入預存程式。預設會核取 [將 **選取的預存程式和函式匯入 theentity 模型** ]。*
-   按一下 **[完成]**。

依預設，傳回多個資料行之每個已匯入預存程式或函數的結果圖形，都會自動成為新的複雜型別。 在此範例中，我們想要將 **GetStudentGrades** 函式的結果對應至 **StudentGrade** 實體，並將 **GetDepartmentName** 的結果對應至「 **無** **」 (預設** 值) 。

若要讓函式匯入傳回實體類型，對應的預存程式所傳回的資料行必須完全符合傳回之實體類型的純量屬性。 函式匯入也可以傳回簡單類型、複雜類型或沒有任何值的集合。

-   以滑鼠右鍵按一下設計介面，然後選取 [ **模型瀏覽器**]。
-   在 [ **模型瀏覽器**] 中，選取 [ **函數匯入**]，然後按兩下 **GetStudentGrades** 函式。
-   在 [編輯函數匯入] 對話方塊中，選取 [ **實體**]，   然後選擇 [ **StudentGrade**]。  
    *[函式**匯入] 對話方塊頂端**的 [函式匯**入是可組合**的] 核取方塊，可讓您對應至可組合的函數。如果您核取此方塊，則只會在 [**預存程式/** 函式名稱] 下拉式清單中顯示 (資料表值函式) 的可組合函數。如果未核取此方塊，清單中只會顯示不可組合的函式。*

## <a name="use-the-model"></a>使用模型

開啟定義**Main**方法的**Program.cs**檔案。 將下列程式碼新增至 Main 函數。

程式碼會呼叫兩個預存程式： **GetStudentGrades** (會針對指定的*StudentId*) 傳回**studentgrades.js** ，而**GetDepartmentName** (會傳回輸出參數) 中的部門名稱。  

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

如果使用 output 參數，則在結果完全讀取之前，其值將無法使用。 這是因為 DbDataReader 的基礎行為，請參閱 [使用 DataReader 抓取資料](https://go.microsoft.com/fwlink/?LinkID=398589) 以取得更多詳細資料。
