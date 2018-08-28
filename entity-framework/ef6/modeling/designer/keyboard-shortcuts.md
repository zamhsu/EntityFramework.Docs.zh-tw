---
title: Entity Framework 設計工具的鍵盤快速鍵 EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 3c76cdd5-17c5-4c54-a6a5-cf21b974636b
ms.openlocfilehash: 70c9705956b58f4d00908dd9cca6ad0e0a078fc6
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997759"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Entity Framework 設計工具的鍵盤快速鍵
此頁面提供鍵盤 shorcuts Entity Framework Tools for Visual Studio 的各種畫面中所提供的清單。

## <a name="adonet-entity-data-model-wizard"></a>ADO.NET 實體資料模型精靈

### <a name="step-one-choose-model-contents"></a>第一步： 選擇模型內容

![WizardOne](~/ef6/media/wizardone.png)

| 快速鍵  | 動作                                                     | 注意                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                        | 不適用於所有選取的模型內容的項目。 |
| **Alt + f** | 完成精靈                                              | 不適用於所有選取的模型內容的項目。 |
| **Alt + w** | 焦點切換到 「 什麼模型應該包含？ 」 窗格。 |                                                     |

### <a name="step-two-choose-your-connection"></a>第二個步驟： 選擇您的連線

![WizardTwo](~/ef6/media/wizardtwo.png)

| 快速鍵  | 動作                                                     | 注意                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                        |                                                         |
| **Alt + p** | 移至前一個畫面                                    |                                                         |
| **Alt + w** | 焦點切換到 「 什麼模型應該包含？ 」 窗格。 |                                                         |
| **Alt + c** | 開啟 [連接屬性] 視窗                    | 可讓您在新的資料庫連接的定義。 |
| **Alt + e** | 從連接字串排除敏感性資料          |                                                         |
| **Alt + i** | 連接字串中包括敏感性資料            |                                                         |
| **Alt + s** | 切換 [儲存在 App.Config 中的連線設定] 選項 |                                                         |

### <a name="step-three-choose-your-version"></a>步驟三： 選擇您的版本

![WizardThree](~/ef6/media/wizardthree.png)

| 快速鍵  | 動作                                             | 注意                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                |                                                                                       |
| **Alt + p** | 移至前一個畫面                            |                                                                                       |
| **Alt + w** | Entity Framework 版本選取項目來切換焦點 | 用來指定使用不同版本的 Entity Framework 專案中。 |

### <a name="step-four-choose-your-database-objects-and-settings"></a>步驟四： 選擇您的資料庫物件和設定

![WizardFour](~/ef6/media/wizardfour.png)

| 快速鍵  | 動作                                                                                    | 注意                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | 完成精靈                                                                             |                                                                     |
| **Alt + p** | 移至前一個畫面                                                                   |                                                                     |
| **Alt + w** | 參數焦點設定至資料庫物件選取項目 窗格                                            | 允許指定資料庫物件，為反向工程。    |
| **Alt + s** | 切換 「 化或單數化產生的物件名稱"選項                       |                                                                     |
| **Alt + k** | 切換 [包含在模型中的外部索引鍵資料行] 選項                              | 不適用於所有選取的模型內容的項目。                 |
| **Alt + i** | 切換 [匯入選取的預存程序和函式到實體模型] 選項 | 不適用於所有選取的模型內容的項目。                 |
| **Alt + m** | 將焦點切換到 「 模型命名空間 」 文字欄位                                        | 不適用於所有選取的模型內容的項目。                 |
| **空格鍵** | 切換選取項目上                                                               | 如果項目具有子系，所有子項目會進行如此都切換 |
| **左邊**  | 摺疊子樹狀結構                                                                       |                                                                     |
| **右邊** | 展開子樹狀目錄                                                                         |                                                                     |
| **註冊**    | 瀏覽至樹狀目錄中的上一個項目                                                      |                                                                     |
| **向下**  | 瀏覽至樹狀目錄中的下一個項目                                                          |                                                                     |

## <a name="ef-designer-surface"></a>EF 設計工具介面

![DesignerSurface](~/ef6/media/designersurface.png)

