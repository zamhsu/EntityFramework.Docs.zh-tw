---
title: 空間-EF 設計工具-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
caps.latest.revision: 3
ms.openlocfilehash: 2332818275763fd90274f426b7fced4c3c14ac17
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120455"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="0b5e6-102">空間-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="0b5e6-102">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="0b5e6-103">**EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="0b5e6-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="0b5e6-105">影片以及逐步說明的逐步解說示範如何將使用 Entity Framework Designer 的空間型別對應。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-105">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="0b5e6-106">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-106">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="0b5e6-107">本逐步解說將使用 Model First 來建立新的資料庫，但 EF 設計工具也可搭配[Database First](~/ef6/modeling/designer/workflows/database-first.md)的工作流程，對應到現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-107">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](~/ef6/modeling/designer/workflows/database-first.md) workflow to map to an existing database.</span></span>

<span data-ttu-id="0b5e6-108">在 Entity Framework 5 中引進的空間型別支援。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-108">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="0b5e6-109">請注意，若要使用的空間型別、 列舉、 等資料表值函式的新功能，您必須為目標.NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-109">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="0b5e6-110">Visual Studio 2012 的預設值為目標.NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="0b5e6-111">若要使用的空間資料類型也必須使用有空間支援 Entity Framework 提供者。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-111">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="0b5e6-112">請參閱[空間類型的提供者支援](~/ef6/fundamentals/providers/spatial-support.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-112">See [provider support for spatial types](~/ef6/fundamentals/providers/spatial-support.md) for more information.</span></span>

