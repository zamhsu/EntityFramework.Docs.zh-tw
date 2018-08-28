---
title: 設計工具的查詢預存程序-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 29b7745c2229ce4a38ad81e11406474424adfa24
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994968"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="d8405-102">設計工具的查詢預存程序</span><span class="sxs-lookup"><span data-stu-id="d8405-102">Designer Query Stored Procedures</span></span>
<span data-ttu-id="d8405-103">此逐步解說示範如何使用 Entity Framework Designer （EF 設計工具） 載入模型匯入預存程序，然後呼叫 匯入的預存程序來擷取結果。</span><span class="sxs-lookup"><span data-stu-id="d8405-103">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="d8405-104">請注意，Code First 不支援對應至預存程序或函式。</span><span class="sxs-lookup"><span data-stu-id="d8405-104">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="d8405-105">不過，您可以使用 System.Data.Entity.DbSet.SqlQuery 方法呼叫預存程序或函式。</span><span class="sxs-lookup"><span data-stu-id="d8405-105">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="d8405-106">例如: </span><span class="sxs-lookup"><span data-stu-id="d8405-106">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="d8405-107">必要條件</span><span class="sxs-lookup"><span data-stu-id="d8405-107">Prerequisites</span></span>

<span data-ttu-id="d8405-108">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="d8405-108">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="d8405-109">新版的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="d8405-109">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="d8405-110">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="d8405-110">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="d8405-111">設定專案</span><span class="sxs-lookup"><span data-stu-id="d8405-111">Set up the Project</span></span>

-   <span data-ttu-id="d8405-112">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="d8405-112">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="d8405-113">選取 **檔案&gt;新增-&gt;專案**</span><span class="sxs-lookup"><span data-stu-id="d8405-113">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="d8405-114">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本。</span><span class="sxs-lookup"><span data-stu-id="d8405-114">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="d8405-115">請輸入**EFwithSProcsSample**做為名稱。</span><span class="sxs-lookup"><span data-stu-id="d8405-115">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="d8405-116">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="d8405-116">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="d8405-117">建立模型</span><span class="sxs-lookup"><span data-stu-id="d8405-117">Create a Model</span></span>

-   <span data-ttu-id="d8405-118">以滑鼠右鍵按一下方案總管 中的專案，然後選取**新增-&gt;新的項目**。</span><span class="sxs-lookup"><span data-stu-id="d8405-118">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="d8405-119">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中。</span><span class="sxs-lookup"><span data-stu-id="d8405-119">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="d8405-120">請輸入**EFwithSProcsModel.edmx**為檔案名稱，然後按一下**新增**。</span><span class="sxs-lookup"><span data-stu-id="d8405-120">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="d8405-121">在 選擇模型內容 對話方塊中，選取**從資料庫產生**，然後按一下**下一步**。</span><span class="sxs-lookup"><span data-stu-id="d8405-121">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="d8405-122">按一下 **新的連接**。</span><span class="sxs-lookup"><span data-stu-id="d8405-122">Click **New Connection**.</span></span>  
    <span data-ttu-id="d8405-123">在 連接屬性 對話方塊中，輸入伺服器名稱 (比方說， **(localdb)\\mssqllocaldb**)，選取驗證方法、 型別**學校**的資料庫名稱，然後按一下 **確定**。</span><span class="sxs-lookup"><span data-stu-id="d8405-123">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="d8405-124">[選擇資料連接] 對話方塊中會以您的資料庫連線設定更新。</span><span class="sxs-lookup"><span data-stu-id="d8405-124">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="d8405-125">在 [選擇您的資料庫物件] 對話方塊中，檢查**資料表**核取方塊以選取所有資料表。</span><span class="sxs-lookup"><span data-stu-id="d8405-125">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="d8405-126">此外，請選取下的下列預存程序**預存程序和函式**節點： **GetStudentGrades**並**GetDepartmentName**。</span><span class="sxs-lookup"><span data-stu-id="d8405-126">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![匯入](~/ef6/media/import.jpg)

    <span data-ttu-id="d8405-128">*從 Visual Studio 2012 EF 設計工具支援大量匯入預存程序。**匯入選取的預存程序和函式 theentity 模型**依預設會勾選。*</span><span class="sxs-lookup"><span data-stu-id="d8405-128">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="d8405-129">按一下 [ **完成**]。</span><span class="sxs-lookup"><span data-stu-id="d8405-129">Click **Finish**.</span></span>

