---
title: 列舉支援-EF Designer-EF6
description: 列舉支援-Entity Framework 6 中的 EF 設計工具
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620511"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="e1bbb-103">列舉支援-EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="e1bbb-103">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="e1bbb-104">**EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="e1bbb-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="e1bbb-106">這段影片和逐步解說會示範如何搭配 Entity Framework Designer 使用列舉類型。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-106">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="e1bbb-107">它也會示範如何在 LINQ 查詢中使用列舉。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="e1bbb-108">本逐步解說會使用 Model First 來建立新的資料庫，但 EF 設計工具也可以與 [Database First](xref:ef6/modeling/designer/workflows/database-first) 工作流程搭配使用，以對應至現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="e1bbb-109">列舉支援是在 Entity Framework 5 中引進。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="e1bbb-110">若要使用列舉、空間資料類型和資料表值函式之類的新功能，您必須將目標設為 .NET Framework 4.5。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="e1bbb-111">Visual Studio 2012 預設會以 .NET 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="e1bbb-112">在 Entity Framework 中，列舉可以具有下列基礎類型： **Byte**、 **Int16**、 **Int32**、 **Int64** 或 **SByte**。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="e1bbb-113">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="e1bbb-113">Watch the Video</span></span>
<span data-ttu-id="e1bbb-114">這段影片示範如何搭配 Entity Framework Designer 使用列舉類型。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-114">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="e1bbb-115">它也會示範如何在 LINQ 查詢中使用列舉。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="e1bbb-116">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="e1bbb-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="e1bbb-117">**影片**： [wmv](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)的  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="e1bbb-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="e1bbb-118">必要條件</span><span class="sxs-lookup"><span data-stu-id="e1bbb-118">Pre-Requisites</span></span>

<span data-ttu-id="e1bbb-119">您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="e1bbb-120">設定專案</span><span class="sxs-lookup"><span data-stu-id="e1bbb-120">Set up the Project</span></span>

1.  <span data-ttu-id="e1bbb-121">開啟 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="e1bbb-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="e1bbb-122">**在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="e1bbb-123">在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本</span><span class="sxs-lookup"><span data-stu-id="e1bbb-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="e1bbb-124">輸入 **EnumEFDesigner** 做為專案的名稱，然後按一下 **[確定]**</span><span class="sxs-lookup"><span data-stu-id="e1bbb-124">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="e1bbb-125">使用 EF 設計工具建立新的模型</span><span class="sxs-lookup"><span data-stu-id="e1bbb-125">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="e1bbb-126">在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [**加入**]，然後按一下 [**新增專案**]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-126">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="e1bbb-127">從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]</span><span class="sxs-lookup"><span data-stu-id="e1bbb-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="e1bbb-128">輸入**EnumTestModel**的檔案名，然後按一下 [**新增**]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-128">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="e1bbb-129">在實體資料模型 Wizard] 頁面的 [選擇模型內容] 對話方塊中，選取 [ **空的模型** ]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-129">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="e1bbb-130">按一下 [完成]</span><span class="sxs-lookup"><span data-stu-id="e1bbb-130">Click **Finish**</span></span>

<span data-ttu-id="e1bbb-131">隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-131">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="e1bbb-132">精靈會執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="e1bbb-132">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="e1bbb-133">產生定義概念模型、儲存體模型和兩者之間對應的 EnumTestModel .edmx 檔。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-133">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="e1bbb-134">設定 .edmx 檔的中繼資料成品處理屬性，以內嵌在輸出元件中，以便將產生的中繼資料檔案內嵌至元件。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-134">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="e1bbb-135">將參考加入至下列元件： EntityFramework、ComponentModel DataAnnotations 和 System.object。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-135">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="e1bbb-136">建立 EnumTestModel.tt 和 EnumTestModel.CoNtext.tt 檔案，並將它們新增至 .edmx 檔案底下。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-136">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="e1bbb-137">這些 T4 範本檔產生的程式碼會定義 DbCoNtext 衍生型別，以及對應至 .edmx 模型中之實體的 POCO 型別。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-137">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="e1bbb-138">加入新的實體類型</span><span class="sxs-lookup"><span data-stu-id="e1bbb-138">Add a New Entity Type</span></span>

1.  <span data-ttu-id="e1bbb-139">以滑鼠右鍵按一下設計介面的空白區域，然後選取 [ **加入 &gt; 實體**]，[新增實體] 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-139">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="e1bbb-140">指定類型名稱的 **部門** ，並指定索引鍵屬性名稱的 **DepartmentID** ，將類型保留為 **Int32**</span><span class="sxs-lookup"><span data-stu-id="e1bbb-140">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="e1bbb-141">按一下 [檔案] &gt; [新增] &gt; [專案] </span><span class="sxs-lookup"><span data-stu-id="e1bbb-141">Click **OK**</span></span>
4.  <span data-ttu-id="e1bbb-142">以滑鼠右鍵按一下實體，然後選取 [**加入 &gt; 新的-** 純量屬性]</span><span class="sxs-lookup"><span data-stu-id="e1bbb-142">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="e1bbb-143">將新屬性重新命名為 **Name**</span><span class="sxs-lookup"><span data-stu-id="e1bbb-143">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="e1bbb-144">根據預設，將新屬性的類型變更為 **Int32** (，新屬性的字串類型) 若要變更類型，請開啟屬性視窗，然後將 type 屬性變更為 **Int32**</span><span class="sxs-lookup"><span data-stu-id="e1bbb-144">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="e1bbb-145">新增另一個純量屬性，並將其重新命名為 **預算**，將類型變更為 **Decimal**</span><span class="sxs-lookup"><span data-stu-id="e1bbb-145">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="e1bbb-146">新增列舉型別</span><span class="sxs-lookup"><span data-stu-id="e1bbb-146">Add an Enum Type</span></span>

