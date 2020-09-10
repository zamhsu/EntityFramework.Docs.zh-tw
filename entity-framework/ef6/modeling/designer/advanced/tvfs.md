---
title: 資料表值函式 (Tvf) -EF6
description: 'Entity Framework 6 中的資料表值函數 (Tvf) '
author: divega
ms.date: 10/23/2016
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 1575526fb46f9ddd3ad43c7c4ac0304aefa1d5d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620548"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="1fcb5-103">資料表值函式 (Tvf) </span><span class="sxs-lookup"><span data-stu-id="1fcb5-103">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="1fcb5-104">**EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="1fcb5-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="1fcb5-106">影片和逐步解說示範如何使用 Entity Framework Designer，將資料表值函式 (Tvf) 對應。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-106">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="1fcb5-107">它也會示範如何從 LINQ 查詢呼叫 TVF。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-107">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="1fcb5-108">Database First 工作流程目前僅支援 Tvf。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-108">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="1fcb5-109">TVF 支援是在 Entity Framework 第5版中引進。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-109">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="1fcb5-110">請注意，若要使用新功能（例如資料表值函式、列舉和空間類型），您必須將目標設為 .NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-110">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="1fcb5-111">Visual Studio 2012 預設會以 .NET 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="1fcb5-112">Tvf 與預存程式非常類似，但有一個主要差異： TVF 的結果是可組合的。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-112">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="1fcb5-113">這表示 TVF 的結果可以在 LINQ 查詢中使用，而預存程式的結果則無法使用。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-113">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="1fcb5-114">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="1fcb5-114">Watch the video</span></span>

