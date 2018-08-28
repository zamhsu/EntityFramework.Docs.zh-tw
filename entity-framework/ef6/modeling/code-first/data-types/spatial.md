---
title: 空間-Code First-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
ms.openlocfilehash: d15b407203a7dd7ddf92d0759af00f3ad5e41f57
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995208"
---
# <a name="spatial---code-first"></a><span data-ttu-id="d9f7a-102">空間-Code First</span><span class="sxs-lookup"><span data-stu-id="d9f7a-102">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="d9f7a-103">**EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="d9f7a-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="d9f7a-105">影片以及逐步說明的逐步解說示範如何將使用 Entity Framework Code First 的空間型別對應。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-105">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="d9f7a-106">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-106">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="d9f7a-107">本逐步解說將使用 Code First 來建立新的資料庫，但您也可以使用[Code First 至現有的資料庫](~/ef6/modeling/code-first/workflows/existing-database.md)。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-107">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](~/ef6/modeling/code-first/workflows/existing-database.md).</span></span>

<span data-ttu-id="d9f7a-108">在 Entity Framework 5 中引進的空間型別支援。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-108">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="d9f7a-109">請注意，若要使用的空間型別、 列舉、 等資料表值函式的新功能，您必須為目標.NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-109">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="d9f7a-110">Visual Studio 2012 的預設值為目標.NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="d9f7a-111">若要使用的空間資料類型也必須使用有空間支援 Entity Framework 提供者。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-111">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="d9f7a-112">請參閱[空間類型的提供者支援](~/ef6/fundamentals/providers/spatial-support.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-112">See [provider support for spatial types](~/ef6/fundamentals/providers/spatial-support.md) for more information.</span></span>

