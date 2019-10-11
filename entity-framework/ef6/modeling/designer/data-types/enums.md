---
title: 列舉支援-EF 設計工具-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
ms.openlocfilehash: 92a763b84a04d3ce7ec0853ef2a4852356cf7997
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182512"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="84a82-102">列舉支援-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="84a82-102">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="84a82-103">**EF5 僅限**，此頁面中所討論的功能、api 等內容是在 Entity Framework 5 中引進。</span><span class="sxs-lookup"><span data-stu-id="84a82-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="84a82-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="84a82-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="84a82-105">這段影片和逐步解說示範如何使用列舉類型搭配 Entity Framework Designer。</span><span class="sxs-lookup"><span data-stu-id="84a82-105">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="84a82-106">它也會示範如何在 LINQ 查詢中使用 enum。</span><span class="sxs-lookup"><span data-stu-id="84a82-106">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="84a82-107">這個逐步解說將使用 Model First 建立新的資料庫，但是 EF 設計工具也可以與[Database First](~/ef6/modeling/designer/workflows/database-first.md)工作流程搭配使用，以對應至現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="84a82-107">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](~/ef6/modeling/designer/workflows/database-first.md) workflow to map to an existing database.</span></span>

<span data-ttu-id="84a82-108">列舉支援已于 Entity Framework 5 中引進。</span><span class="sxs-lookup"><span data-stu-id="84a82-108">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="84a82-109">若要使用列舉、空間資料類型和資料表值函式等新功能，您必須將目標設為 .NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="84a82-109">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="84a82-110">Visual Studio 2012 預設會以 .NET 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="84a82-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="84a82-111">在 Entity Framework 中，列舉可以具有下列基礎類型：**Byte**、 **Int16**、 **Int32**、 **Int64**或**SByte**。</span><span class="sxs-lookup"><span data-stu-id="84a82-111">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="84a82-112">觀看影片</span><span class="sxs-lookup"><span data-stu-id="84a82-112">Watch the Video</span></span>
<span data-ttu-id="84a82-113">這段影片示範如何使用列舉類型搭配 Entity Framework Designer。</span><span class="sxs-lookup"><span data-stu-id="84a82-113">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="84a82-114">它也會示範如何在 LINQ 查詢中使用 enum。</span><span class="sxs-lookup"><span data-stu-id="84a82-114">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="84a82-115">**提供者**：Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="84a82-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="84a82-116">**影片**：[WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)@NO__T[-1 個](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="84a82-116">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="84a82-117">先決條件</span><span class="sxs-lookup"><span data-stu-id="84a82-117">Pre-Requisites</span></span>

<span data-ttu-id="84a82-118">您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="84a82-118">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="84a82-119">設定專案</span><span class="sxs-lookup"><span data-stu-id="84a82-119">Set up the Project</span></span>

1.  <span data-ttu-id="84a82-120">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="84a82-120">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="84a82-121">**在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]</span><span class="sxs-lookup"><span data-stu-id="84a82-121">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="84a82-122">在左窗格中，按一下 [ **Visual C @ no__t-1**]，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="84a82-122">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="84a82-123">輸入**EnumEFDesigner**做為專案的名稱，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="84a82-123">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="84a82-124">使用 EF 設計工具建立新的模型</span><span class="sxs-lookup"><span data-stu-id="84a82-124">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="84a82-125">以滑鼠右鍵按一下方案總管中的專案名稱，指向 [**加入**]，然後按一下 [**新增專案**]</span><span class="sxs-lookup"><span data-stu-id="84a82-125">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="84a82-126">從左側功能表中選取 **資料**，然後選取 範本 窗格中的  **ADO.NET 實體資料模型**</span><span class="sxs-lookup"><span data-stu-id="84a82-126">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="84a82-127">在 [檔案名] 中輸入**EnumTestModel** ，然後按一下 [**新增**]</span><span class="sxs-lookup"><span data-stu-id="84a82-127">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="84a82-128">在 [實體資料模型 Wizard] 頁面上，選取 [選擇模型內容] 對話方塊中的 [**空的模型**]</span><span class="sxs-lookup"><span data-stu-id="84a82-128">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="84a82-129">按一下 **[完成]**</span><span class="sxs-lookup"><span data-stu-id="84a82-129">Click **Finish**</span></span>

