---
title: 設計工具查詢預存程式-EF6
description: Entity Framework 6 中的設計工具查詢預存程式
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: c647516e1c6ea010c791df6857782b2304ceed1c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620351"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="8ed81-103">設計工具查詢預存程式</span><span class="sxs-lookup"><span data-stu-id="8ed81-103">Designer Query Stored Procedures</span></span>
<span data-ttu-id="8ed81-104">本逐步解說將示範如何使用 Entity Framework Designer (EF) 設計工具，將預存程式匯入至模型，然後呼叫匯入的預存程式來取得結果。</span><span class="sxs-lookup"><span data-stu-id="8ed81-104">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="8ed81-105">請注意，Code First 不支援對應至預存程式或函數。</span><span class="sxs-lookup"><span data-stu-id="8ed81-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="8ed81-106">不過，您可以使用 DbSet 方法來呼叫預存程式或函數。</span><span class="sxs-lookup"><span data-stu-id="8ed81-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="8ed81-107">例如：</span><span class="sxs-lookup"><span data-stu-id="8ed81-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="8ed81-108">必要條件</span><span class="sxs-lookup"><span data-stu-id="8ed81-108">Prerequisites</span></span>

<span data-ttu-id="8ed81-109">若要完成這個逐步解說，您將需要：</span><span class="sxs-lookup"><span data-stu-id="8ed81-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="8ed81-110">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="8ed81-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="8ed81-111">[School 範例資料庫](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="8ed81-111">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="8ed81-112">設定專案</span><span class="sxs-lookup"><span data-stu-id="8ed81-112">Set up the Project</span></span>

-   <span data-ttu-id="8ed81-113">開啟 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="8ed81-113">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="8ed81-114">選取檔案 **- &gt; 新增- &gt; 專案**</span><span class="sxs-lookup"><span data-stu-id="8ed81-114">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="8ed81-115">在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本。</span><span class="sxs-lookup"><span data-stu-id="8ed81-115">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="8ed81-116">輸入 **EFwithSProcsSample**   作為名稱。</span><span class="sxs-lookup"><span data-stu-id="8ed81-116">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="8ed81-117">選取 [確定] \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="8ed81-117">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="8ed81-118">建立模型</span><span class="sxs-lookup"><span data-stu-id="8ed81-118">Create a Model</span></span>

-   <span data-ttu-id="8ed81-119">以滑鼠右鍵按一下方案總管中的專案，然後選取 [ **加入 &gt; 新專案**]。</span><span class="sxs-lookup"><span data-stu-id="8ed81-119">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="8ed81-120">從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]。</span><span class="sxs-lookup"><span data-stu-id="8ed81-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="8ed81-121">輸入 **EFwithSProcsModel** 的檔案名，然後按一下 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="8ed81-121">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="8ed81-122">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="8ed81-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="8ed81-123">按一下 [ **新增連接**]。</span><span class="sxs-lookup"><span data-stu-id="8ed81-123">Click **New Connection**.</span></span>  
    <span data-ttu-id="8ed81-124">在 [連接屬性] 對話方塊中，輸入伺服器名稱 (例如\*\* (localdb) \\ mssqllocaldb**) 、選取驗證方法、輸入 **School\*\*   作為資料庫名稱，然後按一下 **[確定]**。</span><span class="sxs-lookup"><span data-stu-id="8ed81-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="8ed81-125">[選擇您的資料連線] 對話方塊會以您的資料庫連接設定進行更新。</span><span class="sxs-lookup"><span data-stu-id="8ed81-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="8ed81-126">在 [選擇您的資料庫物件] 對話方塊中，選取 [**資料表**   ] 核取方塊以選取所有資料表。</span><span class="sxs-lookup"><span data-stu-id="8ed81-126">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="8ed81-127">此外，請在 [ **預存程式和函數** ] 節點底下選取下列預存程式： **GetStudentGrades** 和 **GetDepartmentName**。</span><span class="sxs-lookup"><span data-stu-id="8ed81-127">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![匯入預存程式](~/ef6/media/import.jpg)

    <span data-ttu-id="8ed81-129">*從 Visual Studio 2012 開始，EF 設計工具支援大量匯入預存程式。預設會核取 [將 **選取的預存程式和函式匯入 theentity 模型** ]。*</span><span class="sxs-lookup"><span data-stu-id="8ed81-129">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="8ed81-130">按一下 **[完成]**。</span><span class="sxs-lookup"><span data-stu-id="8ed81-130">Click **Finish**.</span></span>

