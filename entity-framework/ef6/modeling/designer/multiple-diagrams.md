---
title: 每個模型的 EF6 的多個圖表
author: divega
ms.date: 2016-10-23
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
ms.openlocfilehash: f27afbd3b44ff3eb8044ab960f10b2856a603ee3
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993774"
---
# <a name="multiple-diagrams-per-model"></a>每個模型的多個圖表
> [!NOTE]
> **EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。 如果您使用的是較早版本，則不適用部分或全部的資訊。

本影片和頁面會顯示如何將模型分割成多個使用 Entity Framework Designer （EF 設計工具） 的圖表。 您可能想要使用這項功能，當您的模型變得太大而無法檢視或編輯。

舊版 EF 設計工具中您只可以有一個圖表每個 EDMX 檔案。 從 Visual Studio 2012 開始，您可以使用 EF 設計工具以將您的 EDMX 檔案分割成多個圖表。

## <a name="watch-the-video"></a>觀看影片
這段影片示範如何將模型分割成多個使用 Entity Framework Designer （EF 設計工具） 的圖表。 您可能想要使用這項功能，當您的模型變得太大而無法檢視或編輯。

**由**: Julia Kornich

**視訊**: [WMV](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)

## <a name="ef-designer-overview"></a>EF 設計工具概觀

當您建立模型，使用 EF 設計工具的 Entity Data Model 精靈時，.edmx 檔案建立，並新增至您的解決方案。 此檔案會定義您的實體，以及它們如何對應到資料庫的圖形。

EF 設計工具是由下列元件所組成：

-   編輯模型的視覺化設計介面。 您可以建立、修改或刪除實體與關聯。
-   A**模型瀏覽器**提供模型的樹狀結構檢視的視窗。  實體和其關聯位於*\[ModelName\]* 資料夾。 資料庫資料表和條件約束位於 *\[ModelName\]*。存放區 資料夾中。
-   A**對應詳細資料**視窗來檢視及編輯對應。 您可以將實體類型或關聯對應到資料庫資料表、資料行和預存程序。 

當 Entity Data Model 精靈完成時，會自動開啟視覺化設計介面視窗。 如果看不到 模型瀏覽器，以滑鼠右鍵按一下主設計介面，然後選取**模型瀏覽器**。

下列螢幕擷取畫面顯示在 EF 設計工具中開啟.edmx 檔案。 螢幕擷取畫面顯示的視覺化設計介面 （左邊） 和**模型瀏覽器**視窗 （右邊）。

![EFDesigner2](~/ef6/media/efdesigner2.png)

若要復原在 EF 設計工具中進行的操作，請按一下 CTRL-Z。

## <a name="working-with-diagrams"></a>使用圖表

依預設 EF 設計工具會建立一個稱為 Diagram1 的圖表。 如果您有大量的實體和關聯的圖表，您最想要以邏輯方式分割它們。 從 Visual Studio 2012 開始，您可以在多個圖表中檢視您的概念模型。   

當您新增新的圖表時，它們會出現在 模型瀏覽器視窗中的 圖表 資料夾底下。 若要重新命名圖表： 在 模型瀏覽器視窗中選取圖表，一次按一下名稱，輸入新名稱。  您也可以以滑鼠右鍵按一下圖表的名稱，並選取**重新命名**。

Visual Studio 編輯器中的.edmx 檔案名稱旁邊顯示的圖表名稱。 例如 Model1.edmx\[Diagram1\]。

![DiagramName](~/ef6/media/diagramname.png)

圖表有更多的內容 （形狀和色彩的實體和關聯） 儲存在。 edmx.diagram 檔案。 若要檢視此檔案，請選取方案總管 中，展開.edmx 檔案。 

![DiagramFiles](~/ef6/media/diagramfiles.png)

您不應該編輯。 edmx.diagram 手動，檔案可能由 EF 設計工具覆寫此檔案的內容。
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a>分割實體和關聯到新的圖表

您可以選取現有的圖表 （按住 shift 鍵選取多個實體） 上的實體。 按一下滑鼠右鍵，然後選取**移至新的圖表**。 已建立新的圖表，並為選取的實體和其關聯都會移至圖表。

或者，您可以在 模型瀏覽器中的 圖表 資料夾上按一下滑鼠右鍵，然後選取**加入新的圖表。** 您可以再拖放實體拖曳至設計介面的 模型瀏覽器中的實體類型資料夾底下。

您也可以剪下或複製一個圖表中的實體 （使用 Ctrl-X 或 Ctrl + C 鍵），並貼上 （使用 Ctrl-V 鍵） 」，另。 如果的圖表在其中您要貼上的實體已經包含具有相同名稱的實體，會建立新的實體，並加入至模型。  例如： 圖表 2 包含 Department 實體。 然後，您會圖表 2 上貼另一個部門。 Department1 實體已建立，並加入至概念模型。   

若要在圖表中包含相關的實體，滑鼠右鍵按一下該實體並選取**包含相關**。 指定圖表中，這會讓一份相關的實體和關聯。

## <a name="changing-the-color-of-entities"></a>變更實體的色彩

除了將模型分割成多個圖表中，您也可以變更您的實體的色彩。

若要變更色彩，請在設計介面上選取實體 （或多個實體）。 然後，按一下滑鼠右鍵，然後選取**屬性**。 在 [屬性] 視窗中，選取**填滿色彩**屬性。 指定使用有效的色彩名稱 （例如，紅色） 或有效的 RGB （比方說，255，128，128） 的色彩。 

![色彩](~/ef6/media/color.png)

## <a name="summary"></a>總結

本主題中我們探討了如何將模型分割成多個圖表以及如何指定不同的色彩，使用 Entity Framework Designer 中的實體。 