<span data-ttu-id="0b5e6-113">有兩種主要的空間資料類型： geography 和 geometry。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-113">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="0b5e6-114">Geography 資料類型會儲存橢圓體資料 （例如 GPS 經緯度座標）。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-114">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="0b5e6-115">Geometry 資料類型代表 Euclidean （平面） 座標系統。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-115">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="0b5e6-116">觀看影片</span><span class="sxs-lookup"><span data-stu-id="0b5e6-116">Watch the video</span></span>
<span data-ttu-id="0b5e6-117">這段影片示範如何將使用 Entity Framework Designer 的空間型別對應。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-117">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="0b5e6-118">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-118">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="0b5e6-119">**由**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="0b5e6-119">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="0b5e6-120">**視訊**: [WMV](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="0b5e6-120">**Video**: [WMV](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="0b5e6-121">必要條件</span><span class="sxs-lookup"><span data-stu-id="0b5e6-121">Pre-Requisites</span></span>

<span data-ttu-id="0b5e6-122">您必須擁有 Visual Studio 2012、 Ultimate、 Premium、 Professional 或 Web Express edition 安裝才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-122">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="0b5e6-123">設定專案</span><span class="sxs-lookup"><span data-stu-id="0b5e6-123">Set up the Project</span></span>

1.  <span data-ttu-id="0b5e6-124">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="0b5e6-124">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="0b5e6-125">在 **檔案**功能表上，指向**新增**，然後按一下 **專案**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-125">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="0b5e6-126">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="0b5e6-126">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="0b5e6-127">請輸入**SpatialEFDesigner**做為名稱的專案，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-127">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="0b5e6-128">建立新的模型使用 EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="0b5e6-128">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="0b5e6-129">以滑鼠右鍵按一下方案總管] 中的專案名稱，指向**新增**，然後按一下 [**新項目**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="0b5e6-130">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中</span><span class="sxs-lookup"><span data-stu-id="0b5e6-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="0b5e6-131">請輸入**UniversityModel.edmx**為檔案名稱，然後按一下**新增**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-131">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="0b5e6-132">在 Entity Data Model 精靈 頁面上，選取**空的模型**在選擇模型內容 對話方塊</span><span class="sxs-lookup"><span data-stu-id="0b5e6-132">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="0b5e6-133">按一下 **完成**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-133">Click **Finish**</span></span>

<span data-ttu-id="0b5e6-134">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-134">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="0b5e6-135">精靈會執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="0b5e6-135">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="0b5e6-136">產生 EnumTestModel.edmx 檔案定義概念模型、 儲存體模型和兩者之間的對應。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-136">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="0b5e6-137">讓產生的中繼資料檔案取得內嵌於組件，請將.edmx 檔案的 Metadata Artifact Processing 屬性設定為內嵌於輸出組件中。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-137">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="0b5e6-138">加入下列組件的參考： EntityFramework、 System.ComponentModel.DataAnnotations 和 System.Data.Entity。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-138">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="0b5e6-139">建立 UniversityModel.tt 和 UniversityModel.Context.tt 檔案，並將它們加入在.edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-139">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="0b5e6-140">這些 T4 範本檔案產生的衍生 DbContext 類型和對應至.edmx 模型中實體的 POCO 型別定義的程式碼</span><span class="sxs-lookup"><span data-stu-id="0b5e6-140">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="0b5e6-141">加入新的實體類型</span><span class="sxs-lookup"><span data-stu-id="0b5e6-141">Add a New Entity Type</span></span>

1.  <span data-ttu-id="0b5e6-142">以滑鼠右鍵按一下設計介面上選取的空白處**新增-&gt;實體**，新的實體 對話方塊隨即出現</span><span class="sxs-lookup"><span data-stu-id="0b5e6-142">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="0b5e6-143">指定**大學**類型的名稱，並指定**UniversityID**做為索引鍵屬性名稱，將保留的類型為**Int32**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-143">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="0b5e6-144">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="0b5e6-144">Click **OK**</span></span>
4.  <span data-ttu-id="0b5e6-145">以滑鼠右鍵按一下實體，然後選取**加入新-&gt;純量屬性**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-145">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="0b5e6-146">重新命名新的屬性到**名稱**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-146">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="0b5e6-147">新增另一個純量屬性，並重新命名為**位置**開啟 [屬性] 視窗，並變更新屬性的型別**地理位置**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-147">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="0b5e6-148">儲存模型，並建置專案</span><span class="sxs-lookup"><span data-stu-id="0b5e6-148">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="0b5e6-149">當您建置時，未對應的實體和關聯有關的警告可能會出現在 錯誤清單。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-149">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="0b5e6-150">您可以忽略這些警告，因為錯誤，我們選擇將透過模型產生資料庫之後，就會消失。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-150">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="0b5e6-151">從模型產生資料庫</span><span class="sxs-lookup"><span data-stu-id="0b5e6-151">Generate Database from Model</span></span>

<span data-ttu-id="0b5e6-152">現在我們可以產生以模型為基礎的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-152">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="0b5e6-153">以滑鼠右鍵按一下 Entity designer 介面，然後選取空白空間**從模型產生資料庫**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-153">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="0b5e6-154">選擇您的資料連接對話方塊的 產生資料庫精靈 會顯示按一下**新的連線**按鈕指定 **(localdb)\\mssqllocaldb**伺服器名稱 和  **大學**資料庫，然後按一下  **確定**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-154">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="0b5e6-155">將快顯對話方塊，詢問您是否要建立新的資料庫中，按一下**是**。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-155">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="0b5e6-156">按一下 [**下一步]** 並建立資料庫產生的 DDL 會顯示在摘要和設定對話方塊方塊中請注意，DDL 不包含的定義，建立資料庫精靈會產生資料定義語言 (DDL)對應至列舉類型的資料表</span><span class="sxs-lookup"><span data-stu-id="0b5e6-156">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="0b5e6-157">按一下 **完成**按一下 完成 將不會執行 DDL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-157">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="0b5e6-158">建立資料庫精靈會進行下列作業： 開啟**UniversityModel.edmx.sql** T-SQL 編輯器產生之 EDMX 的存放區結構描述和對應區段檔案中加入連接字串資訊加入 App.config 檔案</span><span class="sxs-lookup"><span data-stu-id="0b5e6-158">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="0b5e6-159">按一下滑鼠右鍵在 T-SQL 編輯器中，然後選取**Execute** [連接到伺服器] 對話方塊出現時，請從步驟 2 輸入的連接資訊，然後按一下**Connect**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-159">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="0b5e6-160">若要檢視產生的結構描述，以滑鼠右鍵按一下 SQL Server 物件總管 中的資料庫名稱，然後選取**重新整理**</span><span class="sxs-lookup"><span data-stu-id="0b5e6-160">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="0b5e6-161">持續儲存並擷取資料</span><span class="sxs-lookup"><span data-stu-id="0b5e6-161">Persist and Retrieve Data</span></span>

<span data-ttu-id="0b5e6-162">開啟 Program.cs 檔案的 Main 方法定義的位置。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-162">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="0b5e6-163">將下列程式碼新增至 Main 函式。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-163">Add the following code into the Main function.</span></span>

<span data-ttu-id="0b5e6-164">程式碼會新增兩個新的大學物件內容。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-164">The code adds two new University objects to the context.</span></span> <span data-ttu-id="0b5e6-165">空間的屬性會初始化使用 DbGeography.FromText 方法。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-165">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="0b5e6-166">表示如 WellKnownText 傳遞給方法的地理點。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-166">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="0b5e6-167">程式碼接著會將資料儲存。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-167">The code then saves the data.</span></span> <span data-ttu-id="0b5e6-168">然後，在 LINQ 查詢中傳回最接近指定的位置，其位置所在的大學物件建構及執行。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-168">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

<span data-ttu-id="0b5e6-169">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-169">Compile and run the application.</span></span> <span data-ttu-id="0b5e6-170">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="0b5e6-170">The program produces the following output:</span></span>

```
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="0b5e6-171">若要檢視資料庫中的資料，以滑鼠右鍵按一下 SQL Server 物件總管 中的資料庫名稱，然後選取**重新整理**。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-171">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="0b5e6-172">然後，按一下資料表，然後選取滑鼠右鍵**檢視資料**。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-172">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="0b5e6-173">總結</span><span class="sxs-lookup"><span data-stu-id="0b5e6-173">Summary</span></span>

<span data-ttu-id="0b5e6-174">在此逐步解說中，我們討論過如何將使用 Entity Framework 設計工具的空間型別對應，以及如何在程式碼中使用的空間類型。</span><span class="sxs-lookup"><span data-stu-id="0b5e6-174">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
