---
title: EF4、EF5 和 EF6 的性能注意事項 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434335"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>EF 4、5 和 6 的性能注意事項
由大衛·奧班多,埃裡克·丁格和其他人

發佈時間:2012 年 4 月

上次更新時間:2014 年 5 月

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. 簡介

對象關係映射框架是一種為面向物件的應用程序中的數據訪問提供抽象的便捷方法。 對於 .NET 應用程式,Microsoft 推薦的 O/RM 是實體框架。 然而,對於任何抽象,性能都可能成為一個問題。

編寫本白皮書是為了顯示使用實體框架開發應用程式時的性能注意事項,讓開發人員瞭解可能影響性能的實體框架內部演演演算法,並提供調查和改進使用實體框架的應用程式的性能的提示。 Web 上已有許多有關性能的好主題,我們還嘗試盡可能指向這些資源。

性能是一個棘手的話題。 本白皮書旨在作為資源,説明您為使用實體框架的應用程式做出與性能相關的決策。 我們包括一些測試指標來演示性能,但這些指標並非旨在作為您在應用程式中看到的性能的絕對指標。

出於實際目的,本文檔假定實體框架 4 在 .NET 4.0 下運行,實體框架 5 和 6 在 .NET 4.5 下運行。 實體框架 5 的許多性能改進都位於提供 .NET 4.5 的核心元件中。

實體框架 6 是帶外版本,不依賴於隨 .NET 一起發貨的實體框架元件。 實體框架 6 在 .NET 4.0 和 .NET 4.5 上都工作,可以為尚未從 .NET 4.0 升級但希望在其應用程式中使用最新實體框架位的使用者提供巨大的性能優勢。 當本文檔提到實體框架 6 時,它指的是本文編寫時可用的最新版本:版本 6.1.0。

## <a name="2-cold-vs-warm-query-execution"></a>2. 冷與暖查詢執行

首次對給定模型進行任何查詢時,實體框架會在幕後執行大量工作來載入和驗證模型。 我們經常將第一個查詢稱為"冷"查詢。針對已載入模型的進一步查詢稱為「暖」查詢,並且速度更快。

讓我們從高級檢視來瞭解使用實體框架執行查詢時所花費的時間,並瞭解實體框架 6 中情況正在改善。

**第一次查詢執行 = 冷查詢**

| 代碼使用者寫入                                                                                     | 動作                    | EF4 效能影響                                                                                                                                                                                                                                                                                                                                                                                                        | EF5 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                    | EF6 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 內容建立          | 中                                                                                                                                                                                                                                                                                                                                                                                                                        | 中                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查詢運算式建立 | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | LINQ 查詢執行      | - 中繼資料載入:高但快取 <br/> - 檢視產生:可能非常高但快取 <br/> - 參數評估:中等 <br/> - 查詢翻譯:中等 <br/> - 具體產生:中等但快取 <br/> - 資料庫查詢執行:可能很高 <br/> • 連接。開啟 <br/> • 命令.執行讀取器 <br/> • 資料閱讀器。閱讀 <br/> 物件具體化:中等 <br/> - 身份尋找:中等 | - 中繼資料載入:高但快取 <br/> - 檢視產生:可能非常高但快取 <br/> - 參數評估:低 <br/> - 查詢翻譯:中等但快取 <br/> - 具體產生:中等但快取 <br/> - 資料庫查詢執行:可能很高(在某些情況下查詢更好) <br/> • 連接。開啟 <br/> • 命令.執行讀取器 <br/> • 資料閱讀器。閱讀 <br/> 物件具體化:中等 <br/> - 身份尋找:中等 | - 中繼資料載入:高但快取 <br/> - 檢視產生:中等但快取 <br/> - 參數評估:低 <br/> - 查詢翻譯:中等但快取 <br/> - 具體產生:中等但快取 <br/> - 資料庫查詢執行:可能很高(在某些情況下查詢更好) <br/> • 連接。開啟 <br/> • 命令.執行讀取器 <br/> • 資料閱讀器。閱讀 <br/> 物件具體化:中等(快於 EF5) <br/> - 身份尋找:中等 |
| `}`                                                                                                  | 連線.關閉          | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**第二次查詢執行 = 暖查詢**

| 代碼使用者寫入                                                                                     | 動作                    | EF4 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | EF5 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | EF6 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 內容建立          | 中                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | 中                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查詢運算式建立 | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | LINQ 查詢執行      | - 中~~繼資料~~~~High but cached~~ <br/> - 查看~~產生~~搜尋:~~可能非常高但快取~~低 <br/> - 參數評估:中等 <br/> - 查詢~~翻譯~~搜尋:中等 <br/> - 具體~~產生搜尋~~:~~中等但快取~~低 <br/> - 資料庫查詢執行:可能很高 <br/> • 連接。開啟 <br/> • 命令.執行讀取器 <br/> • 資料閱讀器。閱讀 <br/> 物件具體化:中等 <br/> - 身份尋找:中等 | - 中~~繼資料~~~~High but cached~~ <br/> - 查看~~產生~~搜尋:~~可能非常高但快取~~低 <br/> - 參數評估:低 <br/> - 查詢~~翻譯~~搜尋:~~中但快取~~低 <br/> - 具體~~產生搜尋~~:~~中等但快取~~低 <br/> - 資料庫查詢執行:可能很高(在某些情況下查詢更好) <br/> • 連接。開啟 <br/> • 命令.執行讀取器 <br/> • 資料閱讀器。閱讀 <br/> 物件具體化:中等 <br/> - 身份尋找:中等 | - 中~~繼資料~~~~High but cached~~ <br/> - 查看~~產生~~搜尋:~~中等但快取~~低 <br/> - 參數評估:低 <br/> - 查詢~~翻譯~~搜尋:~~中但快取~~低 <br/> - 具體~~產生搜尋~~:~~中等但快取~~低 <br/> - 資料庫查詢執行:可能很高(在某些情況下查詢更好) <br/> • 連接。開啟 <br/> • 命令.執行讀取器 <br/> • 資料閱讀器。閱讀 <br/> 物件具體化:中等(快於 EF5) <br/> - 身份尋找:中等 |
| `}`                                                                                                  | 連線.關閉          | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


有幾種方法可以降低冷查詢和暖查詢的性能成本,我們將在下一節中介紹這些查詢。 具體來說,我們將考慮通過使用預生成的視圖來降低冷查詢中模型載入的成本,這將有助於緩解在生成視圖期間遇到的性能難題。 對於暖查詢,我們將介紹查詢計劃緩存、無跟蹤查詢和不同的查詢執行選項。

### <a name="21-what-is-view-generation"></a>2.1 什麼是視圖生成?

為了瞭解什麼是視圖生成,我們必須首先了解什麼是"映射視圖"。 映射檢視是映射中為每個實體集和關聯指定的轉換的可執行表示形式。 在內部,這些映射檢視採用 CQT(規範查詢樹)的形狀。 有兩種類型的映射檢視:

-   查詢檢視:這些檢視表示從資料庫架構到概念模型所需的轉換。
-   更新檢視:這些檢視表示從概念模型到資料庫架構所需的轉換。

請記住,概念模型可能以各種方式與資料庫架構不同。 例如,一個表可用於存儲兩種不同實體類型的數據。 繼承和非常規映射在映射視圖的複雜性中起著一定的作用。

基於映射規範計算這些檢視的過程就是我們所說的檢視生成。 視圖生成可以在載入模型時動態進行,也可以在生成時使用「預生成的檢視」進行動態生成;後者以實體 SQL 語句的形式序列化\#到 C 或 VB 檔。

生成視圖時,也會驗證視圖。 從性能角度來看,視圖生成成本的絕大多數實際上是對視圖的驗證,這可確保實體之間的連接有意義,並且對所有支援的操作具有正確的基數。

執行對實體集的查詢時,查詢將與相應的查詢檢視組合,並且此合成的結果通過計劃編譯器運行,以創建備份存儲可以理解的查詢的表示形式。 對於 SQL 伺服器,此編譯的最終結果將是 T-SQL SELECT 語句。 首次對實體集執行更新時,更新視圖將通過類似的過程運行,以將其轉換為目標資料庫的 DML 語句。

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 影響檢視產生性能的因素

視圖生成步驟的性能不僅取決於模型的大小,還取決於模型的互連程度。 如果兩個實體通過繼承鏈或關聯連接,則它們表示已連接。 同樣,如果兩個表通過外鍵連接,則它們被連接。 隨著架構中連接的實體和表數量的增加,視圖生成成本也會增加。

在最壞的情況下,我們用於生成和驗證視圖的演演演算法呈指數級,儘管我們使用一些優化來改進這一點。 似乎對性能產生負面影響的最大因素是:

-   模型大小,指實體的數量和這些實體之間的關聯量。
-   模型複雜性,特別是涉及大量類型的繼承。
-   使用獨立關聯,而不是外國金鑰關聯。

