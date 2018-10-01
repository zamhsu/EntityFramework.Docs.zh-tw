---
title: EF4、 EF5，與 EF6 的效能考量
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: fb184fe8720b552a2050607bb17648f0413c31d1
ms.sourcegitcommit: c568d33214fc25c76e02c8529a29da7a356b37b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2018
ms.locfileid: "47459587"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>EF 4、 5 和 6 的效能考量
David Obando、 Eric Dettinger 和其他項目

發行日期： 4 月 2012

上次更新日期： 2014 年

------------------------------------------------------------------------

## <a name="1-introduction"></a>1.簡介

物件關聯式對應架構都提供物件導向應用程式中的資料存取的抽象概念的便利方式。 .NET 應用程式，Microsoft 建議的 O/RM 是 Entity Framework。 不過，使用任何抽象效能可以成為問題。

本白皮書中寫入時使用 Entity Framework，讓開發人員了解 Entity Framework 內部演算法可能會影響效能，並提供秘訣以調查開發應用程式顯示的效能考量，改善他們使用 Entity Framework 的應用程式中的效能。 有幾個良好的主題，在效能上的已在 web 上，我們也試指向這些資源，可能的話，

效能是容易令人混淆。 本白皮書旨在做為資源協助您進行效能相關供應用程式使用 Entity Framework 的決策。 我們已包含一些測試度量資訊，來示範的效能，但這些計量為絕對的指標，您會看到您的應用程式中的效能。

實際上，這份文件會假設 Entity Framework 4 執行.NET 4.0 和 Entity Framework 5 和 6.NET 4.5 下執行。 Entity Framework 5 的效能改良的許多位於隨附於.NET 4.5 的核心元件。

Entity Framework 6 不足的頻外版本且不需依賴.NET 所隨附的 Entity Framework 元件。 Entity Framework 6 在.NET 4.0 和.NET 4.5 上運作，並可以提供.NET 4.0 從尚未升級，但想要在其應用程式中最新的 Entity Framework 版本的人巨量的效能優勢。 當這份文件提及 Entity Framework 6 時，它是指在撰寫本文時可用的最新版本： 版本 6.1.0。

## <a name="2-cold-vs-warm-query-execution"></a>2.冷 vs。暖的查詢執行

第一次的任何查詢對給定的模型，Entity Framework 會大量載入和驗證模型，在幕後的工作。 我們經常參考此第一個查詢做為 「 冷 」 的查詢。  針對已經載入模型進行進一步查詢稱為 「 暖 」 查詢，並更快。

讓我們花時間的執行查詢，使用 Entity Framework 時的高階檢視，並查看其中 Entity Framework 6 改善項目。

**第一次的查詢執行-冷的查詢**

| 程式碼使用者寫入                                                                                     | 動作                    | EF4 效能影響                                                                                                                                                                                                                                                                                                                                                                                                        | EF5 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                    | EF6 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 內容建立          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                        | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查詢運算式建立 | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | LINQ 查詢執行      | 中繼資料載入： 高但快取 <br/> -檢視產生： 可能很高，但快取 <br/> 參數的評估： 中型 <br/> -Query 轉譯： 中型 <br/> -具體化程式都會產生： 中度，但快取 <br/> -資料庫執行查詢： 可能會很高 <br/> + C <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> 物件具體化： 中型 <br/> 身分識別查閱： 中型 | 中繼資料載入： 高但快取 <br/> -檢視產生： 可能很高，但快取 <br/> 參數的評估： 低 <br/> -Query 轉譯： 中度，但快取 <br/> -具體化程式都會產生： 中度，但快取 <br/> -資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下） <br/> + C <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> 物件具體化： 中型 <br/> 身分識別查閱： 中型 | 中繼資料載入： 高但快取 <br/> -檢視產生： 中度，但快取 <br/> 參數的評估： 低 <br/> -Query 轉譯： 中度，但快取 <br/> -具體化程式都會產生： 中度，但快取 <br/> -資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下） <br/> + C <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> 物件具體化： 中度 （更快速比 EF5） <br/> 身分識別查閱： 中型 |
| `}`                                                                                                  | Connection.Close          | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**第二個查詢執行-暖查詢**

| 程式碼使用者寫入                                                                                     | 動作                    | EF4 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | EF5 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | EF6 效能影響                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 內容建立          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查詢運算式建立 | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | LINQ 查詢執行      | 中繼資料~~載入~~查閱：~~高但快取~~低 <br/> -檢視~~代~~查閱：~~可能很高，但快取~~低 <br/> 參數的評估： 中型 <br/> -Query~~翻譯~~查閱： 中型 <br/> -具體化程式都會~~代~~查閱：~~中型但快取~~低 <br/> -資料庫執行查詢： 可能會很高 <br/> + C <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> 物件具體化： 中型 <br/> 身分識別查閱： 中型 | 中繼資料~~載入~~查閱：~~高但快取~~低 <br/> -檢視~~代~~查閱：~~可能很高，但快取~~低 <br/> 參數的評估： 低 <br/> -Query~~翻譯~~查閱：~~中型但快取~~低 <br/> -具體化程式都會~~代~~查閱：~~中型但快取~~低 <br/> -資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下） <br/> + C <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> 物件具體化： 中型 <br/> 身分識別查閱： 中型 | 中繼資料~~載入~~查閱：~~高但快取~~低 <br/> -檢視~~代~~查閱：~~中型但快取~~低 <br/> 參數的評估： 低 <br/> -Query~~翻譯~~查閱：~~中型但快取~~低 <br/> -具體化程式都會~~代~~查閱：~~中型但快取~~低 <br/> -資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下） <br/> + C <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> 物件具體化： 中度 （更快速比 EF5） <br/> 身分識別查閱： 中型 |
| `}`                                                                                                  | Connection.Close          | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


有數種方式可減少冷和暖查詢的效能成本，並讓我們看看這些在下一節。 具體來說，我們將探討降低模型載入原始的查詢中使用預先產生的檢視，這應有助於減輕效能難題檢視表產生期間發生的成本。 暖的查詢，我們將討論查詢計畫快取、 無追蹤查詢，以及不同的查詢執行選項。

### <a name="21-what-is-view-generation"></a>2.1 檢視表產生是什麼？

若要了解哪些檢視產生是，我們必須先了解有哪些 「 對應檢視 」。 對應檢視是可執行檔的每個實體集和關聯的對應中指定的轉換。 就內部而言，這些對應檢視需要 CQTs （標準查詢樹狀目錄） 的形狀。 有兩種類型的對應檢視：

-   查詢檢視： 這些代表，從資料庫結構描述移至概念模型所需的轉換。
-   更新檢視： 這些代表前往從概念模型的資料庫結構描述所需的轉換。

請記住，概念模型可能會與不同的資料庫結構描述，以各種方式。 例如，一個單一的資料表可能會用於儲存兩個不同的實體類型的資料。 繼承和非一般的對應中扮演角色的對應檢視的複雜度。

計算這些檢視對應的規格為基礎的程序是我們呼叫檢視表產生。 檢視表產生是進行以動態方式載入模型時，或在建置階段，使用 「 預先產生的檢視 」;後者會序列化為 C 的 Entity SQL 陳述式的形式\#或 VB 檔案。

當產生檢視時，也會驗證它們。 從效能觀點來看，大部分的檢視表產生會是成本的實際驗證的檢視以確保實體之間的連線有意義，並具有正確的基數為所有支援的作業。

實體集合的查詢執行時，查詢會與對應的查詢檢視結合，此組合的結果執行計劃編譯器建立的備份存放區可以了解查詢的表示法。 SQL Server 的這個編譯的最終結果會是 T-SQL SELECT 陳述式。 第一次執行更新的實體集合時，[更新] 檢視會透過類似的程序，將它轉換成目標資料庫的 DML 陳述式執行。

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 因素會影響檢視表產生效能

檢視產生的步驟的效能不只取決於您的模型大小還會依據如何相互連結的模型是。 如果兩個實體連線透過繼承鏈結或關聯，就可以說連線。 同樣地如果外部索引鍵透過連線兩個資料表，它們會連線。 當連接的實體和結構描述中的資料表數目增加時，檢視產生的成本增加。

但我們進行一些最佳化來改善此，我們用來產生及驗證檢視的演算法是最糟的情況，指數。 似乎會對效能造成負面影響的最大因素如下：

-   模型的大小，參考的實體數目和這些實體之間的關聯的量。
-   模型的複雜性，尤其是牽涉到大量類型的繼承。
-   使用獨立關聯，而不外部索引鍵關聯。

小型的簡單模型的成本可能夠小，不需要考慮使用預先產生的檢視。 當模型大小和複雜度增加，有幾個選項可用來降低成本檢視產生和驗證。

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 使用 Pre-Generated 檢視，以減少模型載入時間

