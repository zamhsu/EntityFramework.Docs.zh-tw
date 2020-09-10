---
title: 空間-Code First-EF6
description: Entity Framework 6 中的空間 Code First
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 8c2d47a66979a228fdc4902857df999a21945547
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618166"
---
# <a name="spatial---code-first"></a><span data-ttu-id="8b3ac-103">空間-Code First</span><span class="sxs-lookup"><span data-stu-id="8b3ac-103">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="8b3ac-104">**EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="8b3ac-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="8b3ac-106">影片和逐步解說會示範如何使用 Entity Framework Code First 來對應空間類型。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-106">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="8b3ac-107">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="8b3ac-108">本逐步解說將使用 Code First 建立新的資料庫，但您也可以使用 [Code First 到現有的資料庫](xref:ef6/modeling/code-first/workflows/existing-database)。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="8b3ac-109">Entity Framework 5 引進了空間類型支援。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="8b3ac-110">請注意，若要使用空間類型、列舉和資料表值函式之類的新功能，您必須將目標設為 .NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="8b3ac-111">Visual Studio 2012 預設會以 .NET 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="8b3ac-112">若要使用空間資料類型，您也必須使用具有空間支援的 Entity Framework 提供者。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="8b3ac-113">如需詳細資訊，請參閱 [提供者的空間類型支援](xref:ef6/fundamentals/providers/spatial-support) 。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="8b3ac-114">有兩個主要的空間資料類型： geography 和 geometry。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="8b3ac-115">Geography 資料類型會儲存橢圓體資料 (例如，GPS 緯度和經度座標) 。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="8b3ac-116">Geometry 資料類型代表 Euclidean (的平面) 座標系統。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="8b3ac-117">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="8b3ac-117">Watch the video</span></span>
<span data-ttu-id="8b3ac-118">這段影片示範如何使用 Entity Framework Code First 來對應空間類型。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-118">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="8b3ac-119">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="8b3ac-120">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="8b3ac-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="8b3ac-121">**影片**： [wmv](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)的  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="8b3ac-121">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="8b3ac-122">必要條件</span><span class="sxs-lookup"><span data-stu-id="8b3ac-122">Pre-Requisites</span></span>

<span data-ttu-id="8b3ac-123">您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="8b3ac-124">設定專案</span><span class="sxs-lookup"><span data-stu-id="8b3ac-124">Set up the Project</span></span>

1.  <span data-ttu-id="8b3ac-125">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="8b3ac-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="8b3ac-126">**在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="8b3ac-127">在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="8b3ac-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="8b3ac-128">輸入 **SpatialCodeFirst** 做為專案的名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="8b3ac-128">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="8b3ac-129">使用 Code First 定義新模型</span><span class="sxs-lookup"><span data-stu-id="8b3ac-129">Define a New Model using Code First</span></span>

<span data-ttu-id="8b3ac-130">使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-130">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="8b3ac-131">下列程式碼會定義大學類別。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-131">The code below defines the University class.</span></span>

<span data-ttu-id="8b3ac-132">大學具有 DbGeography 類型的 Location 屬性。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-132">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="8b3ac-133">若要使用 DbGeography 型別，您必須加入 system.string 元件的參考，並使用語句加入 system.string。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-133">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="8b3ac-134">開啟 Program.cs 檔案，並將下列 using 語句貼到檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="8b3ac-134">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="8b3ac-135">將下列大學類別定義新增至 Program.cs 檔案。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-135">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="8b3ac-136">定義 DbCoNtext 衍生類型</span><span class="sxs-lookup"><span data-stu-id="8b3ac-136">Define the DbContext Derived Type</span></span>

<span data-ttu-id="8b3ac-137">除了定義實體之外，您還必須定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="8b3ac-138">DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="8b3ac-139">DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="8b3ac-140">DbCoNtext 和 DbSet 類型是在 EntityFramework 元件中定義。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-140">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="8b3ac-141">我們會使用 EntityFramework NuGet 封裝來新增此 DLL 的參考。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-141">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="8b3ac-142">在方案總管中，以滑鼠右鍵按一下專案名稱。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-142">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="8b3ac-143">選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="8b3ac-143">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="8b3ac-144">在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-144">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="8b3ac-145">按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="8b3ac-145">Click **Install**</span></span>