<span data-ttu-id="8ed81-131">依預設，傳回多個資料行之每個已匯入預存程式或函數的結果圖形，都會自動成為新的複雜型別。</span><span class="sxs-lookup"><span data-stu-id="8ed81-131">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="8ed81-132">在此範例中，我們想要將 **GetStudentGrades** 函式的結果對應至 **StudentGrade** 實體，並將 **GetDepartmentName** 的結果對應至「 **無** **」 (預設** 值) 。</span><span class="sxs-lookup"><span data-stu-id="8ed81-132">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="8ed81-133">若要讓函式匯入傳回實體類型，對應的預存程式所傳回的資料行必須完全符合傳回之實體類型的純量屬性。</span><span class="sxs-lookup"><span data-stu-id="8ed81-133">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="8ed81-134">函式匯入也可以傳回簡單類型、複雜類型或沒有任何值的集合。</span><span class="sxs-lookup"><span data-stu-id="8ed81-134">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="8ed81-135">以滑鼠右鍵按一下設計介面，然後選取 [ **模型瀏覽器**]。</span><span class="sxs-lookup"><span data-stu-id="8ed81-135">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="8ed81-136">在 [ **模型瀏覽器**] 中，選取 [ **函數匯入**]，然後按兩下 **GetStudentGrades** 函式。</span><span class="sxs-lookup"><span data-stu-id="8ed81-136">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="8ed81-137">在 [編輯函數匯入] 對話方塊中，選取 [ **實體**]，   然後選擇 [ **StudentGrade**]。</span><span class="sxs-lookup"><span data-stu-id="8ed81-137">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="8ed81-138">*[函式**匯入] 對話方塊頂端**的 [函式匯**入是可組合**的] 核取方塊，可讓您對應至可組合的函數。如果您核取此方塊，則只會在 [\*\*預存程式/*\* 函式名稱] 下拉式清單中顯示 (資料表值函式) 的可組合函數。如果未核取此方塊，清單中只會顯示不可組合的函式。\*</span><span class="sxs-lookup"><span data-stu-id="8ed81-138">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="8ed81-139">使用模型</span><span class="sxs-lookup"><span data-stu-id="8ed81-139">Use the Model</span></span>

<span data-ttu-id="8ed81-140">開啟定義**Main**方法的**Program.cs**檔案。</span><span class="sxs-lookup"><span data-stu-id="8ed81-140">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="8ed81-141">將下列程式碼新增至 Main 函數。</span><span class="sxs-lookup"><span data-stu-id="8ed81-141">Add the following code into the Main function.</span></span>

<span data-ttu-id="8ed81-142">程式碼會呼叫兩個預存程式： **GetStudentGrades** (會針對指定的*StudentId*) 傳回**studentgrades.js** ，而**GetDepartmentName** (會傳回輸出參數) 中的部門名稱。</span><span class="sxs-lookup"><span data-stu-id="8ed81-142">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

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

<span data-ttu-id="8ed81-143">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="8ed81-143">Compile and run the application.</span></span> <span data-ttu-id="8ed81-144">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="8ed81-144">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="8ed81-145">輸出參數</span><span class="sxs-lookup"><span data-stu-id="8ed81-145">Output Parameters</span></span>
-----------------

<span data-ttu-id="8ed81-146">如果使用 output 參數，則在結果完全讀取之前，其值將無法使用。</span><span class="sxs-lookup"><span data-stu-id="8ed81-146">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="8ed81-147">這是因為 DbDataReader 的基礎行為，請參閱 [使用 DataReader 抓取資料](https://go.microsoft.com/fwlink/?LinkID=398589) 以取得更多詳細資料。</span><span class="sxs-lookup"><span data-stu-id="8ed81-147">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
