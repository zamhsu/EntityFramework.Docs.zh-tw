---
title: 列舉支援-EF 設計工具-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
caps.latest.revision: 3
ms.openlocfilehash: cbf9b01fcbe21274ff3644c6ae6bc8fdfd338e3b
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120471"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="80f85-102">列舉支援-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="80f85-102">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="80f85-103">**EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="80f85-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="80f85-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="80f85-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="80f85-105">此影片和逐步解說示範如何使用 Entity Framework 設計工具中的列舉型別。</span><span class="sxs-lookup"><span data-stu-id="80f85-105">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="80f85-106">它也會示範如何使用 LINQ 查詢中的列舉。</span><span class="sxs-lookup"><span data-stu-id="80f85-106">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="80f85-107">本逐步解說將使用 Model First 來建立新的資料庫，但 EF 設計工具也可搭配[Database First](~/ef6/modeling/designer/workflows/database-first.md)的工作流程，對應到現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="80f85-107">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](~/ef6/modeling/designer/workflows/database-first.md) workflow to map to an existing database.</span></span>

<span data-ttu-id="80f85-108">在 Entity Framework 5 中引進了列舉支援。</span><span class="sxs-lookup"><span data-stu-id="80f85-108">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="80f85-109">若要使用新的功能，例如列舉、 空間資料類型和資料表值函式，您必須以.NET Framework 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="80f85-109">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="80f85-110">Visual Studio 2012 的預設值為目標.NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="80f85-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="80f85-111">在 Entity Framework 列舉型別可以有下列的基礎類型：**位元組**， **Int16**， **Int32**， **Int64** ，或**SByte**。</span><span class="sxs-lookup"><span data-stu-id="80f85-111">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="80f85-112">觀看影片</span><span class="sxs-lookup"><span data-stu-id="80f85-112">Watch the Video</span></span>
<span data-ttu-id="80f85-113">這段影片示範如何使用 Entity Framework 設計工具中的列舉型別。</span><span class="sxs-lookup"><span data-stu-id="80f85-113">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="80f85-114">它也會示範如何使用 LINQ 查詢中的列舉。</span><span class="sxs-lookup"><span data-stu-id="80f85-114">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="80f85-115">**由**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="80f85-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="80f85-116">**視訊**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="80f85-116">**Video**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="80f85-117">必要條件</span><span class="sxs-lookup"><span data-stu-id="80f85-117">Pre-Requisites</span></span>

<span data-ttu-id="80f85-118">您必須擁有 Visual Studio 2012、 Ultimate、 Premium、 Professional 或 Web Express edition 安裝才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="80f85-118">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="80f85-119">設定專案</span><span class="sxs-lookup"><span data-stu-id="80f85-119">Set up the Project</span></span>

