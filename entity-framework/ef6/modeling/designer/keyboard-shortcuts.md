---
title: Entity Framework Designer 鍵盤快速鍵-EF6
description: Entity Framework Designer Entity Framework 6 中的鍵盤快速鍵
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: 90589419a52a198747eadee740775e2761bc757c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064883"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Entity Framework Designer 鍵盤快速鍵
此頁面提供的鍵盤 shorcuts 清單，可用於 Visual Studio 之 Entity Framework Tools 的各種畫面。

## <a name="adonet-entity-data-model-wizard"></a>ADO.NET 實體資料模型 Wizard

### <a name="step-one-choose-model-contents"></a>步驟1：選擇模型內容

![Wizard One](~/ef6/media/wizardone.png)

| 快速鍵  | 動作                                                     | 備註                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                        | 不適用於所有選取的模型內容。 |
| **Alt + f** | 完成精靈                                              | 不適用於所有選取的模型內容。 |
| **Alt + w** | 將焦點切換至「模型應該包含什麼？」 窗格。 |                                                     |

### <a name="step-two-choose-your-connection"></a>步驟2：選擇您的連接

![Wizard 二](~/ef6/media/wizardtwo.png)

| 快速鍵  | 動作                                                     | 備註                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                        |                                                         |
| **Alt + p** | 移至上一個畫面                                    |                                                         |
| **Alt + w** | 將焦點切換至「模型應該包含什麼？」 窗格。 |                                                         |
| **Alt + c** | 開啟 [連接屬性] 視窗                    | 允許新資料庫連接的定義。 |
| **Alt + e** | 從連接字串排除敏感性資料          |                                                         |
| **Alt + i** | 在連接字串中包含敏感性資料            |                                                         |
| **Alt + s** | 切換 [在 App.Config 中儲存連接設定] 選項 |                                                         |

### <a name="step-three-choose-your-version"></a>步驟3：選擇您的版本

![Wizard 三](~/ef6/media/wizardthree.png)

| 快速鍵  | 動作                                             | 備註                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | 移至下一個畫面                                |                                                                                       |
| **Alt + p** | 移至上一個畫面                            |                                                                                       |
| **Alt + w** | 將焦點切換至 Entity Framework 版本選取專案 | 可指定要在專案中使用的不同 Entity Framework 版本。 |

### <a name="step-four-choose-your-database-objects-and-settings"></a>步驟四：選擇您的資料庫物件和設定

![Wizard 四](~/ef6/media/wizardfour.png)

| 快速鍵  | 動作                                                                                    | 備註                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | 完成精靈                                                                             |                                                                     |
| **Alt + p** | 移至上一個畫面                                                                   |                                                                     |
| **Alt + w** | 將焦點切換到資料庫物件選取窗格                                            | 允許指定要進行反向工程的資料庫物件。    |
| **Alt + s** | 切換 [複數化或單數化產生的物件名稱] 選項                       |                                                                     |
| **Alt + k** | 切換 [在模型中包含外鍵資料行] 選項                              | 不適用於所有選取的模型內容。                 |
| **Alt + i** | 切換 [將選取的預存程式和函式匯入實體模型] 選項 | 不適用於所有選取的模型內容。                 |
| **Alt + m** | 將焦點切換至 [模型命名空間] 文字欄位                                        | 不適用於所有選取的模型內容。                 |
| **空間** | 切換元素上的選取範圍                                                               | 如果元素有子系，則所有子項目也會切換 |
| **離開**  | 折迭子樹狀結構                                                                       |                                                                     |
| **對** | 展開子樹狀結構                                                                         |                                                                     |
| **Up**    | 流覽至樹狀結構中的上一個元素                                                      |                                                                     |
| **向下**  | 流覽至樹狀結構中的下一個元素                                                          |                                                                     |

## <a name="ef-designer-surface"></a>EF 設計工具介面

![設計師介面](~/ef6/media/designersurface.png)