<span data-ttu-id="8b3ac-146">請注意，除了 EntityFramework 元件之外，也會加入 ComponentModel. DataAnnotations 元件的參考。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-146">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="8b3ac-147">在 Program.cs 檔案的頂端，新增下列 using 語句：</span><span class="sxs-lookup"><span data-stu-id="8b3ac-147">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="8b3ac-148">在 Program.cs 中加入內容定義。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-148">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="8b3ac-149">保存和取出資料</span><span class="sxs-lookup"><span data-stu-id="8b3ac-149">Persist and Retrieve Data</span></span>

<span data-ttu-id="8b3ac-150">開啟定義 Main 方法的 Program.cs 檔案。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-150">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="8b3ac-151">將下列程式碼新增至 Main 函數。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-151">Add the following code into the Main function.</span></span>

<span data-ttu-id="8b3ac-152">此程式碼會將兩個新的大學物件新增至內容中。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-152">The code adds two new University objects to the context.</span></span> <span data-ttu-id="8b3ac-153">空間屬性會使用 DbGeography. Value.fromtext 方法來初始化。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-153">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="8b3ac-154">以 WellKnownText 表示的地理位置點會傳遞給方法。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-154">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="8b3ac-155">然後，程式碼會儲存資料。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-155">The code then saves the data.</span></span> <span data-ttu-id="8b3ac-156">然後，會建立並執行 LINQ 查詢，該查詢會傳回其位置最接近指定位置的大學物件。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-156">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
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

<span data-ttu-id="8b3ac-157">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-157">Compile and run the application.</span></span> <span data-ttu-id="8b3ac-158">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="8b3ac-158">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="8b3ac-159">查看產生的資料庫</span><span class="sxs-lookup"><span data-stu-id="8b3ac-159">View the Generated Database</span></span>

<span data-ttu-id="8b3ac-160">當您第一次執行應用程式時，Entity Framework 會為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-160">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="8b3ac-161">因為我們安裝了 Visual Studio 2012，所以會在 LocalDB 實例上建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-161">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="8b3ac-162">根據預設，Entity Framework 會在衍生內容的完整名稱之後，將資料庫命名 (在此範例中為 **SpatialCodeFirst. UniversityCoNtext**) 。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-162">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="8b3ac-163">將會使用現有資料庫的後續時間。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-163">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="8b3ac-164">請注意，如果您在建立資料庫之後對模型進行任何變更，則應該使用 Code First 移轉來更新資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-164">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="8b3ac-165">如需使用遷移的範例，請參閱 [Code First 至新的資料庫](xref:ef6/modeling/code-first/workflows/new-database) 。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-165">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="8b3ac-166">若要查看資料庫和資料，請執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="8b3ac-166">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="8b3ac-167">在 [Visual Studio 2012] 主功能表中，選取 [ **View**  - &gt; **SQL Server 物件總管**。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-167">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="8b3ac-168">如果 LocalDB 不在伺服器清單中，請在 **SQL Server** 上按一下滑鼠右鍵，然後選取 [ **加入] SQL Server** 使用預設的 **Windows 驗證** 連接到 LocalDB 實例</span><span class="sxs-lookup"><span data-stu-id="8b3ac-168">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="8b3ac-169">展開 LocalDB 節點</span><span class="sxs-lookup"><span data-stu-id="8b3ac-169">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="8b3ac-170">展開 [ **資料庫** ] 資料夾以查看新的資料庫，並流覽至 [ **大學** ] 資料表</span><span class="sxs-lookup"><span data-stu-id="8b3ac-170">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="8b3ac-171">若要查看資料，請以滑鼠右鍵按一下資料表，然後選取 [ **View data** ]</span><span class="sxs-lookup"><span data-stu-id="8b3ac-171">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="8b3ac-172">摘要</span><span class="sxs-lookup"><span data-stu-id="8b3ac-172">Summary</span></span>

<span data-ttu-id="8b3ac-173">在本逐步解說中，我們探討了如何搭配 Entity Framework Code First 來使用空間類型。</span><span class="sxs-lookup"><span data-stu-id="8b3ac-173">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