<span data-ttu-id="84a82-130">會顯示 Entity Designer （提供編輯模型的設計介面）。</span><span class="sxs-lookup"><span data-stu-id="84a82-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="84a82-131">精靈會執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="84a82-131">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="84a82-132">產生 EnumTestModel 定義概念模型、儲存體模型和兩者之間對應的 .edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="84a82-132">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="84a82-133">設定 .edmx 檔案的中繼資料成品處理屬性，以內嵌在輸出元件中，使產生的中繼資料檔案內嵌在元件中。</span><span class="sxs-lookup"><span data-stu-id="84a82-133">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="84a82-134">加入下列元件的參考：EntityFramework、System.workflow.componentmodel.activity. DataAnnotations 和 System.web。</span><span class="sxs-lookup"><span data-stu-id="84a82-134">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="84a82-135">建立 EnumTestModel.tt 和 EnumTestModel.CoNtext.tt 檔案，並將它們新增至 .edmx 檔案底下。</span><span class="sxs-lookup"><span data-stu-id="84a82-135">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="84a82-136">這些 T4 範本檔案會產生程式碼，以定義 DbCoNtext 衍生類型，以及對應至 .edmx 模型中之實體的 POCO 類型。</span><span class="sxs-lookup"><span data-stu-id="84a82-136">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="84a82-137">加入新的實體類型</span><span class="sxs-lookup"><span data-stu-id="84a82-137">Add a New Entity Type</span></span>

1.  <span data-ttu-id="84a82-138">以滑鼠右鍵按一下設計介面的空白區域，然後選取 [新增 **-&gt; 實體**]，就會出現 [新增實體] 對話方塊</span><span class="sxs-lookup"><span data-stu-id="84a82-138">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="84a82-139">針對 [類型名稱] 指定 [**部門**]，並指定索引鍵屬性名稱的**DepartmentID** ，並將類型保留為 [ **Int32** ]</span><span class="sxs-lookup"><span data-stu-id="84a82-139">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="84a82-140">按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="84a82-140">Click **OK**</span></span>
4.  <span data-ttu-id="84a82-141">以滑鼠右鍵按一下實體，然後選取 [**新增-&gt;** 純量屬性]</span><span class="sxs-lookup"><span data-stu-id="84a82-141">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="84a82-142">將新屬性重新命名為**名稱**</span><span class="sxs-lookup"><span data-stu-id="84a82-142">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="84a82-143">將新屬性的類型變更為**Int32** （根據預設，新的屬性為 String 類型），以變更類型、開啟屬性視窗，並將 type 屬性變更為**Int32**</span><span class="sxs-lookup"><span data-stu-id="84a82-143">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="84a82-144">新增另一個純量屬性並將它重新命名為**預算**，將類型變更為**Decimal**</span><span class="sxs-lookup"><span data-stu-id="84a82-144">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="84a82-145">新增列舉類型</span><span class="sxs-lookup"><span data-stu-id="84a82-145">Add an Enum Type</span></span>