1.  <span data-ttu-id="e1bbb-147">在 Entity Framework Designer 中，以滑鼠右鍵按一下 [名稱] 屬性，然後選取 [**轉換為列舉**]</span><span class="sxs-lookup"><span data-stu-id="e1bbb-147">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![轉換成列舉](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="e1bbb-149">在 [ **加入列舉** ] 對話方塊中，輸入 **DepartmentNames** 作為列舉型別名稱，將基礎類型變更為 **Int32**，然後將下列成員加入至類型：英文、數學和經濟效益</span><span class="sxs-lookup"><span data-stu-id="e1bbb-149">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![新增列舉類型](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="e1bbb-151">按下 [確定]\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="e1bbb-151">Press **OK**</span></span>
4.  <span data-ttu-id="e1bbb-152">儲存模型並建立專案</span><span class="sxs-lookup"><span data-stu-id="e1bbb-152">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="e1bbb-153">當您建立時，有關未對應實體和關聯的警告可能會出現在 [錯誤清單] 中。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-153">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="e1bbb-154">您可以忽略這些警告，因為在我們選擇從模型產生資料庫之後，錯誤就會消失。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-154">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="e1bbb-155">如果您查看屬性視窗，您會發現 Name 屬性的型別已變更為 **DepartmentNames** ，而新增的列舉型別已加入至類型清單。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-155">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="e1bbb-156">如果您切換到 [模型瀏覽器] 視窗，您將會看到類型也已加入至 [列舉類型] 節點。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-156">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![模型瀏覽器](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="e1bbb-158">您也可以在此視窗中加入新的列舉類型，方法是按一下滑鼠右鍵，然後選取 [ **加入列舉類型**]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-158">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="e1bbb-159">一旦建立型別，它就會出現在類型清單中，而且您可以與屬性產生關聯。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-159">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="e1bbb-160">從模型產生資料庫</span><span class="sxs-lookup"><span data-stu-id="e1bbb-160">Generate Database from Model</span></span>

<span data-ttu-id="e1bbb-161">現在我們可以產生以模型為基礎的資料庫。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-161">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="e1bbb-162">以滑鼠右鍵按一下 Entity Designer 介面上的空白處，然後選取 [**從模型產生資料庫**]</span><span class="sxs-lookup"><span data-stu-id="e1bbb-162">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="e1bbb-163">[產生資料庫嚮導] 的 [選擇您的資料連線] 對話方塊隨即顯示，請按一下 [**新增連接**] 按鈕，為資料庫的伺服器名稱和**EnumTest**指定\*\* (localdb) \\ Mssqllocaldb\*\* ，然後按一下 **[確定]** 。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-163">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="e1bbb-164">對話方塊會詢問您是否要建立新的資料庫，請按一下 **[是**]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-164">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="e1bbb-165">按一下 **[下一步** ]，[建立資料庫嚮導] 會產生資料定義語言 (DDL) 若要建立資料庫，則會在 [摘要和設定] 對話方塊中顯示產生的 DDL，請注意，ddl 不包含對應至列舉類型之資料表的定義</span><span class="sxs-lookup"><span data-stu-id="e1bbb-165">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="e1bbb-166">按一下 **[** 完成]，然後按一下 [完成]，就不會執行 DDL 腳本。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-166">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="e1bbb-167">Create Database Wizard 會執行下列動作：在 T-sql 編輯器中開啟 **EnumTest** 會產生儲存架構，而 edmx 檔案的對應區段會將連接字串資訊加入 App.config 檔案</span><span class="sxs-lookup"><span data-stu-id="e1bbb-167">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="e1bbb-168">在 T-sql 編輯器中按一下滑鼠右鍵，然後選取 [**執行**連接至伺服器] 對話方塊，輸入步驟2中的連接資訊，然後按一下 **[連接]** 。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-168">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="e1bbb-169">若要查看產生的架構，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵，**然後選取 [** 重新整理]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-169">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="e1bbb-170">保存和取出資料</span><span class="sxs-lookup"><span data-stu-id="e1bbb-170">Persist and Retrieve Data</span></span>

<span data-ttu-id="e1bbb-171">開啟定義 Main 方法的 Program.cs 檔案。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-171">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="e1bbb-172">將下列程式碼新增至 Main 函數。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-172">Add the following code into the Main function.</span></span> <span data-ttu-id="e1bbb-173">程式碼會將新的部門物件新增至內容。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-173">The code adds a new Department object to the context.</span></span> <span data-ttu-id="e1bbb-174">然後，它會儲存資料。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-174">It then saves the data.</span></span> <span data-ttu-id="e1bbb-175">程式碼也會執行 LINQ 查詢，該查詢會傳回名稱為 DepartmentNames 的部門。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-175">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

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

<span data-ttu-id="e1bbb-176">編譯並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-176">Compile and run the application.</span></span> <span data-ttu-id="e1bbb-177">此程式會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="e1bbb-177">The program produces the following output:</span></span>

```console
DepartmentID: 1 Name: English
```

<span data-ttu-id="e1bbb-178">若要在資料庫中查看資料，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵， **然後選取 [** 重新整理]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-178">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="e1bbb-179">然後，按一下資料表上的滑鼠右鍵，然後選取 [ **View Data**]。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-179">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="e1bbb-180">摘要</span><span class="sxs-lookup"><span data-stu-id="e1bbb-180">Summary</span></span>

<span data-ttu-id="e1bbb-181">在這個逐步解說中，我們探討了如何使用 Entity Framework Designer 來對應列舉型別，以及如何在程式碼中使用列舉。</span><span class="sxs-lookup"><span data-stu-id="e1bbb-181">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
