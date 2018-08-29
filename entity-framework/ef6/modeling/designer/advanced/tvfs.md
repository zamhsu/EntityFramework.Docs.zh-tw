---
title: 資料表值函式 (Tvf)-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
ms.openlocfilehash: f4b8c1bd442bac67a06584bd23c040381374f303
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993243"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="c7522-102">資料表值函式 (Tvf)</span><span class="sxs-lookup"><span data-stu-id="c7522-102">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="c7522-103">**EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="c7522-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="c7522-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="c7522-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="c7522-105">影片以及逐步說明的逐步解說會示範如何將資料表值函式 (Tvf) 使用 Entity Framework 設計工具的對應。</span><span class="sxs-lookup"><span data-stu-id="c7522-105">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="c7522-106">它也會示範如何從 LINQ 查詢中呼叫 TVF。</span><span class="sxs-lookup"><span data-stu-id="c7522-106">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="c7522-107">Tvf 目前會在第一個資料庫的工作流程中才支援。</span><span class="sxs-lookup"><span data-stu-id="c7522-107">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="c7522-108">Entity Framework 第 5 版中引進 TVF 支援。</span><span class="sxs-lookup"><span data-stu-id="c7522-108">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="c7522-109">請注意，使用新的功能，例如資料表值函式、 列舉、 和空間類型，您必須以.NET Framework 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="c7522-109">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="c7522-110">Visual Studio 2012 的預設值為目標.NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="c7522-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="c7522-111">Tvf 會非常類似於預存程序只有一個主要差異： TVF 的結果是可組合。</span><span class="sxs-lookup"><span data-stu-id="c7522-111">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="c7522-112">這表示 TVF 的結果可以用 LINQ 查詢中，而預存程序的結果不能。</span><span class="sxs-lookup"><span data-stu-id="c7522-112">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="c7522-113">觀看影片</span><span class="sxs-lookup"><span data-stu-id="c7522-113">Watch the video</span></span>