| 快速鍵                                                                                | 動作                      | 備註                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **空格鍵/Enter 鍵**                                                                         | 切換選取範圍            | 切換具有焦點的物件上的選取範圍。                                                                                                                                                                                         |
| **Esc**                                                                                 | 取消選取專案            | 取消目前的選取範圍。                                                                                                                                                                                                      |
| **Ctrl + A**                                                                            | 全選                  | 選取設計介面上的所有圖形。                                                                                                                                                                                       |
| **向上箭頭**                                                                            | 上移                     | 將選取的實體向上移動一個方格增量。 <br/> 如果在清單中，則移至先前的同級子欄位。                                                                                                                            |
| **向下箭頭**                                                                          | 下移                   | 將選取的實體向下移動一個方格增量。 <br/> 如果在清單中，則會移至下一個同級欄位。                                                                                                                              |
| **向左箭號**                                                                          | 左移                   | 將選取的實體向左移動一個方格增量。 <br/> 如果在清單中，則移至先前的同級子欄位。                                                                                                                          |
| **向右箭號**                                                                         | 右移                  | 將選取的實體向右移動一個方格增量。 <br/> 如果在清單中，則會移至下一個同級欄位。                                                                                                                             |
| **Shift + 向左鍵**                                                                  | 左邊的大小圖形             | 將所選實體的寬度減少一個方格增量。                                                                                                                                                                     |
| **Shift + 向右鍵**                                                                 | 調整形狀右邊            | 將所選實體的寬度增加一個方格增量。                                                                                                                                                                   |
| **首頁**                                                                                | 第一個對等                  | 在相同的對等層級上，將焦點和選取範圍移至設計介面上的第一個物件。                                                                                                                                         |
| **結束**                                                                                 | 上次對等                   | 在相同的對等層級上，將焦點和選取範圍移至設計介面上的最後一個物件。                                                                                                                                          |
| **Ctrl + Home**                                                                         | 第一個對等 (焦點)           | 與第一個對等的相同，但會移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                          |
| **Ctrl + End**                                                                          | 上次對等 (焦點)            | 與最後一個對等的相同，但會移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                           |
| **索引標籤**                                                                                 | 下一個對等                   | 在相同的對等層級上，將焦點和選取範圍移至設計介面上的下一個物件。                                                                                                                                          |
| **Shift + Tab**                                                                           | 上一個對等               | 將焦點和選取範圍移至設計介面上相同對等層級上的上一個物件。                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | 下一個對等 (焦點)            | 與下一個對等的相同，但會移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                           |
| **Alt + Ctrl + Shift + Tab**                                                                  | 先前的對等 (焦點)        | 與先前的對等相同，但會移動焦點，而不是移動焦點和選取範圍。                                                                                                                                                       |
| **&lt;**                                                                                | 提升                      | 移至設計介面上較高層級中的下一個物件。 如果階層中的這個圖形上方沒有任何圖形 (也就是，物件直接放在設計介面) 上，則會選取圖表。 |
| **&gt;**                                                                                | 下降                     | 移至設計介面上的下一個包含的物件，在階層中的這個層級下一層級。 如果沒有包含物件，這就不會有任何作用。                                                                              |
| **Ctrl + &lt;**                                                                         | 遞增 (焦點)               | 與遞增的命令相同，但不選取則移動焦點。                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         | 下降 (焦點)              | 與下降的命令相同，但不選取則移動焦點。                                                                                                                                                                         |
| **Shift + End**                                                                         | 關注連接         | 從實體移至這個實體所連接的實體。                                                                                                                                                               |
| **戴爾**                                                                                 | 刪除                      | 從圖表中刪除物件或連接器。                                                                                                                                                                                     |
| **.Ins**                                                                                 | 插入                      | 當選取了 [純量屬性] 區間標題或屬性本身時，就會將新的屬性加入至實體。                                                                                                           |
| **Pg**                                                                               | 向上滾動圖表           | 將設計介面向上滾動，以相當於目前可見設計介面高度的75% 遞增。                                                                                                                    |
| **下一頁**                                                                             | 向下滾動圖表         | 向下滾動設計介面。                                                                                                                                                                                                    |
| **Shift + 下一頁**                                                                     | 向右滾動圖表        | 將設計介面向右滾動。                                                                                                                                                                                            |
| **Shift + 下一頁**                                                                       | 左側滾動圖         | 將設計介面向左滾動。                                                                                                                                                                                             |
| **F2**                                                                                  | 進入編輯模式             | 輸入文字控制項編輯模式的標準鍵盤快捷方式。                                                                                                                                                               |
| **Shift + F10**                                                                         | 顯示捷徑功能表       | 用來顯示所選項目的快捷方式功能表的標準鍵盤快捷方式。                                                                                                                                                          |
| **Ctrl + Shift + 滑鼠左鍵按一下**  <br/> **Ctrl + Shift + 滑鼠滾輪向前**  | 語義放大            | 在 [圖表] 視圖的區域下，放大滑鼠指標。                                                                                                                                                                 |
| **Ctrl + Shift + 滑鼠右鍵按一下** <br/> **Control + Shift + 滑鼠滾輪向後** | 語義縮小           | 從滑鼠指標下方的圖表視圖區域放大。 當您針對目前的圖表中心放大太遠時，它會將圖表重新置中。                                                                          |
| **Ctrl + Shift + ' + '** <br/> **Control + 滑鼠滾輪轉寄**                        | 放大                     | 放大圖表視圖的中央。                                                                                                                                                                                         |
| **Control + Shift + '-'** <br/> **Control + 滑鼠滾輪向後**                       | 縮小                    | 從圖表視圖的點擊區域放大。 當您針對目前的圖表中心放大太遠時，它會將圖表重新置中。                                                                                            |
| **Ctrl + Shift + 繪製具有滑鼠左鍵的矩形**                  | 縮放區域                   | 放大您所選取區域的中央。 當您按住 Ctrl + Shift 鍵時，會看到游標變成放大鏡，可讓您定義要放大的區域。                        |
| **內容功能表鍵 + 是 '**                                                              | 開啟對應詳細資料視窗 | 開啟 [對應詳細資料] 視窗，以編輯所選取實體的對應                                                                                                                                                               |

