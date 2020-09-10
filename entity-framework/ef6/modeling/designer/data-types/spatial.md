---
title: 空間-EF Designer-EF6
description: 空間-Entity Framework 6 中的 EF 設計工具
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 10430d6a043182e5536dfad148dc4158cf4c96c9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620484"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="fa9ba-103">空間-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="fa9ba-103">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="fa9ba-104">**EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="fa9ba-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="fa9ba-106">影片和逐步解說會示範如何使用 Entity Framework Designer 來對應空間類型。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-106">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="fa9ba-107">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="fa9ba-108">本逐步解說會使用 Model First 來建立新的資料庫，但 EF 設計工具也可以與 [Database First](xref:ef6/modeling/designer/workflows/database-first) 工作流程搭配使用，以對應至現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="fa9ba-109">Entity Framework 5 引進了空間類型支援。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="fa9ba-110">請注意，若要使用空間類型、列舉和資料表值函式之類的新功能，您必須將目標設為 .NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="fa9ba-111">Visual Studio 2012 預設會以 .NET 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="fa9ba-112">若要使用空間資料類型，您也必須使用具有空間支援的 Entity Framework 提供者。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="fa9ba-113">如需詳細資訊，請參閱 [提供者的空間類型支援](xref:ef6/fundamentals/providers/spatial-support) 。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="fa9ba-114">有兩個主要的空間資料類型： geography 和 geometry。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="fa9ba-115">Geography 資料類型會儲存橢圓體資料 (例如，GPS 緯度和經度座標) 。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="fa9ba-116">Geometry 資料類型代表 Euclidean (的平面) 座標系統。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="fa9ba-117">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="fa9ba-117">Watch the video</span></span>
<span data-ttu-id="fa9ba-118">這段影片示範如何使用 Entity Framework Designer 來對應空間類型。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-118">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="fa9ba-119">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="fa9ba-120">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="fa9ba-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="fa9ba-121">**影片**： [wmv](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)的  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="fa9ba-121">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="fa9ba-122">必要條件</span><span class="sxs-lookup"><span data-stu-id="fa9ba-122">Pre-Requisites</span></span>

<span data-ttu-id="fa9ba-123">您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="fa9ba-124">設定專案</span><span class="sxs-lookup"><span data-stu-id="fa9ba-124">Set up the Project</span></span>

