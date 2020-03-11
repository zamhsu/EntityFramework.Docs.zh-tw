---
title: 每個模型多個圖表-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
ms.openlocfilehash: e78b927a147143629ac5b73e23edf439ba6d0264
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418291"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="0cb0e-102">每個模型多個圖表</span><span class="sxs-lookup"><span data-stu-id="0cb0e-102">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="0cb0e-103">**EF5 僅限**，此頁面中所討論的功能、api 等內容是在 Entity Framework 5 中引進。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="0cb0e-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="0cb0e-105">這部影片和頁面會顯示如何使用 Entity Framework Designer （EF Designer）將模型分割成多個圖表。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-105">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="0cb0e-106">當您的模型變得太大而無法觀看或編輯時，您可能會想要使用這項功能。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-106">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="0cb0e-107">在舊版的 EF Designer 中，每個 EDMX 檔案只能有一個圖表。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-107">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="0cb0e-108">從 Visual Studio 2012 開始，您可以使用 EF 設計工具將您的 EDMX 檔案分割成多個圖表。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-108">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="0cb0e-109">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="0cb0e-109">Watch the video</span></span>
<span data-ttu-id="0cb0e-110">這段影片示範如何使用 Entity Framework Designer （EF Designer）將模型分割成多個圖表。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-110">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="0cb0e-111">當您的模型變得太大而無法觀看或編輯時，您可能會想要使用這項功能。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-111">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="0cb0e-112">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="0cb0e-112">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="0cb0e-113">**影片**： [wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [.wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [wmv （ZIP）](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="0cb0e-113">**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="0cb0e-114">EF 設計工具總覽</span><span class="sxs-lookup"><span data-stu-id="0cb0e-114">EF Designer Overview</span></span>

<span data-ttu-id="0cb0e-115">當您使用 EF Designer 的實體資料模型 Wizard 建立模型時，會建立 .edmx 檔案並將其新增至您的方案。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-115">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="0cb0e-116">這個檔案會定義實體的形狀，以及它們如何對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-116">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="0cb0e-117">EF 設計工具組含下列元件：</span><span class="sxs-lookup"><span data-stu-id="0cb0e-117">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="0cb0e-118">用於編輯模型的視覺化設計介面。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-118">A visual design surface for editing the model.</span></span> <span data-ttu-id="0cb0e-119">您可以建立、修改或刪除實體與關聯。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-119">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="0cb0e-120"> **模型瀏覽器** 視窗，提供模型的樹狀檢視。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-120">A **Model Browser** window that provides tree views of the model.</span></span><span data-ttu-id="0cb0e-121">  實體及其關聯位於 *\[ModelName\]*  資料夾底下。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-121">  The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="0cb0e-122">資料庫資料表和條件約束位於 *\[ModelName\]* 之下。存放區資料夾。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-122">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="0cb0e-123">[ **對應詳細資料**] 視窗，用於查看和編輯對應。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-123">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="0cb0e-124">您可以將實體類型或關聯對應到資料庫資料表、資料行和預存程序。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-124">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="0cb0e-125">[視覺化設計介面] 視窗會在實體資料模型 Wizard 完成時自動開啟。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-125">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="0cb0e-126">如果看不到 [模型瀏覽器]，請以滑鼠右鍵按一下主要設計介面，然後選取 [ **模型瀏覽器**]。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-126">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="0cb0e-127">下列螢幕擷取畫面顯示在 EF 設計工具中開啟的 .edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-127">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="0cb0e-128">螢幕擷取畫面會顯示視覺化設計介面（左側）和 [ **模型瀏覽器**] 視窗（右側）。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-128">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF Designer 2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="0cb0e-130">若要復原在 EF 設計工具中完成的作業，請按一下 [Ctrl-Z]。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-130">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="0cb0e-131">使用圖表</span><span class="sxs-lookup"><span data-stu-id="0cb0e-131">Working with Diagrams</span></span>

<span data-ttu-id="0cb0e-132">根據預設，EF Designer 會建立一個稱為 Diagram1 的圖表。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-132">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="0cb0e-133">如果您的圖表中有大量的實體和關聯，您會想要以邏輯方式將它們分割。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-133">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="0cb0e-134">從 Visual Studio 2012 開始，您可以在多個圖表中查看概念模型。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-134">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="0cb0e-135">當您加入新的圖表時，它們會出現在 [模型瀏覽器] 視窗的 [圖表] 資料夾底下。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-135">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="0cb0e-136">若要重新命名圖表：選取 [模型瀏覽器] 視窗中的圖表，在名稱上按一下 [一次]，然後輸入新的名稱。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-136">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span> <span data-ttu-id="0cb0e-137"> 您也可以用滑鼠右鍵按一下圖表名稱，然後選取 [**重新命名**]。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-137"> You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="0cb0e-138">圖表名稱會顯示在 [Visual Studio 編輯器] 中 .edmx 檔案名的旁邊。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-138">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="0cb0e-139">例如，Model1\[Diagram1\]。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-139">For example Model1.edmx\[Diagram1\].</span></span>

![DiagramName](~/ef6/media/diagramname.png)

<span data-ttu-id="0cb0e-141">圖表內容（實體和關聯的形狀和色彩）會儲存在 .edmx. 圖表檔案中。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-141">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="0cb0e-142">若要查看此檔案，請選取方案總管並展開 .edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-142">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![DiagramFiles](~/ef6/media/diagramfiles.png)

<span data-ttu-id="0cb0e-144">您不應該手動編輯 .edmx 檔案，因此 EF Designer 可能會覆寫這個檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-144">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="0cb0e-145">將實體和關聯分割成新的圖表</span><span class="sxs-lookup"><span data-stu-id="0cb0e-145">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="0cb0e-146">您可以選取現有圖表上的實體（按住 Shift 鍵可選取多個實體）。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-146">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="0cb0e-147">按一下滑鼠右鍵，然後選取 [**移至新圖表**]。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-147">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="0cb0e-148">隨即建立新的圖表，並將選取的實體及其關聯移至圖表。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-148">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="0cb0e-149">或者，您也可以用滑鼠右鍵按一下 [模型瀏覽器] 中的 [圖表] 資料夾，然後選取 [**新增圖表]。**</span><span class="sxs-lookup"><span data-stu-id="0cb0e-149">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="0cb0e-150">接著，您可以從 [模型瀏覽器] 中的 [實體類型] 資料夾，將實體拖放到設計介面上。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-150">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="0cb0e-151">您也可以從一個圖表剪下或複製實體（使用 Ctrl X 或 Ctrl-c 鍵），並貼上（使用 Ctrl + V 鍵）。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-151">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="0cb0e-152">如果您要在其中貼上實體的圖表已經包含具有相同名稱的實體，則會建立新的實體，並將其加入至模型。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-152">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span><span data-ttu-id="0cb0e-153">  例如： Diagram2 包含部門實體。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-153">  For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="0cb0e-154">然後，在 Diagram2 上貼上另一個部門。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-154">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="0cb0e-155">Department1 實體會建立並加入至概念模型。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-155">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="0cb0e-156">若要在圖表中包含相關實體，請在實體上按一下，然後選取 [**包含相關**專案]。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-156">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="0cb0e-157">這會在指定的圖表中建立相關實體和關聯的複本。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-157">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="0cb0e-158">變更實體的色彩</span><span class="sxs-lookup"><span data-stu-id="0cb0e-158">Changing the Color of Entities</span></span>

<span data-ttu-id="0cb0e-159">除了將模型分割成多個圖表，您也可以變更實體的色彩。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-159">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="0cb0e-160">若要變更色彩，請在設計介面上選取實體（或多個實體）。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-160">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="0cb0e-161">然後，按一下滑鼠右鍵並選取 [**屬性**]。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-161">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="0cb0e-162">在 屬性視窗中，選取 **填滿色彩** 屬性。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-162">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="0cb0e-163">請使用有效的色彩名稱（例如，紅色）或有效的 RGB （例如，255、128、128）來指定色彩。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-163">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![Color](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="0cb0e-165">摘要</span><span class="sxs-lookup"><span data-stu-id="0cb0e-165">Summary</span></span>

<span data-ttu-id="0cb0e-166">在本主題中，我們探討了如何將模型分割成多個圖表，以及如何使用 Entity Framework Designer 來指定實體的不同色彩。</span><span class="sxs-lookup"><span data-stu-id="0cb0e-166">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