1.  <span data-ttu-id="80f85-120">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="80f85-120">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="80f85-121">在 **檔案**功能表上，指向**新增**，然後按一下 **專案**</span><span class="sxs-lookup"><span data-stu-id="80f85-121">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="80f85-122">在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="80f85-122">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="80f85-123">請輸入**EnumEFDesigner**做為名稱的專案，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="80f85-123">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="80f85-124">建立新的模型使用 EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="80f85-124">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="80f85-125">以滑鼠右鍵按一下方案總管] 中的專案名稱，指向**新增**，然後按一下 [**新項目**</span><span class="sxs-lookup"><span data-stu-id="80f85-125">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="80f85-126">選取 [**資料**從左側的功能表，然後選取**ADO.NET 實體資料模型**範本] 窗格中</span><span class="sxs-lookup"><span data-stu-id="80f85-126">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="80f85-127">請輸入**EnumTestModel.edmx**為檔案名稱，然後按一下**新增**</span><span class="sxs-lookup"><span data-stu-id="80f85-127">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="80f85-128">在 Entity Data Model 精靈 頁面上，選取**空的模型**在選擇模型內容 對話方塊</span><span class="sxs-lookup"><span data-stu-id="80f85-128">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="80f85-129">按一下 **完成**</span><span class="sxs-lookup"><span data-stu-id="80f85-129">Click **Finish**</span></span>

<span data-ttu-id="80f85-130">Entity Designer 中，提供用於編輯模型的設計介面，會顯示。</span><span class="sxs-lookup"><span data-stu-id="80f85-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="80f85-131">精靈會執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="80f85-131">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="80f85-132">產生 EnumTestModel.edmx 檔案定義概念模型、 儲存體模型和兩者之間的對應。</span><span class="sxs-lookup"><span data-stu-id="80f85-132">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="80f85-133">讓產生的中繼資料檔案取得內嵌於組件，請將.edmx 檔案的 Metadata Artifact Processing 屬性設定為內嵌於輸出組件中。</span><span class="sxs-lookup"><span data-stu-id="80f85-133">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="80f85-134">加入下列組件的參考： EntityFramework、 System.ComponentModel.DataAnnotations 和 System.Data.Entity。</span><span class="sxs-lookup"><span data-stu-id="80f85-134">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="80f85-135">建立 EnumTestModel.tt 和 EnumTestModel.Context.tt 檔案，並將它們加入在.edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="80f85-135">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="80f85-136">這些 T4 範本檔案產生的衍生 DbContext 類型和對應至.edmx 模型中實體的 POCO 型別定義的程式碼。</span><span class="sxs-lookup"><span data-stu-id="80f85-136">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="80f85-137">加入新的實體類型</span><span class="sxs-lookup"><span data-stu-id="80f85-137">Add a New Entity Type</span></span>

1.  <span data-ttu-id="80f85-138">以滑鼠右鍵按一下設計介面上選取的空白處**新增-&gt;實體**，新的實體 對話方塊隨即出現</span><span class="sxs-lookup"><span data-stu-id="80f85-138">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="80f85-139">指定**部門**類型的名稱，並指定**DepartmentID**做為索引鍵屬性名稱，將保留的類型為**Int32**</span><span class="sxs-lookup"><span data-stu-id="80f85-139">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="80f85-140">按一下 [確定] </span><span class="sxs-lookup"><span data-stu-id="80f85-140">Click **OK**</span></span>
4.  <span data-ttu-id="80f85-141">以滑鼠右鍵按一下實體，然後選取**加入新-&gt;純量屬性**</span><span class="sxs-lookup"><span data-stu-id="80f85-141">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="80f85-142">重新命名新的屬性到**名稱**</span><span class="sxs-lookup"><span data-stu-id="80f85-142">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="80f85-143">變更新屬性的型別**Int32** （根據預設，新的屬性是字串類型） 若要變更型別，開啟 [屬性] 視窗，並變更類型屬性，以**Int32**</span><span class="sxs-lookup"><span data-stu-id="80f85-143">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="80f85-144">新增另一個純量屬性，並重新命名為**預算**，將類型變更為**十進位**</span><span class="sxs-lookup"><span data-stu-id="80f85-144">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="80f85-145">新增列舉型別</span><span class="sxs-lookup"><span data-stu-id="80f85-145">Add an Enum Type</span></span>

1.  <span data-ttu-id="80f85-146">在 Entity Framework Designer 中，以滑鼠右鍵按一下 [名稱] 屬性中，選取**轉換成列舉**</span><span class="sxs-lookup"><span data-stu-id="80f85-146">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![ConvertToEnum](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="80f85-148">在 **新增列舉**對話方塊方塊中，輸入**DepartmentNames**列舉型別名稱，將基礎類型變更為**Int32**，然後將下列成員新增至型別： 英文版，數學與經濟效益</span><span class="sxs-lookup"><span data-stu-id="80f85-148">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![AddEnumType](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="80f85-150">按下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="80f85-150">Press **OK**</span></span>
4.  <span data-ttu-id="80f85-151">儲存模型，並建置專案</span><span class="sxs-lookup"><span data-stu-id="80f85-151">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="80f85-152">當您建置時，未對應的實體和關聯有關的警告可能會出現在 錯誤清單。</span><span class="sxs-lookup"><span data-stu-id="80f85-152">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="80f85-153">您可以忽略這些警告，因為錯誤，我們選擇將透過模型產生資料庫之後，就會消失。</span><span class="sxs-lookup"><span data-stu-id="80f85-153">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="80f85-154">如果您查看 [屬性] 視窗時，您會注意到名稱屬性的型別已變更為**DepartmentNames**和新加入的列舉型別已加入至型別的清單。</span><span class="sxs-lookup"><span data-stu-id="80f85-154">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="80f85-155">如果您切換至 模型瀏覽器視窗，您會看到類型也已新增至列舉型別節點。</span><span class="sxs-lookup"><span data-stu-id="80f85-155">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![ModelBrowser](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="80f85-157">您也可以加入此視窗從新的列舉類型，按一下滑鼠右鍵，然後選取**新增的列舉型別**。</span><span class="sxs-lookup"><span data-stu-id="80f85-157">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="80f85-158">建立型別之後它會顯示在清單中的類型和您就能夠與屬性產生關聯</span><span class="sxs-lookup"><span data-stu-id="80f85-158">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="80f85-159">從模型產生資料庫</span><span class="sxs-lookup"><span data-stu-id="80f85-159">Generate Database from Model</span></span>

<span data-ttu-id="80f85-160">現在我們可以產生以模型為基礎的資料庫。</span><span class="sxs-lookup"><span data-stu-id="80f85-160">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="80f85-161">以滑鼠右鍵按一下 Entity designer 介面，然後選取空白空間**從模型產生資料庫**</span><span class="sxs-lookup"><span data-stu-id="80f85-161">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="80f85-162">選擇您的資料連接對話方塊的 產生資料庫精靈 會顯示按一下**新的連線**按鈕指定 **(localdb)\\mssqllocaldb**伺服器名稱 和  **EnumTest**資料庫，然後按一下  **確定**</span><span class="sxs-lookup"><span data-stu-id="80f85-162">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="80f85-163">將快顯對話方塊，詢問您是否要建立新的資料庫中，按一下**是**。</span><span class="sxs-lookup"><span data-stu-id="80f85-163">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="80f85-164">按一下 [**下一步]** 並建立資料庫產生的 DDL 會顯示在摘要和設定對話方塊方塊中請注意，DDL 不包含的定義，建立資料庫精靈會產生資料定義語言 (DDL)對應至列舉類型的資料表</span><span class="sxs-lookup"><span data-stu-id="80f85-164">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="80f85-165">按一下 **完成**按一下 完成 將不會執行 DDL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="80f85-165">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="80f85-166">建立資料庫精靈會進行下列作業： 開啟**EnumTest.edmx.sql** T-SQL 編輯器產生之 EDMX 的存放區結構描述和對應區段檔案中加入連接字串資訊加入 App.config 檔案</span><span class="sxs-lookup"><span data-stu-id="80f85-166">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="80f85-167">按一下滑鼠右鍵在 T-SQL 編輯器中，然後選取**Execute** [連接到伺服器] 對話方塊出現時，請從步驟 2 輸入的連接資訊，然後按一下**Connect**</span><span class="sxs-lookup"><span data-stu-id="80f85-167">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="80f85-168">若要檢視產生的結構描述，以滑鼠右鍵按一下 SQL Server 物件總管 中的資料庫名稱，然後選取**重新整理**</span><span class="sxs-lookup"><span data-stu-id="80f85-168">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="80f85-169">持續儲存並擷取資料</span><span class="sxs-lookup"><span data-stu-id="80f85-169">Persist and Retrieve Data</span></span>

<span data-ttu-id="80f85-170">開啟 Program.cs 檔案的 Main 方法定義的位置。</span><span class="sxs-lookup"><span data-stu-id="80f85-170">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="80f85-171">將下列程式碼新增至 Main 函式。</span><span class="sxs-lookup"><span data-stu-id="80f85-171">Add the following code into the Main function.</span></span> <span data-ttu-id="80f85-172">程式碼會將新的部門物件加入內容。</span><span class="sxs-lookup"><span data-stu-id="80f85-172">The code adds a new Department object to the context.</span></span> <span data-ttu-id="80f85-173">然後，它會儲存資料。</span><span class="sxs-lookup"><span data-stu-id="80f85-173">It then saves the data.</span></span> <span data-ttu-id="80f85-174">程式碼也會執行可傳回的部門，其中 name 是 DepartmentNames.English 的 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="80f85-174">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="80f85-175">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="80f85-175">Compile and run the application.</span></span> <span data-ttu-id="80f85-176">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="80f85-176">The program produces the following output:</span></span>

```
DepartmentID: 1 Name: English
```

<span data-ttu-id="80f85-177">若要檢視資料庫中的資料，以滑鼠右鍵按一下 SQL Server 物件總管 中的資料庫名稱，然後選取**重新整理**。</span><span class="sxs-lookup"><span data-stu-id="80f85-177">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="80f85-178">然後，按一下資料表，然後選取滑鼠右鍵**檢視資料**。</span><span class="sxs-lookup"><span data-stu-id="80f85-178">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="80f85-179">總結</span><span class="sxs-lookup"><span data-stu-id="80f85-179">Summary</span></span>

<span data-ttu-id="80f85-180">在此逐步解說中，我們討論過如何將使用 Entity Framework 設計工具的列舉型別對應，以及如何使用程式碼中的列舉。</span><span class="sxs-lookup"><span data-stu-id="80f85-180">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
