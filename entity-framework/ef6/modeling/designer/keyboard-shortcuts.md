---
title: Entity Framework Designer 鍵盤快速鍵-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3c76cdd5-17c5-4c54-a6a5-cf21b974636b
ms.openlocfilehash: c75eafcca0863faa1ad64202e98b61832827377c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418515"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Entity Framework Designer 鍵盤快速鍵
此頁面提供可用於 Visual Studio Entity Framework Tools 的各種畫面中的鍵盤 shorcuts 清單。

## <a name="adonet-entity-data-model-wizard"></a>ADO.NET 實體資料模型 Wizard

### <a name="step-one-choose-model-contents"></a>步驟一：選擇模型內容

![Wizard 一](~/ef6/media/wizardone.png)

| 快速鍵  | 動作                                                     | 注意                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                        | 不適用於模型內容的所有選取專案。 |
| **Alt + f** | 完成精靈                                              | 不適用於模型內容的所有選取專案。 |
| **Alt + w** | 將焦點切換到「模型應該包含什麼？」 顯示各種選項。 |                                                     |

### <a name="step-two-choose-your-connection"></a>步驟二：選擇您的連線

![Wizard 二](~/ef6/media/wizardtwo.png)

| 快速鍵  | 動作                                                     | 注意                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                        |                                                         |
| **Alt + p** | 移至上一個畫面                                    |                                                         |
| **Alt + w** | 將焦點切換到「模型應該包含什麼？」 顯示各種選項。 |                                                         |
| **Alt + c** | 開啟 [連接屬性] 視窗                    | 允許定義新的資料庫連接。 |
| **Alt + e** | 從連接字串排除敏感性資料          |                                                         |
| **Alt + i** | 在連接字串中包含敏感性資料            |                                                         |
| **Alt + s** | 切換 [儲存 App.config 中的連接設定] 選項 |                                                         |

### <a name="step-three-choose-your-version"></a>步驟三：選擇您的版本

![Wizard 三](~/ef6/media/wizardthree.png)

| 快速鍵  | 動作                                             | 注意                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                |                                                                                       |
| **Alt + p** | 移至上一個畫面                            |                                                                                       |
| **Alt + w** | 將焦點切換到 Entity Framework 版本選擇 | 可指定要在專案中使用的不同 Entity Framework 版本。 |

### <a name="step-four-choose-your-database-objects-and-settings"></a>步驟四：選擇您的資料庫物件和設定

![Wizard 四](~/ef6/media/wizardfour.png)

| 快速鍵  | 動作                                                                                    | 注意                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | 完成精靈                                                                             |                                                                     |
| **Alt + p** | 移至上一個畫面                                                                   |                                                                     |
| **Alt + w** | 將焦點切換到資料庫物件選取窗格                                            | 允許指定要反向工程的資料庫物件。    |
| **Alt + s** | 切換 [將複數化或單數化產生的物件名稱] 選項                       |                                                                     |
| **Alt + k** | 切換 [在模型中包含外鍵資料行] 選項                              | 不適用於模型內容的所有選取專案。                 |
| **Alt + i** | 切換 [將選取的預存程式和函式匯入實體模型] 選項 | 不適用於模型內容的所有選取專案。                 |
| **Alt + m** | 將焦點切換至 [模型命名空間] 文字欄位                                        | 不適用於模型內容的所有選取專案。                 |
| **空格鍵** | 切換元素上的選取專案                                                               | 如果元素具有子系，則也會切換所有子專案。 |
| **Left**  | 折迭子樹狀結構                                                                       |                                                                     |
| **Right** | 展開子樹狀結構                                                                         |                                                                     |
| **Up**    | 流覽至樹狀結構中的上一個元素                                                      |                                                                     |
| **向下**  | 流覽至樹狀結構中的下一個元素                                                          |                                                                     |

## <a name="ef-designer-surface"></a>EF 設計工具介面

![設計師介面](~/ef6/media/designersurface.png)

