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
# <a name="multiple-diagrams-per-model"></a>每個模型的多個圖表
> [!NOTE]
> **EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

此影片和頁面會示範如何使用 Entity Framework Designer (EF 設計工具) ，將模型分割成多個圖表。 當您的模型變得太大而無法進行查看或編輯時，您可能會想要使用這項功能。

在舊版的 EF 設計工具中，每個 EDMX 檔案只能有一個圖表。 從 Visual Studio 2012 開始，您可以使用 EF 設計工具將您的 EDMX 檔案分割成多個圖表。

## <a name="watch-the-video"></a>觀賞影片
這段影片說明如何使用 Entity Framework Designer (EF 設計工具) ，將模型分割成多個圖表。 當您的模型變得太大而無法進行查看或編輯時，您可能會想要使用這項功能。

**提供者**： Julia Kornich

**影片**： [wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)的  |  [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)

## <a name="ef-designer-overview"></a>EF 設計工具總覽

當您使用 EF Designer 的實體資料模型 Wizard 建立模型時，會建立 .edmx 檔並將其加入至您的方案。 此檔案會定義實體的形狀，以及它們如何對應到資料庫。

EF 設計工具是由下列元件所組成：

-   用來編輯模型的視覺化設計介面。 您可以建立、修改或刪除實體與關聯。
-   提供模型樹狀檢視的 [ **模型瀏覽器**   ] 視窗。實體及其關聯位於* \[ ModelName \] *資料夾底下。 資料庫資料表和條件約束位於* \[ ModelName \] *下。存放區資料夾。
-   用於查看和編輯對應的 [ **對應詳細資料** ] 視窗。 您可以將實體類型或關聯對應到資料庫資料表、資料行和預存程序。 

當實體資料模型 Wizard 完成時，會自動開啟視覺化設計介面視窗。 如果看不到 [模型瀏覽器]，請以滑鼠右鍵按一下主要設計介面，然後選取 [ **模型瀏覽器**]。

下列螢幕擷取畫面顯示在 EF 設計工具中開啟的 .edmx 檔案。 螢幕擷取畫面顯示視覺設計介面 (左) ，而 [ **模型瀏覽器**   ] 視窗 (到右邊的) 。

![EF 設計工具2](~/ef6/media/efdesigner2.png)

若要復原在 EF Designer 中完成的作業，請按一下 Ctrl + Z。

## <a name="working-with-diagrams"></a>使用圖表

根據預設，EF Designer 會建立一個名為 Diagram1 的圖表。 如果您的圖表有大量的實體和關聯，您最喜歡以邏輯方式將它們分割。 從 Visual Studio 2012 開始，您可以在多個圖表中查看您的概念模型。   

當您加入新的圖表時，它們會出現在 [模型瀏覽器] 視窗的 [圖表] 資料夾底下。 若要重新命名圖表：請在 [模型瀏覽器] 視窗中選取圖表，並在名稱上按一下 [一次]，然後輸入新的名稱。 您也可以用滑鼠右鍵按一下圖表名稱，然後選取 [ **重新命名**]。

在 [Visual Studio 編輯器] 的 .edmx 檔案名旁邊會顯示圖表名稱。 例如 Model1 .edmx \[ Diagram1 \] 。

![圖表名稱](~/ef6/media/diagramname.png)

圖表內容 (實體和關聯) 的圖形和色彩會儲存在 .edmx. 圖表檔案中。 若要查看這個檔案，請選取方案總管並展開 .edmx 檔案。 

![圖表檔案](~/ef6/media/diagramfiles.png)

您不應該手動編輯 .edmx 檔案，但 EF Designer 可能會覆寫這個檔案的內容。
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a>將實體和關聯分割成新的圖表

您可以選取現有圖表上的實體 (按住 Shift 鍵選取多個實體) 。 按一下滑鼠右鍵，然後選取 [ **移至新的圖表**]。 系統會建立新的圖表，並將選取的實體及其關聯移至圖表。

或者，您可以用滑鼠右鍵按一下 [模型瀏覽器] 中的 [圖表] 資料夾，然後選取 [**加入新的圖表**] 然後，您可以從 [模型瀏覽器] 中的 [實體類型] 資料夾，將實體拖放至設計介面。

您也可以使用從某個圖表) 的 Ctrl-X 或 Ctrl-c 鍵來剪下或複製實體 (，然後使用 Ctrl + V 鍵) 在另一個圖表上貼上 (。 如果您要貼上實體的圖表已包含具有相同名稱的實體，則會建立新的實體並將其加入至模型。例如： Diagram2 包含部門實體。 然後，在 Diagram2 上貼上另一個部門。 Department1 實體會建立並加入至概念模型。   

若要在圖表中包含相關實體，請在實體上按一下滑鼠右鍵，然後選取 [ **包含相關**專案]。 這會在指定的圖表中建立相關實體和關聯的複本。

## <a name="changing-the-color-of-entities"></a>變更實體的色彩

除了將模型分割成多個圖表，您也可以變更實體的色彩。

若要變更色彩，請在設計介面上選取實體 (或多個實體) 。 然後，按一下滑鼠右鍵，然後選取 [ **屬性**]。 在 [屬性視窗中，選取 [ **填滿色彩** ] 屬性。 使用有效的色彩名稱指定色彩 (例如，紅色) 或有效的 RGB (例如255、128、128) 。 

![變更色彩](~/ef6/media/color.png)

## <a name="summary"></a>摘要

在本主題中，我們討論了如何將模型分割成多個圖表，也會說明如何使用 Entity Framework Designer 為實體指定不同的色彩。 