| 快速鍵                                                                                | 動作                      | 注意                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **輸入空間 /**                                                                         | 切換選取項目            | 切換選取項目上具有焦點的物件。                                                                                                                                                                                         |
| **Esc**                                                                                 | 取消選取項目            | 取消目前的選取範圍。                                                                                                                                                                                                      |
| **Ctrl + A**                                                                            | 全選                  | 選取設計介面上的所有圖案。                                                                                                                                                                                       |
| **向上鍵**                                                                            | 上移                     | 將選取的實體組成一個格線。 <br/> 如果在清單中，移至前一個同層級子欄位。                                                                                                                            |
| **向下箭號**                                                                          | 下移                   | 將選取的下一個格線的實體。 <br/> 如果在清單中，移至下一個同層級子欄位。                                                                                                                              |
| **向左鍵**                                                                          | 左移                   | 將選取的實體左一個格線。 <br/> 如果在清單中，移至前一個同層級子欄位。                                                                                                                          |
| **向右鍵**                                                                         | 向右移動                  | 將選取的實體右移一個方格遞增值。 <br/> 如果在清單中，移至下一個同層級子欄位。                                                                                                                             |
| **Shift + 向的左鍵**                                                                  | 向左調整形狀大小             | 減少一個格線中選取的實體的寬度。                                                                                                                                                                     |
| **Shift + 向右箭號**                                                                 | 正確的大小圖形            | 增加一個格線中選取的實體的寬度。                                                                                                                                                                   |
| **Home**                                                                                | 第一個對等                  | 將焦點和選取範圍到相同的對等層級的設計介面上的第一個物件。                                                                                                                                         |
| **End**                                                                                 | 最後一個對等                   | 將焦點和選取範圍到相同的對等層級的設計介面上的最後一個物件。                                                                                                                                          |
| **Ctrl + Home**                                                                         | 第一個對等 （焦點）          | 與第一個對等，但移動焦點，而不需要移動焦點和選取範圍相同。                                                                                                                                                          |
| **Ctrl + End**                                                                          | 最後一個對等 （焦點）           | 與上次相同對等互連，但移動焦點，而不需要移動焦點和選取範圍。                                                                                                                                                           |
| **Tab**                                                                                 | 下一步 的對等                   | 將焦點和選取範圍到下一個物件，在相同的對等層級的設計介面上。                                                                                                                                          |
| **Shift+Tab**                                                                           | 先前的對等               | 將焦點和選取範圍到上一個物件相同的對等層級的設計介面上。                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | 下一步 的對等 （焦點）           | 下一步 相同對等互連，但移動焦點，而不需要移動焦點和選取範圍。                                                                                                                                                           |
| **Alt + Ctrl + Shift + Tab**                                                                  | 先前的對等 （焦點）       | 與先前的對等，但移動焦點，而不需要移動焦點和選取範圍相同。                                                                                                                                                       |
| **&lt;**                                                                                | 遞增                      | 移至設計介面高一個層級階層中的下一個物件。 如果沒有此階層中的形狀之上的圖形 （也就是該物件位於直接在設計介面上），則會選取圖表。 |
| **&gt;**                                                                                | 下降                     | 移至下一步 在設計介面下一個層級的階層架構中包含的物件。 如果不有任何包含的物件，這不是執行任何作業。                                                                              |
| **Ctrl + &lt;**                                                                         | 遞增 （焦點）              | 相同遞增命令，但不選取項目移動焦點。                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         | 下降 （焦點）             | 相同下降命令，但不選取項目移動焦點。                                                                                                                                                                         |
| **Shift + End**                                                                         | 請依照下列至連接         | 從實體中，移至這個實體連接到實體。                                                                                                                                                               |
| **Del**                                                                                 | 刪除                      | 從圖表中刪除的物件或連接器。                                                                                                                                                                                     |
| **集**                                                                                 | Insert                      | 選取 [純量屬性] 區間標頭或屬性本身時，則您可以將實體的新屬性。                                                                                                           |
| **Pg 設定**                                                                               | 向上捲動圖表           | 以累加單位即等於目前可見的設計介面的高度的 75%中捲動，在設計介面。                                                                                                                    |
| **向下的 Pg**                                                                             | 向下捲動圖表         | 向下捲動的設計介面。                                                                                                                                                                                                    |
| **Shift + 向下的 Pg**                                                                     | 右側的捲軸圖表        | 捲動到右邊的設計介面。                                                                                                                                                                                            |
| **Shift + 向上 Pg**                                                                       | 剩餘的捲軸圖表         | 捲動至左的設計介面。                                                                                                                                                                                             |
| **F2**                                                                                  | 進入編輯模式             | 標準的鍵盤快速鍵文字控制項進入編輯模式。                                                                                                                                                               |
| **Shift + F10**                                                                         | 顯示快顯功能表       | 顯示選取之項目的捷徑功能表的標準鍵盤快速鍵。                                                                                                                                                          |
| **按一下滑鼠控制 + Shift + 滑鼠左鍵**  <br/> **Control + Shift + 滑鼠滾輪向前**  | 中的語意縮放            | 放大滑鼠指標下方的 [圖表] 檢視的區域。                                                                                                                                                                 |
| **以滑鼠右鍵按一下控制項 + Shift + 滑鼠** <br/> **Control + Shift + 滑鼠滾輪向後** | 語意縮小           | 縮小圖表檢視，將滑鼠指標下方的區域。 它重新中心圖表，縮小時牽扯太廣用於目前的圖表中心。                                                                          |
| **控制 + Shift + '+'** <br/> **Control + 滑鼠滾輪向前**                        | 放大                     | 放大圖表檢視的中心。                                                                                                                                                                                         |
| **控制 + Shift + '-'** <br/> **Control + 滑鼠滾輪向後**                       | 縮小                    | 縮小圖表檢視的已按下的區域。 它重新中心圖表，縮小時牽扯太廣用於目前的圖表中心。                                                                                            |
| **控制 + Shift + 向下繪製滑鼠左的按鈕的矩形**                  | 縮放區域                   | 放大您所選取的區域上置中。 當您按住 Control + Shift 鍵時，您會看到游標變更為 放大鏡，可讓您定義要縮放的區域。                        |
| **操作功能表鍵 + 是 '**                                                              | 開啟 對應詳細資料視窗 | 開啟 編輯對應選取的實體對應詳細資料視窗                                                                                                                                                               |

## <a name="mapping-details-window"></a>對應詳細資料視窗

![MappingDetailsShortcuts](~/ef6/media/mappingdetailsshortcuts.png)

| 快速鍵                  | 動作         | 注意                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **Tab**                   | 將內容切換 | 主視窗區域和在左邊工具列模式間切換                                                                     |
| **方向鍵**            | 巡覽     | 增加或減少資料列，或向右鍵和向移動，跨資料行中的主視窗區域。 在左邊工具列中的按鈕之間移動。 |
| **Enter** <br/> **空格鍵** | 選用版         | 在左邊工具列中選取一個按鈕。                                                                                          |
| **Alt + 向下箭號**      | 開啟清單      | 下拉式清單中，如果已選取的資料格具有下拉式清單。                                                                     |
| **Enter**                 | 清單中，選取    | 在下拉式清單中選取的項目。                                                                                               |
| **Esc**                   | 關閉清單     | 關閉下拉式清單。                                                                                                              |

## <a name="visual-studio-navigation"></a>Visual Studio 導覽

Entity Framework 也提供數個可以有對應的自訂鍵盤快速鍵的動作 （任何捷徑會依預設對應）。 若要建立這些自訂的快速鍵，請按一下 工具 功能表、 選項。  在環境中，選擇 [鍵盤]。  向下捲動直到您可以選取想要的命令，快顯文字方塊中輸入 「 按快速鍵 」，然後按一下中間清單指派。 可能的快速鍵如下所示：

| 快速鍵                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Add.ComplexProperty.ComplexTypes**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddCodeGenerationItem**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddFunctionImport**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.AddEnumType**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.Association**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.ComplexProperty**                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.ComplexType**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.Entity**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.FunctionImport**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.Inheritance**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.NavigationProperty**               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.ScalarProperty**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNewDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddtoDiagram**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Close**                                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Collapse**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ConverttoEnum**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.CollapseAll**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.ExpandAll**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.ExportasImage**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.LayoutDiagram**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Edit**                                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.EntityKey**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Expand**                                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.FunctionImportMapping**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GenerateDatabasefromModel**               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GoToDefinition**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Grid.ShowGrid**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Grid.SnaptoGrid**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.IncludeRelated**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MappingDetails**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ModelBrowser**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveDiagramstoSeparateFile**              |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down5**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToBottom**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToTop**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up5**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MovetonewDiagram**                        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Open**                                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.MovetoNewComplexType**           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.Rename**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.RemovefromDiagram**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Rename**                                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayName**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.BaseType**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.Entity**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.Property**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.Subtype**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAll**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAssociation**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinModelBrowser**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.StoredProcedureMapping**                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.TableMapping**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.UpdateModelfromDatabase**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Validate**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.10**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.100**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.125**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.150**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.200**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.25**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.300**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.33**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.400**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.50**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.66**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.75**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.Custom**                             |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.ZoomIn**                             |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.ZoomOut**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.ZoomtoFit**                          |
| **View.EntityDataModelBrowser**                                                                |
| **View.EntityDataModelMappingDetails**                                                         |
