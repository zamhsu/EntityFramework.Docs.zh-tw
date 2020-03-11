---
title: 設計工具查詢預存程式-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 2e0092b526278597e8477d47eeb642598647bb91
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418389"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="52584-102">設計工具查詢預存程式</span><span class="sxs-lookup"><span data-stu-id="52584-102">Designer Query Stored Procedures</span></span>
<span data-ttu-id="52584-103">這個逐步解說會示範如何使用 Entity Framework Designer （EF 設計工具）將預存程式匯入模型中，然後呼叫匯入的預存程式來抓取結果。</span><span class="sxs-lookup"><span data-stu-id="52584-103">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="52584-104">請注意，該 Code First 不支援對應至預存程式或函數。</span><span class="sxs-lookup"><span data-stu-id="52584-104">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="52584-105">不過，您可以使用 DbSet. SqlQuery 方法來呼叫預存程式或函數。</span><span class="sxs-lookup"><span data-stu-id="52584-105">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="52584-106">例如：</span><span class="sxs-lookup"><span data-stu-id="52584-106">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="52584-107">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="52584-107">Prerequisites</span></span>

<span data-ttu-id="52584-108">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="52584-108">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="52584-109">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="52584-109">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="52584-110">[School 範例資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="52584-110">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="52584-111">設定專案</span><span class="sxs-lookup"><span data-stu-id="52584-111">Set up the Project</span></span>

-   <span data-ttu-id="52584-112">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="52584-112">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="52584-113">選取檔案 **&gt; 新&gt; 專案**</span><span class="sxs-lookup"><span data-stu-id="52584-113">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="52584-114">在左窗格中，按一下 [ **Visual C\#** ]，然後選取 [**主控台**] 範本。</span><span class="sxs-lookup"><span data-stu-id="52584-114">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="52584-115">在 [名稱] 中輸入 **EFwithSProcsSample** 。</span><span class="sxs-lookup"><span data-stu-id="52584-115">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="52584-116">選取 [確定] \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="52584-116">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="52584-117">建立模型</span><span class="sxs-lookup"><span data-stu-id="52584-117">Create a Model</span></span>

-   <span data-ttu-id="52584-118">以滑鼠右鍵按一下方案總管中的專案，然後選取 [**新增-&gt; 新專案**]。</span><span class="sxs-lookup"><span data-stu-id="52584-118">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="52584-119">從左側功能表中選取 [**資料**]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型**]。</span><span class="sxs-lookup"><span data-stu-id="52584-119">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="52584-120">在 [檔案名] 中輸入**EFwithSProcsModel** ，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="52584-120">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="52584-121">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="52584-121">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="52584-122">按一下 [ **新增連接**]。</span><span class="sxs-lookup"><span data-stu-id="52584-122">Click **New Connection**.</span></span>  
    <span data-ttu-id="52584-123">在 [連接屬性] 對話方塊中，輸入伺服器名稱（例如， **（localdb）\\mssqllocaldb**），選取驗證方法，輸入 **School** 作為資料庫名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="52584-123">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="52584-124">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="52584-124">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="52584-125">在 [選擇您的資料庫物件] 對話方塊中，核取 [**資料表** ] 核取方塊，以選取所有資料表。</span><span class="sxs-lookup"><span data-stu-id="52584-125">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="52584-126">此外，請在 [**預存程式和函數**] 節點底下選取下列預存程式： **GetStudentGrades**和**GetDepartmentName**。</span><span class="sxs-lookup"><span data-stu-id="52584-126">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![匯入](~/ef6/media/import.jpg)

    <span data-ttu-id="52584-128">*從 Visual Studio 2012 開始，EF 設計工具支援大量匯入預存程式。預設會核取 [將**選取的預存程式和函數匯入 theentity 模型**]。*</span><span class="sxs-lookup"><span data-stu-id="52584-128">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="52584-129">按一下 **[完成]** 。</span><span class="sxs-lookup"><span data-stu-id="52584-129">Click **Finish**.</span></span>

<span data-ttu-id="52584-130">根據預設，每個傳回多個資料行之匯入預存程式或函數的結果圖形都會自動成為新的複雜型別。</span><span class="sxs-lookup"><span data-stu-id="52584-130">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="52584-131">在此範例中，我們想要將 **GetStudentGrades**函數的結果對應至 **StudentGrade**實體，並將**GetDepartmentName**的結果對應至**none** （**none**是預設值）。</span><span class="sxs-lookup"><span data-stu-id="52584-131">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="52584-132">若要讓函式匯入傳回實體類型，對應的預存程式所傳回的資料行必須完全符合傳回之實體類型的純量屬性。</span><span class="sxs-lookup"><span data-stu-id="52584-132">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="52584-133">函式匯入也可以傳回簡單類型、複雜類型或無值的集合。</span><span class="sxs-lookup"><span data-stu-id="52584-133">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="52584-134">以滑鼠右鍵按一下設計介面，然後選取 [ **模型瀏覽器**]。</span><span class="sxs-lookup"><span data-stu-id="52584-134">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="52584-135">在 [**模型瀏覽器**] 中，選取 [函式匯 **入**]，然後按兩下 **GetStudentGrades**函數。</span><span class="sxs-lookup"><span data-stu-id="52584-135">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="52584-136">在 [編輯函式匯入] 對話方塊中，選取 [ **實體**] 然後選擇 [ **StudentGrade**]。</span><span class="sxs-lookup"><span data-stu-id="52584-136">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="52584-137">*[函式匯入 **] 對話方塊頂端**的 [函式匯**入是可組合**的] 核取方塊，可讓您對應至可組合的函數。如果您核取此方塊，[**預存程式/函數名稱**] 下拉式清單中只會顯示可組合的函式（資料表值函數）。如果未核取此方塊，清單中只會顯示不可組合的函式。*</span><span class="sxs-lookup"><span data-stu-id="52584-137">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="52584-138">使用模型</span><span class="sxs-lookup"><span data-stu-id="52584-138">Use the Model</span></span>

<span data-ttu-id="52584-139">開啟定義**Main**方法的**Program.cs**檔案。</span><span class="sxs-lookup"><span data-stu-id="52584-139">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="52584-140">將下列程式碼新增至 Main 函式中。</span><span class="sxs-lookup"><span data-stu-id="52584-140">Add the following code into the Main function.</span></span>

<span data-ttu-id="52584-141">程式碼會呼叫兩個預存程式： **GetStudentGrades** （傳回指定*StudentId*的**StudentGrades** ）和**GetDepartmentName** （傳回輸出參數中的部門名稱）。</span><span class="sxs-lookup"><span data-stu-id="52584-141">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

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

<span data-ttu-id="52584-142">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="52584-142">Compile and run the application.</span></span> <span data-ttu-id="52584-143">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="52584-143">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="52584-144">輸出參數</span><span class="sxs-lookup"><span data-stu-id="52584-144">Output Parameters</span></span>
-----------------

<span data-ttu-id="52584-145">如果使用輸出參數，則在完全讀取結果之前，其值將無法使用。</span><span class="sxs-lookup"><span data-stu-id="52584-145">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="52584-146">這是因為 DbDataReader 的基礎行為，請參閱[使用 DataReader 抓取資料](https://go.microsoft.com/fwlink/?LinkID=398589)以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="52584-146">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
