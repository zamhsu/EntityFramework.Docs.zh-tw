---
title: 每個模型的 EF6 的多個圖表
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
caps.latest.revision: 3
ms.openlocfilehash: 3a022b3e44ecd4b6b62224cb6494c794397a9739
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120470"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="e16f9-102">每個模型的多個圖表</span><span class="sxs-lookup"><span data-stu-id="e16f9-102">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="e16f9-103">**EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="e16f9-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="e16f9-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="e16f9-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="e16f9-105">本影片和頁面會顯示如何將模型分割成多個使用 Entity Framework Designer （EF 設計工具） 的圖表。</span><span class="sxs-lookup"><span data-stu-id="e16f9-105">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e16f9-106">您可能想要使用這項功能，當您的模型變得太大而無法檢視或編輯。</span><span class="sxs-lookup"><span data-stu-id="e16f9-106">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="e16f9-107">舊版 EF 設計工具中您只可以有一個圖表每個 EDMX 檔案。</span><span class="sxs-lookup"><span data-stu-id="e16f9-107">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="e16f9-108">從 Visual Studio 2012 開始，您可以使用 EF 設計工具以將您的 EDMX 檔案分割成多個圖表。</span><span class="sxs-lookup"><span data-stu-id="e16f9-108">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="e16f9-109">觀看影片</span><span class="sxs-lookup"><span data-stu-id="e16f9-109">Watch the video</span></span>
<span data-ttu-id="e16f9-110">這段影片示範如何將模型分割成多個使用 Entity Framework Designer （EF 設計工具） 的圖表。</span><span class="sxs-lookup"><span data-stu-id="e16f9-110">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e16f9-111">您可能想要使用這項功能，當您的模型變得太大而無法檢視或編輯。</span><span class="sxs-lookup"><span data-stu-id="e16f9-111">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="e16f9-112">**由**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="e16f9-112">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="e16f9-113">**視訊**: [WMV](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="e16f9-113">**Video**: [WMV](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="e16f9-114">EF 設計工具概觀</span><span class="sxs-lookup"><span data-stu-id="e16f9-114">EF Designer Overview</span></span>

<span data-ttu-id="e16f9-115">當您建立模型，使用 EF 設計工具的 Entity Data Model 精靈時，.edmx 檔案建立，並新增至您的解決方案。</span><span class="sxs-lookup"><span data-stu-id="e16f9-115">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="e16f9-116">此檔案會定義您的實體，以及它們如何對應到資料庫的圖形。</span><span class="sxs-lookup"><span data-stu-id="e16f9-116">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="e16f9-117">EF 設計工具是由下列元件所組成：</span><span class="sxs-lookup"><span data-stu-id="e16f9-117">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="e16f9-118">編輯模型的視覺化設計介面。</span><span class="sxs-lookup"><span data-stu-id="e16f9-118">A visual design surface for editing the model.</span></span> <span data-ttu-id="e16f9-119">您可以建立、修改或刪除實體與關聯。</span><span class="sxs-lookup"><span data-stu-id="e16f9-119">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="e16f9-120">A**模型瀏覽器**提供模型的樹狀結構檢視的視窗。</span><span class="sxs-lookup"><span data-stu-id="e16f9-120">A **Model Browser** window that provides tree views of the model.</span></span>  <span data-ttu-id="e16f9-121">實體和其關聯位於*\[ModelName\]* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="e16f9-121">The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="e16f9-122">資料庫資料表和條件約束位於 *\[ModelName\]*。存放區 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e16f9-122">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="e16f9-123">A**對應詳細資料**視窗來檢視及編輯對應。</span><span class="sxs-lookup"><span data-stu-id="e16f9-123">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="e16f9-124">您可以將實體類型或關聯對應到資料庫資料表、資料行和預存程序。</span><span class="sxs-lookup"><span data-stu-id="e16f9-124">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="e16f9-125">當 Entity Data Model 精靈完成時，會自動開啟視覺化設計介面視窗。</span><span class="sxs-lookup"><span data-stu-id="e16f9-125">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="e16f9-126">如果看不到 模型瀏覽器，以滑鼠右鍵按一下主設計介面，然後選取**模型瀏覽器**。</span><span class="sxs-lookup"><span data-stu-id="e16f9-126">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="e16f9-127">下列螢幕擷取畫面顯示在 EF 設計工具中開啟.edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="e16f9-127">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="e16f9-128">螢幕擷取畫面顯示的視覺化設計介面 （左邊） 和**模型瀏覽器**視窗 （右邊）。</span><span class="sxs-lookup"><span data-stu-id="e16f9-128">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EFDesigner2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="e16f9-130">若要復原在 EF 設計工具中進行的操作，請按一下 CTRL-Z。</span><span class="sxs-lookup"><span data-stu-id="e16f9-130">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="e16f9-131">使用圖表</span><span class="sxs-lookup"><span data-stu-id="e16f9-131">Working with Diagrams</span></span>