<span data-ttu-id="c7522-114">**由**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="c7522-114">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="c7522-115">[WMV](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="c7522-115">[WMV](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="c7522-116">必要條件</span><span class="sxs-lookup"><span data-stu-id="c7522-116">Pre-Requisites</span></span>

<span data-ttu-id="c7522-117">若要完成此逐步解說，您需要：</span><span class="sxs-lookup"><span data-stu-id="c7522-117">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="c7522-118">安裝[School 資料庫](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="c7522-118">Install the [School database](~/ef6/resources/school-database.md).</span></span>

- <span data-ttu-id="c7522-119">有新版的 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c7522-119">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="c7522-120">設定專案</span><span class="sxs-lookup"><span data-stu-id="c7522-120">Set up the Project</span></span>

1.  <span data-ttu-id="c7522-121">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c7522-121">Open Visual Studio</span></span>
2.  <span data-ttu-id="c7522-122">在 **檔案**功能表上，指向**新增**，然後按一下 **專案**</span><span class="sxs-lookup"><span data-stu-id="c7522-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="c7522-123">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="c7522-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="c7522-124">請輸入**TVF**做為名稱的專案，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="c7522-124">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="c7522-125">加入資料庫中的 TVF</span><span class="sxs-lookup"><span data-stu-id="c7522-125">Add a TVF to the Database</span></span>

-   <span data-ttu-id="c7522-126">選取 **檢視-&gt; SQL Server 物件總管**</span><span class="sxs-lookup"><span data-stu-id="c7522-126">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="c7522-127">如果伺服器清單中不是 LocalDB： 以滑鼠右鍵按一下**SQL Server** ，然後選取**加入 SQL Server**使用預設值**Windows 驗證**連接到 LocalDB 伺服器</span><span class="sxs-lookup"><span data-stu-id="c7522-127">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="c7522-128">展開 [LocalDB] 節點</span><span class="sxs-lookup"><span data-stu-id="c7522-128">Expand the LocalDB node</span></span>
-   <span data-ttu-id="c7522-129">在 [資料庫] 節點中，School 資料庫節點上按一下滑鼠右鍵，然後選取**新增查詢...**</span><span class="sxs-lookup"><span data-stu-id="c7522-129">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="c7522-130">在 T-SQL 編輯器中，貼上下列 TVF 定義</span><span class="sxs-lookup"><span data-stu-id="c7522-130">In T-SQL Editor, paste the following TVF definition</span></span>

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   <span data-ttu-id="c7522-131">按一下滑鼠右鍵，在 T-SQL 編輯器中，選取**Execute**</span><span class="sxs-lookup"><span data-stu-id="c7522-131">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="c7522-132">GetStudentGradesForCourse 函式加入至 School 資料庫</span><span class="sxs-lookup"><span data-stu-id="c7522-132">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="c7522-133">建立模型</span><span class="sxs-lookup"><span data-stu-id="c7522-133">Create a Model</span></span>

1.  <span data-ttu-id="c7522-134">以滑鼠右鍵按一下方案總管] 中的專案名稱，指向**新增**，然後按一下 [**新項目**</span><span class="sxs-lookup"><span data-stu-id="c7522-134">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="c7522-135">選取 **資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**中**範本**窗格</span><span class="sxs-lookup"><span data-stu-id="c7522-135">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="c7522-136">請輸入**TVFModel.edmx**為檔案名稱，然後按一下**新增**</span><span class="sxs-lookup"><span data-stu-id="c7522-136">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="c7522-137">在 [選擇模型內容] 對話方塊中，選取**從資料庫產生**，然後按一下 [**下一步]**</span><span class="sxs-lookup"><span data-stu-id="c7522-137">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="c7522-138">按一下 **新的連線**Enter **(localdb)\\mssqllocaldb**伺服器的 名稱 文字方塊在方塊中輸入**學校**資料庫名稱按一下**確定**</span><span class="sxs-lookup"><span data-stu-id="c7522-138">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="c7522-139">在 選擇您的資料庫物件對話方塊的 **資料表**節點中，選取**人員**， **StudentGrade**，以及**課程**資料表</span><span class="sxs-lookup"><span data-stu-id="c7522-139">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="c7522-140">選取  **GetStudentGradesForCourse**函式位於**預存程序和函式**節點附註，該開始使用 Visual Studio 2012，Entity Designer 可讓您將批次匯入您的預存程序和函式</span><span class="sxs-lookup"><span data-stu-id="c7522-140">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="c7522-141">按一下 **完成**</span><span class="sxs-lookup"><span data-stu-id="c7522-141">Click **Finish**</span></span>
9.  <span data-ttu-id="c7522-142">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="c7522-142">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="c7522-143">您在選取的所有物件**選擇您的資料庫物件**對話方塊加入至模型。</span><span class="sxs-lookup"><span data-stu-id="c7522-143">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="c7522-144">根據預設，每個匯入的預存程序或函式的結果圖案會自動成為您的實體模型中的新複雜型別。</span><span class="sxs-lookup"><span data-stu-id="c7522-144">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="c7522-145">但我們想要將 GetStudentGradesForCourse 函式的結果對應至 StudentGrade 實體： 以滑鼠右鍵按一下設計介面，然後選取**模型瀏覽器**模型瀏覽器中，選取**函式匯入**，然後按兩下**GetStudentGradesForCourse**函式中編輯函式匯入對話方塊中，選取**實體**，然後選擇  **StudentGrade**</span><span class="sxs-lookup"><span data-stu-id="c7522-145">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="c7522-146">持續儲存並擷取資料</span><span class="sxs-lookup"><span data-stu-id="c7522-146">Persist and Retrieve Data</span></span>

<span data-ttu-id="c7522-147">開啟的 Main 方法定義所在的檔案。</span><span class="sxs-lookup"><span data-stu-id="c7522-147">Open the file where the Main method is defined.</span></span> <span data-ttu-id="c7522-148">將下列程式碼新增至 Main 函式。</span><span class="sxs-lookup"><span data-stu-id="c7522-148">Add the following code into the Main function.</span></span>

<span data-ttu-id="c7522-149">下列程式碼示範如何建置使用資料表值函式的查詢。</span><span class="sxs-lookup"><span data-stu-id="c7522-149">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="c7522-150">查詢會將結果投射至匿名型別，其中包含相關的課程標題和相關的學生成績大於或等於為 3.5。</span><span class="sxs-lookup"><span data-stu-id="c7522-150">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

<span data-ttu-id="c7522-151">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="c7522-151">Compile and run the application.</span></span> <span data-ttu-id="c7522-152">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="c7522-152">The program produces the following output:</span></span>

```
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="c7522-153">總結</span><span class="sxs-lookup"><span data-stu-id="c7522-153">Summary</span></span>

<span data-ttu-id="c7522-154">在此逐步解說中，我們討論過如何將資料表值函式 (Tvf) 使用 Entity Framework 設計工具的對應。</span><span class="sxs-lookup"><span data-stu-id="c7522-154">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="c7522-155">它也會示範如何從 LINQ 查詢中呼叫 TVF。</span><span class="sxs-lookup"><span data-stu-id="c7522-155">It also demonstrated how to call a TVF from a LINQ query.</span></span>