| 快速鍵                                                                                | 動作                      | 注意                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **空格/Enter**                                                                         | 切換選取範圍            | 以焦點切換物件上的選取範圍。                                                                                                                                                                                         |
| **Esc**                                                                                 | 取消選取            | 取消目前的選取範圍。                                                                                                                                                                                                      |
| **Ctrl + A**                                                                            | 全選                  | 選取設計介面上的所有圖形。                                                                                                                                                                                       |
| **向上箭頭**                                                                            | 上移                     | 將選取的實體向上移動一個格線增量。 <br/> 如果在清單中，則會移至先前的兄弟子欄位。                                                                                                                            |
| **向下箭頭**                                                                          | 下移                   | 將選取的實體向下移動一個格線增量。 <br/> 如果在清單中，則會移至下一個兄弟子欄位。                                                                                                                              |
| **向左鍵**                                                                          | 左移                   | 將選取的實體向左移動一個格線增量。 <br/> 如果在清單中，則會移至先前的兄弟子欄位。                                                                                                                          |
| **向右鍵**                                                                         | 右移                  | 將選取的實體向右移動一個格線增量。 <br/> 如果在清單中，則會移至下一個兄弟子欄位。                                                                                                                             |
| **Shift + 向左鍵**                                                                  | 左大小圖形             | 將所選取實體的寬度減少一個格線增量。                                                                                                                                                                     |
| **Shift + 向右鍵**                                                                 | 向右大小圖形            | 將所選取實體的寬度增加一個格線增量。                                                                                                                                                                   |
| **首頁**                                                                                | 第一個對等                  | 在相同對等層級的設計介面上，將焦點和選取範圍移至第一個物件。                                                                                                                                         |
| **結束**                                                                                 | 上次對等                   | 將焦點和選取範圍移至設計介面上位於相同對等層級的最後一個物件。                                                                                                                                          |
| **Ctrl + Home**                                                                         | 第一個對等（焦點）          | 與第一個對等相同，但是移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                          |
| **Ctrl + End**                                                                          | 最後對等（焦點）           | 與最後一個對等相同，但是移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                           |
| **Tab**                                                                                 | 下一個對等                   | 在相同對等層級的設計介面上，將焦點和選取範圍移至下一個物件。                                                                                                                                          |
| **Shift+Tab**                                                                           | 先前的對等               | 在相同的對等層級上，將焦點和選取範圍移至設計介面上的上一個物件。                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | 下一個對等（焦點）           | 與下一個對等相同，但是移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                           |
| **Alt + Ctrl + Shift + Tab**                                                                  | 上一個對等（焦點）       | 與先前的對等相同，但會移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                       |
| **&lt;**                                                                                | 于                      | 移至設計介面上的下一個物件，階層中的一個層級較高。 如果階層中沒有此圖形之上的圖形（也就是，物件直接放在設計介面上），則會選取圖表。 |
| **&gt;**                                                                                | 下降                     | 移至設計介面上的下一個包含物件，此階層中的一個層級底下。 如果沒有包含物件，這就不是任何作業。                                                                              |
| **Ctrl + &lt;**                                                                         | 遞增（焦點）              | 與 [遞增] 命令相同，但不選取即可移動焦點。                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         | 下降（焦點）             | 與下降命令相同，但不選取即可移動焦點。                                                                                                                                                                         |
| **Shift + End**                                                                         | 關注連線         | 從實體移至此實體所連接的實體。                                                                                                                                                               |
| **Del**                                                                                 | 刪除                      | 從圖表中刪除物件或連接器。                                                                                                                                                                                     |
| **接**                                                                                 | 插入                      | 當選取 [純量屬性] 區間標頭或屬性本身時，將新的屬性加入至實體。                                                                                                           |
| **Pg**                                                                               | 向上滾動圖           | 將設計介面向上滾動，其增量等於目前可見設計介面高度的75%。                                                                                                                    |
| **下一頁**                                                                             | 向下滾動圖表         | 向下滾動設計介面。                                                                                                                                                                                                    |
| **Shift + 下一頁**                                                                     | 向右滾動圖表        | 將設計介面向右滾動。                                                                                                                                                                                            |
| **Shift + Pg**                                                                       | 左側滾動圖         | 將設計介面向左滾動。                                                                                                                                                                                             |
| **F2**                                                                                  | 進入編輯模式             | 為文字控制項輸入編輯模式的標準鍵盤快捷方式。                                                                                                                                                               |
| **Shift + F10**                                                                         | 顯示捷徑功能表       | 用於顯示所選取專案快捷方式功能表的標準鍵盤快捷方式。                                                                                                                                                          |
| **Ctrl + Shift + 滑鼠左鍵按一下**  <br/> **Ctrl + Shift + 滑鼠滾輪向前**  | 語義放大            | 放大滑鼠指標底下圖表視圖的區域。                                                                                                                                                                 |
| **Ctrl + Shift + 滑鼠右鍵按一下** <br/> **Ctrl + Shift + 滑鼠滾輪向後** | 語義縮小           | 從滑鼠指標下方的圖表視圖區域縮小。 當您針對目前的圖表中心縮小太遠時，它會將圖表重新置中。                                                                          |
| **Ctrl + Shift + ' + '** <br/> **控制 + 向前滑鼠滾輪**                        | 放大                     | 放大圖表視圖的中央。                                                                                                                                                                                         |
| **Ctrl + Shift + '-'** <br/> **控制項 + 向後滑鼠滾輪**                       | 縮小                    | 從圖表視圖的已按一下區域縮小。 當您針對目前的圖表中心縮小太遠時，它會將圖表重新置中。                                                                                            |
| **控制項 + Shift + 繪製矩形並向下滑鼠左鍵**                  | 縮放區域                   | 以您選取的區域為中心放大。 當您按住 Ctrl + Shift 鍵時，您會看到游標變成放大鏡，讓您定義要放大的區域。                        |
| **內容功能表鍵 + m '**                                                              | 開啟對應詳細資料視窗 | 開啟 [對應詳細資料] 視窗，以編輯所選實體的對應                                                                                                                                                               |

