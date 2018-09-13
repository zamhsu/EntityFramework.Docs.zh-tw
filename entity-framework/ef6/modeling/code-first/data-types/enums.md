---
title: 列舉支援-Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
ms.openlocfilehash: 986991c2dd9470867aaf5424ecb176d7db172426
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489501"
---
# <a name="enum-support---code-first"></a><span data-ttu-id="13889-102">列舉支援-Code First</span><span class="sxs-lookup"><span data-stu-id="13889-102">Enum Support - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="13889-103">**EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="13889-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="13889-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="13889-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="13889-105">本影片以及逐步說明的逐步解說會示範如何使用 Entity Framework Code First 使用列舉型別。</span><span class="sxs-lookup"><span data-stu-id="13889-105">This video and step-by-step walkthrough shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="13889-106">它也會示範如何使用 LINQ 查詢中的列舉。</span><span class="sxs-lookup"><span data-stu-id="13889-106">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="13889-107">本逐步解說將使用 Code First 來建立新的資料庫，但您也可以使用[Code First 至對應到現有的資料庫](~/ef6/modeling/code-first/workflows/existing-database.md)。</span><span class="sxs-lookup"><span data-stu-id="13889-107">This walkthrough will use Code First to create a new database, but you can also use [Code First to map to an existing database](~/ef6/modeling/code-first/workflows/existing-database.md).</span></span>

<span data-ttu-id="13889-108">在 Entity Framework 5 中引進了列舉支援。</span><span class="sxs-lookup"><span data-stu-id="13889-108">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="13889-109">若要使用新的功能，例如列舉、 空間資料類型和資料表值函式，您必須以.NET Framework 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="13889-109">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="13889-110">Visual Studio 2012 的預設值為目標.NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="13889-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="13889-111">在 Entity Framework 列舉型別可以有下列的基礎類型：**位元組**， **Int16**， **Int32**， **Int64** ，或**SByte**。</span><span class="sxs-lookup"><span data-stu-id="13889-111">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="13889-112">觀看影片</span><span class="sxs-lookup"><span data-stu-id="13889-112">Watch the video</span></span>
<span data-ttu-id="13889-113">這段影片示範如何使用 Entity Framework Code First 使用列舉型別。</span><span class="sxs-lookup"><span data-stu-id="13889-113">This video shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="13889-114">它也會示範如何使用 LINQ 查詢中的列舉。</span><span class="sxs-lookup"><span data-stu-id="13889-114">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="13889-115">**由**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="13889-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="13889-116">**視訊**: [WMV](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="13889-116">**Video**: [WMV](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="13889-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="13889-117">Pre-Requisites</span></span>

<span data-ttu-id="13889-118">您必須擁有 Visual Studio 2012、 Ultimate、 Premium、 Professional 或 Web Express edition 安裝才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="13889-118">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

 

## <a name="set-up-the-project"></a><span data-ttu-id="13889-119">設定專案</span><span class="sxs-lookup"><span data-stu-id="13889-119">Set up the Project</span></span>

1.  <span data-ttu-id="13889-120">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="13889-120">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="13889-121">在 **檔案**功能表上，指向**新增**，然後按一下 **專案**</span><span class="sxs-lookup"><span data-stu-id="13889-121">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="13889-122">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="13889-122">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="13889-123">請輸入**EnumCodeFirst**做為名稱的專案，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="13889-123">Enter **EnumCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="13889-124">定義新的模型使用 Code First</span><span class="sxs-lookup"><span data-stu-id="13889-124">Define a New Model using Code First</span></span>

<span data-ttu-id="13889-125">使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。</span><span class="sxs-lookup"><span data-stu-id="13889-125">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="13889-126">下列程式碼定義部門類別。</span><span class="sxs-lookup"><span data-stu-id="13889-126">The code below defines the Department class.</span></span>

<span data-ttu-id="13889-127">程式碼也會定義 DepartmentNames 列舉型別。</span><span class="sxs-lookup"><span data-stu-id="13889-127">The code also defines the DepartmentNames enumeration.</span></span> <span data-ttu-id="13889-128">根據預設，列舉型別屬於**int**型別。</span><span class="sxs-lookup"><span data-stu-id="13889-128">By default, the enumeration is of **int** type.</span></span> <span data-ttu-id="13889-129">部門類別上的 Name 屬性是 DepartmentNames 型別。</span><span class="sxs-lookup"><span data-stu-id="13889-129">The Name property on the Department class is of the DepartmentNames type.</span></span>

<span data-ttu-id="13889-130">開啟 Program.cs 檔案並貼上下列類別定義。</span><span class="sxs-lookup"><span data-stu-id="13889-130">Open the Program.cs file and paste the following class definitions.</span></span>

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="13889-131">定義 DbContext 衍生型別</span><span class="sxs-lookup"><span data-stu-id="13889-131">Define the DbContext Derived Type</span></span>

<span data-ttu-id="13889-132">除了定義實體，您需要定義類別，衍生自 DbContext，並公開 DbSet&lt;TEntity&gt;屬性。</span><span class="sxs-lookup"><span data-stu-id="13889-132">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="13889-133">DbSet&lt;TEntity&gt;屬性可讓知道您想要在模型中包含哪些的類型的內容。</span><span class="sxs-lookup"><span data-stu-id="13889-133">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="13889-134">DbContext 衍生型別的執行個體在執行階段，其中包含填入資料庫的資料的物件期間同時管理實體物件、 變更追蹤，和保存資料至資料庫。</span><span class="sxs-lookup"><span data-stu-id="13889-134">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="13889-135">EntityFramework 組件中定義的 DbContext 和 DbSet 型別。</span><span class="sxs-lookup"><span data-stu-id="13889-135">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="13889-136">我們將使用 EntityFramework NuGet 套件，以新增此 DLL 的參考。</span><span class="sxs-lookup"><span data-stu-id="13889-136">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="13889-137">在 [方案總管] 中，以滑鼠右鍵按一下專案名稱。</span><span class="sxs-lookup"><span data-stu-id="13889-137">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="13889-138">選取**管理 NuGet 封裝...**</span><span class="sxs-lookup"><span data-stu-id="13889-138">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="13889-139">在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝。</span><span class="sxs-lookup"><span data-stu-id="13889-139">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="13889-140">按一下 **安裝**</span><span class="sxs-lookup"><span data-stu-id="13889-140">Click **Install**</span></span>

