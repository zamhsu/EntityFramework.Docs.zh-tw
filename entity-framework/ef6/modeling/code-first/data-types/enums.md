---
title: 列舉支援-Code First-EF6
description: 列舉支援-Entity Framework 6 中的 Code First
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: e9e1412afb097cc4c944be63130dba0496a80f14
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073973"
---
# <a name="enum-support---code-first"></a><span data-ttu-id="7d349-103">列舉支援-Code First</span><span class="sxs-lookup"><span data-stu-id="7d349-103">Enum Support - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="7d349-104">**EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。</span><span class="sxs-lookup"><span data-stu-id="7d349-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="7d349-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="7d349-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="7d349-106">這段影片和逐步解說會示範如何搭配 Entity Framework Code First 來使用列舉類型。</span><span class="sxs-lookup"><span data-stu-id="7d349-106">This video and step-by-step walkthrough shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="7d349-107">它也會示範如何在 LINQ 查詢中使用列舉。</span><span class="sxs-lookup"><span data-stu-id="7d349-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="7d349-108">本逐步解說會使用 Code First 來建立新的資料庫，但您也可以使用 [Code First 來對應至現有的資料庫](xref:ef6/modeling/code-first/workflows/existing-database)。</span><span class="sxs-lookup"><span data-stu-id="7d349-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to map to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="7d349-109">列舉支援是在 Entity Framework 5 中引進。</span><span class="sxs-lookup"><span data-stu-id="7d349-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="7d349-110">若要使用列舉、空間資料類型和資料表值函式之類的新功能，您必須將目標設為 .NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="7d349-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="7d349-111">Visual Studio 2012 預設會以 .NET 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="7d349-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="7d349-112">在 Entity Framework 中，列舉可以具有下列基礎類型： **Byte**、 **Int16**、 **Int32**、 **Int64** 或 **SByte**。</span><span class="sxs-lookup"><span data-stu-id="7d349-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="7d349-113">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="7d349-113">Watch the video</span></span>
<span data-ttu-id="7d349-114">這段影片示範如何使用列舉類型搭配 Entity Framework Code First。</span><span class="sxs-lookup"><span data-stu-id="7d349-114">This video shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="7d349-115">它也會示範如何在 LINQ 查詢中使用列舉。</span><span class="sxs-lookup"><span data-stu-id="7d349-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="7d349-116">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="7d349-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="7d349-117">**影片**： [wmv](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)的  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="7d349-117">**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7d349-118">必要條件</span><span class="sxs-lookup"><span data-stu-id="7d349-118">Pre-Requisites</span></span>

<span data-ttu-id="7d349-119">您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="7d349-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

 

## <a name="set-up-the-project"></a><span data-ttu-id="7d349-120">設定專案</span><span class="sxs-lookup"><span data-stu-id="7d349-120">Set up the Project</span></span>

1.  <span data-ttu-id="7d349-121">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="7d349-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="7d349-122">**在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。</span><span class="sxs-lookup"><span data-stu-id="7d349-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="7d349-123">在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="7d349-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="7d349-124">輸入 **EnumCodeFirst** 做為專案的名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="7d349-124">Enter **EnumCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="7d349-125">使用 Code First 定義新模型</span><span class="sxs-lookup"><span data-stu-id="7d349-125">Define a New Model using Code First</span></span>

<span data-ttu-id="7d349-126">使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。</span><span class="sxs-lookup"><span data-stu-id="7d349-126">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="7d349-127">下列程式碼會定義部門類別。</span><span class="sxs-lookup"><span data-stu-id="7d349-127">The code below defines the Department class.</span></span>

<span data-ttu-id="7d349-128">程式碼也會定義 DepartmentNames 列舉。</span><span class="sxs-lookup"><span data-stu-id="7d349-128">The code also defines the DepartmentNames enumeration.</span></span> <span data-ttu-id="7d349-129">根據預設，列舉是 **int** 類型。</span><span class="sxs-lookup"><span data-stu-id="7d349-129">By default, the enumeration is of **int** type.</span></span> <span data-ttu-id="7d349-130">部門類別的 Name 屬性屬於 DepartmentNames 型別。</span><span class="sxs-lookup"><span data-stu-id="7d349-130">The Name property on the Department class is of the DepartmentNames type.</span></span>

<span data-ttu-id="7d349-131">開啟 Program.cs 檔案，並貼上下列類別定義。</span><span class="sxs-lookup"><span data-stu-id="7d349-131">Open the Program.cs file and paste the following class definitions.</span></span>

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
 

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="7d349-132">定義 DbCoNtext 衍生類型</span><span class="sxs-lookup"><span data-stu-id="7d349-132">Define the DbContext Derived Type</span></span>

<span data-ttu-id="7d349-133">除了定義實體之外，您還必須定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。</span><span class="sxs-lookup"><span data-stu-id="7d349-133">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="7d349-134">DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="7d349-134">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="7d349-135">DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="7d349-135">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="7d349-136">DbCoNtext 和 DbSet 類型是在 EntityFramework 元件中定義。</span><span class="sxs-lookup"><span data-stu-id="7d349-136">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="7d349-137">我們會使用 EntityFramework NuGet 封裝來新增此 DLL 的參考。</span><span class="sxs-lookup"><span data-stu-id="7d349-137">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="7d349-138">在方案總管中，以滑鼠右鍵按一下專案名稱。</span><span class="sxs-lookup"><span data-stu-id="7d349-138">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="7d349-139">選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="7d349-139">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="7d349-140">在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件。</span><span class="sxs-lookup"><span data-stu-id="7d349-140">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="7d349-141">按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="7d349-141">Click **Install**</span></span>