## <a name="mapping-details-window"></a>對應詳細資料視窗

![對應詳細資料快捷方式](~/ef6/media/mappingdetailsshortcuts.png)

| 快速鍵                  | 動作         | 注意                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **Tab**                   | 切換內容 | 在主視窗區域和左側工具列之間切換                                                                     |
| **方向鍵**            | 導覽     | 向上和向下移動資料列，或在主視窗區域中跨資料行向右或向左移動。 在左側工具列的按鈕之間移動。 |
| **Enter** <br/> **空格鍵** | Select         | 選取左側工具列中的按鈕。                                                                                          |
| **Alt + 向下鍵**      | 開啟清單      | 如果選取了具有下拉式清單的資料格，則下拉式清單。                                                                     |
| **Enter**                 | 清單選取    | 選取下拉式清單中的元素。                                                                                               |
| **Esc**                   | 列出關閉     | 關閉下拉式清單。                                                                                                              |

## <a name="visual-studio-navigation"></a>Visual Studio 導覽

Entity Framework 也會提供一些可以對應自訂鍵盤快速鍵的動作（預設沒有對應的快捷方式）。 若要建立這些自訂快捷方式，請依序按一下 [工具] 功能表和 [選項]。  在 [環境] 下，選擇 [鍵盤]。  在中間往下滾動清單，直到您可以選取所需的命令，並在 [按快速鍵] 文字方塊中輸入快捷方式，然後按一下 [指派]。 可能的快捷方式如下所示：

| 快速鍵                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Add. ComplexProperty. ComplexTypes**        |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.AddCodeGenerationItem**                   |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.AddFunctionImport**                       |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. AddEnumType**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. 關聯**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. ComplexProperty**                  |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew ComplexType**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. 實體**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. FunctionImport**                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. 繼承**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. NavigationProperty**               |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. AddNew. ScalarProperty**                   |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.AddNewDiagram**                           |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.AddtoDiagram**                            |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Close**                                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Collapse**                                |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ConverttoEnum**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 全部折迭**                     |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 全部展開**                       |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. ExportasImage**                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. LayoutDiagram**                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 編輯**                                    |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. EntityKey**                               |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 展開**                                  |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. FunctionImportMapping**                   |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.GenerateDatabasefromModel**               |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.GoToDefinition**                          |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Grid. ShowGrid**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext。貼齊格線**                         |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.IncludeRelated**                          |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MappingDetails**                          |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ModelBrowser**                            |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MoveDiagramstoSeparateFile**              |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. MoveProperties**                     |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MoveProperties.Down5**                    |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MoveProperties.ToBottom**                 |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MoveProperties.ToTop**                    |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. MoveProperties. Up**                       |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. MoveProperties. Up5**                      |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MovetonewDiagram**                        |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Open**                                    |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 重構. MovetoNewComplexType**           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 重構. 重新命名**                         |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.RemovefromDiagram**                       |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Rename**                                  |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. ScalarPropertyFormat. DisplayName**        |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 選取 BaseType**                         |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Select. 實體**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Select. Property**                         |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Select. 子類型**                          |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. SelectAll**                               |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.SelectAssociation**                       |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ShowinDiagram**                           |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ShowinModelBrowser**                      |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.StoredProcedureMapping**                  |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.TableMapping**                            |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.UpdateModelfromDatabase**                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Validate**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom 10**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 放大100**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 125**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 150**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 200**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 25**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 300**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 33**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 400**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 50**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. 75**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. Custom**                             |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. Vemap.zoomin**                             |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. Vemap.zoomout**                            |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom. ZoomtoFit**                          |
| **EntityDataModelBrowser**                                                                |
| **EntityDataModelMappingDetails**                                                         |