如需如何使用 Entity Framework 6 的預先產生的檢視的詳細資訊，請造訪[Pre-Generated 對應檢視](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 使用 Entity Framework Power Tools Community Edition 的預先產生檢視

您可以使用[Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)來產生 EDMX 和 Code First 模型的檢視模型類別檔案上按一下滑鼠右鍵，然後使用 [Entity Framework] 功能表選取 「 產生檢視 」。 Entity Framework Power Tools Community Edition 只能用於 DbContext 衍生的內容。

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 如何使用 EDMGen 所建立的模型中的預先產生的檢視

EDMGen 是公用程式隨附於.NET，並使用 Entity Framework 4 和 5，但不是能搭配 Entity Framework 6 的運作方式。 EDMGen 可讓您從命令列產生的模型檔案中，物件層和檢視。 其中一個輸出將會在您選擇的語言，VB 或 C 中的檢視檔案\#。 這是包含 Entity SQL 程式碼片段，每一個實體集的程式碼檔案。 若要啟用預先產生的檢視，您只需包含檔案在您的專案。

如果您手動編輯模型的結構描述檔案，您必須重新產生檢視檔案。 您可以執行使用 EDMGen **/mode:ViewGeneration**旗標。

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 如何 Pre-Generated 檢視使用 EDMX 檔案

您也可以使用 EDMGen 來產生的 EDMX 檔案的檢視表-先前參考的 MSDN 主題描述如何新增建置前事件，若要這樣做-這是複雜但有某些情況下，而不可能。 它是通常更容易使用 T4 範本產生檢視，當模型處於 edmx 檔案。

ADO.NET 小組部落格有某篇文章說明如何使用 T4 範本產生檢視表 ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)。 這篇文章包含可以下載並加入至專案的範本。 範本是針對 Entity Framework 的第一個版本撰寫的因此它們不保證適用於 Entity Framework 的最新版本。 不過，您可以下載較新的一組檢視產生範本，針對 Entity Framework 4 和 5from Visual Studio 組件庫：

-   VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

如果您使用 Entity Framework 6 您可以檢視產生 T4 範本從取得 Visual Studio 組件庫，在\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>。

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 減少檢視產生的成本

使用預先產生的檢視時，會將檢視表 （執行階段） 從載入的模型產生的成本移至設計階段。 雖然這可改善啟動效能，在執行階段，則會在您在開發時仍發生檢視產生的痛苦。 有數個其他的竅門，幫助降低成本的檢視表產生，在編譯時期和執行的階段。

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 使用外部索引鍵關聯，以減少檢視產生的成本

我們看到其中切換關聯外部索引鍵關聯將模型從獨立關聯中，大幅改善在檢視表產生所花費的時間的案例數目。

為了示範這項改進，我們會使用 EDMGen 產生 Navision 模型的兩個版本。 *注意： seeappendix Cfor Navision 模型的描述。* 針對此練習，因為其非常大量的實體和它們之間的關聯性的有趣 Navision 模型。

使用外部索引鍵關聯所產生的極大的模型的一個版本和其他已產生使用獨立關聯。 此外，我們再逾時產生每個模型的檢視所需的時間。 實體 Framework5 測試會使用類別 EntityViewGenerator GenerateViews() 方法來產生檢視，而 Entity Framework 6 測試用來從類別 storagemappingitemcollection 由 GenerateViews() 方法。 這由於程式碼重構 Entity Framework 6 程式碼基底中發生。

使用 Entity Framework 5，外部索引鍵之模型的檢視表產生可以採用 65 分鐘實驗室機器中。 它的未知多久得花上產生使用獨立關聯模型的檢視。 我們保留超過一個月，我們的實驗室，安裝每月更新的電腦已重新開機前執行的測試。

使用 Entity Framework 6，外部索引鍵之模型的檢視表產生作業需要在相同的實驗室電腦 28 秒。 使用獨立關聯模型的檢視表產生花費了 58 秒。 為 Entity Framework 6 對其檢視產生程式碼的改善表示許多專案，不需要預先產生的檢視，以取得更快速的啟動時間。

務必預先產生的 Entity Framework 4 和 5 中的檢視可以使用 EDMGen 或 Entity Framework Power Tools 的備註。 Entity Framework 6 檢視產生可透過 Entity Framework Power Tools 或以程式設計方式中所述[Pre-Generated 對應檢視](~/ef6/fundamentals/performance/pre-generated-views.md)。

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1、azure 如何使用外部索引鍵，而不是獨立關聯

根據預設，當使用 EDMGen 或實體設計工具在 Visual Studio 中，取得 Fk，它只需要 Fk 和 IAs 之間切換單一核取方塊或命令列旗標。

如果您有大型的 Code First 模型時，使用獨立關聯將會有檢視表產生相同的效果。 雖然有些開發人員會認為這到處其物件模型，您可以避免這種影響加上您相依的物件類別的外部索引鍵屬性。 您可以找到更多有關這個主題進行\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>。

