---
title: 資料表值函式 (Tvf)-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
caps.latest.revision: 3
ms.openlocfilehash: 7d652725a2655b691b03aa3f43103753fe72ede7
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120490"
---
# <a name="table-valued-functions-tvfs"></a>資料表值函式 (Tvf)
> [!NOTE]
> **EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。 如果您使用的是較早版本，則不適用部分或全部的資訊。

影片以及逐步說明的逐步解說會示範如何將資料表值函式 (Tvf) 使用 Entity Framework 設計工具的對應。 它也會示範如何從 LINQ 查詢中呼叫 TVF。

Tvf 目前會在第一個資料庫的工作流程中才支援。

Entity Framework 第 5 版中引進 TVF 支援。 請注意，使用新的功能，例如資料表值函式、 列舉、 和空間類型，您必須以.NET Framework 4.5 為目標。 Visual Studio 2012 的預設值為目標.NET 4.5。

Tvf 會非常類似於預存程序只有一個主要差異： TVF 的結果是可組合。 這表示 TVF 的結果可以用 LINQ 查詢中，而預存程序的結果不能。

## <a name="watch-the-video"></a>觀看影片

**由**: Julia Kornich

[WMV](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)

## <a name="pre-requisites"></a>必要條件

若要完成此逐步解說，您需要：

- 安裝[School 資料庫](~/ef6/resources/school-database.md)。

- 有新版的 Visual Studio

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio
2.  在 **檔案**功能表上，指向**新增**，然後按一下 **專案**
3.  在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本
4.  請輸入**TVF**做為名稱的專案，然後按一下 **[確定]**

## <a name="add-a-tvf-to-the-database"></a>加入資料庫中的 TVF

-   選取 **檢視-&gt; SQL Server 物件總管**
-   如果伺服器清單中不是 LocalDB： 以滑鼠右鍵按一下**SQL Server** ，然後選取**加入 SQL Server**使用預設值**Windows 驗證**連接到 LocalDB 伺服器
-   展開 [LocalDB] 節點
-   在 [資料庫] 節點中，School 資料庫節點上按一下滑鼠右鍵，然後選取**新增查詢...**
-   在 T-SQL 編輯器中，貼上下列 TVF 定義

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

-   按一下滑鼠右鍵，在 T-SQL 編輯器中，選取**Execute**
-   GetStudentGradesForCourse 函式加入至 School 資料庫

 

## <a name="create-a-model"></a>建立模型

1.  以滑鼠右鍵按一下方案總管] 中的專案名稱，指向**新增**，然後按一下 [**新項目**
2.  選取 **資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**中**範本**窗格
3.  請輸入**TVFModel.edmx**為檔案名稱，然後按一下**新增**
4.  在 [選擇模型內容] 對話方塊中，選取**從資料庫產生**，然後按一下 [**下一步]**
5.  按一下 **新的連線**Enter **(localdb)\\mssqllocaldb**伺服器的 名稱 文字方塊在方塊中輸入**學校**資料庫名稱按一下**確定**
6.  在 選擇您的資料庫物件對話方塊的 **資料表**節點中，選取**人員**， **StudentGrade**，以及**課程**資料表
7.  選取  **GetStudentGradesForCourse**函式位於**預存程序和函式**節點附註，該開始使用 Visual Studio 2012，Entity Designer 可讓您將批次匯入您的預存程序和函式
8.  按一下 **完成**
9.  Entity Designer 中，提供用於編輯模型的設計介面，會顯示。 您在選取的所有物件**選擇您的資料庫物件**對話方塊加入至模型。
10. 根據預設，每個匯入的預存程序或函式的結果圖案會自動成為您的實體模型中的新複雜型別。 但我們想要將 GetStudentGradesForCourse 函式的結果對應至 StudentGrade 實體： 以滑鼠右鍵按一下設計介面，然後選取**模型瀏覽器**模型瀏覽器中，選取**函式匯入**，然後按兩下**GetStudentGradesForCourse**函式中編輯函式匯入對話方塊中，選取**實體**，然後選擇  **StudentGrade**

## <a name="persist-and-retrieve-data"></a>持續儲存並擷取資料

開啟的 Main 方法定義所在的檔案。 將下列程式碼新增至 Main 函式。

下列程式碼示範如何建置使用資料表值函式的查詢。 查詢會將結果投射至匿名型別，其中包含相關的課程標題和相關的學生成績大於或等於為 3.5。

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

編譯並執行應用程式。 此程式會產生下列輸出：

```
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a>總結

在此逐步解說中，我們討論過如何將資料表值函式 (Tvf) 使用 Entity Framework 設計工具的對應。 它也會示範如何從 LINQ 查詢中呼叫 TVF。
