---
title: "串聯刪除-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
ms.technology: entity-framework-core
uid: core/saving/cascade-delete
ms.openlocfilehash: a9481fe851cc264ab3eaecad052c2e683ae57a44
ms.sourcegitcommit: 5367516f063cb42804ec92c31cdf76322554f2b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="cascade-delete"></a>串聯刪除

串聯刪除常用於資料庫詞彙來描述可讓刪除的資料列會自動觸發相關的資料列的刪除作業特性。 密切相關的概念也涵蓋在 EF 核心刪除行為是子實體的父系的關聯性時自動刪除已執行過-通常稱為 「 刪除的遺棄項目 」 這個 i。

EF 核心實作數個不同的刪除行為，並可讓個別的關聯性的刪除行為的組態。 EF 核心也會實作自動設定 [關聯性的 requiredness] 為基礎的每個關聯性的實用的預設刪除行為的慣例 (../modeling/relationships.md#required-and-optional-relationships)。

## <a name="delete-behaviors"></a>刪除行為
刪除中所定義的行為*DeleteBehavior*列舉值類型，而且可以傳遞至*OnDelete* fluent API 來控制是否刪除主體/父實體或的英國相依性/子實體的關聯性應該有副作用的相依性/子實體上。

有三個 EF 主體/父實體被刪除或切斷子系關聯性時，可以採取的動作：
* 您可以刪除子/相依性
* 您可以將子系的外部索引鍵值為 null
* 子系會保持不變

> [!NOTE]  
> 當主要實體會刪除使用 EF 核心，而且相依的實體不會載入記憶體中 （也就是追蹤相依性），只會套用在 EF 核心模式中設定刪除行為。 對應的重疊顯示行為必須能夠確保資料不受到內容追蹤資料庫中的安裝程式已套用的必要動作。 如果您使用 EF 核心來建立資料庫時，此重疊顯示行為會為您的安裝程式。

上述第二個動作，將外部索引鍵的值設定為 null 不是有效，如果外部索引鍵不是可為 null。 （非可為 null 的外部索引鍵就相當於必要的關聯性）。在這些情況下，EF 核心追蹤，外部索引鍵屬性已被標示為 null 呼叫 SaveChanges，因為變更無法保存至資料庫，發生例外狀況在這之前。 這是類似於從資料庫取得條件約束違規。

有四個 delete 行為，如下列表格中所列。 選擇性的關聯性 （可為 null 的外部索引鍵） 它_是_能夠儲存的 null 外部索引鍵值，而造成下列影響：

| 行為名稱 | 在記憶體中的相依性/子系上的效果 | 在資料庫中的相依性/子系上的效果
|-|-|-
| **重疊顯示** | 刪除實體 | 刪除實體
| **ClientSetNull** （預設值） | 外部索引鍵屬性會設定為 null | 無
| **SetNull** | 外部索引鍵屬性會設定為 null | 外部索引鍵屬性會設定為 null
| **限制** | 無 | 無

它是必要的關聯性 （非可為 null 的外部索引鍵）_不_能夠儲存的 null 外部索引鍵值，而造成下列影響：

| 行為名稱 | 在記憶體中的相依性/子系上的效果 | 在資料庫中的相依性/子系上的效果
|-|-|-
| **Cascade** （預設值） | 刪除實體 | 刪除實體
| **ClientSetNull** | SaveChanges 擲回 | 無
| **SetNull** | SaveChanges 擲回 | SaveChanges 擲回
| **限制** | 無 | 無

在上述資料表中*無*可能會導致條件約束違規。 比方說，如果主體/子實體已刪除，但若要變更相依/子系的外部索引鍵會採取任何動作，然後資料庫將可能擲回 SaveChanges 上因為外部索引條件約束違規。

在高的層級：
* 如果您有沒有父代，就無法存在的實體，而且您想要小心，自動刪除子系的 EF，然後使用*Cascade*。
  * 沒有父代通常使不能存在的實體，使用必要的關聯性， *Cascade*是預設值。
* 如果您有可能會或可能沒有父代，實體，而且您想 EF 需要小心的 null 外部索引鍵，然後使用*ClientSetNull*
  * 實體可以存在沒有父代通常使用於選擇性的關聯性，其中*ClientSetNull*是預設值。
  * 如果您想要同時嘗試甚至傳播到子外部索引鍵的 null 值的資料庫時的子實體未載入，然後使用*SetNull*。 不過，請注意，資料庫必須支援此功能，設定資料庫，就像這樣可能會導致其他限制，這樣實際上會使得這個選項並不實用。 這就是為什麼*SetNull*不是預設值。
* 如果您不想永遠自動刪除實體或 null 外部索引鍵時自動執行，然後使用 EF 核心*限制*。 請注意，這需要，您的程式碼保持子實體外部索引鍵值同步和手動否則條件約束將會擲回例外狀況。

> [!NOTE]
> 在 EF 核心，不同於 EF6，串聯效果不會發生立即，而是只呼叫 SaveChanges 是。

> [!NOTE]  
> **在 EF 核心 2.0 中的變更：**在舊版中，*限制*會導致追蹤的相依實體設為選擇性的外部索引鍵屬性為 null，且預設刪除選擇性的關聯性的行為。 在 EF 核心 2.0 中， *ClientSetNull*已引入來代表該行為，並成為選擇性的關聯性的預設值。 行為*限制*已調整為永遠不會相依的實體上有任何副作用。

## <a name="entity-deletion-examples"></a>實體刪除範例

下列程式碼是一部分[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)可以下載可執行的。 此範例會示範當刪除父實體時，每個刪除行為選擇性及必要的關聯性會發生什麼事。

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

讓我們逐步解說來了解這為什麼每個變化。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade 必要或選用的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
```

* 部落格會標示為已刪除
* 文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges
* SaveChanges 傳送刪除相依項目/子系 （文章） 和然後主體/父 （部落格）
* 儲存之後，所有實體已卸都離，因為它們現在已從資料庫刪除

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與必要的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* 部落格會標示為已刪除
* 文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges
* SaveChanges 嘗試將 post FK 設定為 null，但這會失敗，因為 FK 不是可為 null

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與選擇性的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
```

* 部落格會標示為已刪除
* 文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges
* SaveChanges 嘗試設定為 null 的兩個相依性/子系 （文章） FK，然後再刪除主體/父 （部落格）
* 儲存之後，主體/父系 （部落格） 被刪除，但仍然會追蹤相依性/子系 （文章）
* 追蹤相依性/子系 （文章） 現在有 null FK 值，並已移除其參考已刪除主體/父 （部落格）

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict 必要或選用的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* 部落格會標示為已刪除
* 文章一開始保持未變更，因為串聯，聯集不會發生直到 SaveChanges
* 因為*限制*告訴 EF 不會自動將 FK 設定為 null，則它仍然非 null，而不儲存的 SaveChanges 擲回

## <a name="delete-orphans-examples"></a>刪除孤立的範例

下列程式碼是一部分[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)可以下載可執行的。 此範例會示範切斷父/主體及其子系/相依性之間的關聯性時，每個刪除行為選擇性及必要的關聯性會發生什麼事。 在此範例中，關聯性切斷移除主體/父系 （部落格） 上的集合導覽屬性的相依性/子系 （文章）。 不過，行為會是相同，如果主體/父代參考從相依式改為 nulled 出。

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

讓我們逐步解說來了解這為什麼每個變化。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade 必要或選用的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '1' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
```

* 因為英國關聯性標示為 null FK 文章標示為已修改
  * 如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null
* SaveChanges 傳送刪除相依項目/子系 （文章）
* 儲存之後，相依性/子系 （文章） 會卸離，因為它們現在已從資料庫刪除

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與必要的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* 因為英國關聯性標示為 null FK 文章標示為已修改
  * 如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null
* SaveChanges 嘗試將 post FK 設定為 null，但這會失敗，因為 FK 不是可為 null

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull 與選擇性的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
```

* 因為英國關聯性標示為 null FK 文章標示為已修改
  * 如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null
* SaveChanges 設定這兩個相依性/子系 （文章） 的 FK 設為 null
* 儲存之後，現在有相依性/子系 （文章） 的 null FK 值且已移除其參考已刪除主體/父 （部落格）

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict 必要或選用的關聯性

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '1' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* 因為英國關聯性標示為 null FK 文章標示為已修改
  * 如果 FK 不是可為 null，然後實際的值不會變更即使標示為 null
* 因為*限制*告訴 EF 不會自動將 FK 設定為 null，則它仍然非 null，而不儲存的 SaveChanges 擲回

## <a name="cascading-to-untracked-entities"></a>階層式未被追蹤的實體

當您呼叫*SaveChanges*，串聯刪除規則將套用到內容正在追蹤的任何實體。 這是在所有情況的範例中，如上所示，這就是為什麼已產生 sql 陳述式來刪除主體/父 （部落格） 和所有相依項目/子系 （文章）：

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

如果只有主體已載入-例如，查詢進行時不含部落格`Include(b => b.Posts)`成也包括文章-然後 SaveChanges 只會產生 SQL 來刪除主體/父系：

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

如果資料庫有對應的重疊顯示行為設定，只會刪除相依項目/子系 （文章）。 如果您使用 EF 來建立資料庫時，此重疊顯示行為會為您的安裝程式。
