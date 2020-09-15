---
title: 每個模型的多個圖表-EF6
description: Entity Framework 6 中每個模型的多個圖表
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/multiple-diagrams
ms.openlocfilehash: d934a1b709c93fe8e6b57d40dd874b564885b19f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073327"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="da024-103">每個模型的多個圖表</span><span class="sxs-lookup"><span data-stu-id="da024-103">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="da024-104">**EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。</span><span class="sxs-lookup"><span data-stu-id="da024-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="da024-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="da024-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="da024-106">此影片和頁面會示範如何使用 Entity Framework Designer (EF 設計工具) ，將模型分割成多個圖表。</span><span class="sxs-lookup"><span data-stu-id="da024-106">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="da024-107">當您的模型變得太大而無法進行查看或編輯時，您可能會想要使用這項功能。</span><span class="sxs-lookup"><span data-stu-id="da024-107">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="da024-108">在舊版的 EF 設計工具中，每個 EDMX 檔案只能有一個圖表。</span><span class="sxs-lookup"><span data-stu-id="da024-108">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="da024-109">從 Visual Studio 2012 開始，您可以使用 EF 設計工具將您的 EDMX 檔案分割成多個圖表。</span><span class="sxs-lookup"><span data-stu-id="da024-109">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="da024-110">觀賞影片</span><span class="sxs-lookup"><span data-stu-id="da024-110">Watch the video</span></span>
<span data-ttu-id="da024-111">這段影片說明如何使用 Entity Framework Designer (EF 設計工具) ，將模型分割成多個圖表。</span><span class="sxs-lookup"><span data-stu-id="da024-111">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="da024-112">當您的模型變得太大而無法進行查看或編輯時，您可能會想要使用這項功能。</span><span class="sxs-lookup"><span data-stu-id="da024-112">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="da024-113">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="da024-113">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="da024-114">**影片**： [wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)的  |  [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="da024-114">**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="da024-115">EF 設計工具總覽</span><span class="sxs-lookup"><span data-stu-id="da024-115">EF Designer Overview</span></span>

<span data-ttu-id="da024-116">當您使用 EF Designer 的實體資料模型 Wizard 建立模型時，會建立 .edmx 檔並將其加入至您的方案。</span><span class="sxs-lookup"><span data-stu-id="da024-116">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="da024-117">此檔案會定義實體的形狀，以及它們如何對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="da024-117">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="da024-118">EF 設計工具是由下列元件所組成：</span><span class="sxs-lookup"><span data-stu-id="da024-118">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="da024-119">用來編輯模型的視覺化設計介面。</span><span class="sxs-lookup"><span data-stu-id="da024-119">A visual design surface for editing the model.</span></span> <span data-ttu-id="da024-120">您可以建立、修改或刪除實體與關聯。</span><span class="sxs-lookup"><span data-stu-id="da024-120">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="da024-121">提供模型樹狀檢視的 [ **模型瀏覽器**   ] 視窗。</span><span class="sxs-lookup"><span data-stu-id="da024-121">A **Model Browser** window that provides tree views of the model.</span></span><span data-ttu-id="da024-122">實體及其關聯位於\* \[ ModelName \] \*資料夾底下。</span><span class="sxs-lookup"><span data-stu-id="da024-122">  The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="da024-123">資料庫資料表和條件約束位於\* \[ ModelName \] \*下。存放區資料夾。</span><span class="sxs-lookup"><span data-stu-id="da024-123">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="da024-124">用於查看和編輯對應的 [ **對應詳細資料** ] 視窗。</span><span class="sxs-lookup"><span data-stu-id="da024-124">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="da024-125">您可以將實體類型或關聯對應到資料庫資料表、資料行和預存程序。</span><span class="sxs-lookup"><span data-stu-id="da024-125">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="da024-126">當實體資料模型 Wizard 完成時，會自動開啟視覺化設計介面視窗。</span><span class="sxs-lookup"><span data-stu-id="da024-126">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="da024-127">如果看不到 [模型瀏覽器]，請以滑鼠右鍵按一下主要設計介面，然後選取 [ **模型瀏覽器**]。</span><span class="sxs-lookup"><span data-stu-id="da024-127">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="da024-128">下列螢幕擷取畫面顯示在 EF 設計工具中開啟的 .edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="da024-128">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="da024-129">螢幕擷取畫面顯示視覺設計介面 (左) ，而 [ **模型瀏覽器**   ] 視窗 (到右邊的) 。</span><span class="sxs-lookup"><span data-stu-id="da024-129">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF 設計工具2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="da024-131">若要復原在 EF Designer 中完成的作業，請按一下 Ctrl + Z。</span><span class="sxs-lookup"><span data-stu-id="da024-131">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="da024-132">使用圖表</span><span class="sxs-lookup"><span data-stu-id="da024-132">Working with Diagrams</span></span>

<span data-ttu-id="da024-133">根據預設，EF Designer 會建立一個名為 Diagram1 的圖表。</span><span class="sxs-lookup"><span data-stu-id="da024-133">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="da024-134">如果您的圖表有大量的實體和關聯，您最喜歡以邏輯方式將它們分割。</span><span class="sxs-lookup"><span data-stu-id="da024-134">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="da024-135">從 Visual Studio 2012 開始，您可以在多個圖表中查看您的概念模型。</span><span class="sxs-lookup"><span data-stu-id="da024-135">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="da024-136">當您加入新的圖表時，它們會出現在 [模型瀏覽器] 視窗的 [圖表] 資料夾底下。</span><span class="sxs-lookup"><span data-stu-id="da024-136">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="da024-137">若要重新命名圖表：請在 [模型瀏覽器] 視窗中選取圖表，並在名稱上按一下 [一次]，然後輸入新的名稱。</span><span class="sxs-lookup"><span data-stu-id="da024-137">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span> <span data-ttu-id="da024-138">您也可以用滑鼠右鍵按一下圖表名稱，然後選取 [ **重新命名**]。</span><span class="sxs-lookup"><span data-stu-id="da024-138"> You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="da024-139">在 [Visual Studio 編輯器] 的 .edmx 檔案名旁邊會顯示圖表名稱。</span><span class="sxs-lookup"><span data-stu-id="da024-139">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="da024-140">例如 Model1 .edmx \[ Diagram1 \] 。</span><span class="sxs-lookup"><span data-stu-id="da024-140">For example Model1.edmx\[Diagram1\].</span></span>

![圖表名稱](~/ef6/media/diagramname.png)

<span data-ttu-id="da024-142">圖表內容 (實體和關聯) 的圖形和色彩會儲存在 .edmx. 圖表檔案中。</span><span class="sxs-lookup"><span data-stu-id="da024-142">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="da024-143">若要查看這個檔案，請選取方案總管並展開 .edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="da024-143">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![圖表檔案](~/ef6/media/diagramfiles.png)

<span data-ttu-id="da024-145">您不應該手動編輯 .edmx 檔案，但 EF Designer 可能會覆寫這個檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="da024-145">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="da024-146">將實體和關聯分割成新的圖表</span><span class="sxs-lookup"><span data-stu-id="da024-146">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="da024-147">您可以選取現有圖表上的實體 (按住 Shift 鍵選取多個實體) 。</span><span class="sxs-lookup"><span data-stu-id="da024-147">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="da024-148">按一下滑鼠右鍵，然後選取 [ **移至新的圖表**]。</span><span class="sxs-lookup"><span data-stu-id="da024-148">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="da024-149">系統會建立新的圖表，並將選取的實體及其關聯移至圖表。</span><span class="sxs-lookup"><span data-stu-id="da024-149">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="da024-150">或者，您可以用滑鼠右鍵按一下 [模型瀏覽器] 中的 [圖表] 資料夾，然後選取 [**加入新的圖表**]</span><span class="sxs-lookup"><span data-stu-id="da024-150">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="da024-151">然後，您可以從 [模型瀏覽器] 中的 [實體類型] 資料夾，將實體拖放至設計介面。</span><span class="sxs-lookup"><span data-stu-id="da024-151">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="da024-152">您也可以使用從某個圖表) 的 Ctrl-X 或 Ctrl-c 鍵來剪下或複製實體 (，然後使用 Ctrl + V 鍵) 在另一個圖表上貼上 (。</span><span class="sxs-lookup"><span data-stu-id="da024-152">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="da024-153">如果您要貼上實體的圖表已包含具有相同名稱的實體，則會建立新的實體並將其加入至模型。</span><span class="sxs-lookup"><span data-stu-id="da024-153">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span><span data-ttu-id="da024-154">例如： Diagram2 包含部門實體。</span><span class="sxs-lookup"><span data-stu-id="da024-154">  For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="da024-155">然後，在 Diagram2 上貼上另一個部門。</span><span class="sxs-lookup"><span data-stu-id="da024-155">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="da024-156">Department1 實體會建立並加入至概念模型。</span><span class="sxs-lookup"><span data-stu-id="da024-156">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="da024-157">若要在圖表中包含相關實體，請在實體上按一下滑鼠右鍵，然後選取 [ **包含相關**專案]。</span><span class="sxs-lookup"><span data-stu-id="da024-157">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="da024-158">這會在指定的圖表中建立相關實體和關聯的複本。</span><span class="sxs-lookup"><span data-stu-id="da024-158">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="da024-159">變更實體的色彩</span><span class="sxs-lookup"><span data-stu-id="da024-159">Changing the Color of Entities</span></span>

<span data-ttu-id="da024-160">除了將模型分割成多個圖表，您也可以變更實體的色彩。</span><span class="sxs-lookup"><span data-stu-id="da024-160">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="da024-161">若要變更色彩，請在設計介面上選取實體 (或多個實體) 。</span><span class="sxs-lookup"><span data-stu-id="da024-161">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="da024-162">然後，按一下滑鼠右鍵，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="da024-162">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="da024-163">在 [屬性視窗中，選取 [ **填滿色彩** ] 屬性。</span><span class="sxs-lookup"><span data-stu-id="da024-163">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="da024-164">使用有效的色彩名稱指定色彩 (例如，紅色) 或有效的 RGB (例如255、128、128) 。</span><span class="sxs-lookup"><span data-stu-id="da024-164">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![變更色彩](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="da024-166">摘要</span><span class="sxs-lookup"><span data-stu-id="da024-166">Summary</span></span>

<span data-ttu-id="da024-167">在本主題中，我們討論了如何將模型分割成多個圖表，也會說明如何使用 Entity Framework Designer 為實體指定不同的色彩。</span><span class="sxs-lookup"><span data-stu-id="da024-167">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