<span data-ttu-id="e16f9-132">依預設 EF 設計工具會建立一個稱為 Diagram1 的圖表。</span><span class="sxs-lookup"><span data-stu-id="e16f9-132">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="e16f9-133">如果您有大量的實體和關聯的圖表，您最想要以邏輯方式分割它們。</span><span class="sxs-lookup"><span data-stu-id="e16f9-133">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="e16f9-134">從 Visual Studio 2012 開始，您可以在多個圖表中檢視您的概念模型。</span><span class="sxs-lookup"><span data-stu-id="e16f9-134">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="e16f9-135">當您新增新的圖表時，它們會出現在 模型瀏覽器視窗中的 圖表 資料夾底下。</span><span class="sxs-lookup"><span data-stu-id="e16f9-135">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="e16f9-136">若要重新命名圖表： 在 模型瀏覽器視窗中選取圖表，一次按一下名稱，輸入新名稱。</span><span class="sxs-lookup"><span data-stu-id="e16f9-136">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span>  <span data-ttu-id="e16f9-137">您也可以以滑鼠右鍵按一下圖表的名稱，並選取**重新命名**。</span><span class="sxs-lookup"><span data-stu-id="e16f9-137">You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="e16f9-138">Visual Studio 編輯器中的.edmx 檔案名稱旁邊顯示的圖表名稱。</span><span class="sxs-lookup"><span data-stu-id="e16f9-138">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="e16f9-139">例如 Model1.edmx\[Diagram1\]。</span><span class="sxs-lookup"><span data-stu-id="e16f9-139">For example Model1.edmx\[Diagram1\].</span></span>

![DiagramName](~/ef6/media/diagramname.png)

<span data-ttu-id="e16f9-141">圖表有更多的內容 （形狀和色彩的實體和關聯） 儲存在。 edmx.diagram 檔案。</span><span class="sxs-lookup"><span data-stu-id="e16f9-141">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="e16f9-142">若要檢視此檔案，請選取方案總管 中，展開.edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="e16f9-142">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![DiagramFiles](~/ef6/media/diagramfiles.png)

<span data-ttu-id="e16f9-144">您不應該編輯。 edmx.diagram 手動，檔案可能由 EF 設計工具覆寫此檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e16f9-144">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="e16f9-145">分割實體和關聯到新的圖表</span><span class="sxs-lookup"><span data-stu-id="e16f9-145">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="e16f9-146">您可以選取現有的圖表 （按住 shift 鍵選取多個實體） 上的實體。</span><span class="sxs-lookup"><span data-stu-id="e16f9-146">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="e16f9-147">按一下滑鼠右鍵，然後選取**移至新的圖表**。</span><span class="sxs-lookup"><span data-stu-id="e16f9-147">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="e16f9-148">已建立新的圖表，並為選取的實體和其關聯都會移至圖表。</span><span class="sxs-lookup"><span data-stu-id="e16f9-148">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="e16f9-149">或者，您可以在 模型瀏覽器中的 圖表 資料夾上按一下滑鼠右鍵，然後選取**加入新的圖表。**</span><span class="sxs-lookup"><span data-stu-id="e16f9-149">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="e16f9-150">您可以再拖放實體拖曳至設計介面的 模型瀏覽器中的實體類型資料夾底下。</span><span class="sxs-lookup"><span data-stu-id="e16f9-150">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="e16f9-151">您也可以剪下或複製一個圖表中的實體 （使用 Ctrl-X 或 Ctrl + C 鍵），並貼上 （使用 Ctrl-V 鍵） 」，另。</span><span class="sxs-lookup"><span data-stu-id="e16f9-151">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="e16f9-152">如果的圖表在其中您要貼上的實體已經包含具有相同名稱的實體，會建立新的實體，並加入至模型。</span><span class="sxs-lookup"><span data-stu-id="e16f9-152">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span>  <span data-ttu-id="e16f9-153">例如： 圖表 2 包含 Department 實體。</span><span class="sxs-lookup"><span data-stu-id="e16f9-153">For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="e16f9-154">然後，您會圖表 2 上貼另一個部門。</span><span class="sxs-lookup"><span data-stu-id="e16f9-154">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="e16f9-155">Department1 實體已建立，並加入至概念模型。</span><span class="sxs-lookup"><span data-stu-id="e16f9-155">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="e16f9-156">若要在圖表中包含相關的實體，滑鼠右鍵按一下該實體並選取**包含相關**。</span><span class="sxs-lookup"><span data-stu-id="e16f9-156">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="e16f9-157">指定圖表中，這會讓一份相關的實體和關聯。</span><span class="sxs-lookup"><span data-stu-id="e16f9-157">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="e16f9-158">變更實體的色彩</span><span class="sxs-lookup"><span data-stu-id="e16f9-158">Changing the Color of Entities</span></span>

<span data-ttu-id="e16f9-159">除了將模型分割成多個圖表中，您也可以變更您的實體的色彩。</span><span class="sxs-lookup"><span data-stu-id="e16f9-159">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="e16f9-160">若要變更色彩，請在設計介面上選取實體 （或多個實體）。</span><span class="sxs-lookup"><span data-stu-id="e16f9-160">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="e16f9-161">然後，按一下滑鼠右鍵，然後選取**屬性**。</span><span class="sxs-lookup"><span data-stu-id="e16f9-161">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="e16f9-162">在 [屬性] 視窗中，選取**填滿色彩**屬性。</span><span class="sxs-lookup"><span data-stu-id="e16f9-162">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="e16f9-163">指定使用有效的色彩名稱 （例如，紅色） 或有效的 RGB （比方說，255，128，128） 的色彩。</span><span class="sxs-lookup"><span data-stu-id="e16f9-163">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![色彩](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="e16f9-165">總結</span><span class="sxs-lookup"><span data-stu-id="e16f9-165">Summary</span></span>

<span data-ttu-id="e16f9-166">本主題中我們探討了如何將模型分割成多個圖表以及如何指定不同的色彩，使用 Entity Framework Designer 中的實體。</span><span class="sxs-lookup"><span data-stu-id="e16f9-166">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