## <a name="mapping-details-window"></a>對應詳細資料視窗

![對應詳細資料快速鍵](~/ef6/media/mappingdetailsshortcuts.png)

| 快速鍵                  | 動作         | 備註                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **索引標籤**                   | 切換內容 | 在主視窗區域和左邊的工具列之間切換                                                                     |
| **方向鍵**            | 瀏覽     | 在主視窗區域中的資料行上移和下移資料列，或靠右和向左移動。 在左側工具列中的按鈕之間移動。 |
| **進入** <br/> **空間** | 選取         | 選取左側工具列中的按鈕。                                                                                          |
| **Alt + 向下鍵**      | 開啟清單      | 如果已選取具有下拉式清單的資料格，則下拉式清單。                                                                     |
| **進入**                 | 清單選取    | 選取下拉式清單中的元素。                                                                                               |
| **Esc**                   | 列出關閉     | 關閉下拉式清單。                                                                                                              |

## <a name="visual-studio-navigation"></a>Visual Studio 導覽

Entity Framework 也會提供一些可對應自訂鍵盤快速鍵的動作 (預設) 不會對應任何快捷方式。 若要建立這些自訂快速鍵，請按一下 [工具] 功能表，然後按一下 [選項]。選擇 [環境] 下的 [鍵盤]。在中間向下移動清單，直到您可以選取所需的命令，在 [按快速鍵] 文字方塊中輸入快捷方式，然後按一下 [指派]。 可能的快速鍵如下所示：

| 快速鍵                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. ComplexProperty. ComplexTypes**        |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.AddCodeGenerationItem**                   |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.AddFunctionImport**                       |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext AddEnumType**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext ComplexProperty**                  |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 實體**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. FunctionImport**                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 繼承**                      |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. NavigationProperty**               |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext ScalarProperty**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNewDiagram**                           |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.AddtoDiagram**                            |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 關閉**                                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Collapse**                                |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ConverttoEnum**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 全部折迭**                     |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 全部展開**                       |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. ExportasImage**                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. LayoutDiagram**                   |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 編輯**                                    |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext EntityKey**                               |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Expand**                                  |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. FunctionImportMapping**                   |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.GenerateDatabasefromModel**               |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.GoToDefinition**                          |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. ShowGrid**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext**                         |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.IncludeRelated**                          |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MappingDetails**                          |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ModelBrowser**                            |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MoveDiagramstoSeparateFile**              |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down5**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToBottom**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToTop**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up5**                      |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.MovetonewDiagram**                        |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext。開啟**                                    |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. MovetoNewComplexType**           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.Rename**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.RemovefromDiagram**                       |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext。重新命名**                                  |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. ScalarPropertyFormat. DisplayName**        |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext，請選取 BaseType**                         |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Select. Entity**                           |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Property**                         |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext，請選取子類型**                          |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. SelectAll**                               |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.SelectAssociation**                       |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ShowinDiagram**                           |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.ShowinModelBrowser**                      |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.StoredProcedureMapping**                  |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. TableMapping**                            |
| **OtherCoNtextMenus.MicrosoftDataEntityDesignCoNtext.UpdateModelfromDatabase**                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Validate**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 10**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 100**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 125**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 150**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 放大200**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Zoom**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 300**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext.-33**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 400**                                |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 50**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext * 66**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. 75**                                 |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext。自訂**                             |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Vemap.zoomin**                             |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. Vemap.zoomout**                            |
| **OtherCoNtextMenus. MicrosoftDataEntityDesignCoNtext. ZoomtoFit**                          |
| **View.EntityDataModelBrowser**                                                                |
| **View.EntityDataModelMappingDetails**                                                         |
