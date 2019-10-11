---
title: EF4、EF5 和 EF6 的效能考慮-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 07eb605f0d39f0c1bcfe781540525180f0dd0b22
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181679"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>EF 4、5和6的效能考慮
依 David Obando、Eric Dettinger 及其他

發佈2012年4月

上次更新日期：5月2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1.簡介

物件關聯式對應架構是一個便利的方式，可在物件導向應用程式中提供資料存取的抽象概念。 針對 .NET 應用程式，Microsoft 建議的 O/RM Entity Framework。 不過，透過任何抽象概念，效能可能會成為一大顧慮。

本白皮書的撰寫目的是要示範使用 Entity Framework 開發應用程式時的效能考慮，讓開發人員瞭解可能會影響效能的 Entity Framework 內部演算法，以及提供調查秘訣和改善使用 Entity Framework 之應用程式的效能。 有一些很好的主題可瞭解網路上已有的效能，我們也嘗試盡可能指向這些資源。

效能是一種棘手的主題。 本白皮書旨在協助您針對使用 Entity Framework 的應用程式，做出與效能相關的決策。 我們已包含一些測試度量來示範效能，但這些計量並不是要做為您在應用程式中所看到效能的絕對指標。

基於實際目的，本檔假設 Entity Framework 4 是在 .NET 4.0 之下執行，而 Entity Framework 5 和6則是在 .NET 4.5 之下執行。 Entity Framework 5 所做的許多效能改進，都位於 .NET 4.5 隨附的核心元件中。

Entity Framework 6 是頻外版本，不依賴 .NET 隨附的 Entity Framework 元件。 Entity Framework 6 適用于 .NET 4.0 和 .NET 4.5，而且可以為尚未從 .NET 4.0 升級，但想要在應用程式中使用最新 Entity Framework 位的使用者提供極大的效能優勢。 當本檔提及 Entity Framework 6 時，它是指在撰寫本文時可用的最新版本：版本6.1.0。

## <a name="2-cold-vs-warm-query-execution"></a>2.冷與暖查詢執行

第一次針對指定的模型進行任何查詢時，Entity Framework 會在幕後執行許多工作，以載入和驗證模型。 我們經常將這個第一個查詢稱為「冷」查詢。  針對已載入的模型進行進一步的查詢稱為「暖」查詢，而且速度更快。

讓我們看看使用 Entity Framework 執行查詢時所花費時間的高階觀點，並查看 Entity Framework 6 中的專案的改善程度。

**第一個查詢執行–冷查詢**

| 程式碼使用者寫入                                                                                     | Action                    | EF4 效能影響                                                                                                                                                                                                                                                                                                                                                                                                        | EF5 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                    | EF6 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 內容建立          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                        | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查詢運算式建立 | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | LINQ 查詢執行      | -中繼資料載入：高但快取 <br/> -View 世代：可能非常高，但已快取 <br/> -參數評估：Medium <br/> -查詢轉譯：Medium <br/> -具體化程式產生：中但快取 <br/> -資料庫查詢執行：可能很高 <br/> + 連接。開啟 <br/> + 命令。 ExecuteReader <br/> + DataReader. 讀取 <br/> 物件具體化：Medium <br/> -身分識別查閱：Medium | -中繼資料載入：高但快取 <br/> -View 世代：可能非常高，但已快取 <br/> -參數評估：低 <br/> -查詢轉譯：中但快取 <br/> -具體化程式產生：中但快取 <br/> -資料庫查詢執行：可能很高（在某些情況下為較佳的查詢） <br/> + 連接。開啟 <br/> + 命令。 ExecuteReader <br/> + DataReader. 讀取 <br/> 物件具體化：Medium <br/> -身分識別查閱：Medium | -中繼資料載入：高但快取 <br/> -View 世代：中但快取 <br/> -參數評估：低 <br/> -查詢轉譯：中但快取 <br/> -具體化程式產生：中但快取 <br/> -資料庫查詢執行：可能很高（在某些情況下為較佳的查詢） <br/> + 連接。開啟 <br/> + 命令。 ExecuteReader <br/> + DataReader. 讀取 <br/> 物件具體化：中（速度比 EF5 快） <br/> -身分識別查閱：Medium |
| `}`                                                                                                  | Connection.Close          | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**第二個查詢執行–暖查詢**

| 程式碼使用者寫入                                                                                     | Action                    | EF4 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | EF5 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | EF6 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 內容建立          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查詢運算式建立 | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | LINQ 查詢執行      | -中繼資料~~載入~~查閱：~~高但快~~取量 <br/> -View~~世代~~查閱：~~可能非常高，但已快~~取量 <br/> -參數評估：Medium <br/> -查詢~~轉譯~~查閱：Medium <br/> -具體化程式~~產生~~查閱：~~中但~~快取量 <br/> -資料庫查詢執行：可能很高 <br/> + 連接。開啟 <br/> + 命令。 ExecuteReader <br/> + DataReader. 讀取 <br/> 物件具體化：Medium <br/> -身分識別查閱：Medium | -中繼資料~~載入~~查閱：~~高但快~~取量 <br/> -View~~世代~~查閱：~~可能非常高，但已快~~取量 <br/> -參數評估：低 <br/> -查詢~~轉譯~~查閱：~~中但~~快取量 <br/> -具體化程式~~產生~~查閱：~~中但~~快取量 <br/> -資料庫查詢執行：可能很高（在某些情況下為較佳的查詢） <br/> + 連接。開啟 <br/> + 命令。 ExecuteReader <br/> + DataReader. 讀取 <br/> 物件具體化：Medium <br/> -身分識別查閱：Medium | -中繼資料~~載入~~查閱：~~高但快~~取量 <br/> -View~~世代~~查閱：~~中但~~快取量 <br/> -參數評估：低 <br/> -查詢~~轉譯~~查閱：~~中但~~快取量 <br/> -具體化程式~~產生~~查閱：~~中但~~快取量 <br/> -資料庫查詢執行：可能很高（在某些情況下為較佳的查詢） <br/> + 連接。開啟 <br/> + 命令。 ExecuteReader <br/> + DataReader. 讀取 <br/> 物件具體化：中（速度比 EF5 快） <br/> -身分識別查閱：Medium |
| `}`                                                                                                  | Connection.Close          | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


有數種方式可降低冷和暖查詢的效能成本，我們將在下一節中討論這些功能。 具體而言，我們將探討如何使用預先產生的視圖來降低在冷查詢中載入模型的成本，這應該有助於減輕在視圖產生期間所發生的效能難題。 針對暖查詢，我們將討論查詢計劃快取、沒有追蹤查詢和不同的查詢執行選項。

### <a name="21-what-is-view-generation"></a>2.1 什麼是視圖產生？

為了瞭解什麼是產生視圖，我們必須先瞭解「對應視圖」是什麼。 對應視圖是對應中針對每個實體集和關聯所指定之轉換的可執行表示。 就內部而言，這些對應視圖會採用 CQTs （標準查詢樹狀結構）的形式。 對應視圖有兩種類型：

-   查詢檢視：這些表示從資料庫架構移至概念模型所需的轉換。
-   更新視圖：這些是從概念模型移至資料庫架構所需的轉換。

請記住，概念模型可能會以各種不同的方式與資料庫架構不同。 例如，一個單一資料表可能用來儲存兩個不同實體類型的資料。 繼承和非一般對應在對應視圖的複雜性中扮演著角色。

根據對應規格來計算這些觀點的程式，就是所謂的「視圖產生」。 當載入模型時，或在建立時使用「預先產生的視圖」，可以動態地進行視圖產生;後者會以 Entity SQL 語句的形式序列化為 C @ no__t-0 或 VB 檔案。

當視圖產生時，也會進行驗證。 從效能的觀點來看，大部分的視圖產生成本實際上都是驗證視圖，這可確保實體之間的連接有意義，並對所有支援的作業具有正確的基數。

執行實體集的查詢時，查詢會與對應的查詢檢視結合，而此組合的結果會透過計畫編譯器執行，以建立支援存放區可瞭解的查詢標記法。 針對 SQL Server，此編譯的最終結果會是 T-sql SELECT 語句。 第一次執行實體集的更新時，更新視圖會透過類似的進程執行，將它轉換成目標資料庫的 DML 語句。

### <a name="22-factors-that-affect-view-generation-performance"></a>影響視圖產生效能的2.2 因素

「視圖產生」步驟的效能不只取決於模型的大小，也會根據模型的互連方式而定。 如果兩個實體透過繼承鏈或關聯來連線，則稱為「已連接」。 同樣地，如果兩個數據表透過外鍵連接，它們就會連接。 當您的架構中已連線的實體和資料表數目增加時，視圖產生成本就會增加。

在最差的情況下，我們用來產生和驗證視圖的演算法是指數的，不過我們的確使用一些優化來改善這項功能。 似乎會對效能造成負面影響的最大因素如下：

-   模型大小，參考實體數目和這些實體之間的關聯量。
-   模型複雜度，特別是涉及大量類型的繼承。
-   使用獨立關聯，而不是外鍵關聯。

針對小型的簡單模型，成本可能夠小，而不是使用預先產生的視圖。 隨著模型大小和複雜度的增加，有數個選項可用來減少產生和驗證的成本。

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 使用預先產生的視圖來減少模型載入時間

