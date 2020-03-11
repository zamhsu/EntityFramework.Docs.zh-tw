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
# <a name="multiple-diagrams-per-model"></a>每個模型多個圖表
> [!NOTE]
> **EF5 僅限**，此頁面中所討論的功能、api 等內容是在 Entity Framework 5 中引進。 如果您使用的是較早版本，則不適用部分或全部的資訊。

這部影片和頁面會顯示如何使用 Entity Framework Designer （EF Designer）將模型分割成多個圖表。 當您的模型變得太大而無法觀看或編輯時，您可能會想要使用這項功能。

在舊版的 EF Designer 中，每個 EDMX 檔案只能有一個圖表。 從 Visual Studio 2012 開始，您可以使用 EF 設計工具將您的 EDMX 檔案分割成多個圖表。

## <a name="watch-the-video"></a>觀賞影片
這段影片示範如何使用 Entity Framework Designer （EF Designer）將模型分割成多個圖表。 當您的模型變得太大而無法觀看或編輯時，您可能會想要使用這項功能。

**提供者**： Julia Kornich

**影片**： [wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [.wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [wmv （ZIP）](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)

## <a name="ef-designer-overview"></a>EF 設計工具總覽

當您使用 EF Designer 的實體資料模型 Wizard 建立模型時，會建立 .edmx 檔案並將其新增至您的方案。 這個檔案會定義實體的形狀，以及它們如何對應到資料庫。

EF 設計工具組含下列元件：

-   用於編輯模型的視覺化設計介面。 您可以建立、修改或刪除實體與關聯。
-    **模型瀏覽器** 視窗，提供模型的樹狀檢視。  實體及其關聯位於 *\[ModelName\]*  資料夾底下。 資料庫資料表和條件約束位於 *\[ModelName\]* 之下。存放區資料夾。
-   [ **對應詳細資料**] 視窗，用於查看和編輯對應。 您可以將實體類型或關聯對應到資料庫資料表、資料行和預存程序。 

[視覺化設計介面] 視窗會在實體資料模型 Wizard 完成時自動開啟。 如果看不到 [模型瀏覽器]，請以滑鼠右鍵按一下主要設計介面，然後選取 [ **模型瀏覽器**]。

下列螢幕擷取畫面顯示在 EF 設計工具中開啟的 .edmx 檔案。 螢幕擷取畫面會顯示視覺化設計介面（左側）和 [ **模型瀏覽器**] 視窗（右側）。

![EF Designer 2](~/ef6/media/efdesigner2.png)

若要復原在 EF 設計工具中完成的作業，請按一下 [Ctrl-Z]。

## <a name="working-with-diagrams"></a>使用圖表

根據預設，EF Designer 會建立一個稱為 Diagram1 的圖表。 如果您的圖表中有大量的實體和關聯，您會想要以邏輯方式將它們分割。 從 Visual Studio 2012 開始，您可以在多個圖表中查看概念模型。   

當您加入新的圖表時，它們會出現在 [模型瀏覽器] 視窗的 [圖表] 資料夾底下。 若要重新命名圖表：選取 [模型瀏覽器] 視窗中的圖表，在名稱上按一下 [一次]，然後輸入新的名稱。  您也可以用滑鼠右鍵按一下圖表名稱，然後選取 [**重新命名**]。

圖表名稱會顯示在 [Visual Studio 編輯器] 中 .edmx 檔案名的旁邊。 例如，Model1\[Diagram1\]。

![DiagramName](~/ef6/media/diagramname.png)

圖表內容（實體和關聯的形狀和色彩）會儲存在 .edmx. 圖表檔案中。 若要查看此檔案，請選取方案總管並展開 .edmx 檔案。 

![DiagramFiles](~/ef6/media/diagramfiles.png)

您不應該手動編輯 .edmx 檔案，因此 EF Designer 可能會覆寫這個檔案的內容。
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a>將實體和關聯分割成新的圖表

您可以選取現有圖表上的實體（按住 Shift 鍵可選取多個實體）。 按一下滑鼠右鍵，然後選取 [**移至新圖表**]。 隨即建立新的圖表，並將選取的實體及其關聯移至圖表。

或者，您也可以用滑鼠右鍵按一下 [模型瀏覽器] 中的 [圖表] 資料夾，然後選取 [**新增圖表]。** 接著，您可以從 [模型瀏覽器] 中的 [實體類型] 資料夾，將實體拖放到設計介面上。

您也可以從一個圖表剪下或複製實體（使用 Ctrl X 或 Ctrl-c 鍵），並貼上（使用 Ctrl + V 鍵）。 如果您要在其中貼上實體的圖表已經包含具有相同名稱的實體，則會建立新的實體，並將其加入至模型。  例如： Diagram2 包含部門實體。 然後，在 Diagram2 上貼上另一個部門。 Department1 實體會建立並加入至概念模型。   

若要在圖表中包含相關實體，請在實體上按一下，然後選取 [**包含相關**專案]。 這會在指定的圖表中建立相關實體和關聯的複本。

## <a name="changing-the-color-of-entities"></a>變更實體的色彩

除了將模型分割成多個圖表，您也可以變更實體的色彩。

若要變更色彩，請在設計介面上選取實體（或多個實體）。 然後，按一下滑鼠右鍵並選取 [**屬性**]。 在 屬性視窗中，選取 **填滿色彩** 屬性。 請使用有效的色彩名稱（例如，紅色）或有效的 RGB （例如，255、128、128）來指定色彩。 

![Color](~/ef6/media/color.png)

## <a name="summary"></a>摘要

在本主題中，我們探討了如何將模型分割成多個圖表，以及如何使用 Entity Framework Designer 來指定實體的不同色彩。 