<span data-ttu-id="1fcb5-115">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="1fcb5-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="1fcb5-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)的  | [WMV (ZIP) ](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="1fcb5-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="1fcb5-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="1fcb5-117">Pre-Requisites</span></span>

<span data-ttu-id="1fcb5-118">若要完成這個逐步解說，您需要：</span><span class="sxs-lookup"><span data-stu-id="1fcb5-118">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="1fcb5-119">安裝 [School 資料庫](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-119">Install the [School database](xref:ef6/resources/school-database).</span></span>

- <span data-ttu-id="1fcb5-120">具有最新版本的 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fcb5-120">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="1fcb5-121">設定專案</span><span class="sxs-lookup"><span data-stu-id="1fcb5-121">Set up the Project</span></span>

1.  <span data-ttu-id="1fcb5-122">開啟 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fcb5-122">Open Visual Studio</span></span>
2.  <span data-ttu-id="1fcb5-123">**在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-123">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="1fcb5-124">在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="1fcb5-124">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="1fcb5-125">輸入 **TVF** 作為專案的名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="1fcb5-125">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="1fcb5-126">將 TVF 新增至資料庫</span><span class="sxs-lookup"><span data-stu-id="1fcb5-126">Add a TVF to the Database</span></span>

-   <span data-ttu-id="1fcb5-127">選取 **View- &gt; SQL Server 物件總管**</span><span class="sxs-lookup"><span data-stu-id="1fcb5-127">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="1fcb5-128">如果 LocalDB 不在伺服器清單中：以滑鼠右鍵按一下 [ **SQL Server** ]，然後選取 [ **新增] SQL Server** 使用預設的 **Windows 驗證** 連接到 LocalDB 伺服器</span><span class="sxs-lookup"><span data-stu-id="1fcb5-128">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="1fcb5-129">展開 LocalDB 節點</span><span class="sxs-lookup"><span data-stu-id="1fcb5-129">Expand the LocalDB node</span></span>
-   <span data-ttu-id="1fcb5-130">在 [資料庫] 節點底下，以滑鼠右鍵按一下 School 資料庫節點，然後選取 [追加 **查詢**]。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-130">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="1fcb5-131">在 [T-sql 編輯器] 中，貼上下列 TVF 定義</span><span class="sxs-lookup"><span data-stu-id="1fcb5-131">In T-SQL Editor, paste the following TVF definition</span></span>

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

-   <span data-ttu-id="1fcb5-132">在 T-sql 編輯器上按一下滑鼠右鍵，然後選取 [ **執行**]</span><span class="sxs-lookup"><span data-stu-id="1fcb5-132">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="1fcb5-133">GetStudentGradesForCourse 函式會新增至 School 資料庫</span><span class="sxs-lookup"><span data-stu-id="1fcb5-133">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="1fcb5-134">建立模型</span><span class="sxs-lookup"><span data-stu-id="1fcb5-134">Create a Model</span></span>

1.  <span data-ttu-id="1fcb5-135">在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [**加入**]，然後按一下 [**新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-135">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="1fcb5-136">從左側功能表中選取 [**資料**]，然後選取 [**範本**] 窗格中的 [ **ADO.NET 實體資料模型**]</span><span class="sxs-lookup"><span data-stu-id="1fcb5-136">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="1fcb5-137">輸入**TVFModel**的檔案名，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-137">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="1fcb5-138">在 [選擇模型內容] 對話方塊中，選取 [ **從資料庫產生**]，然後按 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-138">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="1fcb5-139">在 [伺服器名稱] 文字方塊中，按一下 [ **新增連接**] \*\* (localdb) \\ Mssqllocaldb**中，輸入**School\*\*   作為資料庫名稱，按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-139">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="1fcb5-140">在 [選擇您的資料庫物件] 對話方塊的 [ **資料表**   ] 節點底下，選取 [ **Person**]、[ **StudentGrade**] 和 [ **課程**   ] 資料表。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-140">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="1fcb5-141">選取位於 **預存程式和**函式節點附注下的 **GetStudentGradesForCourse**函式，該函式   會從 Visual Studio 2012 開始，Entity Designer 可讓您將預存程式和函式分批匯入</span><span class="sxs-lookup"><span data-stu-id="1fcb5-141">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="1fcb5-142">按一下 **[完成]**</span><span class="sxs-lookup"><span data-stu-id="1fcb5-142">Click **Finish**</span></span>
9.  <span data-ttu-id="1fcb5-143">隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-143">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="1fcb5-144">在 [ **選擇您的資料庫物件**] 對話方塊中選取的所有物件   都會加入至模型中。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-144">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="1fcb5-145">根據預設，每個匯入的預存程式或函數的結果圖形都會自動成為實體模型中的新複雜型別。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-145">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="1fcb5-146">但是，我們想要將 GetStudentGradesForCourse 函式的結果對應至 StudentGrade 實體：以滑鼠右鍵按一下設計介面，然後選取 [模型瀏覽器] 中的 [ **模型瀏覽器**] **，選取 [** 函式匯入]，然後按兩下 [編輯函式匯入] 對話方塊中的 **GetStudentGradesForCourse**函式，選取 **實體**，   然後選擇 **StudentGrade**</span><span class="sxs-lookup"><span data-stu-id="1fcb5-146">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="1fcb5-147">保存和取出資料</span><span class="sxs-lookup"><span data-stu-id="1fcb5-147">Persist and Retrieve Data</span></span>

<span data-ttu-id="1fcb5-148">開啟定義主要方法的檔案。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-148">Open the file where the Main method is defined.</span></span> <span data-ttu-id="1fcb5-149">將下列程式碼新增至 Main 函數。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-149">Add the following code into the Main function.</span></span>

<span data-ttu-id="1fcb5-150">下列程式碼示範如何建立使用資料表值函式的查詢。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-150">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="1fcb5-151">此查詢會將結果投射至匿名型別，其中包含相關的課程標題和等級大於或等於3.5 的相關學生。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-151">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

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

<span data-ttu-id="1fcb5-152">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-152">Compile and run the application.</span></span> <span data-ttu-id="1fcb5-153">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="1fcb5-153">The program produces the following output:</span></span>

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="1fcb5-154">摘要</span><span class="sxs-lookup"><span data-stu-id="1fcb5-154">Summary</span></span>

<span data-ttu-id="1fcb5-155">在這個逐步解說中，我們探討了如何使用 Entity Framework Designer (Tvf) 來對應資料表值函式。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-155">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="1fcb5-156">它也示範了如何從 LINQ 查詢呼叫 TVF。</span><span class="sxs-lookup"><span data-stu-id="1fcb5-156">It also demonstrated how to call a TVF from a LINQ query.</span></span>