<span data-ttu-id="d9f7a-113">有兩種主要的空間資料類型： geography 和 geometry。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-113">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="d9f7a-114">Geography 資料類型會儲存橢圓體資料 （例如 GPS 經緯度座標）。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-114">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="d9f7a-115">Geometry 資料類型代表 Euclidean （平面） 座標系統。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-115">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="d9f7a-116">觀看影片</span><span class="sxs-lookup"><span data-stu-id="d9f7a-116">Watch the video</span></span>
<span data-ttu-id="d9f7a-117">這段影片示範如何將使用 Entity Framework Code First 的空間型別對應。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-117">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="d9f7a-118">它也會示範如何使用 LINQ 查詢來尋找兩個位置之間的距離。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-118">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="d9f7a-119">**由**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="d9f7a-119">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="d9f7a-120">**視訊**: [WMV](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-120">**Video**: [WMV](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="d9f7a-121">必要條件</span><span class="sxs-lookup"><span data-stu-id="d9f7a-121">Pre-Requisites</span></span>

<span data-ttu-id="d9f7a-122">您必須擁有 Visual Studio 2012、 Ultimate、 Premium、 Professional 或 Web Express edition 安裝才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-122">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="d9f7a-123">設定專案</span><span class="sxs-lookup"><span data-stu-id="d9f7a-123">Set up the Project</span></span>

1.  <span data-ttu-id="d9f7a-124">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="d9f7a-124">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="d9f7a-125">在 **檔案**功能表上，指向**新增**，然後按一下 **專案**</span><span class="sxs-lookup"><span data-stu-id="d9f7a-125">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="d9f7a-126">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="d9f7a-126">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="d9f7a-127">請輸入**SpatialCodeFirst**做為名稱的專案，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="d9f7a-127">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="d9f7a-128">定義新的模型使用 Code First</span><span class="sxs-lookup"><span data-stu-id="d9f7a-128">Define a New Model using Code First</span></span>

<span data-ttu-id="d9f7a-129">使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-129">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="d9f7a-130">下列程式碼定義 University 類別。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-130">The code below defines the University class.</span></span>

<span data-ttu-id="d9f7a-131">該大學有 DbGeography 類型的 Location 屬性。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-131">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="d9f7a-132">若要使用的 DbGeography 型別，您必須加入 System.Data.Entity 組件的參考，而且也加入 System.Data.Spatial using 陳述式。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-132">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="d9f7a-133">開啟 Program.cs 檔案並貼上下列 using 陳述式在檔案頂端：</span><span class="sxs-lookup"><span data-stu-id="d9f7a-133">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="d9f7a-134">Program.cs 檔案中加入下列的大學類別定義。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-134">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="d9f7a-135">定義 DbContext 衍生型別</span><span class="sxs-lookup"><span data-stu-id="d9f7a-135">Define the DbContext Derived Type</span></span>

<span data-ttu-id="d9f7a-136">除了定義實體，您需要定義類別，衍生自 DbContext，並公開 DbSet&lt;TEntity&gt;屬性。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-136">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="d9f7a-137">DbSet&lt;TEntity&gt;屬性可讓知道您想要在模型中包含哪些的類型的內容。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-137">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="d9f7a-138">DbContext 衍生型別的執行個體在執行階段，其中包含填入資料庫的資料的物件期間同時管理實體物件、 變更追蹤，和保存資料至資料庫。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-138">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="d9f7a-139">EntityFramework 組件中定義的 DbContext 和 DbSet 型別。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-139">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="d9f7a-140">我們將使用 EntityFramework NuGet 套件，以新增此 DLL 的參考。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-140">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="d9f7a-141">在 [方案總管] 中，以滑鼠右鍵按一下專案名稱。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-141">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="d9f7a-142">選取**管理 NuGet 封裝...**</span><span class="sxs-lookup"><span data-stu-id="d9f7a-142">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="d9f7a-143">在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-143">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="d9f7a-144">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="d9f7a-144">Click **Install**</span></span>

<span data-ttu-id="d9f7a-145">請注意，除了 EntityFramework 組件 System.ComponentModel.DataAnnotations 組件的參考也會加入。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-145">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="d9f7a-146">在 Program.cs 檔案頂端，新增下列 using 陳述式：</span><span class="sxs-lookup"><span data-stu-id="d9f7a-146">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="d9f7a-147">在 Program.cs 中新增內容定義。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-147">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="d9f7a-148">持續儲存並擷取資料</span><span class="sxs-lookup"><span data-stu-id="d9f7a-148">Persist and Retrieve Data</span></span>

<span data-ttu-id="d9f7a-149">開啟 Program.cs 檔案的 Main 方法定義的位置。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-149">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="d9f7a-150">將下列程式碼新增至 Main 函式。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-150">Add the following code into the Main function.</span></span>

<span data-ttu-id="d9f7a-151">程式碼會新增兩個新的大學物件內容。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-151">The code adds two new University objects to the context.</span></span> <span data-ttu-id="d9f7a-152">空間的屬性會初始化使用 DbGeography.FromText 方法。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-152">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="d9f7a-153">表示如 WellKnownText 傳遞給方法的地理點。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-153">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="d9f7a-154">程式碼接著會將資料儲存。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-154">The code then saves the data.</span></span> <span data-ttu-id="d9f7a-155">然後，在 LINQ 查詢中傳回最接近指定的位置，其位置所在的大學物件建構及執行。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-155">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

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

<span data-ttu-id="d9f7a-156">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-156">Compile and run the application.</span></span> <span data-ttu-id="d9f7a-157">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="d9f7a-157">The program produces the following output:</span></span>

```
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="d9f7a-158">檢視所產生的資料庫</span><span class="sxs-lookup"><span data-stu-id="d9f7a-158">View the Generated Database</span></span>

<span data-ttu-id="d9f7a-159">當您執行應用程式第一次時，Entity Framework 會為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-159">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="d9f7a-160">因為我們沒有安裝 Visual Studio 2012，則會在 LocalDB 執行個體上建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-160">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="d9f7a-161">根據預設，Entity Framework 資料庫的名稱衍生內容的完整名稱 (在此範例中是**SpatialCodeFirst.UniversityContext**)。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-161">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="d9f7a-162">後續的時間，將會使用現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-162">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="d9f7a-163">請注意，是否您的模型建立資料庫之後，進行任何變更，您應該使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-163">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="d9f7a-164">請參閱[Code First 至新的資料庫](~/ef6/modeling/code-first/workflows/new-database.md)如使用移轉的範例。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-164">See [Code First to a New Database](~/ef6/modeling/code-first/workflows/new-database.md) for an example of using Migrations.</span></span>

<span data-ttu-id="d9f7a-165">若要檢視的資料庫和資料，請執行下列作業：</span><span class="sxs-lookup"><span data-stu-id="d9f7a-165">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="d9f7a-166">在 Visual Studio 2012 主功能表中，選取**檢視** - &gt; **SQL Server 物件總管**。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-166">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="d9f7a-167">如果 LocalDB 不在伺服器清單中，按一下滑鼠右鍵**SQL Server** ，然後選取**加入 SQL Server**使用預設值**Windows 驗證**連接到LocalDB 執行個體</span><span class="sxs-lookup"><span data-stu-id="d9f7a-167">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="d9f7a-168">展開 [LocalDB] 節點</span><span class="sxs-lookup"><span data-stu-id="d9f7a-168">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="d9f7a-169">Unfold**資料庫**查看新的資料庫，並瀏覽至資料夾**大學**資料表</span><span class="sxs-lookup"><span data-stu-id="d9f7a-169">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="d9f7a-170">若要檢視資料，以滑鼠右鍵按一下資料表並選取**檢視資料**</span><span class="sxs-lookup"><span data-stu-id="d9f7a-170">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="d9f7a-171">總結</span><span class="sxs-lookup"><span data-stu-id="d9f7a-171">Summary</span></span>

<span data-ttu-id="d9f7a-172">在此逐步解說中，我們討論過如何使用 Entity Framework Code First 使用空間的類型。</span><span class="sxs-lookup"><span data-stu-id="d9f7a-172">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