| 使用時      | 請執行                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Entity Designer | 新增兩個實體之間的關聯之後, 請確定您已參考條件約束。 參考條件約束會告知 Entity Framework 使用外部索引鍵，而不是獨立的關聯。 如需其他詳細資料請造訪\<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>。 |
| EDMGen          | 當使用 EDMGen 從資料庫產生您的檔案，將遵守您的外部索引鍵，並加入至這類模型。 如需 EDMGen 所公開的不同選項的詳細資訊請造訪[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。                           |
| Code First      | 請參閱 「 關聯性慣例 」 一節[程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/built-in.md)主題以取得有關如何使用 Code First 時，包含相依物件上的外部索引鍵屬性的資訊。                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 將模型移至不同的組件

當您的模型直接包含在您的應用程式專案，並產生透過建置前事件或 T4 範本檢視時，檢視產生和驗證就會進行時就會重建專案，即使模型沒有變更。 如果您將模型移至不同的組件，並從您的應用程式專案參考它，您可以您的應用程式進行其他變更，而無須重新建置專案包含模型項目。

*注意：* 時移動您的模型，來分隔組件，請記得將此模型的連接字串複製到用戶端專案的應用程式組態檔。

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 停用的 edmx 式模型的驗證

EDMX 模型會在編譯時期驗證，即使模型不會變更。 如果您的模型已經過驗證，您可以隱藏在編譯時期的驗證屬性 視窗中將驗證上建置 屬性設定為 false。 當您變更您的對應或模型時，您可以暫時重新啟用驗證，以確認您的變更。

請注意，效能已改善 Entity Framework 設計工具的 Entity Framework 6，而且 「 驗證上建置 」 的成本會遠低於舊版設計工具中。

## <a name="3-caching-in-the-entity-framework"></a>Entity Framework 中的 3 個快取

Entity Framework 有快取的內建以下列形式：

1.  物件快取 – 內建的 ObjectContext 執行個體在 ObjectStateManager 中已使用該執行個體已擷取之物件的記憶體會追蹤。 這也就是第一層快取。
2.  查詢計劃快取-多次執行查詢時，重複使用產生的存放區命令。
3.  中繼資料快取-不同的連線以相同的模型之間共用模型的中繼資料。

除了根據預設，也就是一種特殊的 ADO.NET 資料提供者稱為包裝提供者也可用來擴充 Entity Framework 從資料庫擷取的結果快取的 EF 提供的快取也稱為第二層快取。

### <a name="31-object-caching"></a>3.1 物件快取

預設查詢的結果中傳回實體時只 EF 會具體化之前, ObjectContext 會檢查具有相同索引鍵的實體是否已載入至其 ObjectStateManager。 如果具有相同的索引鍵的實體已存在於 EF 會包含在查詢的結果。 雖然 EF 仍會發出對資料庫的查詢，此行為可以略過大部分的具體化實體多次的成本。

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 從使用 DbContext 尋找的物件快取取得實體

不同於一般的查詢，DbSet (第一次在 EF 4.1 中所包含的 Api) 中的 Find 方法會搜尋在記憶體中執行之前甚至發出對資料庫的查詢。 請務必請注意，兩個不同的 ObjectContext 執行個體將會有兩個不同 ObjectStateManager 執行個體，這表示它們有不同的物件快取。

尋找使用以嘗試尋找內容追蹤之實體的主索引鍵值。 如果實體不在內容中會接著執行並針對資料庫中，評估查詢，如果實體找不到內容中，或在資料庫中，會傳回 null。 請注意，尋找也會傳回已加入至內容，但尚未儲存到資料庫的實體。

沒有使用尋找時所要採取的效能考量。 根據預設，這個方法的引動過程將會觸發物件快取驗證，以便偵測仍在等待認可到資料庫的變更。 此程序可能非常昂貴，如果有非常大量的物件快取中，或加入物件快取的大型物件圖形中的物件，但它可以也停用。 在某些情況下，您可能會感覺透過一個級距中呼叫方法，當您停用自動偵測尋找的差異變更。 尚未第二個量級被認為當物件實際上是與快取中物件具有要從資料庫擷取時。 以下是與使用一些我們 microbenchmarks，以毫秒表示，負載為 5000 實體所做的度量的範例圖表：

![.NET 4.5 對數刻度](~/ef6/media/net45logscale.png ".NET 4.5-對數刻度")

停用偵測變更的尋找範例：

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

您必須使用 Find 方法時，請考慮為：

1.  物件是否不在快取中尋找的優點否定的但的語法是由索引鍵的查詢比仍簡單。
2.  如果自動偵測變更已啟用一個級距，或甚至更是根據您的模型和您的物件快取中的實體數量的複雜度，可能會增加成本的 Find 方法。

此外，請記住，尋找只會傳回您所需的實體，而且它不會自動載入其相關聯的實體如果它們不存在於物件快取。 如果您需要擷取相關聯的實體，您可以使用查詢，依據索引鍵搭配積極式載入。 如需詳細資訊，請參閱**8.1 消極式載入 vs。積極式載入**。

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 效能問題，當物件快取具有許多實體

物件快取有助於增加 Entity Framework 的整體回應。 不過，當物件快取有非常大量的實體載入它可能會影響特定作業，例如新增、 移除、 尋找項目、 SaveChanges 和更多功能。 特別的是，觸發呼叫 DetectChanges 作業將會造成負面影響的超大型物件快取。 以物件狀態管理員和直接由物件圖形的大小決定其效能會 DetectChanges 同步處理的物件圖形。 如需 DetectChanges 的詳細資訊，請參閱[追蹤 POCO 實體變更](https://msdn.microsoft.com/library/dd456848.aspx)。

當使用 Entity Framework 6，開發人員都能夠直接在 DbSet，而不是逐一查看集合，並呼叫一次每個執行個體上呼叫 AddRange 與 RemoveRange。 使用範圍方法的優點是，DetectChanges 成本只支付一次完整的實體，而不是每個加入的實體每一次。

### <a name="32-query-plan-caching"></a>3.2，查詢計畫快取

第一次執行查詢時，會經過內部的計劃編譯器概念的查詢轉譯為存放區命令 (例如，T-SQL 時對 SQL Server 執行所執行)。  如果已啟用查詢計畫快取下, 一次查詢執行存放區命令會擷取直接從執行，並計劃編譯器略過的查詢計畫快取。

查詢計畫快取會共用相同的 AppDomain 內的 ObjectContext 執行個體。 您不需要保留的 ObjectContext 執行個體，才會受益於查詢計畫快取。

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 某些查詢計劃快取的注意事項

-   查詢計畫快取會共用所有的查詢類型： Entity SQL，LINQ to Entities，和 CompiledQuery 物件。
-   根據預設，查詢計劃啟用快取 Entity SQL 查詢，是否執行了 EntityCommand 來透過或 ObjectQuery。 它也會啟用預設 linq to Entities 查詢中在.NET 4.5 中，Entity Framework 和 Entity Framework 6
    -   查詢計畫快取可停用 （在 EntityCommand 或 ObjectQuery） EnablePlanCaching 屬性設定為 false。 例如: 
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
-   參數化查詢，只要變更參數的值會仍然達到快取的查詢。 不過，變更參數的 facet （比方說，大小、 有效位數或小數位數） 會叫用快取中的不同項目。
-   使用 Entity SQL，查詢字串時的索引鍵的一部分。 所有變更查詢會導致不同的快取的項目，即使查詢的功能相同。 這包括變更大小寫或空白字元。
-   使用 LINQ 時，會處理查詢時產生索引鍵的一部分。 LINQ 運算式變更將會因此產生不同的金鑰。
-   其他技術可能會限制;如需詳細資訊，請參閱 Autocompiled 查詢。

#### <a name="322------cache-eviction-algorithm"></a>3.2.2 快取收回演算法

了解內部演算法運作方式如何協助您找出何時要啟用或停用查詢計畫快取。 清除演算法如下所示：

1.  快取包含固定數目的項目 (800) 之後, 我們會啟動計時器，定期 （一次每分鐘） 掃掠快取。
2.  快取掃掠，期間從 LFRU （最低常見問題-最近使用過的） 上的快取移除項目為基礎。 此演算法會叫用的次數和年齡考量決定哪些項目會退出時。
3.  在每個快取掃掠的結束時，快取一次包含 800 的項目。

判斷要收回的項目時，會同等對待所有快取項目。 這表示 CompiledQuery 的存放區命令有相同機會收回與 Entity SQL 查詢存放區命令。

請注意，快取收回計時器已開始時快取中，有 800 的實體，但快取只會清理此計時器就會啟動後的 60 秒。 這表示最多 60 秒拓展您的快取可能會相當龐大。

#### <a name="323-------test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 測試示範查詢計劃快取效能度量

為了示範應用程式的效能上的快取的查詢計劃的效果，我們執行測試我們用來執行對 Navision 模型的 Entity SQL 查詢的數目。 請參閱附錄 Navision 模型所執行的查詢類型的描述。 在此測試中，我們會先逐一查看查詢的清單並將它們新增至快取 （如果已啟用快取） 一次執行每一個。 這個步驟是 untimed。 接下來，我們進入睡眠狀態以允許快取掃掠，以進行應用程式; 超過 60 秒的主執行緒最後，我們逐一查看清單的第 2 個開始執行快取的查詢。 此外，他的 SQL Server 計畫快取中排清之前執行查詢的每個集時，讓我們取得準確的時間會反映指定的查詢計劃快取的優點。

##### <a name="3231-------test-results"></a>3.2.3.1 測試結果

| 測試                                                                   | EF5 無快取 | EF5 快取 | EF6 沒有快取 | 快取的 EF6 |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| 列舉所有 18723 查詢                                          | 124          | 125.4      | 124.3        | 125.3      |
| 避免掃掠 （只是先 800 查詢，不論複雜性）。  | 41.7         | 5.5        | 40.5         | 5.4        |
| 只要 AggregatingSubtotals 查詢 （178 總計-這可避免掃掠） | 39.5         | 4.5        | 38.1         | 4.6        |

*所有時間以秒為單位。*

道德-時執行其他的相異查詢 （例如，動態建立的查詢），快取沒有幫助，並產生清除快取可以保留最多可從計畫快取從實際使用它獲益的查詢。

AggregatingSubtotals 查詢是最複雜的測試使用的查詢。 如預期般，越複雜的查詢是，您會看到從查詢計畫快取的效益越多。

由於 CompiledQuery 其實它快取的計畫的 LINQ 查詢，比較的對等的 Entity SQL 查詢與 CompiledQuery 應該有類似的結果。 事實上，如果應用程式有許多動態的 Entity SQL 查詢，填入查詢的快取也可以有效地會 CompiledQueries 「 不良 」 時它們會從快取中排清。 在此案例中，停用快取上設定優先順序 CompiledQueries 動態的查詢可能會提升效能。 更棒的是，當然是重寫應用程式而不是動態的查詢使用參數化的查詢。

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 使用 CompiledQuery 來改善使用 LINQ 查詢的效能

我們的測試顯示，使用 CompiledQuery 帶來的優點 7 %autocompiled 透過 LINQ 查詢;這表示，您會縮短 7%的時間執行的程式碼，從 Entity Framework 堆疊;它並不表示您的應用程式將會更快 7%。 一般而言，撰寫和維護 EF 5.0 CompiledQuery 物件的成本可能不值得相較於優點的麻煩。 您的級距可能有所不同，因此執行這個選項，如果您的專案需要額外的推播。 請注意，CompiledQueries 只與 ObjectContext 衍生模型相容，並且與 DbContext 衍生模式不相容。

如需有關建立和叫用 CompiledQuery 的詳細資訊，請參閱 <<c0> [編譯的查詢 (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx)。

有兩個您必須進行時使用 CompiledQuery 時，也就是需要使用靜態的執行個體和問題具備複合性的考量。 以下遵循深入的說明，這些兩個考量。

#### <a name="331-------use-static-compiledquery-instances"></a>3.3.1 使用靜態 CompiledQuery 執行個體

編譯的 LINQ 查詢作業非常耗時，因為我們不想這麼做，每次我們要從資料庫擷取資料。 CompiledQuery 執行個體可讓您一次編譯及執行多次，但您必須小心，並重複使用相同的 CompiledQuery 執行個體而不是不斷地編譯它每次採購。 使用儲存 CompiledQuery 執行個體的靜態成員會變成必要的;否則，您不會看到任何好處。

例如，假設您的網頁有下列的方法主體，以處理顯示選取之類別目錄的產品：

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

在此情況下，您將建立新的 CompiledQuery 執行個體即時，每次呼叫的方法。 而不是所擷取的查詢計劃快取存放區命令看到效能優勢，CompiledQuery 會瀏覽計劃編譯器，每次建立新的執行個體。 事實上，您將會到處查詢計劃快取與新的 CompiledQuery 項目每次呼叫的方法。

相反地，您會想要建立靜態的執行個體的已編譯的查詢，以便每次呼叫方法時，會叫用相同的已編譯的查詢。 其中一種方式，這是由物件內容的成員身分加入 CompiledQuery 執行個體。  您接著可以事項的一些更乾淨藉由存取 CompiledQuery 透過 helper 方法：

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

這個 helper 方法會叫用，如下所示：

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-------composing-over-a-compiledquery"></a>3.3.2 撰寫透過 CompiledQuery

撰寫任何 LINQ 查詢的功能非常有用。若要這樣做，您只需叫用方法之後 IQueryable 這類*skip （)* 或是*count （)*。 不過，所以基本上會傳回新的 IQueryable 物件。 沒有要阻止您技術上透過 CompiledQuery 撰寫項目，這樣會導致產生新的 IQueryable 物件所需要傳遞計劃編譯器透過一次。

某些元件，將使用撰寫 IQueryable 的物件，以啟用進階的功能。 例如，ASP。NET 的 GridView 可以是資料繫結至 IQueryable 物件透過 SelectMethod 屬性。 然後 GridView 會撰寫此 IQueryable 物件，以允許排序和分頁資料模型。 如您所見，使用 CompiledQuery gridview 不會叫用已編譯的查詢，但會產生新的 autocompiled 查詢。

客戶諮詢小組討論此主題中其 「 潛在效能問題與編譯的 LINQ 查詢重新編譯 」 部落格文章： <http://blogs.msdn.com/b/appfabriccat/archive/2010/08/06/potential-performance-issues-with-compiled-linq-query-re-compiles.aspx>。

其中您可能會遇到這個問題的一個位置時，漸進式的篩選器新增至查詢。 例如，假設您有使用選用的篩選條件 （例如，國家/地區和 OrdersCount） 的數個下拉式清單 [客戶] 頁面。 您可以撰寫 IQueryable 結果的 CompiledQuery，這些篩選器，但這樣做會導致新的查詢進行計劃編譯器，每次您執行它。

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

 若要避免此重新編譯，您可以重新撰寫 CompiledQuery，若要篩選條件納入考量：

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

等 UI 中，會叫用：

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

 的代價就是產生的存放區命令永遠會篩選具有 null 檢查，但這些應該是相當簡單的資料庫伺服器，以最佳化：

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 中繼資料快取

Entity Framework 也支援中繼資料快取。 這基本上跨不同的連線以相同的模型快取的類型資訊和資料庫型別對應資訊。 中繼資料快取是每個 AppDomain 唯一的。

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 中繼資料快取演算法

1.  模型的中繼資料資訊會儲存每個 EntityConnection ItemCollection 中。
    -   附帶一提，有不同的 ItemCollection 物件模型的不同部分。 例如，StoreItemCollections 包含資料庫模型的相關資訊ObjectItemCollection 包含資料模型的相關資訊EdmItemCollection 包含概念模型的相關資訊。

2.  如果兩個連線使用相同的連接字串，它們會共用相同的 ItemCollection 執行個體。
3.  功能相同，但賦予不同的連接字串可能會導致不同的中繼資料快取。 我們語彙基元化的連接字串，因此只要變更一個語彙基元順序應該會導致共用的中繼資料。 但是，兩個功能似乎相同的連接字串可能不會評估為相同 token 化之後。
4.  ItemCollection 會定期檢查使用中。 如果判斷該工作區未存取最近，它會標示為清理在下一步 的快取掃掠。
5.  只建立 EntityConnection 會 （但不會初始化中的項目集合，直到開啟連接） 建立的中繼資料快取。 此工作區會保留於記憶體中，直到快取演算法可讓您決定不是 「 使用中 」。

客戶諮詢小組已撰寫描述保存 ItemCollection 的參考，以使用大型模型時，避免 「 取代 」 的部落格文章： \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>。

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 中繼資料快取和查詢計畫快取之間的關聯性

查詢計劃快取執行個體都存在於存放區類型的 MetadataWorkspace 的 ItemCollection。 這表示快取存放區命令，將會用於針對任何的內容中使用給定的 MetadataWorkspace 具現化的查詢。 這也表示，如果您有兩個連接字串會稍有不同，不符合 token 化之後，您將擁有不同的查詢計畫快取執行個體。

### <a name="35-results-caching"></a>3.5 快取的結果

結果快取 （也稱為 「 第二層快取），您可以保留查詢的結果在本機快取。 發出查詢時，您先查看是否結果可在本機，再查詢對存放區。 雖然快取的結果不直接支援 Entity framework，就可以使用包裝提供者中新增第二個層級快取。 第二層快取的範例包裝提供者是 Alachisoft 的[Entity Framework 第二個層級快取基礎 NCache](http://www.alachisoft.com/ncache/entity-framework.html)。

第二層快取這個實作會在評估 LINQ 運算式之後的位置 （和 funcletized） 插入功能，而且查詢執行計畫是計算，或從第一層快取擷取。 第二層快取會儲存只有未經處理的資料庫結果，因此具體化管線仍會執行之後。

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 其他參考資料與換行的提供者快取的結果

-   Julie Lerman 寫過 < 第二層快取 Entity Framework 和 Windows Azure 中 「 MSDN 文件，包括如何更新範例包裝提供者，以使用 Windows Server AppFabric 快取： [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   如果您正在使用 Entity Framework 5，在小組部落格有某篇文章說明如何使用 Entity Framework 5 的快取提供者執行的工作： \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>。 它也包含可協助執行自動化新增至您的專案第 2 個層級快取的 T4 範本。

## <a name="4-autocompiled-queries"></a>4 個 Autocompiled 查詢

當使用 Entity Framework 對資料庫發出查詢時，必須先經歷一系列的步驟之前實際具體化結果。這類的一個步驟就是查詢編譯。 Entity SQL 查詢已知道有良好的效能，因為它們會自動快取，因此第二個或第三個階段執行相同的查詢就可以略過方案編譯器，並改為使用快取的計畫。

Entity Framework 5 引進了自動快取的 LINQ to Entities 查詢以及。 在過去版本的 Entity Framework 建立加快速度 CompiledQuery 您執行速度很常見的做法，因為這會讓您的 LINQ to Entities 查詢快取。 因為快取會立即自動完成而不使用 CompiledQuery，我們將這項功能 「 autocompiled 查詢 」。 如需有關查詢計畫快取和其機制的詳細資訊，請參閱查詢計畫快取。

Entity Framework 會偵測，當查詢需要進行重新編譯，而且也這麼做，即使它已編譯之前，叫用的查詢時。 會導致查詢重新編譯的常見狀況是：

-   變更與查詢相關聯 MergeOption。 將不會使用快取的查詢、 計劃編譯器將會再次執行而新建立的計畫快取。
-   變更 ContextOptions.UseCSharpNullComparisonBehavior 的值。 您會取得變更 MergeOption 相同的效果。

其他條件可防止查詢使用快取。 常見範例包括：

-   使用 IEnumerable&lt;T&gt;。包含&lt;&gt;（T 值）。
-   使用函式會產生常數的查詢。
-   使用非對應的物件的屬性。
-   正在查詢連結到另一個需要重新編譯的查詢。

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 使用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值）

Entity Framework 不會快取查詢，以叫用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值） 針對記憶體中集合，因為集合的值會視為 volatile。 下列範例查詢將不會快取，因此一律先處理計劃編譯器：

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

請注意，在執行其包含針對 IEnumerable 的大小決定如何快慢如何編譯您的查詢。 使用在上述範例所示的大型集合時，可以大幅降低效能。

Entity Framework 6 包含最佳化方式 IEnumerable&lt;T&gt;。包含&lt;T&gt;執行查詢時的運作方式 （T 值）。 產生的 SQL 程式碼會產生更快和更容易閱讀，並在大部分情況下它也會執行更快在伺服器中。

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 使用產生查詢與常數的函式

Skip （）、 take （）、 contains （） 和 DefautIfEmpty() LINQ 運算子不會產生含有參數的 SQL 查詢，但改為放以常數的形式傳遞給它們的值。 基於這個原因，可能會完全相同的最後可能會到處查詢的查詢 EF 堆疊上和資料庫伺服器上 計畫快取，並執行未取得 reutilized 除非相同的常數用在後續的查詢執行中。 例如: 

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

在此範例中，每次執行此查詢具有不同值的識別碼查詢將會編譯成新的計劃。

在特定注意的 Skip 和 Take 進行分頁時使用。 在 EF6 中這些方法會具有有效地讓快取的查詢計畫可重複使用因為 EF 可以擷取傳遞給這些方法的變數，並將它們轉譯成 SQLparameters lambda 多載。 這也有助於保留快取更簡潔，因為否則每個查詢使用的不同的常數，Skip 和 Take 會取得自己的查詢計劃快取項目。

請考慮下列的程式碼，它是次佳，但只為了提供這個類別的查詢：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

這個相同的程式碼的更快速版本會牽涉到使用 lambda 呼叫略過：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i \< count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

第二個程式碼片段可能會因為每次執行查詢時，這可節省 CPU 時間，並避免查詢快取，會使用相同的查詢計畫執行速度快達 11%。 此外，因為略過的參數是在 closure 中的程式碼可能也看起來像這樣現在：

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 使用非對應的物件的屬性

當查詢使用非對應的物件類型的屬性，因為參數，則將不會被快取。 例如: 

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

在此範例中，假設類別 NonMappedType 不是實體模型的一部分。 此查詢可以輕鬆地變更不使用非對應的型別，並改為使用本機變數，做為查詢的參數：

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

在此情況下，查詢將能夠取得快取，並將受益於查詢計畫快取。

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 連結需要重新編譯的查詢

下列將上述相同的範例，如果您依賴需要重新編譯查詢的第二個查詢整個第二個查詢會也重新編譯。 以下是範例來說明此案例中：

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

範例為泛型，但它也說明了如何連結至 firstQuery 造成 secondQuery 無法取得快取。 如果 firstQuery 已經不需要重新編譯的查詢，然後 secondQuery 就已快取。

## <a name="5-notracking-queries"></a>5 NoTracking 查詢

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 停用變更追蹤來減少狀態管理負荷

如果您是在唯讀案例中，而且想要避免 ObjectStateManager 中載入之物件的額外負荷，您可以發出 「 追蹤 」 的查詢。  在查詢層級，可以停用變更追蹤。

請注意，停用變更追蹤，您會有效地關閉物件快取。 當您查詢實體時，我們無法從 ObjectStateManager 提取先前具體化查詢結果來略過具體化。 如果您要重複查詢相同的實體，在相同的內容，您實際上可能會看到受益於啟用變更追蹤效能。

當查詢使用 ObjectContext，ObjectQuery 和 ObjectSet 執行個體將會記住 MergeOption，一旦設定，並在其上所組成的查詢將會繼承父系查詢有效 MergeOption。 當使用 DbContext，則可以停用追蹤 DbSet 上呼叫 AsNoTracking() 修飾詞。

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 停用變更追蹤查詢，使用 DbContext 時

您可以切換到 NoTracking 的查詢模式，由鏈結 AsNoTracking() 方法，在查詢中呼叫。 不同 ObjectQuery，DbContext API 的 DbSet 和 DbQuery 類別沒有可變動的屬性為 MergeOption。

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 停用變更追蹤使用 ObjectContext 的查詢層級

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 停用變更追蹤整個實體集使用 ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52-test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 測試示範 NoTracking 查詢的效能優勢的度量

在這項測試，我們討論代價是藉由比較追蹤 NoTracking Navision 模型的查詢填入 ObjectStateManager。 請參閱附錄 Navision 模型所執行的查詢類型的描述。 在此測試中，我們會逐一查看查詢的清單，並一次執行每一個。 我們使用"AppendOnly 」 的預設合併選項執行兩種變化的測試使用 NoTracking 查詢一次，一次。 我們執行了每個變化 3 次，並且需要執行的平均值。 測試之間我們會清除 SQL Server 上的查詢快取，並壓縮 tempdb 執行下列命令：

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb，0)

測試結果、 中位數超過 3 個執行：

|                        | 不追蹤-工作集 | 不是追蹤 – 時間 | 附加只-工作集 | 附加唯一 – 時間 |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

Entity Framework 5 將會有較少的記憶體執行結尾比 Entity Framework 6。 Entity Framework 6 所耗用的額外記憶體是額外的記憶體結構和啟用新功能和更佳的效能的程式碼的結果。

使用 ObjectStateManager 時，也是產生的記憶體耗用量的清除差異。 Entity Framework 5 30%的增加其使用量，當追蹤時我們從資料庫具體化的所有實體。 這麼做時，entity Framework 6 會增加其使用量的 28%。

大量時間，Entity Framework 6 會勝過 Entity Framework 5，在這項測試由大型的邊界。 Entity Framework 6 完成大約 16%的時間裡由 Entity Framework 5 中的測試。 此外，Entity Framework 5 會需要更多的 9%的時間，才能完成 ObjectStateManager 為使用中時。 相較之下，Entity Framework 6 使用更多時間使用 ObjectStateManager 的 3%。

## <a name="6-query-execution-options"></a>6 個的查詢執行選項

Entity Framework 提供數個不同的方式，來查詢。 我們將看看下列的選項、 比較各自的優缺點，並檢查其效能特性：

-   LINQ to Entities。
-   不是追蹤 LINQ to Entities。
-   Entity SQL 的 ObjectQuery。
-   Entity SQL 的 EntityCommand。
-   ExecuteStoreQuery。
-   SqlQuery。
-   CompiledQuery。

### <a name="61-------linq-to-entities-queries"></a>6.1 LINQ to Entities 查詢

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**專業人員**

-   適用於 CUD 作業。
-   完全具體化的物件。
-   簡單寫入與內建的程式設計語言的語法。
-   良好的效能。

**缺點**

-   某些技術的限制，例如：
    -   使用 DefaultIfEmpty OUTER JOIN 的查詢模式會導致更複雜的查詢比簡單的外部聯結陳述式，在 Entity SQL。
    -   您仍然無法使用 LIKE 與一般的模式比對。

### <a name="62-------no-tracking-linq-to-entities-queries"></a>6.2 沒有追蹤 LINQ to Entities 查詢

當內容衍生的 ObjectContext:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

當內容衍生 DbContext:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**專業人員**

-   透過一般 LINQ 查詢改善的效能。
-   完全具體化的物件。
-   簡單寫入與內建的程式設計語言的語法。

**缺點**

-   不適合用於 CUD 作業。
-   某些技術的限制，例如：
    -   使用 DefaultIfEmpty OUTER JOIN 的查詢模式會導致更複雜的查詢比簡單的外部聯結陳述式，在 Entity SQL。
    -   您仍然無法使用 LIKE 與一般的模式比對。

請注意即使 NoTracking 未指定，則不會追蹤專案純量屬性的查詢。 例如: 

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

這個特定的查詢未明確指定所 NoTracking，但因為它不會具體化有已知的物件狀態管理員然後具體化的結果型別不會追蹤。

### <a name="63-------entity-sql-over-an-objectquery"></a>6.3 entity SQL 的 ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**專業人員**

-   適用於 CUD 作業。
-   完全具體化的物件。
-   支援查詢計畫快取。

**缺點**

-   包含文字的查詢字串，其為更容易發生使用者錯誤比語言內建的查詢建構。

### <a name="64-------entity-sql-over-an-entity-command"></a>6.4 entity SQL 的實體命令

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

**專業人員**

-   支援查詢計畫快取中 （計畫快取會支援.NET 4.5 中的所有其他查詢類型） 的.NET 4.0。

**缺點**

-   包含文字的查詢字串，其為更容易發生使用者錯誤比語言內建的查詢建構。
-   不適合用於 CUD 作業。
-   結果會不會自動具體化，而且必須從 資料讀取器讀取。

### <a name="65-------sqlquery-and-executestorequery"></a>6.5 SqlQuery 和 ExecuteStoreQuery

在資料庫上 SqlQuery:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery DbSet 上：

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

**專業人員**

-   通常最快的效能因為計劃編譯器會略過。
-   完全具體化的物件。
-   適用於 CUD 作業使用從 DbSet 時。

**缺點**

-   查詢是文字又容易出錯。
-   查詢會繫結至特定的後端，使用存放區語意，而不是概念性的語意。
-   有繼承時，手動的查詢需要處理要求的型別對應條件。

### <a name="66-------compiledquery"></a>6.6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**專業人員**

-   透過一般 LINQ 查詢，以提供最多 7%的效能改進。
-   完全具體化的物件。
-   適用於 CUD 作業。

**缺點**

-   增加複雜度和程式設計的額外負荷。
-   在已編譯的查詢上撰寫時，遺失的效能改善。
-   無法為 CompiledQuery-例如，匿名型別的投影寫入一些 LINQ 查詢。

### <a name="67-------performance-comparison-of-different-query-options"></a>6.7 不同的查詢選項效能比較

其中內容建立而不逾時的簡單 microbenchmarks 暫停測試。 我們測量 5000 次查詢的非快取在受控制的環境中的實體集。 這些識別碼會採取與警告： 不會反映實際的應用程式中，所產生的數字，而它們是非常精確的測量的效能差異，當比較不同的查詢選項時，還有多少蘋果對蘋果，但不包括建立新的內容的成本。

| EF  | 測試                                 | 時間 （毫秒） | 記憶體   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectContext Linq 查詢             | 2692      | 38277120 |
| EF5 | DbContext Linq 查詢不追蹤     | 2818      | 41840640 |
| EF5 | DbContext Linq 查詢                 | 2930      | 41771008 |
| EF5 | ObjectContext Linq 查詢不追蹤 | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectContext Linq 查詢             | 3074      | 45248512 |
| EF6 | DbContext Linq 查詢不追蹤     | 3125      | 47575040 |
| EF6 | DbContext Linq 查詢                 | 3420      | 47652864 |
| EF6 | ObjectContext Linq 查詢不追蹤 | 3593      | 45260800 |

![EF5 微基準測試，5000 暖的反覆項目](~/ef6/media/ef5micro5000warm.png)

![EF6 微基準測試，5000 暖的反覆項目](~/ef6/media/ef6micro5000warm.png)

Microbenchmarks 非常非常敏感的程式碼的小型變更。 在這個案例中，Entity Framework 5 的成本與 Entity Framework 6 之間的差異是由於新增[攔截](~/ef6/fundamentals/logging-and-interception.md)並[異動改進](~/ef6/saving/transactions.md)。 這些 microbenchmarks 數字，不過，會放大的願景，到非常小的片段，Entity Framework 的作用。 從 Entity Framework 5 升級至 Entity Framework 6 的暖查詢的真實世界案例不會看到效能變差。

若要比較不同的查詢選項的實際效能，我們會建立 5 個個別的測試變化，我們使用不同的查詢選項來選取所有的產品類別目錄名稱是 「 飲料 」。 每個反覆項目包含的成本建立內容，以及將所有傳回的實體具體化的成本。 10 個反覆項目會執行 untimed 之前已逾時的 1000年次反覆運算的總和。 顯示的結果是取自 5 次執行每個測試的中間值執行。 如需詳細資訊，請參閱附錄 B，其中包含測試的程式碼。

| EF  | 測試                                        | 時間 （毫秒） | 記憶體   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | ObjectContext 實體命令                | 621       | 39350272 |
| EF5 | DbContext 上資料庫的 Sql 查詢             | 825       | 37519360 |
| EF5 | ObjectContext 存放區查詢                   | 878       | 39460864 |
| EF5 | ObjectContext Linq 查詢不追蹤        | 969       | 38293504 |
| EF5 | 使用物件查詢的 ObjectContext Entity Sql | 1089      | 38981632 |
| EF5 | ObjectContext 已編譯的查詢                | 1099      | 38682624 |
| EF5 | ObjectContext Linq 查詢                    | 1152      | 38178816 |
| EF5 | DbContext Linq 查詢不追蹤            | 1208      | 41803776 |
| EF5 | DbContext DbSet 上的 Sql 查詢                | 1414      | 37982208 |
| EF5 | DbContext Linq 查詢                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | ObjectContext 實體命令                | 480       | 47247360 |
| EF6 | ObjectContext 存放區查詢                   | 493       | 46739456 |
| EF6 | DbContext 上資料庫的 Sql 查詢             | 614       | 41607168 |
| EF6 | ObjectContext Linq 查詢不追蹤        | 684       | 46333952 |
| EF6 | 使用物件查詢的 ObjectContext Entity Sql | 767       | 48865280 |
| EF6 | ObjectContext 已編譯的查詢                | 788       | 48467968 |
| EF6 | DbContext Linq 查詢不追蹤            | 878       | 47554560 |
| EF6 | ObjectContext Linq 查詢                    | 953       | 47632384 |
| EF6 | DbContext DbSet 上的 Sql 查詢                | 1023      | 41992192 |
| EF6 | DbContext Linq 查詢                        | 1290      | 47529984 |


![EF5 暖查詢 1000年反覆項目](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查詢 1000年反覆項目](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> 為求完整性，我們加入我們在 EntityCommand 執行 Entity SQL 查詢的其中一種變化。 不過，因為結果不會針對這類查詢具體化，比較不一定是蘋果對蘋果。 測試包含最接近的近似值具體化嘗試進行了比較。

在此端對端案例中，Entity Framework 6，其表現優於 Entity Framework 5 因為對堆疊，包括更輕的 DbContext 初始化和更快速的 MetadataCollection 的幾個部分的效能改進&lt;T&gt;查閱。

## <a name="7-design-time-performance-considerations"></a>7 設計時間效能考量

### <a name="71-------inheritance-strategies"></a>7.1 繼承策略

另一個使用 Entity Framework 時的效能考量是您所使用的繼承策略。 Entity Framework 支援 3 個基本類型的繼承和其組合：

-   每個階層 (TPH) – 每個繼承用來設定具有一個鑑別子資料行指出在階層中的特定型別所表示的資料列中的資料表對應的資料表。
-   每個型別 (TPT) – 其中每個類型會有自己的資料表在資料庫資料表子資料表只會定義父資料表不包含的資料行。
-   每個類別 (TPC) – 每一種類型其中有它自己的完整資料表在資料庫資料表子資料表定義他們的所有欄位，包括父型別中定義的。

如果您的模型使用 TPT 繼承，產生的查詢將會比使用其他繼承策略，這可能會導致儲存區上的執行時間較長上產生的更為複雜。  它通常會比較長 TPT 模型，產生查詢，具體化結果的物件。

請參閱的 < 效能考量當 Entity Framework 中使用 （每個類型的資料表） TPT 繼承 > MSDN 部落格文章： \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>。

#### <a name="711-------avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 Model First 或 Code First 應用程式中避免 TPT

當您建立模型以覆蓋現有資料庫具有 TPT 結構描述時，您不需要許多選項。 但在建立時使用 Model First 或 Code First 應用程式，您應該避免 TPT 繼承，基於效能考量。

當您使用第一個模型的實體設計工具精靈 中時，您會收到的任何繼承的 TPT 模型中。 如果您想要切換成使用 Model First TPH 繼承策略，您可以使用 「 實體設計工具資料庫產生 Power Pack 」 可從 Visual Studio 組件庫 ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)。

當使用 Code First 來設定模型的對應具有繼承，EF 會使用預設的 TPH，因此繼承階層架構中的所有實體將會都對應到相同的資料表。 請參閱 MSDN Magazine 中的 「 程式碼第一次在實體 Framework4.1"發行項"對應使用 Fluent API 」 一節 ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) 如需詳細資訊。

### <a name="72-------upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 升級從 EF4 來改善模型的產生時間

安裝 Visual Studio 2010 SP1 時，Entity Framework 5 和 6，及做為 Entity Framework 4 的更新可用的模型產生存放區層 (SSDL) 的演算法，SQL Server 特定改進。 下列的測試結果時產生非常大的模型中，在此情況下 Navision 模型，示範改進。 如需進一步瞭解羰旓紵 C。

模型包含 1005年實體集和 4227 關聯集。

| 組態                              | 耗用時間的細項                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010 中，Entity Framework 4     | SSDL 產生： 2 小時 27 分鐘 <br/> 對應層代： 1 秒 <br/> CSDL 產生： 1 秒 <br/> ObjectLayer 產生： 1 秒 <br/> 檢視表產生： 2 小時 14 分鐘 |
| Visual Studio 2010 SP1 中，Entity Framework 4 | SSDL 產生： 1 秒 <br/> 對應層代： 1 秒 <br/> CSDL 產生： 1 秒 <br/> ObjectLayer 產生： 1 秒 <br/> 檢視表產生： 1 小時 53 分鐘   |
| Visual Studio 2013 中，Entity Framework 5     | SSDL 產生： 1 秒 <br/> 對應層代： 1 秒 <br/> CSDL 產生： 1 秒 <br/> ObjectLayer 產生： 1 秒 <br/> 檢視表產生： 65 分鐘    |
| Visual Studio 2013 中，Entity Framework 6     | SSDL 產生： 1 秒 <br/> 對應層代： 1 秒 <br/> CSDL 產生： 1 秒 <br/> ObjectLayer 產生： 1 秒 <br/> 檢視表產生： 28 秒。   |


值得注意的是，當產生的 SSDL，負載幾乎完全花費在 SQL 伺服器上，等候用戶端開發電腦時閒置來自伺服器的結果。 Dba 應該特別喜歡這項改善。 另外值得注意的是基本上模型產生的全部費用現在會在檢視表產生。

### <a name="73-------splitting-large-models-with-database-first-and-model-first"></a>7.3 先分割大型模型與資料庫和 Model First

模型大小增加時，在設計工具介面變得雜亂且難以使用。 我們通常會視為具有 300 個以上的實體太大，無法有效地使用設計工具的模型。 下列部落格文章說明用來分割大型模型的數個選項： \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>。

文章針對 Entity Framework 的第一個版本所撰寫，但步驟仍然適用。

### <a name="74-------performance-considerations-with-the-entity-data-source-control"></a>7.4 實體資料來源控制項的效能考量

我們已了解在多執行緒的效能和壓力測試的情況下使用 EntityDataSource 控制項的 web 應用程式的效能大幅降低的位置。 根本原因是，EntityDataSource 重複 MetadataWorkspace.LoadFromAssembly 上呼叫的 Web 應用程式，以探索要做為實體型別所參考的組件。

解決方法是設定的 EntityDataSource ContextTypeName 衍生的 ObjectContext 類別的型別名稱。 這會關閉掃描的實體類型的所有參考組件的機制。

將 ContextTypeName 欄位時，也可防止功能性的問題，其中在.NET 4.0 中的 EntityDataSource 時擲回 ReflectionTypeLoadException 它無法透過反映的組件載入的類型。 在.NET 4.5 中已修正此問題。

### <a name="75-------poco-entities-and-change-tracking-proxies"></a>7.5 的 POCO 實體和變更追蹤 proxy

Entity Framework 可讓您使用自訂資料類別加上您的資料模型，而不需要對資料類別本身進行任何修改。 這表示您可以使用「單純」(plain-old) CLR 物件 (POCO)，例如現有的網域物件，加上您的資料模型。 這些 POCO 資料類別 （也稱為非持續物件），這會對應到資料模型中定義的實體，支援的大部分相同的查詢、 插入、 更新和刪除行為 Entity Data Model 工具所產生的實體類型。

Entity Framework 也可以建立衍生自您的 POCO 類型，您想要啟用功能，例如消極式載入和自動變更追蹤 POCO 實體時所使用的 proxy 類別。 您的 POCO 類別必須符合特定需求，才能讓 Entity Framework，才可使用 proxy，如下所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。

機會追蹤 proxy 會通知物件狀態管理員每任何的次實體的屬性有變更，其值，讓 Entity Framework 知道您的實體的實際狀態的時間。 這是藉由將通知事件加入至您的屬性 setter 方法的主體並處理這類事件的物件狀態管理員。 請注意，建立 proxy 實體通常會是成本高於建立非 proxy 的 POCO 實體，因為已新增的 Entity Framework 所建立的事件集。

當 POCO 實體並沒有變更追蹤 proxy 時，藉由比較您針對先前已儲存狀態的複本的實體的內容中發現有所變更。 這個深度的比較會變成耗時的程序中您的內容，有許多實體時，或當實體有非常大量的屬性，即使沒有任何變更後的最後一個比較發生。

摘要： 您將需支付對效能造成影響時建立的變更追蹤 proxy，但變更追蹤可協助您加速變更偵測程序，當您的實體有許多屬性，或當您在模型中有許多實體。 對於具有少數屬性所在的實體數量不會太多的實體，具有變更追蹤 proxy 不可能的很多優點。

## <a name="8-loading-related-entities"></a>8 載入相關實體

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 消極式載入 vs。積極式載入

Entity Framework 提供數個不同的方式，將會關聯至您的目標實體的實體。 例如，當您查詢產品時，有許多種相關的訂單將會載入到物件狀態管理員。 從效能觀點來看，來載入相關的實體時，請考慮將最大問題，也將會使用消極式載入，或積極式載入。

當使用積極式載入，以及您的目標實體集載入相關的實體。 您可以在查詢中使用 Include 陳述式，以指出哪些相關的實體，您想要納入。

使用消極式載入，當您最初的查詢只會顯示目標實體集裡。 但每當存取導覽屬性時，另一個查詢會發出對存放區載入相關的實體。

實體載入之後，實體的任何進一步的查詢會將其載入直接從物件狀態管理員中，不論您使用消極式載入 」 或 「 積極式載入。

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 如何選擇消極式載入，並積極式載入

重點是，讓您可以讓您的應用程式中的正確的選擇，了解消極式載入，並積極式載入的差異。 這將協助您評估對資料庫與單一要求可能包含大型承載的多個要求之間的權衡取捨。 它可能適用於在您的應用程式的某些部分的積極式載入和消極式載入其他組件中。

例如在幕後發生了什麼，假設您想要住在英國和其訂單計數的客戶查詢。

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

當使用積極式載入，您將會發出單一查詢，傳回所有客戶] 和 [所有的訂單。 存放區命令看起來像：

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

使用消極式載入，當您將一開始發出下列查詢：

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

然後，每次存取客戶的訂單導覽屬性對存放區，就會發出另一個查詢，如下所示：

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

如需詳細資訊，請參閱 <<c0> [ 載入相關物件](https://msdn.microsoft.com/library/bb896272.aspx)。

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 消極式載入與積極式載入小祕技

沒有所謂一體適用選擇積極式載入和消極式載入的比較。 第一次嘗試了解這兩種策略，因此您可以執行也是明智的決策; 之間的差異此外，請考慮您的程式碼是否符合任何下列案例：

| 情節                                                                    | 我們建議                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 您需要從擷取的實體存取許多的導覽屬性嗎？ | **否**-可能會進行這兩個選項。 不過，如果將您的查詢內容不是太大，您可能會遇到效能優勢，使用積極式載入，因為它會在較不需要進行網路往返來具體化您的物件。 <br/> <br/> **是**-如果您需要從實體存取許多的導覽屬性，一樣，使用多個 include 陳述式在查詢中使用積極式載入。 包含多個實體，越大裝載您的查詢會傳回。 一旦您在查詢中包含三個或多個實體，請考慮改為載入的延遲。 |
| 您知道哪些資料需要在執行階段的完全嗎？                   | **否**-消極式載入會更好。 否則，您可能會得到查詢資料，您不需要。 <br/> <br/> **是**-積極式載入可能是最好的辦法，有助於更快載入整個集合。 如果您的查詢可讓您需要擷取非常大量的資料，而這變得太慢，請嘗試改為載入的延遲。                                                                                                                                                                                                                                                       |
| 遠離您的資料庫執行的程式碼嗎？ （增加的網路延遲）  | **否**-當網路延遲不是問題，請使用消極式載入可能會簡化您的程式碼。 請記住，您的應用程式的拓撲，可能會變更，因此不需要資料庫相近，授與。 <br/> <br/> **是**-網路問題時，只有您可以決定更加符合您的案例。 通常積極式載入會是較佳因為它需要較少的來回行程。                                                                                                                                                                                                      |


#### <a name="822-------performance-concerns-with-multiple-includes"></a>8.2.2 使用多個包含效能考量

我們聽到了涉及伺服器回應時間問題的效能問題，問題的來源時經常使用多個 Include 陳述式查詢。 雖然在查詢中包括相關的實體是功能強大，務必了解在幕後的事情。

花費較長的時間，針對具有多個 Include 陳述式的查詢來瀏覽我們的內部的計劃編譯器產生的存放區命令。 這些時間大部分是花在嘗試最佳化產生的查詢。 產生的存放區命令將包含 Outer Join 或 Union，每個 include，根據您的對應。 這類查詢會顯示在已連線的大型圖表中從您的資料庫，在單一的承載，將 acerbate 任何頻寬的問題，尤其是當有很多 （例如，當包含的多個層級用來周遊承載中的備援中的關聯來從一對多方向）。

您可以檢查其中查詢會傳回過大承載 ToTraceString 的使用，並在 SQL Server Management Studio，若要查看的承載大小執行存放區命令，以存取基礎的 TSQL 查詢的情況。 在此情況下，您可以嘗試減少為只有在查詢中的 Include 陳述式的數目會納入所需的資料。 或者，您可以將查詢分割成較小的一連串的子查詢，例如：

**之前的重大查詢：**

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

**之後的重大查詢：**

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

這會適用於只追蹤查詢，因為我們正在使用的內容必須自動執行識別解析和關聯的修復能力。

如同消極式載入，代價會是較小的裝載多個查詢。 您也可以使用 投射的個別屬性，明確地從每個實體中，選取所需的資料，但您將不會載入實體在此情況下，並不支援更新。

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 因應措施若要取得屬性的消極式載入

Entity Framework 目前不支援純量或複雜屬性的消極式的載入。 不過，在您用來有資料表，其中包含大型的物件，例如 BLOB 的情況下，您可以使用資料表分割分成個別的實體中的較大的屬性。 例如，假設您有 Product 資料表包含 varbinary photo 資料行。 如果您沒有經常需要存取這個屬性，在查詢中的，您可以使用資料表分割，以便納入實體，您通常需要的部分。 當您明確需要時，將只會載入代表產品相片的實體。

說明如何啟用資料表分割的絕佳資源是 Gil Fink 「 資料表分割中實體架構 」 部落格文章： \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>。

## <a name="9-other-considerations"></a>9 其他考量

### <a name="91------server-garbage-collection"></a>9.1 伺服器記憶體回收

某些使用者可能會遇到限制他們預期的記憶體回收行程的設定不正確的平行處理原則的資源爭用。 每當 EF 會在多執行緒案例中，或在任何應用程式，類似於伺服器端系統，請務必啟用伺服器記憶體回收。 這是透過簡單的設定，在您的應用程式組態檔中：

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

這應該減少執行緒爭用，並在 CPU 飽和的情況下最多 30%的增加您的輸送量。 一般而言，您一定要測試您的應用程式的運作方式使用傳統記憶體回收 （其進一步調整 UI 和用戶端端案例中） 以及伺服器記憶體回收。

### <a name="92------autodetectchanges"></a>9.2 AutoDetectChanges

如先前所述，Entity Framework 可能會顯示效能問題，當物件快取具有許多實體。 某些作業，例如 Add、 Remove、 尋找、 項目和 SaveChanges，觸發程序呼叫 DetectChanges，這可能會耗用大量的基礎物件快取已變成多大的 CPU。 這是物件快取，物件狀態管理員嘗試將保持同步處理每個執行的作業內容，以便能夠在各式各樣的案例下正確確保產生的資料越好。

它通常是最好的作法是將啟用您的應用程式的整個生命週期的 Entity Framework 自動變更偵測。 如果您的案例受高 CPU 使用量造成負面影響，而且您的設定檔可讓您表示問題是呼叫 DetectChanges，請考慮暫時關閉 AutoDetectChanges，在您的程式碼的敏感部分：

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

關閉前 AutoDetectChanges，最好了解這可能會導致失去其同時可追蹤特定資訊將會在實體的變更的 Entity Framework。 如果不正確地處理，這可能造成資料不一致，在您的應用程式。 如需有關如何關閉 AutoDetectChanges 的詳細資訊，請閱讀\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。

### <a name="93------context-per-request"></a>9.3 每個要求的內容

Entity Framework 內容應該要當做存留較短的執行個體，以提供最佳效能體驗。 內容應該是短暫存留時間，並捨棄，並因此實作非常輕量和 reutilize 盡可能的中繼資料。 在 web 案例中很重要要謹記在心，在單一要求的持續時間超過沒有內容。 同樣地，在非 web 案例中，內容應捨棄根據不同的層級的快取在 Entity Framework 了解。 一般而言，其中一個應該避免在整個生命週期的應用程式，以及每個執行緒的內容和靜態內容的內容執行個體。

### <a name="94------database-null-semantics"></a>9.4 資料庫 null 語意

預設的 entity Framework 會產生具有 C 的 SQL 程式碼\#null 比較語意。 請考慮下列範例查詢：

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

在此範例中，我們要比較數個可為 null 的變數，針對實體，例如 SupplierID 和 UnitPrice 可為 null 的屬性。 如果參數值的資料行值相同，則參數和資料行值都是 null，則會要求這項查詢產生的 SQL。 這會隱藏資料庫伺服器處理 null 值的方式，也會提供一致的 C\# null 跨不同的資料庫供應商的體驗。 相反地，產生的程式碼一點迂迴，無法執行時，也比較量大的數字，在 where 成長查詢陳述式。

處理這種情況的一個方式是使用資料庫 null 語意。 請注意，這可能會潛在的行為不同於 C\# null 語意之後, 現在 Entity Framework 會產生更簡單的 SQL 資料庫引擎處理 null 值的方式公開 （expose)。 資料庫 null 語意，可以是啟動每個內容與內容組態對單一組態一行：

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

小型到中型大小的查詢將不會顯示可察覺的效能改進時使用資料庫 null 語意，但是差異將會更明顯且含有大量潛在 null 比較的查詢。

在上述範例查詢中，效能差異雖然很低於 2 %microbenchmark，在受控制的環境中執行中。

### <a name="95------async"></a>9.5 非同步

Entity Framework 6 引進了支援的執行在.NET 4.5 或更新版本時的非同步作業。 大部分的情況下，使 IO 的應用程式相關的競爭會受益於最多使用非同步查詢並儲存作業。 如果您的應用程式不會發生從 IO 爭用，非同步處理使用，在最佳的情況下，以同步方式執行和傳回結果，在相同的時間與同步呼叫，或最糟的情況、 只是延後執行非同步工作，並新增額外的 time 完成您的案例。

如需如何非同步的程式設計工作可協助您決定是否非同步會改善您的應用程式的效能請瀏覽[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)。 如需使用 Entity Framework 上的非同步作業的詳細資訊，請參閱 <<c0> [ 非同步查詢和儲存](~/ef6/fundamentals/async.md
)。

### <a name="96------ngen"></a>9.6 NGEN

Entity Framework 6 不會在.NET framework 的預設安裝中。 因此，Entity Framework 組件不會根據預設，這表示所有 Entity Framework 程式碼，受都限於相同 JIT'ing 成本為其他 MSIL 組件的 NGEN。 這可能會降低的 F5 體驗，同時開發以及您在生產環境中的應用程式的冷啟動。 若要降低 CPU 和記憶體的成本 JIT'ing 就建議您在 Entity Framework 映像為適當的 NGEN。 如需有關如何改善使用 NGEN 的 Entity Framework 6 的啟動效能的詳細資訊，請參閱 < [ngen 改善啟動效能](~/ef6/fundamentals/performance/ngen.md)。

### <a name="97------code-first-versus-edmx"></a>9.7 版，code First 與 EDMX

問題有關的阻抗不相符的物件導向程式設計與由概念模型 （物件）、 儲存結構描述 (database) 和之間的對應的記憶體中表示的關聯式資料庫之間的 entity Framework 原因兩個。 此中繼資料會呼叫簡短的實體資料模型或 EDM。 此 EDM 中，從 Entity Framework 會衍生往返的資料的檢視，從資料庫的記憶體中的物件，並備份。

當 Entity Framework 搭配使用 EDMX 檔案也正式指定概念模型、 儲存結構描述和對應，則模型載入階段只有來驗證 EDM 是否正確 （例如，請確定沒有對應沒有），然後產生檢視，然後驗證的檢視，並已備妥此中繼資料。 只執行然後可以查詢或新的資料儲存至資料存放區。

Code First 方法是，它的核心，複雜的實體資料模型產生器。 Entity Framework 必須產生 EDM，從提供的程式碼;它是藉由分析在模型中，套用慣例和設定此模型透過 Fluent API 所涉及的類別。 建立 EDM 之後，Entity Framework 基本上行為相同方式會有 EDMX 檔案已存在於專案中。 因此，從 Code First 建立模型所增加的 Entity framework，相較於具有 EDMX 會轉譯成速度較慢的啟動時間的額外複雜性。 成本是完全取決於大小和正在建置之模型的複雜度。

在選擇要使用 Code First 與 EDMX 時，務必知道，Code First 所導入的彈性，將會增加第一次建立模型的成本。 如果您的應用程式可以承受此第一次負載的成本則通常 Code First 會前往的慣用的方法。

## <a name="10-investigating-performance"></a>10 調查效能

### <a name="101-using-the-visual-studio-profiler"></a>10.1 使用 Visual Studio Profiler

如果您有使用 Entity Framework 的效能問題，您可以使用像建置到 Visual Studio 分析工具，以查看應用程式花費的時間。 這是我們用來產生圓形圖 」 瀏覽 ADO.NET Entity Framework 的效能-第 1 部分 」 部落格文章中的工具 ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>)說明 Entity Framework 要花費在它在冷和暖查詢期間的時間。

資料和模型化客戶諮詢小組所撰寫的 「 使用 Visual Studio 2010 Profiler 分析 Entity Framework 」 部落格文章顯示如何它們使用分析工具來調查效能問題的真實世界範例。  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>。 這篇文章是針對 windows 應用程式所撰寫。 如果您要分析 web 應用程式的 Windows 效能記錄程式 (WPR) 和 Windows Performance Analyzer (WPA) 工具可能會優於從 Visual Studio 工作運作。 WPR 和 WPA 屬於 Windows 效能工具組所附含的 Windows 評定及部署套件 ( [http://www.microsoft.com/en-US/download/details.aspx?id=39982](https://www.microsoft.com/en-US/download/details.aspx?id=39982))。

### <a name="102-applicationdatabase-profiling"></a>10.2 應用程式/資料庫程式碼剖析

內建於 Visual Studio 分析工具等工具會告訴您應用程式花費的時間。  程式碼剖析工具的另一種是可用的執行動態分析執行中應用程式，在實際執行或根據需求，進入生產階段前的，並尋找常見的錯誤和反向模式的資料庫存取權。

兩個販售的分析工具是 Entity Framework Profiler ( \<http://efprof.com>) ORMProfiler 和 ( \<http://ormprofiler.com>)。

如果您的應用程式是使用 Code First 在 MVC 應用程式，您可以使用 StackExchange 的 MiniProfiler。 Scott Hanselman 說明這項工具在他的部落格： \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>。

如需有關您的應用程式資料庫活動，Julie Lerman 的 MSDN Magazine 文章標題，請參閱程式碼剖析[Entity Framework 中的分析資料庫活動](https://msdn.microsoft.com/magazine/gg490349.aspx)。

### <a name="103-database-logger"></a>10.3 資料庫記錄器

如果您使用 Entity Framework 6 也請考慮使用內建記錄功能。 透過簡單的一行設定其活動記錄，即可指示內容的 資料庫屬性：

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

在此範例中的資料庫活動將會記錄至主控台，但您可以設定記錄檔的內容呼叫任何動作&lt;字串&gt;委派。

如果您想要啟用資料庫記錄，而不需要重新編譯，而且您使用 Entity Framework 6.1 或更新版本中，則可以藉由新增您的應用程式的 web.config 或 app.config 檔案中的攔截器。

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

如需有關如何不需要重新編譯移至 新增記錄\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>。

## <a name="11-appendix"></a>11 的附錄

### <a name="111-a-test-environment"></a>11.1 A.測試環境

這種環境會使用從用戶端應用程式在不同電腦上的資料庫中的 2 機器安裝程式。 機器會位於相同的機架，因此網路延遲是相對較低，但比單一電腦環境更逼真。

#### <a name="1111-------app-server"></a>11.1.1 應用程式伺服器

##### <a name="11111------software-environment"></a>11.1.1.1 軟體環境

-   Entity Framework 4 的軟體環境
    -   作業系統名稱： Windows Server 2008 R2 Enterprise SP1。
    -   Visual 的 Studio 2010 – Ultimate。
    -   Visual Studio 2010 SP1 （僅適用於某些比較）。
-   Entity Framework 5 和 6 軟體環境
    -   作業系統名稱： Windows 8.1 企業版
    -   Visual Studio 2013 – Ultimate。

##### <a name="11112------hardware-environment"></a>11.1.1.2 硬體環境

-   雙處理器: @ 2.27 GHz 的 intel （) mhz,8 CPU L5520 W3530、 2261 Mhz8 GHz、 4 個核心、 84 邏輯處理器。
-   2412 GB RamRAM。
-   136 GB SCSI250GB SATA 7200 rpm 3 GB/s 磁碟機將分割成 4 個磁碟分割。

#### <a name="1112-------db-server"></a>11.1.2 DB 伺服器

##### <a name="11121------software-environment"></a>11.1.2.1 軟體環境

-   作業系統名稱： Windows Server 2008 R28.1 Enterprise SP1。
-   SQL Server 2008 R22012。

##### <a name="11122------hardware-environment"></a>11.1.2.2 硬體環境

-   單一處理器： Intel （) mhz,8 CPU 2.27 GHz L5520，2261 MhzES-1620 0 @ 3.60 GHz，4 個核心，8 的邏輯處理器。
-   824 GB RamRAM。
-   465 GB ATA500GB SATA 7200 rpm 6 GB/s 磁碟機將分割成 4 個磁碟分割。

### <a name="112------b-query-performance-comparison-tests"></a>11.2 B.查詢效能比較測試

Northwind 模型用來執行這些測試。 使用 Entity Framework 設計工具，從資料庫產生。 然後，下列程式碼可用來比較查詢執行選項的效能：

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

### <a name="113-c-navision-model"></a>11.3 C.Navision 模型

Navision 資料庫是用來示範 Microsoft Dynamics – 導覽大型資料庫 產生的概念模型包含 1005年實體集和 4227 關聯集。 在測試中使用的模型是 「 一般 」 – 沒有繼承已新增給它。

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 查詢用於 Navision 測試

搭配 Navision 模型的 [查詢] 清單會包含 3 個類別的 Entity SQL 查詢：

##### <a name="11311-lookup"></a>11.3.1.1 查閱

簡單的查閱查詢沒有彙總

-   計數： 16232
-   範例：

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312-singleaggregating"></a>11.3.1.2 SingleAggregating

一般的 BI 查詢多個彙總，但沒有小計 （單一查詢）

-   計數： 2313年
-   範例：

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

其中 MDF\_SessionLogin\_時間\_max （） 做為模型中所定義：

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313-aggregatingsubtotals"></a>11.3.1.3 AggregatingSubtotals

BI 查詢與彙總小計 （透過所有的聯集）

-   計數： 178
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