對於小型、簡單的模型,成本可能足夠小,無需使用預生成的視圖。 隨著模型大小和複雜性的增加,有多種選項可用於降低檢視生成和驗證的成本。

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 使用預生成的檢視減少模型載入時間

有關如何在實體框架 6 上使用預生成的檢視的詳細資訊,請造[訪 預生成的映射檢視](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 使用實體框架電動工具社區版的預生成檢視

您可以使用[實體框架 6 電動工具社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)生成 EDMX 和代碼優先模型的檢視,通過右鍵單擊模型類檔並使用實體框架功能表選擇「生成檢視」。。 實體框架電動工具社區版僅適用於 DbContext 派生的上下文。

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 如何使用預生成的檢視與 EDMGen 建立的模型

EDMGen 是一個實用程式,它與 .NET 一起提供,並與實體框架 4 和 5 一起工作,但與實體框架 6 無關。 EDMGen 允許您從命令列生成模型檔、物件層和檢視。 其中一個輸出是您選擇的語言的「檢視」 檔,VB 或\#C 。 這是一個代碼檔,其中包含每個實體集的實體 SQL 代碼段。 要啟用預生成的檢視,只需將檔包含在專案中即可。

如果手動編輯模型的架構檔,則需要重新生成檢視檔。 可以通過使用 **/mode:ViewGeneration**標誌執行 EDMGen 來執行此操作。

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 如何使用預生成的檢視與 EDMX 檔案

您還可以使用 EDMGen 為 EDMX 檔產生檢視 - 前面引用的 MSDN 主題介紹如何添加預建建事件來執行此操作 - 但這樣做很複雜,在某些情況下,這是不可能的。 當模型位於 edmx 檔中時,通常使用 T4 範本生成檢視會更容易。

ADO.NET團隊部落格有一篇文章,描述如何使用 T4 範本生成\<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)檢視 (。 此帖子包含一個範本,可以下載並添加到您的專案中。 範本是為實體框架的第一個版本編寫的,因此不能保證它們能夠與最新版本的實體框架配合使用。 但是,可以從可視化工作室庫下載實體框架 4 和 5 的最新視圖生成範本集:

-   VB.NET:\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#:\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

如果您使用的是實體框架 6,則可以從\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>中的 可視化工作室庫獲取檢視生成 T4 範本。

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 降低檢視產生成本

使用預生成的檢視可將檢視生成成本從模型載入(運行時間)移動到設計時間。 雖然這提高了運行時的啟動性能,但在開發時,您仍將經歷視圖生成的痛苦。 還有其他幾種技巧可以説明降低在編譯時間和運行時生成檢視的成本。

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 使用外鍵關聯降低檢視生成成本

我們看到許多案例,將模型中的關聯從獨立協會切換到外國密鑰關聯,從而大大縮短了在視圖生成中花費的時間。

為了證明這一改進,我們使用 EDMGen 生成了兩個版本的 Navision 模型。 *注意:有關 Navision 模型的說明,請參閱附錄 C。* Navision 模型對本練習非常感興趣,因為它擁有大量實體和它們之間的關係。

此非常大的模型的一個版本是使用外鍵關聯生成的,另一個版本是使用獨立關聯生成的。 然後,我們安排為每個模型生成視圖需要多長時間。 實體框架 5 測試使用類 EntityViewGenerator 的 GenerateViews() 方法產生檢視,而實體框架 6 測試使用類儲存映射專案集合中的 GenerateViews() 方法。 這是因為實體框架 6 代碼庫中的代碼重組。

使用實體框架 5,使用外鍵生成模型的視圖在實驗室機器中花了 65 分鐘。 不知道為使用獨立關聯的模型生成檢視需要多長時間。 在實驗室中重新啟動計算機以安裝每月更新之前,我們讓測試運行了一個多月。

使用實體框架 6,使用外鍵生成模型的檢視生成需要 28 秒在同一實驗室機器中。 使用獨立關聯的模型的檢視生成需要 58 秒。 實體框架 6 對其檢視生成代碼所做的改進意味著許多專案不需要預生成的檢視來獲取更快的啟動時間。

請務必指出,實體框架 4 和 5 中的預生成檢視可以使用 EDMGen 或實體框架電動工具完成。 對於實體框架 6 視圖生成,可以通過實體框架電源工具完成,也可以以程式設計方式完成,如[預生成的映射視圖](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 如何使用外鍵而不是獨立關聯

在 Visual Studio 中使用 EDMGen 或實體設計器時,預設情況下,您將獲得 PK,並且只需單個複選框或命令列標誌即可在 SDK 和 I 之間切換。

如果您有一個大型 Code First 模型,則使用獨立關聯對檢視生成的影響相同。 可以通過將外鍵屬性包括在從屬物件的類中來避免這種影響,儘管一些開發人員會認為這會污染其物件模型。 您可以在 中\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>找到 有關此主題的詳細資訊。

| 使用時      | 執行方式                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Entity Designer | 在兩個實體之間添加關聯后,請確保具有引用約束。 引用約束告訴實體框架使用外鍵而不是獨立關聯。 有關其他詳細資訊,\<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>請存取 。 |
| EDMGen          | 當使用 EDMGen 從資料庫生成檔時,您的外鍵將受到尊重並添加到模型中。 有關 EDMGen 公開的不同選項的更多資訊,[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)請造訪 。                           |
| Code First      | 有關在使用代碼優先時如何將外鍵屬性包含在從屬物件上的資訊,請參閱[代碼優先約定](~/ef6/modeling/code-first/conventions/built-in.md)主題的「關係約定」 部分。                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 將模型移動到單獨的裝配體

當模型直接包含在應用程式的專案中,並且通過預生成事件或 T4 範本生成檢視時,每當重建專案時,即使模型未更改,也會進行檢視生成和驗證。 如果將模型移動到單獨的程式集並從應用程式的專案中引用它,則可以對應用程式進行其他更改,而無需重新生成包含模型的專案。

*注意:* 將模型移動到單獨的程式集時,請記住將模型的連接字串複製到用戶端專案的應用程式配置檔中。

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 停用基於 edmx 的模型驗證

EDMX 模型在編譯時進行驗證,即使模型保持不變。 如果模型已過驗證,則可以通過在屬性視窗中將"在生成上驗證"屬性設置為 false 來禁止編譯時驗證。 更改映射或模型時,可以臨時重新啟用驗證以驗證更改。

請注意,實體框架 6 的實體框架設計器的性能得到了提高," 在生成時驗證" 的成本比設計器的早期版本要低得多。

## <a name="3-caching-in-the-entity-framework"></a>3 實體框架中快取

實體框架具有以下形式的快取記憶體:

1.  物件快取 - 內置到 ObjectContext 實體中的 Object StateManager 在使用該實例檢索的物件的記憶體中追蹤該物件。 這也稱為第一級緩存。
2.  查詢計劃快取 - 多次執行查詢時重用生成的儲存命令。
3.  中繼資料快取 - 跨與同一模型的不同連接共用模型的元資料。

除了 EF 開箱即用的緩存外,一種稱為包裝提供程式的特殊 ADO.NET 數據提供者還可用於擴展實體框架,並緩存從資料庫檢索的結果(也稱為二級緩存)。

### <a name="31-object-caching"></a>3.1 物件快取

預設情況下,當在查詢結果中返回實體時,就在 EF 實現該實體之前,ObjectContext 將檢查具有相同金鑰的實體是否已載入到其 Object StateManager 中。 如果具有相同金鑰的實體已存在,EF 將在查詢結果中包括該密鑰。 儘管 EF 仍將針對資料庫發出查詢,但此行為可以繞過實體多次實現的大部分成本。

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 使用 DbContext 尋找從物件快取實體

與常規查詢不同,DbSet 中的 Find 方法(在 EF 4.1 中首次包含 API)將在記憶體中執行搜索,甚至針對資料庫發出查詢。 請務必注意,兩個不同的 ObjectObjectContext 實例將具有兩個不同的 Object StateManager 實例,這意味著它們具有單獨的物件緩存。

Find 使用主鍵值嘗試查找由上下文跟蹤的實體。 如果實體不在上下文中,則將針對資料庫執行和評估查詢,如果在上下文中或資料庫中找不到該實體,則返回 null。 請注意,Find 還會返回已添加到上下文但尚未保存到資料庫的實體。

使用 Find 時需要考慮性能。 默認情況下,調用此方法將觸發物件緩存的驗證,以檢測仍掛起的提交到資料庫的更改。 如果物件緩存或大型物件圖形中有大量物件添加到物件緩存中,此過程可能非常昂貴,但也可以禁用。 在某些情況下,在禁用自動檢測更改時,在調用 Find 方法時,您可能會感覺到在調用 Find 方法時,差異程度超過一個數量級。 但是,當對象實際位於緩存中時,以及必須從資料庫中檢索物件時,會感知到第二個數量級。 下面是一個範例圖,使用我們的一些微基準(以毫秒表示)進行測量,負載為 5000 個實體:

![.NET 4.5 對數刻度](~/ef6/media/net45logscale.png ".NET 4.5 - 對數刻度")

關閉自動偵測變更的尋找範例:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

使用 Find 方法時必須考慮的是:

1.  如果物件不在緩存中,則 Find 的好處將被否定,但語法仍比按鍵查詢更簡單。
2.  如果啟用自動檢測更改,Find 方法的成本可能會增加一個數量級,甚至更多取決於模型的複雜性和物件緩存中的實體數量。

此外,請記住,Find 僅返回要查找的實體,如果關聯實體尚未在物件緩存中,則不會自動載入它們。 如果需要檢索關聯的實體,可以使用按鍵進行查詢,並進行熱切載入。 有關詳細資訊,請參閱**8.1 延遲載入與熱載入**。

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 當物件緩存具有多個實體時的性能問題

物件緩存有助於提高實體框架的總體回應能力。 但是,當物件緩存載入的實體量非常大時,可能會影響某些操作,如添加、刪除、查找、輸入、保存更改等。 特別是,觸發調用檢測更改的操作將受到非常大的物件緩存的負面影響。 檢測更改將物件圖形與物件狀態管理器同步,其性能將直接由物件圖形的大小決定。 有關偵測變更的詳細資訊,請參考[追蹤 POCO 實體中的變更](https://msdn.microsoft.com/library/dd456848.aspx)。

使用實體框架 6 時,開發人員可以直接在 DbSet 上調用 AddRange 和 RemoveRange,而不是在集合上反覆運算,並按實例調用一次 Add。 使用範圍方法的優點是,檢測更改的成本僅針對整個實體集支付一次,而不是每個添加的實體支付一次。

### <a name="32-query-plan-caching"></a>3.2 查詢排程快取

第一次執行查詢時,它會通過內部計劃編譯器將概念查詢轉換為儲存命令(例如,在對 SQL Server 運行時執行的 T-SQL)。如果啟用了查詢計劃緩存,則下次執行查詢時,將直接從查詢計劃緩存中檢索存儲命令以執行,繞過計劃編譯器。

查詢計劃緩存在同一 AppDomain 中的 ObjectContext 實例之間共用。 您無需保留 ObjectContext 實例即可從查詢計劃快取中獲益。

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 關於查詢計劃緩存的一些註釋

-   查詢計劃緩存為所有查詢類型共用:實體 SQL、LINQ 到實體和編譯查詢物件。
-   默認情況下,為實體 SQL 查詢啟用查詢計劃快取,無論是透過實體命令還是透過物件查詢執行。 默認情況下,在 .NET 4.5 上的實體框架中,在實體框架中,在實體框架中,LINQ 對實體查詢也啟用了它,
    -   通過將啟用計劃緩存屬性(在實體命令或物件查詢上)設置為 false,可以禁用查詢計劃緩存。 例如：
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   對於參數化查詢,更改參數的值仍將命中緩存的查詢。 但是,更改參數的分面(例如,大小、精度或比例)將擊中緩存中的不同條目。
-   使用實體 SQL 時,查詢字串是密鑰的一部分。 更改查詢將會導致不同的緩存條目,即使查詢在功能上等效。 這包括對套管或空白的更改。
-   使用 LINQ 時,將處理查詢以生成密鑰的一部分。 因此,更改 LINQ 表達式將生成不同的鍵。
-   可能適用其他技術限制;有關詳細資訊,請參閱自動編譯查詢。

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 緩存逐出演演算法

瞭解內部演演演算法的工作原理將説明您確定何時啟用或禁用查詢計劃緩存。 清理演演算法如下:

1.  一旦緩存包含一組條目數 (800),我們將啟動一個計時器,定期(每分鐘一次)掃描緩存。
2.  在快取掃描期間,條目會以 LFRU(最不頻繁和最近使用)為基礎從緩存中刪除。 此演演演算法在決定彈出哪些條目時,會考慮命中計數和年齡。
3.  在每個緩存掃描結束時,緩存再次包含 800 個條目。

在確定要驅逐的條目時,所有緩存條目都一視同仁。 這意味著編譯查詢的儲存命令與實體 SQL 查詢的儲存命令具有相同的逐出機會。

請注意,緩存驅逐計時器在緩存中有 800 個實體時被踢中,但緩存僅在啟動此計時器 60 秒後被掃描。 這意味著,最多 60 秒,您的緩存可能會增長到相當大。

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 展示查詢計劃快取效能的測試指標

為了演示查詢計劃緩存對應用程式性能的影響,我們執行了一個測試,其中針對 Navision 模型執行了許多實體 SQL 查詢。 有關 Navision 模型的說明以及已執行的查詢類型,請參閱附錄。 在此測試中,我們首先反覆運算查詢清單,並執行每個查詢一次以將其添加到緩存(如果啟用了緩存)。 此步驟是非時無刻的。 接下來,我們將主線程休眠超過 60 秒,以便進行緩存掃描;最後,我們第二次遍通清單以執行緩存的查詢。 此外,在執行每組查詢之前刷新 SQL Server 計劃緩存,以便我們獲取的時間準確反映查詢計劃緩存提供的好處。

##### <a name="3231-test-results"></a>3.2.3.1 測試結果

| 測試                                                                   | EF5 無快取 | EF5 快取 | EF6 無快取 | EF6 快取 |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| 列舉所有 18723 個查詢                                          | 124          | 125.4      | 124.3        | 125.3      |
| 避免掃描(僅前 800 個查詢,無論複雜性如何)  | 41.7         | 5.5        | 40.5         | 5.4        |
| 只聚合子的檢查(總計 178 個 - 避免掃描 ) | 39.5         | 4.5        | 38.1         | 4.6        |

*所有時間(以秒為單位)。*

道德 - 在執行大量不同的查詢(例如,動態創建的查詢)時,緩存不起作用,並且生成的緩存刷新可以使從計劃緩存中從實際使用中受益最大的查詢保持。

聚合子總計查詢是測試的查詢中最複雜的。 正如預期的那樣,查詢越複雜,您從查詢計劃緩存中看到的好處就越大。

由於編譯查詢實際上是一個 LINQ 查詢,其計劃緩存,因此編譯查詢與等效實體 SQL 查詢的比較應具有類似的結果。 事實上,如果應用具有大量動態實體 SQL 查詢,則使用查詢填充緩存也會有效地導致編譯查詢在從緩存中刷新時"去編譯」。。 在這種情況下,可以通過禁用動態查詢的緩存來確定編譯查詢的優先順序,從而提高性能。 當然,更好的是重寫應用以使用參數化查詢而不是動態查詢。

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 使用編譯查詢提高 LINQ 查詢的效能

我們的測試表明,使用編譯查詢可以帶來 7% 的好處比自動編譯的 LINQ 查詢;這意味著您將在實體框架堆疊中執行代碼的時間減少7%;這並不意味著您的應用程式將加快 7%。 一般來說,在 EF 5.0 中寫入和維護編譯查詢物件的成本與收益相比可能不值得麻煩。 您的里程可能會有所不同,因此,如果您的專案需要額外的推送,請執行此選項。 請注意,編譯查詢僅與物件上下文派生模型相容,並且與 DbContext 派生的模型不相容。

有關創建和調用編譯查詢的詳細資訊,請參閱[編譯查詢(LINQ 到實體)。](https://msdn.microsoft.com/library/bb896297.aspx)

使用 Compilequery 時,您需要考慮兩個注意事項,即使用靜態實例的要求及其可組合性問題。 下面對這兩個注意事項進行了深入解釋。

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 使用靜態編譯查詢實例

由於編譯 LINQ 查詢是一個耗時的過程,因此我們不希望每次需要從資料庫獲取數據時都這樣做。 編譯查詢實例允許您編譯一次並運行多次,但您必須小心並採購,以便每次都重複使用同一個編譯查詢實例,而不是反覆編譯它。 使用靜態成員來存儲編譯查詢實例是必要的;否則,您不會看到任何好處。

例如,假設您的頁面具有以下方法正文來處理顯示所選類別的產品:

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

在這種情況下,每次調用方法時,您都會動態創建新的編譯查詢實例。 CompiledQuery 不會通過從查詢計劃緩存中檢索儲存命令來看到性能優勢,而是每次創建新實例時都會通過計劃編譯器。 事實上,每次調用該方法時,您都會使用新的編譯查詢條目來污染查詢計劃緩存。

相反,您希望創建已編譯查詢的靜態實例,以便每次調用方法時都調用相同的已編譯查詢。 這樣做的一種方法是添加編譯查詢實例作為物件上下文的成員。然後,您可以通過幫助器方法訪問編譯查詢,使事情變得更乾淨一些:

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

此說明器方法將呼叫如下:

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 通過編譯查詢進行群組

通過任何 LINQ 查詢進行撰寫的能力非常有用;為此,只需在*IQuery()* 或*Count()* 之後調用方法。 但是,這樣做實質上會返回一個新的可查詢物件。 雖然在技術上沒有什麼可以阻止您通過 CompiledQuery 進行創作,但這樣做將導致生成需要再次通過計畫編譯器的新 IQuery 物件。

某些元件將使用組合的 IQuery 對象來實現進階功能。 例如,ASP。NET 的 GridView 可以透過 SelectMethod 屬性將資料綁定到 IQuery 物件。 然後,GridView 將在此可查詢物件上進行組合,以允許對數據模型進行排序和分頁。 如您所見,使用 GridView 的編譯查詢不會命中已編譯的查詢,而是生成新的自動編譯查詢。

在一個可能遇到這種情況的地方是向查詢添加累進篩選器時。 例如,假設您有一個客戶頁面,該頁面包含多個可選篩選器的下拉清單(例如,國家/地區訂單和訂單計數)。 您可以在編譯查詢的 IQuery 可查詢結果上編寫這些篩選器,但這樣做將導致每次執行計畫編譯器時新查詢都會經過計畫編譯器。

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 為了避免此重新編譯,您可以重寫編譯查詢,以考慮可能的篩選器:

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

將在 UI 中呼叫,例如:

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 此處的權衡是生成的儲存命令將始終具有帶有空檢查的篩選器,但對於資料庫伺服器來說,優化這些篩選器應該相當簡單:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 中繼資料快取

實體框架還支援元數據緩存。 這實質上是跨不同連接到同一模型的類型資訊和類型到資料庫映射資訊。 元數據緩存對於每個 AppDomain 是唯一的。

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 中繼資料快取演算法

1.  模型的元數據資訊存儲在每個實體連接的專案集合中。
    -   作為旁注,模型的不同部分有不同的 ItemCollection 物件。 例如,StoreItem集合包含有關資料庫模型的資訊;因此,它包含有關資料庫模型的資訊。ObjectItemCollection 包含有關資料模型的資訊;EdmItemCollection 包含有關概念模型的資訊。

2.  如果兩個連接使用相同的連接字串,它們將共用相同的 ItemCollection 實例。
3.  功能上等效,但文本上不同的連接字串可能會導致不同的元數據緩存。 我們使用權杖化連接字串,因此只需更改權杖的順序即可生成共用元資料。 但是,在標記化后,兩個在功能上看起來相同的連接字串可能不會被計算為相同。
4.  定期檢查專案集合以表示使用。 如果確定最近未訪問工作區,則將在下一次緩存掃描時將其標記為清理。
5.  僅僅創建實體連接就會導致創建元數據緩存(儘管在打開連接之前不會初始化其中的項集合)。 此工作區將保留在記憶體中,直到緩存演演算法確定它不"正在使用"。

客戶諮詢團隊撰寫了一篇部落格文章,其中描述了對 ItemCollection 的引用,以避免在使用大型模型時「\<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>棄用」: 。

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 元數據緩存和查詢計劃快取之間的關係

查詢計劃緩存實例位於元數據工作區的存儲類型的專案集合中。 這意味著快取儲存命令將用於查詢使用給定元資料工作區實例化的任何上下文。 這也意味著,如果您有兩個連接字串略有不同,在標記后不匹配,則將具有不同的查詢計劃緩存實例。

### <a name="35-results-caching"></a>3.5 結果快取

使用結果快取(也稱為"第二級緩存"),您將查詢的結果保存在本地緩存中。 發出查詢時,首先在查詢存儲之前,先查看結果是否在本地可用。 雖然實體框架並不直接支持結果緩存,但可以使用包裝提供程式添加第二級緩存。 以第二級快取的包裝提供者的範例是 Alachisoft[基於 NCache 的實體框架第二級緩存](https://www.alachisoft.com/ncache/entity-framework.html)。

二級緩存的實現是在 LINQ 運算運算(和 fee)並從第一級緩存中計算或檢索查詢執行計畫之後發生的注入功能。 然後,第二級緩存將僅存儲原始資料庫結果,因此具體化管道在之後仍執行。

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 使用包裝提供程式進行結果緩存的其他引用

-   Julie Lerman 撰寫了一篇「實體框架和 Windows Azure 中的二級緩存」MSDN 文章,其中包括如何更新範例包裝提供者以使用 Windows Server AppFabric 快取:[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   如果您正在使用實體框架 5,則團隊部落格有一篇文章,說明如何使用實體框架 5 的快取\<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>提供程式執行內容: 。 它還包括 T4 範本,以幫助自動將二級緩存添加到專案中。

## <a name="4-autocompiled-queries"></a>4 自動編譯查詢

使用實體框架對資料庫發出查詢時,它必須經過一系列步驟才能實際實現結果;其中一個步驟是查詢編譯。 實體 SQL 查詢在自動緩存時具有良好的性能,因此,在進行同一查詢的第二次或第三次時,它可以跳過計劃編譯器並使用緩存的計劃。

實體框架 5 還引入了 LINQ 到實體查詢的自動緩存。 在以往版本的實體框架中,創建編譯查詢以加快性能是一種常見做法,因為這將使 LINQ 到實體查詢可緩存。 由於緩存現在無需使用編譯查詢即可自動完成,因此我們將此功能稱為"自動編譯查詢"。 有關查詢計劃緩存及其機制的詳細資訊,請參閱查詢計劃緩存。

實體框架檢測查詢何時需要重新編譯,並且在調用查詢時(即使以前已編譯)也執行。 導致重新編譯查詢的常見條件是:

-   更改與您的查詢關聯的合併選項。 將不會使用緩存的查詢,而是計劃編譯器將再次運行,並緩存新創建的計劃。
-   更改上下文選項的值。 您將獲得與更改合併選項相同的效果。

其他條件可能會阻止查詢使用緩存。 常見範例包括：

-   使用 IE500T&lt;&gt;。包含&lt;&gt;(T 值)。
-   使用生成具有常量的查詢的函數。
-   使用非映射對象的屬性。
-   將查詢連結到需要重新編譯的另一個查詢。

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 使用 IE500T&lt;。&gt;包含&lt;T(T&gt;值)

實體框架不緩存調用&lt;IE55T&gt;的 查詢。包含&lt;針對記憶體&gt;中集合的 T(T 值),因為集合的值被視為易失性。 以下範例查詢將不會緩存,因此計劃編譯器將始終對其進行處理:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

請注意,執行「包含」的 IE55 的大小的確定查詢的編譯速度或速度。 使用大型集合(如上例中所示的集合)時,性能可能會受到顯著影響。

實體框架 6 包含對 IE50t&lt;T&gt;方式的優化。在執行查詢&lt;&gt;時,包含 T(T 值)工作。 生成的 SQL 代碼生成速度要快得多,而且可讀性更強,在大多數情況下,它還在伺服器中執行得更快。

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 使用產生具有常量的查詢的函數

Skip()、Take()、包含()和 DefautIfEmpty() LINQ 運算符不生成具有參數的 SQL 查詢,而是將傳遞給它們的值作為常量傳遞給它們。 因此,否則可能相同的查詢最終會污染 EF 堆疊和資料庫伺服器上的查詢計劃緩存,除非在後續查詢執行中使用相同的常量,否則不會重新使用。 例如：

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

在此示例中,每次執行此查詢時,使用 ID 的不同值,查詢將編譯為新計劃。

在進行分頁時,尤其要注意使用跳過和取。 在 EF6 中,這些方法具有 lambda 重載,可有效地使緩存的查詢計劃可重用,因為 EF 可以擷取傳遞給這些方法的變數並將其轉換為 SQL 參數。 這也有助於保持緩存更乾淨,否則每個查詢使用不同的常量為 Skip 和 Take 將獲得其自己的查詢計畫緩存條目。

請考慮以下代碼,該代碼不理想,但僅用於舉例說明此類查詢:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

此代碼的更快版本將涉及呼叫使用 lambda 的跳過:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

第二個代碼段的運行速度可能加快 11%,因為每次運行查詢時都使用相同的查詢計劃,從而節省了 CPU 時間並避免污染查詢緩存。 此外,由於 Skip 的參數位於閉包中,因此代碼現在可能如下所示:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 使用非映射物件屬性

當查詢使用非映射物件類型的屬性作為參數時,查詢將不會被緩存。 例如：

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

在此示例中,假設類非映射類型不是實體模型的一部分。 可以輕鬆地更改此查詢,以不使用非映射類型,而是使用本地變數作為查詢的參數:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

在這種情況下,查詢將能夠獲得緩存,並從查詢計劃緩存中獲益。

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 連結到需要重新編譯的查詢

按照上述相同的示例,如果您有第二個依賴於需要重新編譯的查詢,則整個第二個查詢也將重新編譯。 下面是一個範例來說明此方案:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

該示例是泛型的,但它說明了連結到第一查詢如何導致第二查詢無法緩存。 如果第一查詢不是需要重新編譯的查詢,則第二查詢將被緩存。

## <a name="5-notracking-queries"></a>5 無追蹤查詢

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 停用變更追蹤以減少狀態管理花費

如果您處於唯讀方案,並且希望避免將物件載入ObjectStateManager中的開銷,則可以發出「無追蹤」查詢。可以在查詢級別禁用更改跟蹤。

但請注意,通過禁用更改跟蹤,您實際上正在關閉物件緩存。 當您查詢實體時,我們不能通過從ObjectStateManager 提取以前具體化的查詢結果來跳過具體化。 如果在同一上下文中反覆查詢相同的實體,則實際上可能會看到啟用更改跟蹤的性能優勢。

使用 ObjectContext 查詢時,ObjectQuery 和 ObjectSet 實例將在設定合併選項後記住它,並且在它們上組成的查詢將繼承父查詢的有效 MergeOption。 使用 DbContext 時,可以通過在 DbSet 上調用 AsNo 追蹤() 修改器來禁用追蹤。

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 禁用使用 DbContext 時查詢的變更追蹤

通過將調用連結到查詢中的 AsNoTrack() 方法,可以將查詢模式切換到 NoTrack。 與物件查詢不同,DbContext API 中的 DbSet 和 DbQuery 類沒有 MergeOption 的可變屬性。

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 使用 ObjectContext 關閉查詢等級的變更追蹤

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 使用 ObjectContext 關閉整個實體集的變更追蹤

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 測試指標,演示 NoTrack 查詢的性能優勢

在此測試中,我們將透過將追蹤與 Navision 模型的 NoTrack 查詢進行比較來查看填充 Object StateManager 的成本。 有關 Navision 模型的說明以及已執行的查詢類型,請參閱附錄。 在此測試中,我們反覆運算查詢清單並執行每個查詢一次。 我們運行了兩個測試變體,一次使用 NoTrack 查詢,另一次使用默認合併選項"僅追加"。 我們運行每個變體 3 次,並獲取運行的平均值。 在測試之間,我們清除 SQL Server 上的查詢快取,並透過執行以下指令來收縮 tempdb:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINK 資料庫(tempdb, 0)

測試結果,3 次以上的中值:

|                        | 無追蹤 + 工作集 | 無追蹤 + 時間 | 只附加 = 工作集 | 只附加 = 時間 |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **實體框架 5** | 460361728                 | 1163536 毫秒         | 596545536                 | 1273042 毫秒         |
| **Entity Framework 6** | 647127040                 | 190228 毫秒          | 832798720                 | 195521 毫秒          |

實體框架 5 在運行結束時的記憶體佔用量將比實體框架 6 少。 實體框架 6 使用的額外記憶體是啟用新功能和更好性能的額外記憶體結構和代碼的結果。

使用 Object StateManager 時,記憶體佔用空間也有明顯差異。 實體框架 5 在追蹤我們從資料庫中實現的所有實體時,其佔用空間增加了 30%。 實體框架 6 這樣做時佔用空間增加了 28%。

在時間方面,實體框架 6 在此測試中以較大優勢優於實體框架 5。 實體框架 6 在實體框架 5 消耗的時間大約 16% 的時間內完成了測試。 此外,使用ObjectStateManager時,實體框架5需要9%的時間才能完成。 相比之下,實體框架 6 在使用 Object StateManager 時使用的時間要長 3%。

## <a name="6-query-execution-options"></a>6 查詢執行選項

實體框架提供了幾種不同的查詢方法。 我們將介紹以下選項,比較每個選項的優缺點,並檢查它們的性能特徵:

-   林Q 到實體。
-   沒有跟蹤 LINQ 到實體。
-   物件查詢的實體 SQL。
-   實體命令的實體 SQL。
-   執行存儲查詢。
-   SqlQuery。
-   編譯查詢。

### <a name="61-linq-to-entities-queries"></a>6.1 LINQ 到實體查詢

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**優點**

-   適用於 CUD 操作。
-   完全具體化的物件。
-   最簡單的編寫語法內置於程式設計語言中。
-   性能好。

**缺點**

-   某些技術限制,例如:
    -   對外部 JOIN 查詢使用預設 IfEmpty 的模式會導致比實體 SQL 中簡單的外部 JOIN 語句更複雜的查詢。
    -   您仍然不能將 LIKE 用於常規模式匹配。

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 沒有追蹤 LINQ 到實體查詢

當上下文派生物件上下文時:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

當上下文派生 DbContext 時:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**優點**

-   與常規 LINQ 查詢性能提高。
-   完全具體化的物件。
-   最簡單的編寫語法內置於程式設計語言中。

**缺點**

-   不適合 CUD 操作。
-   某些技術限制,例如:
    -   對外部 JOIN 查詢使用預設 IfEmpty 的模式會導致比實體 SQL 中簡單的外部 JOIN 語句更複雜的查詢。
    -   您仍然不能將 LIKE 用於常規模式匹配。

請注意,即使未指定 NoTracking,也不會追蹤專案標量屬性的查詢。 例如：

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

此特定查詢未顯式指定為 NoTracking,但由於它未具體化物件狀態管理器已知的類型,因此不會跟蹤具體化結果。

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 物件查詢的實體 SQL

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**優點**

-   適用於 CUD 操作。
-   完全具體化的物件。
-   支持查詢計劃緩存。

**缺點**

-   涉及文本查詢字串,與內置於語言中的查詢構造相比,這些字串更容易出現使用者錯誤。

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 實體命令的實體 SQL

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

**優點**

-   支援 .NET 4.0 中的查詢計劃快取(.NET 4.5 中的所有其他查詢類型都支援計劃緩存)。

**缺點**

-   涉及文本查詢字串,與內置於語言中的查詢構造相比,這些字串更容易出現使用者錯誤。
-   不適合 CUD 操作。
-   結果不會自動具體化,必須從數據讀取器讀取。

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery 和執行儲存查詢

資料庫上的 SqlQuery:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

DbSet 上的 SqlQuery:

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStore查詢:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**優點**

-   通常性能最快,因為計劃編譯器是繞過的。
-   完全具體化的物件。
-   適用於從 DbSet 使用的 CUD 操作。

**缺點**

-   查詢是文本和容易出錯的。
-   通過使用存儲語義而不是概念語義,查詢綁定到特定的後端。
-   存在繼承時,手工製作的查詢需要考慮所請求類型的映射條件。

### <a name="66-compiledquery"></a>6.6 編譯查詢

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**優點**

-   與常規 LINQ 查詢(LINQ 查詢)提供高達 7% 的性能改進。
-   完全具體化的物件。
-   適用於 CUD 操作。

**缺點**

-   增加了複雜性和程式設計開銷。
-   在編譯的查詢上作曲時,性能改進將丟失。
-   某些 LINQ 查詢不能編寫為編譯查詢 - 例如,匿名類型的投影。

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 不同查詢選項的效能比較

在未進行時間創建時的簡單微觀基準測試已過。 我們在受控環境中對一組非緩存實體進行了 5000 次查詢。 這些數位應該帶有警告:它們並不反映應用程式產生的實際數位,而是非常準確地測量了在比較 Apple 到 apple 的不同查詢選項時存在的性能差異量,不包括創建新上下文的成本。

| EF  | 測試                                 | 時間 (毫秒) | 記憶體   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | 物件上下文 ESQL                   | 2414      | 38801408 |
| EF5 | 物件內容查詢             | 2692      | 38277120 |
| EF5 | 資料庫內容庫內容查詢無追蹤     | 2818      | 41840640 |
| EF5 | 資料庫上下文林克查詢                 | 2930      | 41771008 |
| EF5 | 物件內容查詢無追蹤 | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | 物件上下文 ESQL                   | 2059      | 46039040 |
| EF6 | 物件內容查詢             | 3074      | 45248512 |
| EF6 | 資料庫內容庫內容查詢無追蹤     | 3125      | 47575040 |
| EF6 | 資料庫上下文林克查詢                 | 3420      | 47652864 |
| EF6 | 物件內容查詢無追蹤 | 3593      | 45260800 |

![EF5 微型基準,5000 個暖疊代](~/ef6/media/ef5micro5000warm.png)

![EF6 微型基準,5000 個暖疊代](~/ef6/media/ef6micro5000warm.png)

微基準對代碼中的小更改非常敏感。 在這種情況下,實體框架 5 和實體框架 6 的成本之間的差額是由於[增加了攔截](~/ef6/fundamentals/logging-and-interception.md)和[事務性改進](~/ef6/saving/transactions.md)。 然而,這些微觀基準數位是實體框架所做操作的一個很小的碎片中放大的視野。 從實體框架 5 升級到實體框架 6 時,暖查詢的實際方案不應看到性能回歸。

為了比較不同查詢選項的實際性能,我們創建了 5 個單獨的測試變體,其中我們使用不同的查詢選項來選擇類別名稱為"飲料"的所有產品。 每個反覆運算包括創建上下文的成本,以及實現所有返回的實體的成本。 在採用 1000 個有時算反覆運算的總和之前,將不及時運行 10 個反覆運算。 顯示的結果是從每次測試的 5 次運行中獲取的中位運行。 有關詳細資訊,請參閱附錄 B,其中包含測試的代碼。

| EF  | 測試                                        | 時間 (毫秒) | 記憶體   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | 物件內容文實體命令                | 621       | 39350272 |
| EF5 | 資料庫上的 DbContext Sql 查詢             | 825       | 37519360 |
| EF5 | 物件內容儲存查詢                   | 878       | 39460864 |
| EF5 | 物件內容查詢無追蹤        | 969       | 38293504 |
| EF5 | 使用物件查詢的物件內容內容的文字實體 Sql | 1089      | 38981632 |
| EF5 | 物件內容名稱文編譯查詢                | 1099      | 38682624 |
| EF5 | 物件內容查詢                    | 1152      | 38178816 |
| EF5 | 資料庫內容庫內容查詢無追蹤            | 1208      | 41803776 |
| EF5 | DbSet 上的資料庫內容的資料庫內容                | 1414      | 37982208 |
| EF5 | 資料庫上下文林克查詢                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | 物件內容文實體命令                | 480       | 47247360 |
| EF6 | 物件內容儲存查詢                   | 493       | 46739456 |
| EF6 | 資料庫上的 DbContext Sql 查詢             | 614       | 41607168 |
| EF6 | 物件內容查詢無追蹤        | 684       | 46333952 |
| EF6 | 使用物件查詢的物件內容內容的文字實體 Sql | 767       | 48865280 |
| EF6 | 物件內容名稱文編譯查詢                | 788       | 48467968 |
| EF6 | 資料庫內容庫內容查詢無追蹤            | 878       | 47554560 |
| EF6 | 物件內容查詢                    | 953       | 47632384 |
| EF6 | DbSet 上的資料庫內容的資料庫內容                | 1023      | 41992192 |
| EF6 | 資料庫上下文林克查詢                        | 1290      | 47529984 |


![EF5 暖查詢 1000 次反覆運算](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查詢 1000 次反覆運算](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> 為完整,我們包括一個變體,其中我們在實體命令上執行實體 SQL 查詢。 但是,由於此類查詢的結果未具體化,因此比較不一定是蘋果到蘋果。 測試包括接近具體化,試圖使比較更公平。

在此端到端案例中,實體框架 6 優於實體框架 5,因為對堆疊的幾個部分進行了性能改進,包括更輕的 DbContext 初始化和&lt;更快&gt;的中數據收集 T 尋找。

## <a name="7-design-time-performance-considerations"></a>7 設計時間效能注意事項

### <a name="71-inheritance-strategies"></a>7.1 繼承原則

使用實體框架時的另一個性能考慮是您使用的繼承策略。 實體框架支援 3 種基本類型的繼承與群組:

-   每個層次結構的表 (TPH) - 其中每個繼承集映射到具有區分列的表,以指示在行中表示層次結構中的特定類型。
-   每個類型 (TPT) 的表 - 其中每種類型在資料庫中都有自己的表;子表僅定義父表不包含的列。
-   每個類的表 (TPC) - 其中每種類型的在資料庫中都有自己的完整表;子表定義其所有欄位,包括父類型中定義的欄位。

如果模型使用 TPT 繼承,則生成的查詢將比其他繼承策略生成的查詢複雜,這可能導致存儲上的執行時間較長。通過 TPT 模型生成查詢並實現結果物件通常需要更長時間。

請參考「在實體框架中使用 TPT(按類型表)繼承時的效能注意事項「MSDN 」\<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>文章: 。

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 避免在模型優先或代碼優先應用程式中使用 TPT

當您在具有 TPT 架構的現有資料庫上創建模型時,您沒有很多選項。 但是,在使用 Model First 或代碼優先創建應用程式時,出於性能問題,應避免 TPT 繼承。

在實體設計器精靈中使用模型優先時,您將獲得模型中任何繼承的 TPT。 如果要使用 Model First 切換到 TPH 繼承策略,可以使用 Visual\<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)Studio 庫 (中的實體設計器資料庫生成電源包)中的「實體設計器資料庫生成電源包」。

當使用 Code First 配置具有繼承的模型映射時,EF 預設將使用 TPH,因此繼承層次結構中的所有實體都將映射到同一表。 有關詳細資訊,請參閱 MSDN 雜誌[http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)() 中「實體框架4.1中代碼優先」一文中的「使用流暢 API 映射」部分。

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 從 EF4 升級以提高模型生成時間

生成模型儲存層 (SSDL) 的演演演算法的 SQL Server 特定改進可在實體架構 5 和 6 中提供,並在安裝 Visual Studio 2010 SP1 時作為實體框架 4 的更新提供。 以下測試結果演示了生成非常大的模型時的改進,在這種情況下,Navision 模型。 有關附錄 C 的更多詳細資訊,請參閱附錄 C。

該模型包含 1005 個實體集和 4227 個關聯集。

| 組態                              | 所消耗的時間的細目                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 視覺工作室 2010, 實體框架 4     | SSDL 產生: 2 小時 27 分鐘 <br/> 對應:1 秒 <br/> CSDL 產生:1 秒 <br/> 物件層產生:1 秒 <br/> 檢視產生: 2 小時 14 分鐘 |
| 視覺化工作室 2010 SP1, 實體架構 4 | SSDL 產生:1 秒 <br/> 對應:1 秒 <br/> CSDL 產生:1 秒 <br/> 物件層產生:1 秒 <br/> 檢視產生: 1 小時 53 分鐘   |
| 視覺工作室 2013, 實體框架 5     | SSDL 產生:1 秒 <br/> 對應:1 秒 <br/> CSDL 產生:1 秒 <br/> 物件層產生:1 秒 <br/> 檢視產生: 65 分鐘    |
| 視覺工作室 2013, 實體框架 6     | SSDL 產生:1 秒 <br/> 對應:1 秒 <br/> CSDL 產生:1 秒 <br/> 物件層產生:1 秒 <br/> 視圖生成:28 秒。   |


值得注意的是,在生成 SSDL 時,負載幾乎完全花費在 SQL Server 上,而客戶端開發電腦正在等待從伺服器返回的結果。 DBA 應特別讚賞這一改進。 還值得注意的是,模型生成的全部成本基本上都發生在視圖生成中。

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 使用資料庫優先和模型第一拆分大型模型

隨著模型尺寸的增加,設計器表面變得雜亂無章且難以使用。 我們通常認為具有 300 多個實體的模型太大,無法有效地使用設計器。 以下部落格文章描述了分割大型模型的幾個選項\<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>: 。

該帖子是為實體框架的第一個版本編寫的,但步驟仍然適用。

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 實體資料來源控制的效能注意事項

我們已經看到多線程性能和壓力測試中的情況,其中使用 EntityDataSource 控件的 Web 應用程式的性能顯著惡化。 根本原因是 EntityDataSource 反復調用 Web 應用程式引用的程式集上的元數據工作區.Load From Assembly,以發現要用作實體的類型。

解決方案是將實體數據來源的上下文類型名稱設置為派生物件上下文類的類型名稱。 這將關閉掃描所有引用的程式集的實體類型的機制。

設置 ContextTypeName 欄位還可以防止功能問題,即 .NET 4.0 中的實體資料來源在無法透過反射從程式集載入類型時引發反射類型載入異常。 此問題已在 .NET 4.5 中修復。

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 POCO 實體和變更追蹤代理

實體框架使您能夠將自定義數據類與數據模型一起使用,而無需對數據類本身進行任何修改。 這表示您可以使用「單純」(plain-old) CLR 物件 (POCO)，例如現有的網域物件，加上您的資料模型。 這些 POCO 資料類(也稱為持久性無知物件)映射到數據模型中定義的實體,支援大多數相同的查詢,插入、更新和刪除實體數據模型工具生成的實體類型的行為。

實體框架還可以創建從 POCO 類型派生的代理類,當您希望啟用諸如 POCO 實體上的延遲載入和自動更改追蹤等功能時,將使用這些類。 您的 POCO 類別必須滿足某些要求,才能允許實體架構使用代理,如下所[http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)述: 。

每次實體的任何屬性發生更改時,機會跟蹤代理都會通知物件狀態管理器,因此實體框架會一直瞭解實體的實際狀態。 這是通過將通知事件添加到屬性的 setter 方法的正文,以及讓物件狀態管理器處理此類事件來實現的。 請注意,由於實體框架創建了一組附加的事件,創建代理實體通常比創建非代理 POCO 實體的成本更高。

當 POCO 實體沒有更改追蹤代理時,可以通過將實體的內容與以前保存狀態的副本進行比較來找到更改。 當您的上下文中有許多實體時,或者當實體具有非常大的屬性時,即使自上次比較以來,這些屬性均未更改,這種深層比較將成為一個漫長的過程。

總之:創建更改跟蹤代理時,您將支付性能影響,但當實體具有許多屬性或模型中有許多實體時,更改跟蹤將説明您加快更改檢測過程。 對於具有少量屬性的實體,其中實體數量不會增長太多,因此具有更改跟蹤代理可能沒有多大好處。

## <a name="8-loading-related-entities"></a>8 載入相關實體

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 延遲載入與熱負荷

實體框架提供了幾種載入與目標實體相關的實體的不同方法。 例如,當您查詢產品時,相關訂單將載入到物件狀態管理器中的方式不同。 從性能角度來看,載入相關實體時要考慮的最大問題是是使用延遲載入還是"渴望載入」。

使用「熱載入」時,相關實體將隨目標實體集一起載入。 在查詢中使用"包含"語句來指示要引入哪些相關實體。

使用延遲載入時,初始查詢僅引入目標實體集。 但是,每當訪問導航屬性時,都會針對存儲發出另一個查詢以載入相關實體。

載入實體後,實體的任何進一步查詢都將直接從物件狀態管理器載入它,無論您是使用延遲載入還是熱要載入。

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 如何在延遲載入和渴望載入之間進行選擇

重要的是,您瞭解延遲載入和熱載入之間的區別,以便您可以為您的應用程式做出正確的選擇。 這將有助於評估針對資料庫的多個請求與可能包含大型負載的單個請求之間的權衡。 在應用程式的某些部分使用熱載入和其他部分的延遲載入可能是合適的。

作為引擎蓋下所發生的事情的一個示例,假設您要查詢居住在英國的客戶及其訂單數量。

**使用熱負荷**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**使用延遲載入**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

使用熱要載入時,您將發出一個查詢,返回所有客戶和所有訂單。 儲存命令如下所示:

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

使用延遲載入時,最初將發出以下查詢:

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

每次訪問客戶的「訂單」導航屬性時,都會針對商店發出另一個查詢,如下所示:

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

有關詳細資訊,請參閱[載入相關物件](https://msdn.microsoft.com/library/bb896272.aspx)。

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 延遲載入與渴望載入備忘單

選擇熱切裝載和延遲裝載,沒有一刀切的東西。 首先嘗試瞭解兩種策略之間的差異,以便您可以做出明智的決策;此外,請考慮您的代碼是否適合以下任何方案:

| 狀況                                                                    | 我們的建議                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 是否需要從提取的實體訪問許多導航屬性? | **否**- 兩個選項都可能都能做到。 但是,如果查詢帶來的有效負載不是太大,則使用 Eager 載入可能會獲得性能優勢,因為它需要較少的網路往返行程來實現物件。 <br/> <br/> **是**- 如果需要從實體訪問許多導航屬性,則可以通過在查詢中使用多個包含語句來使用"渴望載入"來執行此操作。 包含的實體越多,查詢返回的有效負載就越大。 將三個或多個實體包含在查詢中後,請考慮切換到延遲載入。 |
| 您確切知道運行時需要哪些數據嗎?                   | **否**- 延遲載入將更好地為您。 否則,您最終可能會查詢不需要的數據。 <br/> <br/> **是的**- 渴望載入可能是您最好的選擇;這將有助於更快地載入整個集。 如果查詢需要獲取大量數據,並且速度太慢,請嘗試"延遲載入"                                                                                                                                                                                                                                                       |
| 您的代碼執行是否遠離資料庫? (增加網路延遲)  | **否**- 當網路延遲不是問題時,使用延遲載入可能會簡化代碼。 請記住,應用程式的拓撲可能會更改,因此不要認為資料庫鄰近性是理所當然的。 <br/> <br/> **是**- 當網路出現問題時,只有您才能決定哪種方案更適合您的方案。 通常,熱負荷會更好,因為它需要更少的往返行程。                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 具有多個包括的性能問題

當我們聽到涉及伺服器回應時間問題的性能問題時,問題的根源是經常使用多個 Include 語句進行查詢。 雖然在查詢中包含相關實體很強大,但瞭解封面下發生的情況非常重要。

包含多個 Include 語句的查詢需要相當長的時間才能通過內部計劃編譯器來生成儲存命令。 這一大部分時間都花在嘗試優化生成的查詢上。 生成的存儲命令將包含每個"包含"的外部聯接或聯合,具體取決於您的映射。 像這樣的查詢將在單個負載中從資料庫引入大型連接圖形,從而消除任何頻寬問題,尤其是在有效負載中存在大量冗餘時(例如,當多個級別的 Include 用於在一對多方向上遍歷關聯時)。

通過使用 ToTraceString 並在 SQL 伺服器管理工作室中執行存儲命令以查看有效負載大小,可以檢查查詢返回過大的有效負載的情況。 在這種情況下,您可以嘗試減少查詢中的包含語句數,以便僅引入所需的數據。 或者,您可能能夠將查詢分解為較小的子查詢序列,例如:

**在斷開查詢之前:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

**中斷查詢後:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

這僅適用於追蹤的查詢,因為我們正在利用上下文自動執行標識解析和關聯修復的能力。

與延遲載入一樣,權衡將是對較小負載的更多查詢。 您還可以使用單個屬性的預測來僅顯式選擇每個實體所需的數據,但在這種情況下,您將不會載入實體,並且不支援更新。

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 取得延遲載入屬性的解決方法

實體框架當前不支援延遲載入標量或複雜屬性。 但是,如果表包含大型物件(如 BLOB),則可以使用表拆分將大型屬性分隔為單獨的實體。 例如,假設您有一個包含 varbinary 照片列的產品表。 如果查詢中頻繁不需要訪問此屬性,則可以使用表拆分僅引入通常需要的實體部分。 僅當您明確需要產品照片時,才會載入表示產品照片的實體。

吉爾·芬克的「實體框架中的表拆分」博客文章是演示如何啟用表拆分的一個很好的資源\<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>: .

## <a name="9-other-considerations"></a>9 其他注意事項

### <a name="91-server-garbage-collection"></a>9.1 伺服器垃圾回收

某些使用者可能會遇到資源爭用,從而限制在垃圾回收器未正確配置時期望的並行性。 每當在多線程方案中使用 EF 時,或任何類似於伺服器端系統的應用程式中,請確保啟用伺服器垃圾回收。 這是透過應用程式設定檔中的簡單設定完成的:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

這將減少線程爭用,並在 CPU 飽和方案中將輸送量提高高達 30%。 通常,您應該始終使用經典垃圾回收(更好地針對 UI 和用戶端方案)以及伺服器垃圾回收測試應用程式的表現。

### <a name="92-autodetectchanges"></a>9.2 自動偵測變更

如前所述,當物件緩存具有多個實體時,實體框架可能會顯示性能問題。 某些操作(如添加、刪除、尋找、輸入和保存更改)會觸發對檢測更改的調用,這些調用可能會根據物件緩存變得多大而消耗大量 CPU。 原因是物件緩存和物件狀態管理器嘗試在對上下文執行的每個操作上保持盡可能同步,以便在各種方案下保證生成的數據正確。

通常,最好在應用程式的整個生命週期內啟用實體框架的自動更改檢測。 如果您的方案受到 CPU 使用率高的負面影響,並且您的設定檔指示罪魁禍首是調用檢測更改,請考慮暫時關閉程式碼敏感部分中的自動檢測更改:

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

在關閉自動檢測更改之前,最好瞭解這可能會導致實體框架失去跟蹤有關實體上發生的更改的某些資訊的能力。 如果處理不正確,則可能會導致應用程式上的數據不一致。 關於關閉自動偵測變更的詳細資訊,請\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>閱讀 。

### <a name="93-context-per-request"></a>9.3 每個要求的上下文

實體框架的上下文旨在用作短期實例,以便提供最佳的性能體驗。 上下文預期是短暫的和丟棄的,因此已經實現為非常輕量級,並盡可能重用元數據。 在 Web 方案中,請務必牢記這一點,並且不應具有超過單個請求持續時間的上下文。 同樣,在非 Web 方案中,應根據您對實體框架中不同緩存級別的理解而丟棄上下文。 一般來說,在應用程式的整個生命週期中,應避免使用上下文實例,以及每個線程和靜態上下文的上下文。

### <a name="94-database-null-semantics"></a>9.4 資料庫無效文意

默認情況下,實體框架將生成具有\#C 空比較語義的 SQL 代碼。 請考量下列範例查詢：

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

在此示例中,我們將許多可空變數與實體上的空屬性(如供應商 ID 和 UnitPrice)進行比較。 此查詢生成的 SQL 將詢問參數值是否與列值相同,或者參數和列值是否為空。 這將隱藏資料庫伺服器處理空的方式,並將跨不同的資料庫供應商提供一致的\#C null 體驗。 另一方面,生成的代碼有點複雜,當查詢語句中的比較量增長到大量時,可能無法很好地執行。

處理這種情況的一種方法是使用資料庫空語義。 請注意,這可能與\#Cnull 語意的行為不同,因為現在實體框架將生成更簡單的 SQL,公開資料庫引擎處理空值的方式。 資料庫空語的義可以針對上下文設定使用單一個設定行啟動每個上下文:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

使用資料庫空語義時,中小型查詢不會顯示可察覺的性能改進,但在具有大量潛在空比較的查詢上,差異將變得更加明顯。

在上面的示例查詢中,在受控環境中運行的微基準中,性能差異小於 2%。

### <a name="95-async"></a>9.5 同步

實體框架 6 在 .NET 4.5 或更高版本上運行時引入了對非同步操作的支援。 在大多數情況下,具有IO相關爭用的應用程式將從使用非同步查詢和保存操作中受益最大。 如果應用程式沒有受到 IO 爭用的影響,則在最好情況下,使用非同步將同步運行,並在與同步調用相同的時間內返回結果,或者在最壞的情況下,只需將執行推遲到異步任務,並添加額外的時間來完成您的方案。

有關非同步編程的工作原理的資訊,這將有助於您確定非同步程式是否會提高應用程式[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)存取的性能。 有關在實體框架上使用非同步操作的詳細資訊,請參閱[非同步查詢和儲存](~/ef6/fundamentals/async.md
)。

### <a name="96-ngen"></a>9.6 NGEN

實體框架 6 不在 .NET 框架的預設安裝中。 因此,默認情況下,實體框架程式集不是 NGEN d,這意味著所有實體框架代碼都受與任何其他 MSIL 程式集相同的 JIT 成本的約束。 這可能會降低 F5 在開發時的經驗,也會降低應用程式在生產環境中的冷啟動。 為了降低 JIT 的 CPU 和記憶體成本,建議酌情使用實體框架映射進行 NGEN。 有關如何使用 NGEN 提高實體框架 6 的啟動效能的詳細資訊,請參閱[使用 NGen 提高啟動性能](~/ef6/fundamentals/performance/ngen.md)。

### <a name="97-code-first-versus-edmx"></a>9.7 程式碼優先與 EDMX

實體框架通過具有概念模型(物件)、存儲架構(資料庫)和兩者之間映射的記憶體中表示形式,導致面向對象程式設計和關係資料庫之間的阻抗不匹配問題。 此元數據稱為實體數據模型,簡稱 EDM。 從這個 EDM 中,實體框架將從記憶體中的物件派生到往返數據的視圖到資料庫並返回。

當實體框架與正式指定概念模型、儲存架構和映射的 EDMX 檔一起使用時,模型載入階段只需驗證 EDM 是否正確(例如,確保沒有缺少映射),然後生成檢視,然後驗證視圖並準備好使用此元數據。 只有這樣,才能執行查詢或將新數據保存到數據存儲。

代碼優先方法的核心是一個複雜的實體數據模型生成器。 實體框架必須從提供的代碼生成 EDM;它通過分析模型中涉及的類、應用約定並通過 Fluent API 配置模型來達到這一要求。 構建 EDM 後,實體框架的工作方式與專案中存在 EDMX 檔的方式相同。 因此,從 Code First 構建模型會增加額外的複雜性,與使用 EDMX 相比,實體框架的啟動時間會變慢。 成本完全取決於正在構建的模型的大小和複雜性。

在選擇使用 EDMX 與代碼優先時,請務必瞭解代碼 First 引入的靈活性會增加首次構建模型的成本。 如果應用程式能夠承受此首次載入的成本,那麼通常 Code First 將是首選方式。

## <a name="10-investigating-performance"></a>10 調查效能

### <a name="101-using-the-visual-studio-profiler"></a>10.1 使用視覺化工作室探查器

如果實體框架存在性能問題,則可以使用 Visual Studio 中內置的探查器來查看應用程式花費的時間。 這是我們用於在"探索ADO.NET實體框架的性能 - 第 1 部分\<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>)"博客文章( 顯示實體框架在冷熱查詢期間花費時間)中生成餅圖的工具。

數據和建模客戶諮詢團隊編寫的「使用 Visual Studio 2010 探查器分析實體框架」博客文章顯示了他們如何使用探查器調查性能問題的真實示例。\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>. 這篇文章是為視窗應用程式編寫的。 如果需要分析 Web 應用程式,Windows 性能記錄器 (WPR) 和 Windows 性能分析器 (WPA) 工具可能比在 Visual Studio 工作更好。 WPR 和 WPA 是 Windows 性能工具組的一部分,該工具[http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)套件包含在 Windows 評估和部署工具組 () 中。

### <a name="102-applicationdatabase-profiling"></a>10.2 應用程式/資料庫分析

像視覺化工作室中內建的探查器這樣的工具告訴您應用程式將花時間的位置。另一種類型的探查器可用於根據需求在生產或預生產中對正在運行的應用程序進行動態分析,並查找常見的陷阱和資料庫訪問的抗模式。

兩個市售的探查器是實體框架探查\<http://efprof.com>)器(和 ORMProfiler(。 \< http://ormprofiler.com>)

如果您的應用程式是使用代碼優先的 MVC 應用程式,則可以使用 StackExchange 的 Mini Profiler。 斯科特·漢塞爾曼在他的博客中描述了這個工具: \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.

有關分析應用程式的資料庫活動的詳細資訊,請參閱 Julie Lerman 的 MSDN 雜誌文章,標題為[實體框架中的分析資料庫活動](https://msdn.microsoft.com/magazine/gg490349.aspx)。

### <a name="103-database-logger"></a>10.3 資料庫記錄器

如果使用實體框架 6,還考慮使用內置日誌記錄功能。 可以指示上下文的資料庫屬性透過簡單的單行設定紀錄其活動:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

在此示例中,資料庫活動將記錄到主控台,但可以將 Log 屬性配置為調&lt;用任何操作&gt;字串 委託。

如果要在不重新編譯的情況下啟用資料庫日誌記錄,並且正在使用實體框架 6.1 或更高版本,則可以通過在應用程式的 Web.config 或 app.config 檔中添加攔截器來執行此操作。

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

有關如何在不重新編譯的情況下加入紀錄紀錄的詳細資訊,\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>請存取 。

## <a name="11-appendix"></a>11 附錄

### <a name="111-a-test-environment"></a>11.1 A. 測試環境

此環境使用2台電腦設置,資料庫與用戶端應用程式分開的電腦上。 計算機位於同一機架中,因此網路延遲相對較低,但比單機環境更逼真。

#### <a name="1111-app-server"></a>11.1.1 應用伺服器

##### <a name="11111-software-environment"></a>11.1.1.1 軟體環境

-   實體框架 4 軟體環境
    -   操作系統名稱:Windows 伺服器 2008 R2 企業 SP1。
    -   視覺工作室 2010 – 終極。
    -   可視化工作室 2010 SP1 (僅適用於某些比較)。
-   實體框架 5 和 6 軟體環境
    -   操作系統名稱:Windows 8.1 企業版
    -   視覺工作室 2013 – 終極。

##### <a name="11112-hardware-environment"></a>11.1.1.2 硬體環境

-   雙處理器:英特爾(R) Xeon(R) CPU L5520 W3530 = 2.27GHz,2261 Mhz8 GHz,4 核(s),84 邏輯處理器。
-   2412 GB RamRAM。
-   136 GB SCSI250GB SATA 7200 rpm 3GB/s 驅動器拆分為 4 個分區。

#### <a name="1112-db-server"></a>11.1.2 資料庫伺服器

##### <a name="11121-software-environment"></a>11.1.2.1 軟體環境

-   操作系統名稱:Windows 伺服器 2008 R28.1 企業 SP1。
-   SQL 伺服器 2008 R22012。

##### <a name="11122-hardware-environment"></a>11.1.2.2 硬體環境

-   單處理器:英特爾(R) Xeon(R) CPU L5520 = 2.27GHz,2261 MhzES-1620 0 = 3.60GHz,4 個核心(s),8 個邏輯處理器。
-   824 GB 拉姆拉姆。
-   465 GB ATA500GB SATA 7200 rpm 6GB/s 驅動器拆分為 4 個分區。

### <a name="112-b-query-performance-comparison-tests"></a>11.2 B. 查詢效能比較測試

北風模型用於執行這些測試。 它是使用實體框架設計器從資料庫生成的。 然後,使用以下代碼比較查詢執行選項的效能:

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a>11.3 C. 納維視覺模型

Navision 資料庫是一個大型資料庫,用於演示 Microsoft 動態和導航。 生成的概念模型包含 1005 個實體集和 4227 個關聯集。 測試中使用的模型為「平面」 - 未向其添加繼承。

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 用於 Navision 測試的查詢

與 Navision 模型一起使用的查詢清單包含 3 類別 SQL 查詢:

##### <a name="11311-lookup"></a>11.3.1.1 查找

沒有聚合的簡單尋找查詢

-   數量: 16232
-   範例：

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 單聚合

具有多個聚合但沒有子值(單一查詢)的普通 BI 查詢

-   數量: 2313
-   範例：

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

其中 MDF\_\_\_工作階段 登入時間最大值() 在模型中定義為:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 聚合子總計

包含聚合和小計的 BI 查詢(透過所有聯合)

-   計數: 178
-   範例：

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