如需如何在 Entity Framework 6 上使用預先產生之視圖的詳細資訊，請造訪[預先產生的對應視圖](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>使用 Entity Framework Power Tools 社區版2.3.1 預先產生的視圖

在模型類別檔案上按一下滑鼠右鍵，然後使用 [Entity Framework] 功能表選取 [產生視圖]，即可使用[Entity Framework 6 Power Tool 社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)來產生 EDMX 和 Code First 模型的查看。 Entity Framework Power Tools 的社區版僅適用于 DbCoNtext 衍生的內容。

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 如何搭配 Edmgen.exe 所建立的模型使用預先產生的視圖

Edmgen.exe 是隨附于 .NET 的公用程式，適用于 Entity Framework 4 和5，但不含 Entity Framework 6。 Edmgen.exe 可讓您從命令列產生模型檔案、物件層和 views。 其中一個輸出將是您選擇的語言、VB 或 C @ no__t-0 的 Views 檔案。 這是一個程式碼檔案，其中包含每個實體集的 Entity SQL 程式碼片段。 若要啟用預先產生的視圖，您只需要在專案中包含檔案。

如果您手動對模型的架構檔案進行編輯，就必須重新產生 views 檔案。 若要這麼做，您可以使用 **/mode： ViewGeneration**旗標執行 edmgen.exe。

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 如何搭配使用預先產生的視圖與 EDMX 檔案

您也可以使用 Edmgen.exe 來產生 EDMX 檔案的 views-先前參考的 MSDN 主題說明如何新增預先建立事件來執行此動作，但這很複雜，而且在某些情況下無法使用。 當您的模型位於 edmx 檔案時，使用 T4 範本來產生視圖通常會比較容易。

ADO.NET 小組部落格有某篇文章說明如何使用 T4 範本產生檢視表 ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>) 。 這篇文章包含可供下載並新增至您專案的範本。 範本是針對第一個 Entity Framework 版本所撰寫，因此不保證會與最新版本的 Entity Framework 搭配使用。 不過，您可以針對 Entity Framework 4 下載更新的視圖產生樣板集，並 5from Visual Studio 資源庫：

-   VB.NET： \< @ NO__T-1
-   C @ NO__T-0： \< @ NO__T-2

如果您使用 Entity Framework 6 您可以檢視產生 T4 範本從取得 Visual Studio 組件庫，在\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f> 。

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 降低視圖產生成本

使用預先產生的視圖，會將產生的視圖成本從模型載入（執行時間）移至設計階段。 雖然這可在執行時間改善啟動效能，但您仍會在開發時遇到產生視圖的困難。 還有幾個額外的訣竅，可以在編譯時間和執行時間，協助降低產生視圖的成本。

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>使用外鍵關聯的2.4.1，以降低視圖產生成本

我們已瞭解將模型中的關聯從獨立關聯切換至外鍵關聯的幾個案例，大幅改善了在視圖產生中所花費的時間。

為了示範這項改進，我們使用 Edmgen.exe 產生了兩個版本的 Navision 模型。 *注意：如需 Navision 模型的說明，請參閱附錄 C。* Navision 模型在此練習中很有趣，因為它的實體數量和關聯性非常龐大。

這個非常大型模型的其中一個版本是使用外鍵關聯所產生，另一個則是使用獨立關聯產生。 接著，我們會計時為每個模型產生視圖所花費的時間。 Entity Framework 5 測試使用類別 EntityViewGenerator 中的 GenerateViews （）方法來產生視圖，而 Entity Framework 6 測試使用類別 StorageMappingItemCollection 的 GenerateViews （）方法。 這是因為在 Entity Framework 6 程式碼基底中發生的程式碼重建。

使用 Entity Framework 5，在實驗室機器中，具有外鍵的模型產生會花費65分鐘的時間。 為使用獨立關聯的模型產生視圖所需的時間是未知的。 在我們的實驗室中，我們已將執行的測試保留一個月以上，以安裝每月更新。

使用 Entity Framework 6，在相同的實驗室電腦中，具有外鍵的模型產生會花費28秒。 使用獨立關聯的模型產生視圖會耗用58秒。 在其視圖產生程式碼上對 Entity Framework 6 所做的改善，表示許多專案不需要預先產生的視圖，即可取得更快速的啟動時間。