<span data-ttu-id="7d349-142">請注意，除了 EntityFramework 元件之外，也會加入 ComponentModel. DataAnnotations 和 System.object 元件的參考。</span><span class="sxs-lookup"><span data-stu-id="7d349-142">Note, that in addition to the EntityFramework  assembly, references to System.ComponentModel.DataAnnotations and System.Data.Entity assemblies are added as well.</span></span>

<span data-ttu-id="7d349-143">在 Program.cs 檔案的頂端，新增下列 using 語句：</span><span class="sxs-lookup"><span data-stu-id="7d349-143">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="7d349-144">在 Program.cs 中加入內容定義。</span><span class="sxs-lookup"><span data-stu-id="7d349-144">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="7d349-145">保存和取出資料</span><span class="sxs-lookup"><span data-stu-id="7d349-145">Persist and Retrieve Data</span></span>

<span data-ttu-id="7d349-146">開啟定義 Main 方法的 Program.cs 檔案。</span><span class="sxs-lookup"><span data-stu-id="7d349-146">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="7d349-147">將下列程式碼新增至 Main 函數。</span><span class="sxs-lookup"><span data-stu-id="7d349-147">Add the following code into the Main function.</span></span> <span data-ttu-id="7d349-148">程式碼會將新的部門物件新增至內容。</span><span class="sxs-lookup"><span data-stu-id="7d349-148">The code adds a new Department object to the context.</span></span> <span data-ttu-id="7d349-149">然後，它會儲存資料。</span><span class="sxs-lookup"><span data-stu-id="7d349-149">It then saves the data.</span></span> <span data-ttu-id="7d349-150">程式碼也會執行 LINQ 查詢，該查詢會傳回名稱為 DepartmentNames 的部門。</span><span class="sxs-lookup"><span data-stu-id="7d349-150">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

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

<span data-ttu-id="7d349-151">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="7d349-151">Compile and run the application.</span></span> <span data-ttu-id="7d349-152">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="7d349-152">The program produces the following output:</span></span>

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a><span data-ttu-id="7d349-153">查看產生的資料庫</span><span class="sxs-lookup"><span data-stu-id="7d349-153">View the Generated Database</span></span>

<span data-ttu-id="7d349-154">當您第一次執行應用程式時，Entity Framework 會為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="7d349-154">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="7d349-155">因為我們安裝了 Visual Studio 2012，所以會在 LocalDB 實例上建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="7d349-155">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="7d349-156">根據預設，Entity Framework 會在衍生 (內容的完整名稱之後，將資料庫命名為此範例中的 **EnumCodeFirst. EnumTestCoNtext**) 。</span><span class="sxs-lookup"><span data-stu-id="7d349-156">By default, the Entity Framework names the database after the fully qualified name of the derived context (for this example that is **EnumCodeFirst.EnumTestContext**).</span></span> <span data-ttu-id="7d349-157">將會使用現有資料庫的後續時間。</span><span class="sxs-lookup"><span data-stu-id="7d349-157">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="7d349-158">請注意，如果您在建立資料庫之後對模型進行任何變更，則應該使用 Code First 移轉來更新資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="7d349-158">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="7d349-159">如需使用遷移的範例，請參閱 [Code First 至新的資料庫](xref:ef6/modeling/code-first/workflows/new-database) 。</span><span class="sxs-lookup"><span data-stu-id="7d349-159">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="7d349-160">若要查看資料庫和資料，請執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="7d349-160">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="7d349-161">在 [Visual Studio 2012] 主功能表中，選取 [ **View**  - &gt; **SQL Server 物件總管**。</span><span class="sxs-lookup"><span data-stu-id="7d349-161">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="7d349-162">如果 LocalDB 不在伺服器清單中，請在 **SQL Server** 上按一下滑鼠右鍵，然後選取 [ **加入] SQL Server** 使用預設的 **Windows 驗證** 連接到 LocalDB 實例</span><span class="sxs-lookup"><span data-stu-id="7d349-162">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB instance</span></span>
3.  <span data-ttu-id="7d349-163">展開 LocalDB 節點</span><span class="sxs-lookup"><span data-stu-id="7d349-163">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="7d349-164">展開 [ **資料庫** ] 資料夾以查看新的資料庫，並流覽至 **部門** 資料表附注，那 Code First 不會建立對應至列舉類型的資料表。</span><span class="sxs-lookup"><span data-stu-id="7d349-164">Unfold the **Databases** folder to see the new database and browse to the **Department** table Note, that Code First does not create a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="7d349-165">若要查看資料，請以滑鼠右鍵按一下資料表，然後選取 [ **View data** ]</span><span class="sxs-lookup"><span data-stu-id="7d349-165">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="7d349-166">摘要</span><span class="sxs-lookup"><span data-stu-id="7d349-166">Summary</span></span>

<span data-ttu-id="7d349-167">在本逐步解說中，我們探討了如何搭配 Entity Framework Code First 來使用列舉類型。</span><span class="sxs-lookup"><span data-stu-id="7d349-167">In this walkthrough we looked at how to use enum types with Entity Framework Code First.</span></span> 