<span data-ttu-id="13889-141">請注意，除了 EntityFramework 組件參考 System.ComponentModel.DataAnnotations 和 System.Data.Entity 組件會新增以及。</span><span class="sxs-lookup"><span data-stu-id="13889-141">Note, that in addition to the EntityFramework  assembly, references to System.ComponentModel.DataAnnotations and System.Data.Entity assemblies are added as well.</span></span>

<span data-ttu-id="13889-142">在 Program.cs 檔案頂端，新增下列 using 陳述式：</span><span class="sxs-lookup"><span data-stu-id="13889-142">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="13889-143">在 Program.cs 中新增內容定義。</span><span class="sxs-lookup"><span data-stu-id="13889-143">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="13889-144">持續儲存並擷取資料</span><span class="sxs-lookup"><span data-stu-id="13889-144">Persist and Retrieve Data</span></span>

<span data-ttu-id="13889-145">開啟 Program.cs 檔案的 Main 方法定義的位置。</span><span class="sxs-lookup"><span data-stu-id="13889-145">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="13889-146">將下列程式碼新增至 Main 函式。</span><span class="sxs-lookup"><span data-stu-id="13889-146">Add the following code into the Main function.</span></span> <span data-ttu-id="13889-147">程式碼會將新的部門物件加入內容。</span><span class="sxs-lookup"><span data-stu-id="13889-147">The code adds a new Department object to the context.</span></span> <span data-ttu-id="13889-148">然後，它會儲存資料。</span><span class="sxs-lookup"><span data-stu-id="13889-148">It then saves the data.</span></span> <span data-ttu-id="13889-149">程式碼也會執行可傳回的部門，其中 name 是 DepartmentNames.English 的 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="13889-149">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="13889-150">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="13889-150">Compile and run the application.</span></span> <span data-ttu-id="13889-151">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="13889-151">The program produces the following output:</span></span>

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a><span data-ttu-id="13889-152">檢視所產生的資料庫</span><span class="sxs-lookup"><span data-stu-id="13889-152">View the Generated Database</span></span>

<span data-ttu-id="13889-153">當您執行應用程式第一次時，Entity Framework 會為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="13889-153">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="13889-154">因為我們沒有安裝 Visual Studio 2012，則會在 LocalDB 執行個體上建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="13889-154">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="13889-155">根據預設，Entity Framework 資料庫的名稱衍生內容的完整名稱 (此範例中是針對**EnumCodeFirst.EnumTestContext**)。</span><span class="sxs-lookup"><span data-stu-id="13889-155">By default, the Entity Framework names the database after the fully qualified name of the derived context (for this example that is **EnumCodeFirst.EnumTestContext**).</span></span> <span data-ttu-id="13889-156">後續的時間，將會使用現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="13889-156">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="13889-157">請注意，是否您的模型建立資料庫之後，進行任何變更，您應該使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="13889-157">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="13889-158">請參閱[Code First 至新的資料庫](~/ef6/modeling/code-first/workflows/new-database.md)如使用移轉的範例。</span><span class="sxs-lookup"><span data-stu-id="13889-158">See [Code First to a New Database](~/ef6/modeling/code-first/workflows/new-database.md) for an example of using Migrations.</span></span>

<span data-ttu-id="13889-159">若要檢視的資料庫和資料，請執行下列作業：</span><span class="sxs-lookup"><span data-stu-id="13889-159">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="13889-160">在 Visual Studio 2012 主功能表中，選取**檢視** - &gt; **SQL Server 物件總管**。</span><span class="sxs-lookup"><span data-stu-id="13889-160">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="13889-161">如果 LocalDB 不在伺服器清單中，按一下滑鼠右鍵**SQL Server** ，然後選取**加入 SQL Server**使用預設值**Windows 驗證**連接到LocalDB 執行個體</span><span class="sxs-lookup"><span data-stu-id="13889-161">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB instance</span></span>
3.  <span data-ttu-id="13889-162">展開 [LocalDB] 節點</span><span class="sxs-lookup"><span data-stu-id="13889-162">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="13889-163">Unfold**資料庫**查看新的資料庫，並瀏覽至資料夾**部門**資料表注意，Code First 不會建立對應至列舉類型的資料表</span><span class="sxs-lookup"><span data-stu-id="13889-163">Unfold the **Databases** folder to see the new database and browse to the **Department** table Note, that Code First does not create a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="13889-164">若要檢視資料，以滑鼠右鍵按一下資料表並選取**檢視資料**</span><span class="sxs-lookup"><span data-stu-id="13889-164">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="13889-165">總結</span><span class="sxs-lookup"><span data-stu-id="13889-165">Summary</span></span>

<span data-ttu-id="13889-166">在此逐步解說中，我們討論過如何使用 Entity Framework Code First 使用列舉型別。</span><span class="sxs-lookup"><span data-stu-id="13889-166">In this walkthrough we looked at how to use enum types with Entity Framework Code First.</span></span> 
