---
title: 已中斷連線的實體 - EF Core
description: 在 Entity Framework Core 中使用跨多個內容實例的已中斷連線、未追蹤實體
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: 1a86c28257449252a9cf7d3e1e65cd54bd5aa461
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023494"
---
# <a name="disconnected-entities"></a>已中斷連線的實體

DbContext 執行個體會自動追蹤從資料庫傳回的實體。 接著，在呼叫 SaveChanges 時，就會偵測已對這些實體進行的變更，然後將會視需要更新資料庫。 如需詳細資料，請參閱[基本儲存](xref:core/saving/basic)和[相關資料](xref:core/saving/related-data)。

不過，有時會在查詢實體時使用一個內容執行個體，然後儲存時又使用不同的執行個體。 這通常發生在「已中斷連線」的案例中，例如 Web 應用程式，其中會在要求中對實體進行查詢、傳送給用戶端、修改、傳回給伺服器，然後再儲存。 在此情況下，第二個內容執行個體必須知道實體是新的 (應該插入) 還是現有的 (應該更新)。

<!-- markdownlint-disable MD028 -->
> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Disconnected/) \(英文\)。

> [!TIP]
> 對於具有指定主索引鍵值的任何實體，EF Core 只能追蹤其中一個執行個體。 若要避免此情況成為問題，最佳方式就是針對每個工作單位都使用短期內容，讓內容從空白開始、有實體與其連結、儲存這些實體，然後再處置及捨棄內容。
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a>識別新實體

### <a name="client-identifies-new-entities"></a>用戶端識別新實體

最簡單的處理情況是由用戶端通知伺服器該實體是新的還是現有的。 例如，插入新實體的要求通常與更新現有實體的要求不同。

本節的其餘部分將涵蓋是否有必要以某種其他方式來判斷是要進行插入還是更新。

### <a name="with-auto-generated-keys"></a>使用自動產生的索引鍵

自動產生索引鍵的值通常可用來判斷是否需要對實體進行插入或更新。 如果尚未設定索引鍵 (亦即，仍具有 CLR 預設值 Null、零等)，則該實體必定是新的而需要插入。 另一方面，如果已設定索引鍵值，則表示實體先前必定已經儲存過，而現在是需要更新。 換句話說，如果索引鍵有值，即表示已對實體進行查詢、傳送給用戶端，而現在是返回來進行更新。

在實體類型為已知的情況下，檢查是否有未設定的索引鍵相當簡單：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

不過，EF 針對所有實體類型和索引鍵類型也提供一個內建的方法來執行此操作：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]
> 只要內容一追蹤實體，就會立即設定索引鍵，即使該實體處於 Added 狀態時也一樣。 周遊實體圖表並判斷要針對每個實體執行什麼動作時 (例如使用 TrackGraph API 時)，這會很有幫助。 使用索引鍵值時，應該只以這裡所示的方式在發出任何呼叫來追蹤實體「之前」使用。

### <a name="with-other-keys"></a>搭配其他索引鍵

當索引鍵值不是以自動方式產生時，需要一些其他機制來識別新的實體。 這包括兩種一般做法：

* 查詢實體
* 從用戶端傳遞旗標

若要查詢實體，請直接使用 Find 方法：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

本文件的範圍不包含顯示從用戶端傳遞旗標的完整程式碼。 在 Web 應用程式中，這通常意謂著針對不同的動作發出不同的要求，或是在要求中傳遞某種狀態，再於控制器中擷取該狀態。

## <a name="saving-single-entities"></a>儲存單一實體

如果已知所需的是插入還是更新，便可適當地使用 Add 或 Update：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

不過，如果實體使用自動產生的索引鍵值，則 Update 方法對這兩種情況都適用：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

Update 方法通常會將實體標示為要進行更新，而不是插入。 不過，如果實體有自動產生的索引鍵，且尚未設定任何索引鍵值，就會改為將實體標示為要進行插入。

如果實體未使用自動產生的金鑰，應用程式就必須判斷是應該插入還是更新實體：例如：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

步驟如下：

* 如果 Find 傳回 Null，即表示資料庫尚未包含具有此識別碼的部落格，因此我們會呼叫 Add 來將它標示為要進行插入。
* 如果 Find 傳回實體，則表示它存在於資料庫中，而內容現在正在追蹤該現有實體
  * 那麼，我們就會使用 SetValues 將此實體上所有屬性的值設定為那些來自用戶端的值。
  * SetValues 呼叫會將實體標示為視需要進行更新。

> [!TIP]
> SetValues 只會將值與所追蹤實體值不同的屬性標示為已修改。 這意謂著傳送更新時，將只會更新已實際變更的資料行。 (如果沒有任何變更，則不會傳送任何更新)。

## <a name="working-with-graphs"></a>使用圖表

### <a name="identity-resolution"></a>身分識別解析

如以上所述，EF Core 對於具有指定主索引鍵值的任何實體，只能追蹤其中一個執行個體。 使用圖表時，應該以理想的方式建立圖表來維持這個不變因素，而內容應該僅用於一個工作單位。 如果圖表包含重複項，則必須在將圖表傳送給 EF 之前，先處理圖表以將多個執行個體合併成一個。 如果執行個體具有衝突的值和關聯性，這可能會相當費工夫，因此應該在您的應用程式管線中儘快合併重複項，以避免需要解決衝突。

### <a name="all-newall-existing-entities"></a>所以新的/所有現有的實體

其中一個使用圖表的範例就是將部落格連同其相關文章集合一起進行插入或更新。 如果圖表中的所有實體都應該進行插入，或是全部都應該進行更新，則程序會與上述單一實體的程序相同。 例如，部落格和文章中圖表的建立方式如果像這樣：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

則插入方式可以像這樣：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

對 Add 的呼叫會將部落格及所有文章標示為要進行插入。

同樣地，如果圖表中的所有實體都需要進行更新，則可以使用 Update：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

部落格及其文章將會標示為要進行更新。

### <a name="mix-of-new-and-existing-entities"></a>新實體與現有實體的混合

使用自動產生的索引鍵時，可同樣使用 Update 來進行插入和更新，即使圖表包含需要插入與需要更新的混合實體時也一樣：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

Update 會將圖表中任何未設定索引鍵值的實體 (部落格或文章) 標示為要進行插入，所有其他實體則標示為要進行更新。

與之前相同，未使用自動產生的索引鍵時，可以使用查詢和某些處理：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>處理刪除

處理刪除可能相當棘手，因為當實體不存在時，常常意謂著應該將其刪除。 其中一個處理此情況的方式是使用「虛刪除」來將實體標示為已刪除，而不是實際進行刪除。 如此一來，刪除就變成與更新相同。 您可以在使用[查詢篩選](xref:core/querying/filters)時實作虛刪除。

針對真實的刪除，常見的模式是使用查詢模式的延伸來執行基本上是圖表差異的操作。 例如：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

就內部而言，Add、Attach 及 Update 會使用圖表周遊搭配針對每個實體所做的判斷，亦即應該將實體標示為 Added (要進行插入)、Modified (要進行更新)、Unchanged (不進行任何動作) 還是 Deleted (要進行刪除)。 這個機制會透過 TrackGraph API 公開。 例如，假設當用戶端傳回實體圖表時，會在每個實體上設定指出處理方式的旗標。 接著，便可使用 TrackGraph 來處理此旗標：

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

為了範例簡便起見，旗標僅顯示為實體的一部分。 一般而言，旗標會是 DTO 或要求中所包含某個其他狀態的一部分。
