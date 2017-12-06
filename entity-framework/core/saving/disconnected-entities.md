---
title: "中斷連接的實體-EF 核心"
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
ms.assetid: 2533b195-d357-4056-b0e0-8698971bc3b0
ms.technology: entity-framework-core
uid: core/saving/disconnected-entities
ms.openlocfilehash: b9d9662ce277e4f7b3d6f997a5117a0592f59fa3
ms.sourcegitcommit: c72d85805db0aa95f980514a18381fdc5e17c786
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="disconnected-entities"></a>中斷連接的實體

DbContext 執行個體將會自動追蹤從資料庫傳回的實體。 當呼叫 SaveChanges，並視需要將更新的資料庫，然後將偵測這些實體所做的變更。 請參閱[基本儲存](basic.md)和[相關資料](related-data.md)如需詳細資訊。

不過，有時候實體會讓查詢使用一個內容執行個體，然後使用不同的執行個體來儲存。 這通常發生在 已中斷連線 」 的情況下，例如 web 應用程式，其中實體的查詢、 傳送至用戶端、 修改、 傳送至伺服器，在要求中，而且再儲存。 在此情況下，第二個內容執行個體需要知道實體是否為新 （應插入），或現有的 （應該更新）。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/)GitHub 上。

## <a name="identifying-new-entities"></a>識別新的實體

### <a name="client-identifies-new-entities"></a>用戶端識別出新的實體

簡單的情況下，處理時，用戶端通知伺服器實體是新的或現有。 例如，通常要插入新實體的要求與不同更新現有實體的要求。

本章節的其餘部分涵蓋的情況下，必須決定是以其他某種方式來插入或更新。

### <a name="with-auto-generated-keys"></a>使用自動產生的索引鍵

自動產生的索引鍵的值通常可用來判斷實體是否需要插入或更新。 如果機碼不具有已設定 （也就是它仍然具有 null、 零 」 等的 CLR 預設值），則必須是新實體，而且需要插入。 相反地，如果已設定的索引鍵值，然後它必須已先前儲存，現在需要更新。 換句話說，如果索引鍵的值，然後實體查詢，傳送至用戶端，而現在是回更新。

很容易知道實體類型時，檢查為未設定的索引鍵：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewSimple)]

不過，EF 也有內建的方法，若要這樣做的任何實體類型和索引鍵類型：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> 索引鍵會設定為實體所追蹤的內容，即使實體處於 Added 狀態。 這有助於周遊的實體，並決定如何處理每個，如為使用 TrackGraph API 時圖形時。 索引鍵的值應該只用於如下所示的方式_之前_進行任何呼叫，以追蹤實體。

### <a name="with-other-keys"></a>其他索引鍵

其他一些機制才能識別新的實體時不會自動產生索引鍵值。 有兩種一般的方法，這個：
 * 查詢實體
 * 從用戶端傳遞旗標

若要查詢的實體，只要使用 Find 方法：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewQuery)]

已超出本文件顯示旗標傳遞從用戶端的完整程式碼的範圍。 在 web 應用程式中，通常表示不同針對提出要求不同的動作，或傳遞要求中的某種狀態，然後擷取控制器中。

## <a name="saving-single-entities"></a>儲存單一實體

如果已知是否需要插入或更新，則可以適當地使用新增或更新：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

不過，如果實體使用自動產生索引鍵值，然後更新方法可以用於這兩種情況：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

更新方法通常會將針對更新、 不插入實體標記。 不過，如果實體具有自動產生的金鑰，而且沒有索引鍵的值已設定，則實體改為自動標示為進行插入。

> [!TIP]  
> 此行為是在 EF 核心 2.0 引進。 較早的版本則一律需要明確地選擇 新增或更新。

如果實體不使用自動產生的索引鍵，則應用程式必須決定是否應該插入或更新實體： 例如：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

這裡的步驟如下：
* 如果尋找傳回 null，則資料庫已不包含具有此 ID，部落格，讓我們呼叫將標示插入作業。
* 如果尋找傳回實體，然後它存在於資料庫中，且內容現在追蹤現有的實體
  * 然後使用 SetValues 來自用戶端的這個實體上設定的所有屬性的值。
  * SetValues 呼叫會將標示為要視需要更新實體。

> [!TIP]  
> SetValues 只會將已修改具有不同的值中的追蹤實體的屬性。 這表示當更新傳送時，將會更新已實際變更這些資料行。 （和如果沒有任何變更，則不會更新將會寄完全）。

## <a name="working-with-graphs"></a>使用圖形

### <a name="all-newall-existing-entities"></a>所有新的或全部現有的實體

使用圖形的範例是插入或更新的部落格與它相關聯的文章的集合。 如果應該插入圖形中的所有實體，或應該更新所有，則處理程序會使用相同的單一實體的上述。 例如，部落格和文章像這樣建立的圖表：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

可插入像這樣：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertGraph)]

部落格和要插入的所有文章時，會將標記加入呼叫。

同樣地，如果在圖表中的所有實體都必須更新，然後更新可用：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#UpdateGraph)]

部落格和其所有的文章將會標示要更新。

### <a name="mix-of-new-and-existing-entities"></a>混用新的和現有的實體

具有自動產生索引鍵更新一次可用進行插入和更新，即使圖形包含混用需要插入的實體和需要更新：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

更新會將標示為圖形、 部落格或 post，如果它沒有機碼值組，而其他所有實體會都標示為更新的插入作業中的任何實體。

之前，不使用自動產生索引鍵時的查詢和某些處理，可以使用：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>處理刪除

刪除可能很困難，因為處理通常不存在的實體表示，應該刪除。 為了解決這個問題的一個方式是使用 「 虛刪除 」 的實體標記為刪除，而不實際刪除。 刪除，然後更新一樣。 非永久性刪除可以實作在使用[查詢篩選器](xref:core/querying/filters)。

為 true 的刪除，常見的模式是用於執行為何基本上 graph 差異查詢模式的延伸模組例如：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

就內部而言，新增、 Attach 和更新使用圖形周遊決定，對未變更對於是否它應該標記為加入 （若要插入），（若要更新） 的修改日期，每個實體 （不執行任何動作），或刪除 （若要刪除）。 這項機制會經由 TrackGraph API 公開。 例如，假設，當用戶端傳送回之實體的圖表，它會設定某些旗標指出它應該要如何處理每個實體。 TrackGraph 可用來處理這個旗標：

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#TrackGraph)]

旗標只會顯示為實體為了簡單起見，此範例的一部分。 通常旗標會是 DTO 或要求中包含的其他特定狀態的一部分。