1.  <span data-ttu-id="84a82-146">在 Entity Framework Designer 中，以滑鼠右鍵按一下 名稱 屬性，然後選取 **轉換為列舉**</span><span class="sxs-lookup"><span data-stu-id="84a82-146">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![轉換為列舉](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="84a82-148">在 [**加入列舉**] 對話方塊中，輸入**DepartmentNames**作為 [列舉類型名稱]，將基礎類型變更為**Int32**，然後將下列成員新增至類型：英文、數學和經濟效益</span><span class="sxs-lookup"><span data-stu-id="84a82-148">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![新增列舉類型](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="84a82-150">按 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="84a82-150">Press **OK**</span></span>
4.  <span data-ttu-id="84a82-151">儲存模型並建立專案</span><span class="sxs-lookup"><span data-stu-id="84a82-151">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="84a82-152">當您建立時，有關未對應實體和關聯的警告可能會出現在錯誤清單中。</span><span class="sxs-lookup"><span data-stu-id="84a82-152">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="84a82-153">您可以忽略這些警告，因為在我們選擇從模型產生資料庫之後，錯誤就會消失。</span><span class="sxs-lookup"><span data-stu-id="84a82-153">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="84a82-154">如果您查看屬性視窗，您會注意到 Name 屬性的類型已變更為**DepartmentNames** ，而新增的列舉類型已加入至類型清單中。</span><span class="sxs-lookup"><span data-stu-id="84a82-154">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="84a82-155">如果您切換至 [模型瀏覽器] 視窗，您會看到該類型也已加入 [列舉類型] 節點。</span><span class="sxs-lookup"><span data-stu-id="84a82-155">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![模型瀏覽器](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="84a82-157">您也可以按一下滑鼠右鍵，然後選取 [新增**列舉類型**]，從這個視窗加入新的列舉類型。</span><span class="sxs-lookup"><span data-stu-id="84a82-157">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="84a82-158">建立類型之後，它會出現在類型清單中，而且您可以與屬性產生關聯</span><span class="sxs-lookup"><span data-stu-id="84a82-158">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="84a82-159">從模型產生資料庫</span><span class="sxs-lookup"><span data-stu-id="84a82-159">Generate Database from Model</span></span>

<span data-ttu-id="84a82-160">現在，我們可以產生以模型為基礎的資料庫。</span><span class="sxs-lookup"><span data-stu-id="84a82-160">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="84a82-161">以滑鼠右鍵按一下 Entity Designer 介面的空白空間，然後選取 [**從模型產生資料庫**]</span><span class="sxs-lookup"><span data-stu-id="84a82-161">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="84a82-162">[產生資料庫嚮導] 的 [選擇您的資料連線] 對話方塊隨即顯示，按一下 [**新增連接**] 按鈕指定 **（localdb） @no__t 2mssqllocaldb**作為資料庫的伺服器名稱和**EnumTest** ，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="84a82-162">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="84a82-163">詢問您是否要建立新資料庫的對話方塊會隨即出現，請按一下 **[是**]。</span><span class="sxs-lookup"><span data-stu-id="84a82-163">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="84a82-164">按 **[下一步**]，[建立資料庫] Wizard 會產生資料定義語言（DDL）來建立資料庫，而產生的 DDL 會顯示在 [摘要和設定] 對話方塊中，請注意，ddl 不會包含對應至之資料表的定義。列舉類型</span><span class="sxs-lookup"><span data-stu-id="84a82-164">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="84a82-165">按一下 **[完成]** 按一下 [完成]，不會執行 DDL 腳本。</span><span class="sxs-lookup"><span data-stu-id="84a82-165">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="84a82-166">[建立資料庫] Wizard 會執行下列動作：在 T-sql 編輯器中開啟**EnumTest** ，產生 .edmx 檔案的存放區架構和對應區段將連接字串資訊新增至 app.config 檔案</span><span class="sxs-lookup"><span data-stu-id="84a82-166">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="84a82-167">在 T-sql 編輯器中按一下滑鼠右鍵，然後選取 [**執行**連接到伺服器] 對話方塊，輸入步驟2中的連接資訊，然後按一下 **[連接]**</span><span class="sxs-lookup"><span data-stu-id="84a82-167">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="84a82-168">若要查看產生的架構，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵，**然後選取 [** 重新整理]。</span><span class="sxs-lookup"><span data-stu-id="84a82-168">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="84a82-169">保存和取出資料</span><span class="sxs-lookup"><span data-stu-id="84a82-169">Persist and Retrieve Data</span></span>

<span data-ttu-id="84a82-170">開啟定義 Main 方法的 Program.cs 檔案。</span><span class="sxs-lookup"><span data-stu-id="84a82-170">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="84a82-171">將下列程式碼新增至 Main 函式中。</span><span class="sxs-lookup"><span data-stu-id="84a82-171">Add the following code into the Main function.</span></span> <span data-ttu-id="84a82-172">程式碼會將新的部門物件新增至內容。</span><span class="sxs-lookup"><span data-stu-id="84a82-172">The code adds a new Department object to the context.</span></span> <span data-ttu-id="84a82-173">然後它會儲存資料。</span><span class="sxs-lookup"><span data-stu-id="84a82-173">It then saves the data.</span></span> <span data-ttu-id="84a82-174">程式碼也會執行 LINQ 查詢，以傳回名稱為 DepartmentNames 的部門。</span><span class="sxs-lookup"><span data-stu-id="84a82-174">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

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

<span data-ttu-id="84a82-175">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="84a82-175">Compile and run the application.</span></span> <span data-ttu-id="84a82-176">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="84a82-176">The program produces the following output:</span></span>

```console
DepartmentID: 1 Name: English
```

<span data-ttu-id="84a82-177">若要在資料庫中查看資料，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵，**然後選取 [** 重新整理]。</span><span class="sxs-lookup"><span data-stu-id="84a82-177">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="84a82-178">然後，按一下資料表上的滑鼠右鍵，然後選取 [ **View Data**]。</span><span class="sxs-lookup"><span data-stu-id="84a82-178">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="84a82-179">總結</span><span class="sxs-lookup"><span data-stu-id="84a82-179">Summary</span></span>

<span data-ttu-id="84a82-180">在本逐步解說中，我們探討了如何使用 Entity Framework Designer 來對應列舉型別，以及如何在程式碼中使用 enum。</span><span class="sxs-lookup"><span data-stu-id="84a82-180">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