請務必注意，Entity Framework 4 和5中預先產生的視圖可以透過 Edmgen.exe 或 Entity Framework Power tool 來完成。 針對 Entity Framework 6 視圖產生可以透過 Entity Framework 的 Power tool 或程式設計方式來完成，如[預先產生的對應視圖](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 如何使用外鍵，而不是獨立關聯

使用 Edmgen.exe 或 Visual Studio 中的 Entity Designer 時，您預設會取得 Fk，而且只會使用單一核取方塊或命令列旗標，在 Fk 與 IAs 之間切換。

如果您有大型的 Code First 模型，使用獨立關聯的效果會與產生視圖相同。 您可以在相依物件的類別上包含外鍵屬性來避免這種影響，不過有些開發人員會將其視為物件模型的污染。 您可以找到更多有關這個主題進行\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/> 。

| 使用時      | 請執行                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Entity Designer | 加入兩個實體之間的關聯之後，請確定您有參考條件約束。 參考條件約束會告訴 Entity Framework 使用外鍵，而不是獨立的關聯。 如需其他詳細資料請造訪\<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx> 。 |
| Edmgen.exe          | 使用 Edmgen.exe 從資料庫產生您的檔案時，將會遵守您的外鍵，並將其加入至模型中。 如需 EDMGen 所公開的不同選項的詳細資訊請造訪[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。                           |
| Code First      | 如需有關使用 Code First 時如何在相依物件上包含外鍵屬性的資訊，請參閱[Code First 慣例](~/ef6/modeling/code-first/conventions/built-in.md)主題的「關聯性慣例」一節。                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 將模型移至不同的元件

當您的模型直接包含在應用程式的專案中，而您透過預先建立事件或 T4 範本產生 views 時，只要重建專案，就會發生視圖產生和驗證，即使模型並未變更也是一樣。 如果您將模型移至不同的元件，並從應用程式的專案中參考它，您可以對應用程式進行其他變更，而不需要重建包含模型的專案。

*注意：* @no__t 1when 將模型移至不同的元件時，請記得將模型的連接字串複製到用戶端專案的應用程式佈建檔中。

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 停用以 edmx 為基礎的模型驗證

EDMX 模型會在編譯時期進行驗證，即使模型不變也一樣。 如果您的模型已經過驗證，您可以在編譯時期隱藏驗證，方法是在 [屬性] 視窗中將 [在組建上驗證] 屬性設定為 [false]。 當您變更對應或模型時，可以暫時重新啟用驗證來驗證您的變更。

請注意，已改善 Entity Framework 6 的 Entity Framework Designer，而「在組建上進行驗證」的成本遠低於舊版的設計工具。

## <a name="3-caching-in-the-entity-framework"></a>3 Entity Framework 中的快取

Entity Framework 具有下列內建的快取形式：

1.  物件快取–內建于 ObjectCoNtext 實例中的 ObjectStateManager 會追蹤使用該實例所抓取之物件的記憶體。 這也稱為「第一層快取」。
2.  查詢計劃快取-執行多次查詢時，重複使用所產生的存放區命令。
3.  中繼資料快取-在相同模型的不同連接之間共用模型的中繼資料。

除了 EF 提供的快取之外，一種特殊的 ADO.NET 資料提供者（稱為包裝提供者）也可以用來針對從資料庫中取得的結果（也稱為第二層快取）來擴充 Entity Framework。

### <a name="31-object-caching"></a>3.1 物件快取

根據預設，在查詢結果中傳回實體時，在 EF 具體化之前，ObjectCoNtext 會檢查是否已將具有相同索引鍵的實體載入其 ObjectStateManager 中。 如果已有具有相同索引鍵的實體存在，EF 會將它包含在查詢的結果中。 雖然 EF 仍然會針對資料庫發出查詢，但此行為可能會略過多個具體化實體的大部分成本。

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 使用 DbCoNtext 尋找從物件快取取得實體

不同于一般查詢，DbSet （EF 4.1 中第一次包含的 Api）中的 Find 方法將會在記憶體中執行搜尋，甚至是針對資料庫發出查詢。 請務必注意，兩個不同的 ObjectCoNtext 實例會有兩個不同的 ObjectStateManager 實例，這表示它們有不同的物件快取。

尋找會使用主要金鑰值來嘗試尋找內容所追蹤的實體。 如果實體不在內容中，則會針對資料庫執行並評估查詢，如果在內容或資料庫中找不到實體，則會傳回 null。 請注意，Find 也會傳回已加入至內容，但尚未儲存到資料庫的實體。

使用 [尋找] 時，應採取效能考慮。 根據預設，叫用此方法將會觸發物件快取的驗證，以偵測仍在等待資料庫認可的變更。 如果物件快取中有非常大量的物件，或在大型物件圖形中新增至物件快取，則此程式可能非常昂貴，但也可以停用。 在某些情況下，當您停用自動偵測變更時，您可能會察覺到呼叫 Find 方法的差異程度。 但是當物件實際上是在快取中，而物件必須從資料庫中抓取時，會發現第二個數量級的順序。 以下是使用我們的一些 microbenchmarks （以毫秒為單位）來測量的範例圖形，其中包含5000個實體的負載：

![.Net 4.5 對數刻度](~/ef6/media/net45logscale.png ".net 4.5-對數刻度")

已停用自動偵測變更的尋找範例：

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

使用 Find 方法時必須考慮的事項是：

1.  如果物件不在快取中，則尋找的優點會是否定的，但語法仍然比依索引鍵的查詢更簡單。
2.  如果已啟用自動偵測變更，尋找方法的成本可能會增加一個程度，或甚至更多，視您的模型複雜度和物件快取中的實體量而定。

此外，請記住，尋找只會傳回您要尋找的實體，如果物件快取中還沒有關聯的實體，它就不會自動載入它們。 如果您需要抓取相關聯的實體，您可以使用依索引鍵進行的查詢與積極式載入。 如需詳細資訊，請參閱 **8.1 消極式載入與積極式載入 @ no__t-0。

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>當物件快取有許多實體時，3.1.2 效能問題

物件快取有助於增加 Entity Framework 的整體回應性。 不過，當物件快取已載入非常大量的實體時，可能會影響某些作業，例如新增、移除、尋找、輸入、SaveChanges 等等。 特別是，觸發呼叫 DetectChanges 的作業會受到非常大型物件快取的負面影響。 DetectChanges 會同步處理物件圖形與物件狀態管理員，而且其效能會直接由物件圖形的大小決定。 如需 DetectChanges 的詳細資訊，請參閱[追蹤 POCO 實體中的變更](https://msdn.microsoft.com/library/dd456848.aspx)。

使用 Entity Framework 6 時，開發人員可以直接在 DbSet 上呼叫 AddRange 和 RemoveRange，而不是逐一查看集合，並針對每個實例呼叫 Add 一次。 使用範圍方法的優點是，DetectChanges 的成本只會針對整個實體集付費一次，而不會針對每個新增的實體支付一次。

### <a name="32-query-plan-caching"></a>3.2 查詢計劃快取

第一次執行查詢時，它會通過內部計畫編譯器，將概念查詢轉譯為 store 命令（例如，針對 SQL Server 執行時所執行的 T-sql）。  如果已啟用查詢計劃快取，則下一次執行查詢時，會直接從查詢計劃快取中取得 store 命令以進行執行，略過計畫編譯器。

查詢計劃快取會在相同 AppDomain 內的 ObjectCoNtext 實例之間共用。 您不需要保留 ObjectCoNtext 實例，就能受益于查詢計劃快取。

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 關於查詢計劃快取的一些注意事項

-   查詢計劃快取會針對所有查詢類型共用：Entity SQL、LINQ to Entities 和 CompiledQuery 物件。
-   根據預設，查詢計劃快取會針對 Entity SQL 查詢而啟用，不論是透過 EntityCommand 或透過 ObjectQuery 來執行。 在 .NET 4.5 上的 Entity Framework 中 LINQ to Entities 查詢，以及 Entity Framework 6，預設也會啟用此功能。
    -   將 EnablePlanCaching 屬性（在 EntityCommand 或 ObjectQuery 上）設定為 false，即可停用查詢計劃快取。 例如:
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
-   對於參數化查詢，變更參數的值仍會到達快取的查詢。 但變更參數的 facet （例如，大小、有效位數或小數位數）會在快取中達到不同的專案。
-   使用 Entity SQL 時，查詢字串是索引鍵的一部分。 同時變更查詢會產生不同的快取專案，即使查詢的功能相同也一樣。 這包括大小寫或空白字元的變更。
-   使用 LINQ 時，會處理查詢以產生部分的索引鍵。 因此，變更 LINQ 運算式會產生不同的索引鍵。
-   可能適用其他技術限制;如需詳細資訊，請參閱 Autocompiled 查詢。

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 快取收回演算法

瞭解內部演算法的運作方式，可協助您找出啟用或停用查詢計劃快取的時機。 清除演算法如下所示：

1.  一旦快取包含設定的專案數（800），我們就會開始定期（每分鐘一次）掃描快取的計時器。
2.  在快取掃描期間，會以 LFRU （最不常使用）為基礎，從快取中移除專案。 在決定要彈出哪些專案時，此演算法會將 [計數] 和 [年齡] 納入考慮。
3.  在每個快取清除結束時，快取再次包含800個專案。

在決定要收回的專案時，會同等地處理所有快取專案。 這表示 CompiledQuery 的存放區命令與 Entity SQL 查詢的存放區命令具有相同的收回機會。

請注意，當快取中有800個實體時，快取收回計時器會在中啟動，但快取只會在此計時器開始之後掃描60秒。 這表示最多60秒，您的快取可能會變得相當大。

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>示範查詢計劃快取效能的3.2.3 測試度量

為了示範查詢計劃快取對應用程式效能的影響，我們執行了一項測試，讓我們針對 Navision 模型執行數個 Entity SQL 查詢。 如需 Navision 模型的描述，以及已執行之查詢的類型，請參閱附錄。 在這項測試中，我們會先逐一查看查詢清單，並執行每一次，將它們新增至快取（如果已啟用快取）。 此步驟為 untimed。 接下來，我們會睡眠超過60秒的主執行緒，以允許進行快取的掃描;最後，我們會逐一查看清單第2次，以執行快取的查詢。 此外，在執行每一組查詢之前，會先清除 SQL Server 計畫快取，讓我們準確地反映查詢計劃快取所提供的好處。

##### <a name="3231-test-results"></a>3.2.3.1 測試結果

| 測試                                                                   | EF5 無快取 | EF5 快取 | EF6 無快取 | EF6 快取 |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| 列舉所有18723查詢                                          | 124          | 125.4      | 124.3        | 125.3      |
| 避免清理（僅限前800個查詢，不論複雜度為何）  | 41.7         | 5.5        | 40.5         | 5.4        |
| 只有 AggregatingSubtotals 查詢（總共178個-這可避免清除） | 39.5         | 4.5        | 38.1         | 4.6        |

*所有時間（以秒為單位）。*

道德-執行許多不同的查詢（例如，動態建立的查詢）時，快取不會有説明，而所產生的快取清除，可以讓從計畫快取中獲益最多的查詢，從實際使用它。

AggregatingSubtotals 查詢是我們用來測試的最複雜查詢。 如預期，查詢越複雜，您就可以從查詢計劃快取中看到更多的好處。

由於 CompiledQuery 其實是已快取其計畫的 LINQ 查詢，因此 CompiledQuery 與對等 Entity SQL 查詢的比較應該會有類似的結果。 事實上，如果應用程式有很多動態 Entity SQL 查詢，則使用查詢來填入快取，也會在從快取中排清時，有效地導致 CompiledQueries 「反編譯」。 在此案例中，您可以藉由停用動態查詢的快取來設定 CompiledQueries 的優先順序，藉此改善效能。 更棒的是，要重寫應用程式以使用參數化查詢，而不是動態查詢。

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 使用 CompiledQuery 來改善 LINQ 查詢的效能

我們的測試指出使用 CompiledQuery 可享有 7% over autocompiled LINQ 查詢的優點;這表示您會花費 7% 的時間來執行 Entity Framework 堆疊的程式碼;這並不表示您的應用程式速度將會加快 7%。 一般來說，相較于優點，在 EF 5.0 中寫入和維護 CompiledQuery 物件的成本可能不是值得一提的問題。 您的里程可能會有所不同，因此如果您的專案需要額外的推送，請執行此選項。 請注意，CompiledQueries 只與 ObjectCoNtext 衍生模型相容，而且與 DbCoNtext 衍生模型不相容。

如需有關建立和叫用 CompiledQuery 的詳細資訊，請參閱 <<c0> [編譯的查詢 (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx)。

使用 CompiledQuery 時，您必須採取兩個考慮，也就是使用靜態實例的需求，以及它們與複合性的問題。 以下是這兩個考慮的深入說明。

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 使用靜態 CompiledQuery 實例

因為編譯 LINQ 查詢是非常耗時的程式，所以我們不想要在每次需要從資料庫提取資料時執行此作業。 CompiledQuery 實例可讓您編譯一次並執行多次，但您必須謹慎和採購，以便每次都重複使用相同的 CompiledQuery 實例，而不是重新編譯。 若要使用靜態成員來儲存 CompiledQuery 實例，就必須這麼做;否則，您將不會看到任何權益。

例如，假設您的頁面具有下列方法主體，可處理顯示所選類別目錄的產品：

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

在此情況下，您會在每次呼叫方法時，即時建立新的 CompiledQuery 實例。 CompiledQuery 會在每次建立新的實例時通過計畫編譯器，而不是從查詢計劃快取中抓取 store 命令來查看效能優勢。 事實上，每次呼叫方法時，您都會以新的 CompiledQuery 專案來污染您的查詢計劃快取。

相反地，您會想要建立已編譯查詢的靜態實例，因此每次呼叫方法時，都會叫用相同的已編譯查詢。 這麼做的方法之一，是將 CompiledQuery 實例新增為物件內容的成員。  接著，您可以透過 helper 方法來存取 CompiledQuery，讓事情更清楚明瞭：

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

此 helper 方法的叫用方式如下：

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 在 CompiledQuery 上撰寫

撰寫任何 LINQ 查詢的功能非常有用;若要這樣做，您只需在 IQueryable 之後叫用方法，例如*Skip （）* 或*Count （）* 。 不過，這麼做基本上會傳回新的 IQueryable 物件。 雖然在技術上不會讓您撰寫 CompiledQuery，但這樣做會產生新的 IQueryable 物件，而需要再次傳遞計畫編譯器。

有些元件會利用組合的 IQueryable 物件來啟用先進的功能。 例如，ASP。NET 的 GridView 可以透過 SelectMethod 屬性，透過資料系結至 IQueryable 物件。 然後 GridView 會透過此 IQueryable 物件撰寫，以允許對資料模型進行排序和分頁。 如您所見，使用 GridView 的 CompiledQuery 並不會叫用編譯過的查詢，而是會產生新的 autocompiled 查詢。

您可能遇到這種情況的一個地方就是將漸進式篩選加入查詢時。 例如，假設您有一個 [客戶] 頁面，其中包含多個選擇性篩選的下拉式清單（例如 Country 和 OrdersCount）。 您可以對 CompiledQuery 的 IQueryable 結果撰寫這些篩選器，但這樣做會導致新的查詢在每次執行時都通過計畫編譯器。

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

 若要避免這種重新編譯，您可以重寫 CompiledQuery，將可能的篩選準則納入考慮：

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

這會在 UI 中叫用，如下所示：

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

 這裡的缺點是，產生的存放區命令一律會有具有 null 檢查的篩選準則，但資料庫伺服器要優化這些動作應該相當簡單：

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 中繼資料快取

Entity Framework 也支援中繼資料快取。 這基本上是快取型別資訊，以及與相同模型的不同連接之間的型別對資料庫對應資訊。 中繼資料快取在每個 AppDomain 中都是唯一的。

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 中繼資料快取演算法

1.  模型的中繼資料資訊會儲存在每個 EntityConnection 的 ItemCollection 中。
    -   請注意，模型的不同部分會有不同的 ItemCollection 物件。 例如，StoreItemCollections 包含資料庫模型的相關資訊。ObjectItemCollection 包含資料模型的相關資訊;EdmItemCollection 包含概念模型的相關資訊。

2.  如果兩個連接使用相同的連接字串，則會共用相同的 ItemCollection 實例。
3.  功能相同但以不同的方式連接字串，可能會導致不同的中繼資料快取。 我們會 token 化連接字串，因此只要變更權杖的順序，就會產生共用的中繼資料。 但是看起來相同的兩個連接字串在 token 化之後可能不會被評估為相同。
4.  ItemCollection 會定期檢查以供使用。 如果判斷出最近尚未存取的工作區，則會在下一次快取清除時將其標示為清理。
5.  只要建立 EntityConnection，就會建立中繼資料快取（不過，在開啟連接之前，其中的專案集合將不會初始化）。 此工作區會保留在記憶體中，直到快取演算法判斷其不是「使用中」為止。

客戶諮詢小組已撰寫描述保存 ItemCollection 的參考，以使用大型模型時，避免 「 取代 」 的部落格文章： \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx> 。

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 中繼資料快取與查詢計劃快取之間的關聯性

查詢計劃快取實例存在於 MetadataWorkspace 的存放區類型 ItemCollection 中。 這表示會使用快取的存放區命令，針對使用指定 MetadataWorkspace 具現化的任何內容進行查詢。 這也表示，如果您的兩個連接字串稍有不同，而且在 token 化之後並不相符，您將會有不同的查詢計劃快取實例。

### <a name="35-results-caching"></a>3.5 結果快取

使用結果快取（也稱為「第二層快取」），您可以將查詢的結果保留在本機快取中。 發出查詢時，您會先看到結果是否可在本機上使用，然後再對存放區進行查詢。 雖然 Entity Framework 不直接支援結果快取，但可以使用包裝提供者來新增第二層快取。 使用第二層快取的範例包裝提供者是 Alachisoft 的[Entity Framework 第二層](https://www.alachisoft.com/ncache/entity-framework.html)快取（以 NCache 為基礎）。

第二層快取的執行是插入的功能，它會在評估 LINQ 運算式之後發生（和 funcletized），並從第一層快取計算或抓取查詢執行計畫。 第二層快取只會儲存原始資料庫結果，因此具體化管線之後仍會執行。

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>使用包裝提供者3.5.1 結果快取的其他參考

-   Julie Lerman 已在 Entity Framework 和 Windows Azure 中撰寫了「第二層快取」 MSDN 文章，其中包括如何更新範例包裝提供者以使用 Windows Server AppFabric 快取： [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   如果您正在使用 Entity Framework 5，在小組部落格有某篇文章說明如何使用 Entity Framework 5 的快取提供者執行的工作： \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx> 。 它也包含 T4 範本，可協助自動化將第二層快取新增至您的專案。

## <a name="4-autocompiled-queries"></a>4 Autocompiled 查詢

使用 Entity Framework 針對資料庫發出查詢時，必須經過一系列的步驟，才能實際將結果具體化。其中一個步驟是查詢編譯。 已知 Entity SQL 查詢會有良好的效能，因為它們會自動快取，因此當您執行相同的查詢時，第二個或第三次會略過計畫編譯器，並改為使用快取的計畫。

Entity Framework 5 也為 LINQ to Entities 的查詢引進了自動快取。 在過去的版本中 Entity Framework 建立 CompiledQuery 來加速效能是常見的作法，因為這會讓您的 LINQ to Entities 查詢得以快取。 因為快取現在會自動完成，而不使用 CompiledQuery，所以我們稱之為「autocompiled 查詢」這項功能。 如需查詢計劃快取和其機制的詳細資訊，請參閱查詢計劃快取。

Entity Framework 會偵測何時需要重新編譯查詢，並在叫用查詢時執行，即使先前已編譯過。 導致重新編譯查詢的常見狀況如下：

-   變更與查詢相關聯的 MergeOption。 將不會使用快取的查詢，而是會重新執行計畫編譯器，並快取新建立的計畫。
-   變更 CoNtextoptions.negotiate. UseCSharpNullComparisonBehavior 的值。 您會得到與變更 MergeOption 相同的效果。

其他條件可能會讓您的查詢無法使用快取。 常見的範例包括：

-   Using IEnumerable @ no__t-0T @ no__t-1。包含 @ no__t-2 @ no__t-3 （T 值）。
-   使用會產生具有常數之查詢的函數。
-   使用非對應物件的屬性。
-   將您的查詢連結到需要重新編譯的另一個查詢。

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 Using IEnumerable @ no__t-0T @ no__t-1。包含 @ no__t-2T @ no__t-3 （T 值）

Entity Framework 不會快取叫用 IEnumerable @ no__t-0T @ no__t-1 的查詢。會針對記憶體中的集合包含 @ no__t-2T @ no__t-3 （T 值），因為集合的值會被視為 volatile。 下列範例查詢將不會快取，因此它一律會由計畫編譯器進行處理：

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

請注意，執行包含的 IEnumerable 大小會決定查詢的速度或速度。 使用大型集合（如上述範例所示）時，效能可能會大幅降低。

Entity Framework 6 包含 IEnumerable @ no__t-0T @ no__t-1 方法的優化。包含 @ no__t-2T @ no__t-3 （T 值）在執行查詢時運作。 產生的 SQL 程式碼速度會更快，且更容易閱讀，而且在大部分情況下，在伺服器中執行的速度也會更快。

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 使用會產生常數查詢的函式

Skip （）、Take （）、Contains （）和 DefautIfEmpty （） LINQ 運算子不會產生具有參數的 SQL 查詢，而是將值傳遞給它們做為常數。 因此，可能是完全相同的查詢最後會污染查詢計劃快取（在 EF 堆疊和資料庫伺服器上），而且除非在後續的查詢執行中使用相同的常數，否則不會取得 reutilized。 例如:

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

在此範例中，每次執行此查詢時，都會有不同的 id 值，查詢將會編譯成新的計畫。

特別注意，在進行分頁時使用 Skip 和 Take。 在 EF6 中，這些方法有一個 lambda 多載，可有效地讓快取的查詢計劃可重複使用，因為 EF 可以捕捉傳遞給這些方法的變數，並將它們轉譯成 SQLparameters。 這也有助於保留快取清除工具，因為否則 Skip 和 Take 的每個查詢都有不同的常數會取得自己的查詢計劃快取專案。

請考慮下列程式碼，這是最理想的，但僅適用于範例說明點此類別的查詢：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

這個相同程式碼的更快速版本會牽涉到使用 lambda 呼叫 Skip：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

第二個程式碼片段的執行速度最多可達 11%，因為每次執行查詢時都會使用相同的查詢計劃，這樣可節省 CPU 時間並避免查詢快取污染。 此外，因為要略過的參數是在結束時，程式碼現在可能也會如下所示：

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 使用非對應物件的屬性

當查詢使用非對應物件類型的屬性做為參數時，查詢將不會被快取。 例如:

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

在此範例中，假設類別 NonMappedType 不是實體模型的一部分。 您可以輕鬆地將此查詢變更為不使用非對應的類型，並改為使用區域變數做為查詢的參數：

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

在此情況下，查詢將能夠進行快取，而且將受益于查詢計劃快取。

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 連結至需要重新編譯的查詢

遵循與上述相同的範例，如果您有第二個查詢需要重新編譯的查詢，則整個第二個查詢也會重新編譯。 以下是說明此案例的範例：

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

此範例是泛型的，但它會說明連結至 firstQuery 如何導致 secondQuery 無法快取。 如果 firstQuery 尚未是需要重新編譯的查詢，則會快取 secondQuery。

## <a name="5-notracking-queries"></a>5個 NoTracking 查詢

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 停用變更追蹤以降低狀態管理額外負荷

如果您是在唯讀案例中，而且想要避免將物件載入至 ObjectStateManager 的額外負荷，您可以發出「沒有追蹤」查詢。  您可以在查詢層級停用變更追蹤。

請注意，藉由停用變更追蹤，您就可以有效地關閉物件快取。 當您查詢實體時，我們無法從 ObjectStateManager 提取先前具體化的查詢結果，以略過具體化。 如果您要在相同的內容上重複查詢相同的實體，可能實際上會看到啟用變更追蹤的效能優勢。

使用 ObjectCoNtext 進行查詢時，ObjectQuery 和 ObjectSet 實例會在設定好之後記住 MergeOption，而在其上組成的查詢將會繼承父查詢的有效 MergeOption。 使用 DbCoNtext 時，可以藉由呼叫 DbSet 上的 AsNoTracking （）修飾詞來停用追蹤。

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 使用 DbCoNtext 時停用查詢的變更追蹤

您可以藉由連結查詢中 AsNoTracking （）方法的呼叫，將查詢的模式切換成 NoTracking。 不同于 ObjectQuery，DbCoNtext API 中的 DbSet 和 DbQuery 類別沒有 MergeOption 的可變屬性。

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 使用 ObjectCoNtext 在查詢層級停用變更追蹤

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 使用 ObjectCoNtext 來停用整個實體集的變更追蹤

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 測試計量示範 NoTracking 查詢的效能優點

在這項測試中，我們將探討藉由比較追蹤與 Navision 模型的 NoTracking 查詢來填滿 ObjectStateManager 的成本。 如需 Navision 模型的描述，以及已執行之查詢的類型，請參閱附錄。 在這項測試中，我們會逐一查看查詢清單，並執行一次。 我們執行了兩種測試變化，一次是使用 NoTracking 查詢，另一次是使用 "AppendOnly" 的預設合併選項。 我們已執行每個變化3次，並採用執行的平均值。 在測試之間，我們會清除 SQL Server 上的查詢快取，並執行下列命令來壓縮 tempdb：

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE （tempdb，0）

測試結果，執行超過3的中間值：

|                        | 無追蹤-工作集 | 無追蹤–時間 | 僅附加-工作集 | 僅附加–時間 |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536毫秒         | 596545536                 | 1273042毫秒         |
| **Entity Framework 6** | 647127040                 | 190228毫秒          | 832798720                 | 195521毫秒          |

Entity Framework 5 在執行結束時，會有較小的記憶體使用量，而不是 Entity Framework 6。 Entity Framework 6 所耗用的額外記憶體，是提供新功能和更佳效能的額外記憶體結構和程式碼所造成的結果。

使用 ObjectStateManager 時，記憶體使用量也有清楚的差異。 Entity Framework 5 在追蹤我們從資料庫具體化的所有實體時，會增加 30% 的使用量。 Entity Framework 6 這麼做時，增加了 28% 的使用量。

在這段時間內，Entity Framework 6 在這項測試中的 Entity Framework 5 會以較大的邊界來勝過。 Entity Framework 6 已在 Entity Framework 5 所耗用的大約 16% 的時間內完成測試。 此外，當使用 ObjectStateManager 時，Entity Framework 5 會花費 9% 以上的時間來完成。 相較之下，使用 ObjectStateManager 時，Entity Framework 6 使用了 3% 的時間。

## <a name="6-query-execution-options"></a>6查詢執行選項

Entity Framework 提供數種不同的查詢方式。 我們將探討下列選項、比較各項的優缺點，並檢查其效能特性：

-   LINQ to Entities。
-   沒有追蹤 LINQ to Entities。
-   透過 ObjectQuery 的 Entity SQL。
-   透過 EntityCommand 的 Entity SQL。
-   ExecuteStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6.1 LINQ to Entities 查詢

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**展開**

-   適用于 CUD 作業。
-   完全具體化的物件。
-   最簡單的方式是以程式設計語言內建的語法撰寫。
-   良好的效能。

**各有利弊**

-   某些技術限制，例如：
    -   使用 DefaultIfEmpty 進行外部聯結查詢的模式會導致比 Entity SQL 中的簡單外部聯結語句更複雜的查詢。
    -   您仍然無法搭配一般模式比對使用 LIKE。

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 沒有追蹤 LINQ to Entities 查詢

當內容衍生 ObjectCoNtext 時：

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

當內容衍生 DbCoNtext 時：

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**展開**

-   透過一般 LINQ 查詢改善效能。
-   完全具體化的物件。
-   最簡單的方式是以程式設計語言內建的語法撰寫。

**各有利弊**

-   不適用於 CUD 作業。
-   某些技術限制，例如：
    -   使用 DefaultIfEmpty 進行外部聯結查詢的模式會導致比 Entity SQL 中的簡單外部聯結語句更複雜的查詢。
    -   您仍然無法搭配一般模式比對使用 LIKE。

請注意，即使未指定 NoTracking，也不會追蹤專案純量屬性的查詢。 例如:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

此特定查詢並未明確指定為 NoTracking，但因為它不會具體化物件狀態管理員已知的類型，所以不會追蹤具體化結果。

### <a name="63-entity-sql-over-an-objectquery"></a>ObjectQuery 上的 6.3 Entity SQL

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**展開**

-   適用于 CUD 作業。
-   完全具體化的物件。
-   支援查詢計劃快取。

**各有利弊**

-   包含文字查詢字串，其比語言內建的查詢結構更容易發生使用者錯誤。

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 Entity SQL 透過實體命令

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

**展開**

-   支援 .NET 4.0 中的查詢計劃快取（.NET 4.5 中的所有其他查詢類型都支援計畫快取）。

**各有利弊**

-   包含文字查詢字串，其比語言內建的查詢結構更容易發生使用者錯誤。
-   不適用於 CUD 作業。
-   結果不會自動具體化，而且必須從資料讀取器讀取。

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery 和 ExecuteStoreQuery

資料庫上的 SqlQuery：

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

DbSet 上的 SqlQuery：

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStoreQuery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**展開**

-   因為已略過計畫編譯器，所以通常是最快的效能。
-   完全具體化的物件。
-   適用于從 DbSet 使用時的 CUD 作業。

**各有利弊**

-   查詢是文字且容易出錯。
-   查詢系結至特定後端，方法是使用存放區的語義，而不是概念性的語義。
-   當繼承存在時，手工打造 query 必須考慮所要求之類型的對應條件。

### <a name="66-compiledquery"></a>6.6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**展開**

-   透過一般 LINQ 查詢，提供最高 7% 的效能改進。
-   完全具體化的物件。
-   適用于 CUD 作業。

**各有利弊**

-   增加複雜度和程式設計負荷。
-   在編譯的查詢之上撰寫時，效能改進會遺失。
-   有些 LINQ 查詢無法以 CompiledQuery 的形式寫入，例如匿名型別的預測。

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 不同查詢選項的效能比較

簡單的 microbenchmarks，其中的內容建立並未計時，會放入測試中。 我們針對受控制環境中的一組非快取實體，測量了5000次的查詢。 這些數位是以警告來取得：它們不會反映應用程式所產生的實際數目，而是在比較不同的查詢選項時，會非常精確地測量效能差異的程度。蘋果到蘋果，不包括建立新內容的成本。

| EF  | 測試                                 | 時間（毫秒） | 記憶體   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectCoNtext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectCoNtext Linq 查詢             | 2692      | 38277120 |
| EF5 | DbCoNtext Linq 查詢沒有追蹤     | 2818      | 41840640 |
| EF5 | DbCoNtext Linq 查詢                 | 2930      | 41771008 |
| EF5 | ObjectCoNtext Linq 查詢沒有追蹤 | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectCoNtext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectCoNtext Linq 查詢             | 3074      | 45248512 |
| EF6 | DbCoNtext Linq 查詢沒有追蹤     | 3125      | 47575040 |
| EF6 | DbCoNtext Linq 查詢                 | 3420      | 47652864 |
| EF6 | ObjectCoNtext Linq 查詢沒有追蹤 | 3593      | 45260800 |

![EF5 微基準測試，5000暖反復專案](~/ef6/media/ef5micro5000warm.png)

![EF6 微基準測試，5000暖反復專案](~/ef6/media/ef6micro5000warm.png)

Microbenchmarks 對程式碼中的小型變更非常敏感。 在此情況下，Entity Framework 5 和 Entity Framework 6 的成本差異，是因為新增了[攔截](~/ef6/fundamentals/logging-and-interception.md)和交易的改良[功能](~/ef6/saving/transactions.md)。 不過，這些 microbenchmarks 的數位是 Entity Framework 的一小部分， 從 Entity Framework 5 升級至 Entity Framework 6 時，經常性查詢的實際案例應該不會看到效能衰退。

為了比較不同查詢選項的實際效能，我們建立了5個不同的測試變化，讓我們使用不同的查詢選項來選取類別目錄名稱為「飲料」的所有產品。 每個反復專案都包含建立內容的成本，以及將所有傳回的實體具體化的成本。 在取得1000計時反復專案總和之前，會執行10次反覆運算 untimed。 顯示的結果是從每個測試的5個回合中執行的中間值。 如需詳細資訊，請參閱附錄 B，其中包含測試的程式碼。

| EF  | 測試                                        | 時間（毫秒） | 記憶體   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | ObjectCoNtext 實體命令                | 621       | 39350272 |
| EF5 | 在資料庫上 DbCoNtext Sql 查詢             | 825       | 37519360 |
| EF5 | ObjectCoNtext 存放區查詢                   | 878       | 39460864 |
| EF5 | ObjectCoNtext Linq 查詢沒有追蹤        | 969       | 38293504 |
| EF5 | 使用物件查詢的 ObjectCoNtext Entity Sql | 1089      | 38981632 |
| EF5 | ObjectCoNtext 已編譯查詢                | 1099      | 38682624 |
| EF5 | ObjectCoNtext Linq 查詢                    | 1152      | 38178816 |
| EF5 | DbCoNtext Linq 查詢沒有追蹤            | 1208      | 41803776 |
| EF5 | DbSet 上的 DbCoNtext Sql 查詢                | 1414      | 37982208 |
| EF5 | DbCoNtext Linq 查詢                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | ObjectCoNtext 實體命令                | 480       | 47247360 |
| EF6 | ObjectCoNtext 存放區查詢                   | 493       | 46739456 |
| EF6 | 在資料庫上 DbCoNtext Sql 查詢             | 614       | 41607168 |
| EF6 | ObjectCoNtext Linq 查詢沒有追蹤        | 684       | 46333952 |
| EF6 | 使用物件查詢的 ObjectCoNtext Entity Sql | 767       | 48865280 |
| EF6 | ObjectCoNtext 已編譯查詢                | 788       | 48467968 |
| EF6 | DbCoNtext Linq 查詢沒有追蹤            | 878       | 47554560 |
| EF6 | ObjectCoNtext Linq 查詢                    | 953       | 47632384 |
| EF6 | DbSet 上的 DbCoNtext Sql 查詢                | 1023      | 41992192 |
| EF6 | DbCoNtext Linq 查詢                        | 1290      | 47529984 |


![EF5 暖查詢1000反覆運算](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查詢1000反覆運算](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> 為求完整性，我們包含在 EntityCommand 上執行 Entity SQL 查詢的變化。 不過，因為這類查詢不會具體化結果，所以比較不一定是蘋果對蘋果。 測試包括要具體化的收盤近似值，以嘗試進行比較 fairer。

在此端對端案例中，由於堆疊的幾個部分的效能改進，包括較輕的 DbCoNtext 初始化，以及更快的 MetadataCollection @ no__t-0T @ no__t-1 查閱，因此 Entity Framework 6 勝過 Entity Framework 5。

## <a name="7-design-time-performance-considerations"></a>7設計階段效能考慮

### <a name="71-inheritance-strategies"></a>7.1 繼承策略

使用 Entity Framework 時的另一個效能考慮是您所使用的繼承策略。 Entity Framework 支援3種基本類型的繼承和其組合：

-   每個階層的資料表（TPH）–其中每個繼承集都對應到具有鑒別子資料行的資料表，以指出階層中的哪一個特定類型要在資料列中表示。
-   每個類型的資料表（TPT）–其中每個類型在資料庫中都有自己的資料表;子資料工作表只會定義父資料表不包含的資料行。
-   每個類別的資料表（TPC）–其中每個類型在資料庫中都有自己的完整資料表。子資料工作表會定義其所有欄位，包括在父類型中定義的欄位。

如果您的模型使用 TPT 繼承，則產生的查詢會比使用其他繼承策略產生的查詢更為複雜，這可能會導致存放區上的執行時間較長。  通常會花較長的時間來產生 TPT 模型的查詢，並具體化產生的物件。

請參閱的 < 效能考量當 Entity Framework 中使用 （每個類型的資料表） TPT 繼承 > MSDN 部落格文章： \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx> 。

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 避免在 Model First 或 Code First 應用程式中進行 TPT

當您透過具有 TPT 架構的現有資料庫建立模型時，您不會有許多選項。 但是，使用 Model First 或 Code First 建立應用程式時，您應該避免 TPT 繼承以進行效能考慮。

當您使用 Entity Designer Wizard 中的 Model First 時，您會取得模型中任何繼承的 TPT。 如果您想要切換成使用 Model First TPH 繼承策略，您可以使用 「 實體設計工具資料庫產生 Power Pack 」 可從 Visual Studio 組件庫 ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>) 。

當您使用 Code First 設定具有繼承的模型對應時，EF 預設會使用 TPH，因此，繼承階層架構中的所有實體都會對應至相同的資料表。 請參閱 MSDN Magazine 中的 「 程式碼第一次在實體 Framework4.1"發行項"對應使用 Fluent API 」 一節 ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) 如需詳細資訊。

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 從 EF4 升級以改善模型產生時間

在 Entity Framework 5 和6中可取得產生模型之存放層（SSDL）之演算法的 SQL Server 特定改進，並在安裝 Visual Studio 2010 SP1 時，做為 Entity Framework 4 的更新。 下列測試結果示範產生非常大的模型時的改進，在此案例中為 Navision 模型。 如需更多詳細資料，請參閱附錄 C。

此模型包含1005個實體集和4227個關聯集。

| 組態                              | 已耗用時間的細目                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010，Entity Framework 4     | SSDL 產生：2小時27分鐘 <br/> 對應產生：1秒 <br/> CSDL 產生：1秒 <br/> ObjectLayer 產生：1秒 <br/> 視圖產生：2小時14分鐘 |
| Visual Studio 2010 SP1，Entity Framework 4 | SSDL 產生：1秒 <br/> 對應產生：1秒 <br/> CSDL 產生：1秒 <br/> ObjectLayer 產生：1秒 <br/> 視圖產生：1小時53分鐘   |
| Visual Studio 2013，Entity Framework 5     | SSDL 產生：1秒 <br/> 對應產生：1秒 <br/> CSDL 產生：1秒 <br/> ObjectLayer 產生：1秒 <br/> 視圖產生：65分鐘    |
| Visual Studio 2013，Entity Framework 6     | SSDL 產生：1秒 <br/> 對應產生：1秒 <br/> CSDL 產生：1秒 <br/> ObjectLayer 產生：1秒 <br/> 視圖產生：28秒。   |


值得注意的是，當產生 SSDL 時，負載幾乎會花在 SQL Server 上，而用戶端開發機器則是等待閒置，讓結果回到伺服器。 Dba 應該特別感謝這方面的改進。 另外值得一提的是，基本上，模型產生的整體成本會在現在的視圖產生中進行。

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 使用 Database First 和 Model First 分割大型模型

隨著模型大小的增加，設計介面會變得雜亂，而且很容易使用。 我們通常會考慮具有超過300個實體的模型太大，而無法有效使用設計工具。 下列部落格文章說明用來分割大型模型的數個選項： \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx> 。

文章是針對第一個 Entity Framework 版本所撰寫，但仍適用這些步驟。

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 實體資料來源控制項的效能考慮

我們已瞭解多執行緒效能和壓力測試中的案例，其中使用 EntityDataSource 控制項的 web 應用程式效能大幅 deteriorates。 根本原因在於，EntityDataSource 會在 Web 應用程式所參考的元件上重複呼叫 MetadataWorkspace. LoadFromAssembly，以探索要當做實體使用的類型。

解決方法是將 EntityDataSource 的 CoNtextTypeName 設定為衍生 ObjectCoNtext 類別的型別名稱。 這會關閉掃描實體類型之所有參考元件的機制。

設定 CoNtextTypeName 欄位也可防止 .NET 4.0 中的 EntityDataSource 在無法透過反映從元件載入型別時擲回 ReflectionTypeLoadException 的功能問題。 此問題已在 .NET 4.5 中修正。

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 POCO 實體和變更追蹤 proxy

Entity Framework 可讓您將自訂資料類別與您的資料模型搭配使用，而不需要對資料類別本身進行任何修改。 這表示您可以使用「單純」(plain-old) CLR 物件 (POCO)，例如現有的網域物件，加上您的資料模型。 這些 POCO 資料類別（也稱為非持續性物件）對應至資料模型中所定義的實體，可支援與實體資料模型工具所產生之實體類型相同的查詢、插入、更新和刪除行為。

Entity Framework 也可以建立衍生自 POCO 類型的 proxy 類別，當您想要在 POCO 實體上啟用消極式載入和自動變更追蹤之類的功能時，就會使用它們。 您的 POCO 類別必須符合特定需求，才能讓 Entity Framework，才可使用 proxy，如下所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。

每當實體的任何屬性變更其值時，追蹤 proxy 就會通知物件狀態管理員，Entity Framework 隨時得知實體的實際狀態。 這是藉由將通知事件加入至屬性的 setter 方法主體來完成，並讓物件狀態管理員處理這類事件。 請注意，由於 Entity Framework 所建立的新增事件集，建立 proxy 實體通常會比建立非 proxy POCO 實體更昂貴。

當 POCO 實體沒有變更追蹤 proxy 時，會藉由比較實體的內容與先前儲存的狀態複本來找到變更。 當您的內容中有許多實體，或當您的實體有非常大量的屬性時（即使自上次進行比較之後，它們都不會變更），此深層比較會變得很冗長。

摘要說明：當您建立變更追蹤 proxy 時，將會對效能造成的影響，但當您的實體有許多屬性或您的模型中有多個實體時，變更追蹤將可協助您加速變更偵測程式。 對於具有少數屬性的實體，其中實體數量不會變得太多，具有變更追蹤 proxy 的好處可能不多。

## <a name="8-loading-related-entities"></a>8載入相關實體

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 消極式載入與積極式載入

Entity Framework 提供幾種不同的方式來載入與您的目標實體相關的實體。 例如，當您查詢產品時，會有不同的方式可將相關的訂單載入物件狀態管理員。 從效能的觀點來看，載入相關實體時要考慮的最大問題，就是要使用消極式載入還是積極式載入。

使用積極式載入時，相關的實體會與您的目標實體集一併載入。 您在查詢中使用 Include 語句，以指出您想要帶入哪些相關實體。

使用消極式載入時，您的初始查詢只會帶入目標實體集。 但是每當您存取導覽屬性時，就會對存放區發出另一個查詢，以載入相關的實體。

載入實體之後，任何進一步的實體查詢都會直接從物件狀態管理員載入，不論您使用的是消極式載入或積極式載入。

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 如何選擇消極式載入和積極式載入

重要的是，您瞭解消極式載入和立即載入之間的差異，讓您可以為應用程式做出正確的選擇。 這可協助您評估對資料庫的多個要求與可能包含大型承載的單一要求之間的取捨。 可能適合在應用程式的某些部分使用積極式載入，並在其他部分中進行消極式載入。

舉例來說，假設您想要查詢居住在英國的客戶及其訂單計數，這就是一個例子。

**使用積極式載入**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**使用消極式載入**

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

使用積極式載入時，您會發出傳回所有客戶和所有訂單的單一查詢。 Store 命令看起來像這樣：

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

使用消極式載入時，您會一開始就發出下列查詢：

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

而且每次您存取客戶的 Orders 導覽屬性時，都會針對該商店發出另一個類似下列的查詢：

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

如需詳細資訊，請參閱[載入相關物件](https://msdn.microsoft.com/library/bb896272.aspx)。

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 消極式載入與積極式載入功能提要

其中沒有任何一種適用于選擇積極式載入和消極式載入的東西。 請先嘗試瞭解這兩種策略之間的差異，讓您可以進行明智的決策;此外，請考慮您的程式碼是否符合下列任一案例：

| 狀況                                                                    | 我們的建議                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 您需要從提取的實體存取許多導覽屬性嗎？ | **否**-這兩個選項都可能會這麼做。 不過，如果您的查詢所帶入的承載不太大，您可能會因為使用立即載入而遇到效能上的好處，因為它需要較少的網路往返，才能具體化您的物件。 <br/> <br/> **是**-如果您需要從實體存取許多導覽屬性，您可以在查詢中使用多個 include 語句搭配積極式載入來執行此動作。 您所包含的實體越多，您的查詢將會傳回的承載越大。 一旦您在查詢中包含三個或多個實體，請考慮切換為消極式載入。 |
| 您是否知道在執行時間需要哪些資料？                   | **否**-延遲載入會更適合您。 否則，您最後可能會查詢不需要的資料。 <br/> <br/> **是**-積極式載入可能是最好的選擇;這將有助於更快速地載入整個集合。 如果您的查詢需要提取非常大量的資料，而這變得太慢，請改為嘗試消極式載入。                                                                                                                                                                                                                                                       |
| 您的程式碼在您的資料庫中執行得很遠嗎？ （網路延遲增加）  | **否**-當網路延遲不是問題時，使用消極式載入可能會簡化您的程式碼。 請記住，您的應用程式拓撲可能會變更，因此不會將資料庫的鄰近性授與。 <br/> <br/> **是**-當網路發生問題時，只有您可以決定適合您案例的內容為何。 通常積極式載入會比較好，因為它需要較少的來回行程。                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>使用多個包含的8.2.2 效能考慮

當我們聽到涉及伺服器回應時間問題的效能問題時，問題的來源經常是使用多個 Include 語句來查詢。 雖然在查詢中包含相關實體很強大，但請務必瞭解幕後的情況。

具有多個 Include 語句的查詢會花相當長的時間來執行內部計畫編譯器，以產生 store 命令。 這段期間的大部分時間都花在嘗試優化產生的查詢。 根據您的對應而定，產生的存放區命令將包含每個包含的外部聯結或聯集。 這類查詢會在單一裝載中帶入來自您資料庫的大型連線圖形，這會 acerbate 任何頻寬問題，尤其是在承載中有很多的冗余時（例如，當您使用多個包含層級來進行遍歷時）。一對多方向的關聯）。

您可以使用 ToTraceString 並在 SQL Server Management Studio 中執行存放區命令來查看裝載大小，藉以檢查查詢是否傳回過大的承載的情況。 在這種情況下，您可以嘗試減少查詢中的 Include 語句數目，只帶入您所需的資料。 或者，您可以將查詢分成較小的子查詢順序，例如：

**在中斷查詢之前：**

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

**中斷查詢之後：**

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

這僅適用于追蹤的查詢，因為我們會利用內容必須自動執行識別解析和關聯修復的功能。

如同消極式載入，代價是較小的承載會有更多的查詢。 您也可以使用個別屬性的投影，從每個實體明確選取您需要的資料，但在此情況下，您將不會載入實體，而且不會支援更新。

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 因應措施以取得屬性的消極式載入

Entity Framework 目前不支援純量或複雜屬性的消極式載入。 不過，如果您的資料表包含大型物件（例如 BLOB），您可以使用資料表分割，將大型屬性分隔成不同的實體。 例如，假設您有一個包含 Varbinary 相片資料行的產品資料表。 如果您不常需要在查詢中存取此屬性，您可以使用資料表分割，只帶入您通常需要的實體部分。 代表產品相片的實體只有在您明確需要時才會載入。

說明如何啟用資料表分割的絕佳資源是 Gil Fink 「 資料表分割中實體架構 」 部落格文章： \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx> 。

## <a name="9-other-considerations"></a>9其他考慮

### <a name="91-server-garbage-collection"></a>9.1 伺服器垃圾收集

某些使用者可能會遇到資源爭用，限制當垃圾收集行程未正確設定時，所預期的平行處理原則。 每當 EF 用於多執行緒案例中，或在類似伺服器端系統的任何應用程式中，請務必啟用伺服器垃圾收集。 這項作業是透過應用程式佈建檔中的簡單設定來完成：

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

這應該會減少執行緒爭用，並增加您的輸送量，最高可達 30% 的 CPU 飽和案例。 一般來說，您應該一律使用傳統垃圾收集（針對 UI 和用戶端案例較佳的微調）以及伺服器垃圾收集，來測試應用程式的行為。

### <a name="92-autodetectchanges"></a>9.2 AutoDetectChanges

如先前所述，當物件快取有許多實體時，Entity Framework 可能會顯示效能問題。 某些作業（例如新增、移除、尋找、輸入和 SaveChanges）會觸發對 DetectChanges 的呼叫，這可能會根據物件快取的大小而耗用大量的 CPU。 這是因為物件快取和物件狀態管理員在每個執行到內容的作業上都嘗試保持同步處理，因此在各種情況下，會保證產生的資料是正確的。

通常在應用程式的整個生命週期中，保留 Entity Framework 的自動變更偵測是個不錯的作法。 如果您的案例受到高 CPU 使用量的負面影響，且您的設定檔指出問題是 DetectChanges 的呼叫，請考慮暫時關閉程式碼敏感部分中的 AutoDetectChanges：

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

在關閉 AutoDetectChanges 之前，最好先瞭解這可能會導致 Entity Framework 失去追蹤實體所發生變更之特定資訊的能力。 如果處理錯誤，這可能會造成應用程式的資料不一致。 如需有關如何關閉 AutoDetectChanges 的詳細資訊，請閱讀\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/> 。

### <a name="93-context-per-request"></a>9.3 每個要求的內容

Entity Framework 的內容應該當做短期實例使用，以提供最佳的效能體驗。 內容應該是短期的，而且會被捨棄，因此，在可能的情況下，這類內容已實作為非常輕量且 reutilize 的中繼資料。 在 web 案例中，請務必記住這一點，而且沒有內容可以超過單一要求的持續時間。 同樣地，在非 web 案例中，應該根據您對 Entity Framework 中不同快取層級的瞭解來捨棄內容。 一般來說，您應該避免在應用程式的整個生命週期中有一個內容實例，以及每個執行緒和一個靜態內容的上下文。

### <a name="94-database-null-semantics"></a>9.4 資料庫 null 語義

Entity Framework 預設會產生具有 C @ no__t-0 null 比較語義的 SQL 程式碼。 請考慮下列範例查詢：

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

在此範例中，我們會將數個可為 null 的變數與實體上的可為 null 屬性進行比較，例如，已建立供應專案和單價。 針對此查詢產生的 SQL 會詢問參數值是否與資料行值相同，或者參數和資料行值是否為 null。 這會隱藏資料庫伺服器處理 null 的方式，並在不同的資料庫廠商之間提供一致的 C @ no__t-0 null 體驗。 相反地，產生的程式碼會有點複雜，而且當查詢的 where 語句中的比較量成長至大量時，可能無法順利執行。

處理這種情況的其中一種方式是使用資料庫 null 語義。 請注意，這可能會因 C @ no__t-0 null 語義而有不同的行為，因為現在 Entity Framework 會產生更簡單的 SQL，以公開資料庫引擎處理 null 值的方式。 針對內容設定，可以針對每個內容啟用資料庫 null 語義，其中單一設定行：

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

使用資料庫 null 語義時，小型到中型的查詢不會顯示明顯的效能改進，但是在具有大量潛在 null 比較的查詢上，其差異會變得更明顯。

在上述的範例查詢中，在受控制的環境中執行的 microbenchmark 中，效能差異小於 2%。

### <a name="95-async"></a>9.5 非同步

Entity Framework 6 在 .NET 4.5 或更新版本上執行時，引進了非同步作業的支援。 在大部分的情況下，具有 IO 相關爭用的應用程式將從使用非同步查詢和儲存作業中獲益最多。 如果您的應用程式不受 IO 爭用的影響，在最佳情況下，使用 async 會同步執行，並以同步呼叫的相同時間量傳回結果，或在最壞的情況下，只需順延強制非同步工作並新增額外的 time 完成您的案例。

如需如何非同步的程式設計工作可協助您決定是否非同步會改善您的應用程式的效能請瀏覽[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)。 如需在 Entity Framework 上使用非同步作業的詳細資訊，請參閱 @no__t 0Async Query 並儲存 @ no__t-1。

### <a name="96-ngen"></a>9.6 NGEN

Entity Framework 6 並未隨附于 .NET Framework 的預設安裝。 因此，Entity Framework 元件的預設值不是 NGEN，這表示所有的 Entity Framework 程式碼都受限於與其他任何 MSIL 元件相同的 JIT'ing 成本。 這可能會在開發時降低 F5 體驗，同時也會在生產環境中執行應用程式的冷啟動。 為了減少 JIT'ing 的 CPU 和記憶體成本，建議您視需要 NGEN Entity Framework 的影像。 如需如何使用 NGEN 改善 Entity Framework 6 之啟動效能的詳細資訊，請參閱[使用 Ngen 改善啟動效能](~/ef6/fundamentals/performance/ngen.md)。

### <a name="97-code-first-versus-edmx"></a>9.7 Code First 與 EDMX 的比較

在物件導向程式設計和關係資料庫之間，透過記憶體內部表示概念模型（物件）、儲存架構（資料庫）和兩者之間的對應，來 Entity Framework 有關阻抗不相符問題的原因兩個. 這個中繼資料稱為「實體資料模型」或簡稱「EDM」。 在此 EDM 中，Entity Framework 會衍生視圖，將記憶體中物件的資料來回往返資料庫。

當 Entity Framework 與正式指定概念模型、儲存架構和對應的 EDMX 檔案搭配使用時，模型載入階段只需要驗證 EDM 是否正確（例如，確定沒有遺漏對應），然後產生視圖，然後驗證視圖並備妥此中繼資料以供使用。 只有然後才可以執行查詢，或將新資料儲存到資料存放區。

Code First 方法是一種精密的實體資料模型產生器。 Entity Framework 必須從提供的程式碼產生 EDM;它會藉由分析包含在模型中的類別、套用慣例，以及透過流暢的 API 來設定模型，來完成此動作。 建立 EDM 之後，Entity Framework 基本上的行為會與專案中存在 EDMX 檔案的方式相同。 因此，從 Code First 建立模型會增加額外的複雜性，而這會轉譯成較慢的 Entity Framework 啟動時間（與具有 EDMX 相比）。 成本完全取決於所建立之模型的大小和複雜度。

選擇使用 EDMX 與 Code First 時，請務必知道 Code First 所引進的彈性，會增加第一次建立模型的成本。 如果您的應用程式可以承受此第一次負載的成本，通常 Code First 會是慣用的方式。

## <a name="10-investigating-performance"></a>10調查效能

### <a name="101-using-the-visual-studio-profiler"></a>10.1 使用 Visual Studio Profiler

如果您的 Entity Framework 發生效能問題，您可以使用類似 Visual Studio 內建的分析工具來查看應用程式花費時間的位置。 這是我們用來產生圓形圖 」 瀏覽 ADO.NET Entity Framework 的效能-第 1 部分 」 部落格文章中的工具 ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) 說明 Entity Framework 要花費在它在冷和暖查詢期間的時間。

「使用 Visual Studio 2010 Profiler 撰寫的分析 Entity Framework」的資料和模型化客戶諮詢小組會顯示如何使用 Profiler 來調查效能問題的真實世界範例。  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>. 這篇文章是針對 windows 應用程式所撰寫的。 如果您需要分析 web 應用程式，Windows 效能記錄檔（WPR）和 Windows Performance Analyzer （WPA）工具的運作效果可能會比 Visual Studio 工作更好。 WPR 和 WPA 屬於 Windows 效能工具組所附含的 Windows 評定及部署套件 ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982))。

### <a name="102-applicationdatabase-profiling"></a>10.2 應用程式/資料庫分析

像是內建在 Visual Studio 的工具，會告訴您應用程式花費時間的位置。  另一種分析工具可供使用，以根據需求在生產或預先生產環境中執行應用程式的動態分析，並尋找常見的錯誤和資料庫存取的反模式。

兩個販售的分析工具是 Entity Framework Profiler ( \<http://efprof.com>) ORMProfiler 和 ( \<http://ormprofiler.com>) 。

如果您的應用程式是使用 Code First 的 MVC 應用程式，您可以使用 Stackexchange.redis 的 Miniprofiler 撼動。 Scott Hanselman 說明這項工具在他的部落格： \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx> 。

如需分析應用程式資料庫活動的詳細資訊，請參閱 Julie Lerman 的 MSDN 雜誌文章，其標題為[Entity Framework 中的分析資料庫活動](https://msdn.microsoft.com/magazine/gg490349.aspx)。

### <a name="103-database-logger"></a>10.3 資料庫記錄器

如果您使用 Entity Framework 6 也請考慮使用內建的記錄功能。 您可以透過簡單的單行設定，指示內容的 Database 屬性來記錄其活動：

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

在此範例中，資料庫活動會記錄到主控台，但是可以設定記錄檔屬性來呼叫 @ no__t-0string @ no__t-1 委派的任何動作。

如果您想要啟用資料庫記錄而不重新編譯，而且您使用 Entity Framework 6.1 或更新版本，您可以在應用程式的 web.config 或 app.config 檔案中新增攔截器來執行此動作。

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

如需有關如何不需要重新編譯移至 新增記錄\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/> 。

## <a name="11-appendix"></a>11附錄

### <a name="111-a-test-environment"></a>11.1 A. 測試環境

此環境會在與用戶端應用程式不同的電腦上，使用具有資料庫的2部電腦設定。 機器位於相同的機架中，因此網路延遲相對較低，但比單一電腦環境更實際。

#### <a name="1111-app-server"></a>11.1.1 應用程式伺服器

##### <a name="11111-software-environment"></a>11.1.1.1 軟體環境

-   Entity Framework 4 軟體環境
    -   OS 名稱：Windows Server 2008 R2 Enterprise SP1。
    -   Visual Studio 2010 –旗艦版。
    -   Visual Studio 2010 SP1 （僅適用于某些比較）。
-   Entity Framework 5 和6軟體環境
    -   OS 名稱：Windows 8.1 Enterprise
    -   Visual Studio 2013 –旗艦版。

##### <a name="11112-hardware-environment"></a>11.1.1.2 硬體環境

-   雙處理器：    Intel （R）2.27 （R） CPU L5520 W3530 @ g h z，2261 Mhz8 GHz，4核心，84個邏輯處理器。
-   2412 GB RamRAM。
-   136 GB SCSI250GB SATA 7200 rpm 3GB/s 磁片磁碟機分割成4個磁碟分割。

#### <a name="1112-db-server"></a>11.1.2 DB 伺服器

##### <a name="11121-software-environment"></a>11.1.2.1 軟體環境

-   OS 名稱：Windows Server 2008 R 28.1 Enterprise SP1。
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2 硬體環境

-   單處理器：Intel （R） MhzES （R） CPU L5520 @ 2.27 GHz，2261-1620 0 @ 3.60 GHz，4核心，8個邏輯處理器。
-   824 GB RamRAM。
-   465 GB ATA500GB SATA 7200 rpm 6GB/s 磁片磁碟機分割成4個磁碟分割。

### <a name="112-b-query-performance-comparison-tests"></a>11.2 b. 查詢效能比較測試

Northwind 模型是用來執行這些測試。 它是使用 Entity Framework 設計工具從資料庫產生的。 然後，使用下列程式碼來比較查詢執行選項的效能：

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

### <a name="113-c-navision-model"></a>11.3 c. Navision 模型

Navision 資料庫是用來示範 Microsoft Dynamics – NAV 的大型資料庫。 產生的概念模型包含1005個實體集和4227個關聯集。 測試中使用的模型是「平面」–尚未新增任何繼承。

#### <a name="1131-queries-used-for-navision-tests"></a>用於 Navision 測試的11.3.1 查詢

與 Navision 模型搭配使用的查詢清單包含3個類別的 Entity SQL 查詢：

##### <a name="11311-lookup"></a>11.3.1.1 查閱

不含匯總的簡單查閱查詢

-   計數:16232
-   範例：

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

具有多個匯總但沒有小計（單一查詢）的一般 BI 查詢

-   計數:2313
-   範例：

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

其中 MDF @ no__t-0SessionLogin @ no__t-1Time @ no__t-2Max （）在模型中定義為：

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AggregatingSubtotals

具有匯總和小計的 BI 查詢（透過 union all）

-   計數:178
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