1.  <span data-ttu-id="fa9ba-125">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="fa9ba-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="fa9ba-126">**在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="fa9ba-127">在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="fa9ba-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="fa9ba-128">輸入 **SpatialEFDesigner** 做為專案的名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="fa9ba-128">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="fa9ba-129">使用 EF 設計工具建立新的模型</span><span class="sxs-lookup"><span data-stu-id="fa9ba-129">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="fa9ba-130">在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [**加入**]，然後按一下 [**新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-130">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="fa9ba-131">從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]</span><span class="sxs-lookup"><span data-stu-id="fa9ba-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="fa9ba-132">輸入**UniversityModel**的檔案名，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-132">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="fa9ba-133">在實體資料模型 Wizard] 頁面的 [選擇模型內容] 對話方塊中，選取 [ **空的模型** ]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-133">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="fa9ba-134">按一下 [完成]</span><span class="sxs-lookup"><span data-stu-id="fa9ba-134">Click **Finish**</span></span>

<span data-ttu-id="fa9ba-135">隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="fa9ba-136">精靈會執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="fa9ba-136">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="fa9ba-137">產生定義概念模型、儲存體模型和兩者之間對應的 EnumTestModel .edmx 檔。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-137">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="fa9ba-138">設定 .edmx 檔的中繼資料成品處理屬性，以內嵌在輸出元件中，以便將產生的中繼資料檔案內嵌至元件。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-138">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="fa9ba-139">將參考加入至下列元件： EntityFramework、ComponentModel DataAnnotations 和 System.object。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-139">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="fa9ba-140">建立 UniversityModel.tt 和 UniversityModel.CoNtext.tt 檔案，並將它們新增至 .edmx 檔案底下。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-140">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="fa9ba-141">這些 T4 範本檔產生的程式碼會定義 DbCoNtext 衍生型別，以及對應至 .edmx 模型中之實體的 POCO 類型。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-141">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="fa9ba-142">加入新的實體類型</span><span class="sxs-lookup"><span data-stu-id="fa9ba-142">Add a New Entity Type</span></span>

1.  <span data-ttu-id="fa9ba-143">以滑鼠右鍵按一下設計介面的空白區域，然後選取 [ **加入 &gt; 實體**]，[新增實體] 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-143">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="fa9ba-144">指定類型名稱的 **大學** ，並為索引鍵屬性名稱指定 **UniversityID** ，將類型保留為 **Int32**</span><span class="sxs-lookup"><span data-stu-id="fa9ba-144">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="fa9ba-145">按一下 [檔案] &gt; [新增] &gt; [專案] </span><span class="sxs-lookup"><span data-stu-id="fa9ba-145">Click **OK**</span></span>
4.  <span data-ttu-id="fa9ba-146">以滑鼠右鍵按一下實體，然後選取 [**加入 &gt; 新的-** 純量屬性]</span><span class="sxs-lookup"><span data-stu-id="fa9ba-146">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="fa9ba-147">將新屬性重新命名為 **Name**</span><span class="sxs-lookup"><span data-stu-id="fa9ba-147">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="fa9ba-148">新增另一個純量屬性，並將它重新命名為 **Location** 開啟屬性視窗，並將新屬性的類型變更為 **Geography**</span><span class="sxs-lookup"><span data-stu-id="fa9ba-148">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="fa9ba-149">儲存模型並建立專案</span><span class="sxs-lookup"><span data-stu-id="fa9ba-149">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="fa9ba-150">當您建立時，有關未對應實體和關聯的警告可能會出現在 [錯誤清單] 中。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-150">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="fa9ba-151">您可以忽略這些警告，因為在我們選擇從模型產生資料庫之後，錯誤就會消失。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-151">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="fa9ba-152">從模型產生資料庫</span><span class="sxs-lookup"><span data-stu-id="fa9ba-152">Generate Database from Model</span></span>

<span data-ttu-id="fa9ba-153">現在我們可以產生以模型為基礎的資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-153">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="fa9ba-154">以滑鼠右鍵按一下 Entity Designer 介面上的空白處，然後選取 [**從模型產生資料庫**]</span><span class="sxs-lookup"><span data-stu-id="fa9ba-154">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="fa9ba-155">[產生資料庫嚮導] 的 [選擇您的資料連線] 對話方塊隨即顯示，請按一下 [**新增連接**] 按鈕，為資料庫的伺服器名稱和**大學**指定\*\* (localdb) \\ Mssqllocaldb\*\* ，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-155">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="fa9ba-156">對話方塊會詢問您是否要建立新的資料庫，請按一下 **[是**]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-156">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="fa9ba-157">按一下 **[下一步** ]，[建立資料庫嚮導] 會產生資料定義語言 (DDL) 若要建立資料庫，則會在 [摘要和設定] 對話方塊中顯示產生的 DDL，請注意，ddl 不包含對應至列舉類型之資料表的定義</span><span class="sxs-lookup"><span data-stu-id="fa9ba-157">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="fa9ba-158">按一下 **[** 完成]，然後按一下 [完成]，就不會執行 DDL 腳本。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-158">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="fa9ba-159">Create Database Wizard 會執行下列動作：在 T-sql 編輯器中開啟 **UniversityModel** 會產生儲存架構，而 edmx 檔案的對應區段會將連接字串資訊加入 App.config 檔案</span><span class="sxs-lookup"><span data-stu-id="fa9ba-159">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="fa9ba-160">在 T-sql 編輯器中按一下滑鼠右鍵，然後選取 [**執行**連接至伺服器] 對話方塊，輸入步驟2中的連接資訊，然後按一下 **[連接]** 。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-160">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="fa9ba-161">若要查看產生的架構，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵，**然後選取 [** 重新整理]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-161">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="fa9ba-162">保存和取出資料</span><span class="sxs-lookup"><span data-stu-id="fa9ba-162">Persist and Retrieve Data</span></span>

<span data-ttu-id="fa9ba-163">開啟定義 Main 方法的 Program.cs 檔案。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-163">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="fa9ba-164">將下列程式碼新增至 Main 函數。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-164">Add the following code into the Main function.</span></span>

<span data-ttu-id="fa9ba-165">此程式碼會將兩個新的大學物件新增至內容中。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-165">The code adds two new University objects to the context.</span></span> <span data-ttu-id="fa9ba-166">空間屬性會使用 DbGeography. Value.fromtext 方法來初始化。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-166">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="fa9ba-167">以 WellKnownText 表示的地理位置點會傳遞給方法。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-167">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="fa9ba-168">然後，程式碼會儲存資料。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-168">The code then saves the data.</span></span> <span data-ttu-id="fa9ba-169">然後，會建立並執行 LINQ 查詢，該查詢會傳回其位置最接近指定位置的大學物件。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-169">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

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

<span data-ttu-id="fa9ba-170">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-170">Compile and run the application.</span></span> <span data-ttu-id="fa9ba-171">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="fa9ba-171">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="fa9ba-172">若要在資料庫中查看資料，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵， **然後選取 [** 重新整理]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-172">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="fa9ba-173">然後，按一下資料表上的滑鼠右鍵，然後選取 [ **View Data**]。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-173">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="fa9ba-174">摘要</span><span class="sxs-lookup"><span data-stu-id="fa9ba-174">Summary</span></span>

<span data-ttu-id="fa9ba-175">在這個逐步解說中，我們探討了如何使用 Entity Framework Designer 來對應空間類型，以及如何在程式碼中使用空間類型。</span><span class="sxs-lookup"><span data-stu-id="fa9ba-175">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