<span data-ttu-id="d8405-130">根據預設，每個匯入的預存程序或函式會傳回多個資料行的結果圖案會自動成為新的複雜型別。</span><span class="sxs-lookup"><span data-stu-id="d8405-130">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="d8405-131">在此範例中我們想要對應的結果**GetStudentGrades**函式**StudentGrade**實體和結果**GetDepartmentName**至**無**(**無**做為預設值)。</span><span class="sxs-lookup"><span data-stu-id="d8405-131">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="d8405-132">函式匯入傳回實體類型，對應的預存程序所傳回的資料行必須完全符合傳回的實體類型的純量屬性。</span><span class="sxs-lookup"><span data-stu-id="d8405-132">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="d8405-133">函式匯入也可以傳回簡單型別、 複雜型別或沒有值的集合。</span><span class="sxs-lookup"><span data-stu-id="d8405-133">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="d8405-134">以滑鼠右鍵按一下設計介面，然後選取**模型瀏覽器**。</span><span class="sxs-lookup"><span data-stu-id="d8405-134">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="d8405-135">在 **模型瀏覽器**，選取**函式匯入**，然後按兩下**GetStudentGrades**函式。</span><span class="sxs-lookup"><span data-stu-id="d8405-135">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="d8405-136">在 [編輯函式匯入] 對話方塊中，選取**實體**，然後選擇**StudentGrade**。</span><span class="sxs-lookup"><span data-stu-id="d8405-136">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="d8405-137">***是可組合的函式匯入**頂端的核取方塊**函式匯入**對話方塊可讓您對應至可組合函式。如果您不要核取此方塊，只可組合函式 （資料表值函式） 會出現在**預存程序 / 函式名稱**下拉式清單。如果您不核取此方塊，只有非可組合函式會顯示在清單中。*</span><span class="sxs-lookup"><span data-stu-id="d8405-137">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="d8405-138">使用模型</span><span class="sxs-lookup"><span data-stu-id="d8405-138">Use the Model</span></span>

<span data-ttu-id="d8405-139">開啟**Program.cs**檔**Main**方法定義。</span><span class="sxs-lookup"><span data-stu-id="d8405-139">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="d8405-140">將下列程式碼新增至 Main 函式。</span><span class="sxs-lookup"><span data-stu-id="d8405-140">Add the following code into the Main function.</span></span>

<span data-ttu-id="d8405-141">程式碼會呼叫兩個預存程序： **GetStudentGrades** (會傳回**StudentGrades**指定*StudentId*) 和**GetDepartmentName**（在輸出參數傳回的部門名稱）。</span><span class="sxs-lookup"><span data-stu-id="d8405-141">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

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

<span data-ttu-id="d8405-142">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d8405-142">Compile and run the application.</span></span> <span data-ttu-id="d8405-143">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="d8405-143">The program produces the following output:</span></span>

```
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="d8405-144">輸出參數</span><span class="sxs-lookup"><span data-stu-id="d8405-144">Output Parameters</span></span>
-----------------

<span data-ttu-id="d8405-145">如果 output 參數使用，直到已完全讀取結果，將無法使用它們的值。</span><span class="sxs-lookup"><span data-stu-id="d8405-145">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="d8405-146">這是因為基礎行為的 DbDataReader，請參閱 <<c0> [ 擷取的資料使用 DataReader](http://go.microsoft.com/fwlink/?LinkID=398589)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="d8405-146">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](http://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
