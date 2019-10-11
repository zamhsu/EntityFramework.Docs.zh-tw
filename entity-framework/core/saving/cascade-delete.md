---
title: 串聯刪除 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
uid: core/saving/cascade-delete
ms.openlocfilehash: af86383bad52c87d2874fa4f8eb247a656601312
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182017"
---
# <a name="cascade-delete"></a>串聯刪除

串聯刪除在資料庫術語中常用來描述一種允許在刪除一個資料列時自動觸發刪除相關資料列的特性。 EF Core 刪除行為也涵蓋一個密切相關的概念，就是在子實體與父系的關聯性已被切斷時便自動刪除子實體，這也通稱為「刪除失去關聯的項目」。

EF Core 實作數種不同的刪除行為，並允許設定個別關聯性的刪除行為。 EF Core 也實作一些慣例，可根據[關聯性的必要性](../modeling/relationships.md#required-and-optional-relationships)，為每個關聯性自動設定實用的預設刪除行為。

## <a name="delete-behaviors"></a>刪除行為
定義刪除行為時，是在 *DeleteBehavior* 列舉程式類型中定義，並可傳遞給 *OnDelete* Fluent API，以控制刪除主體/父系實體或切斷與相依/子系實體的關聯性是否應對相依/子系實體造成副作用。

當主體/父系實體被刪除或與子系的關聯性被切斷時，EF 可以採取三個動作：
* 可以刪除子系/相依項
* 可以將子系的外部索引鍵設定為 Null
* 子系保持不變

> [!NOTE]  
> 只有在使用 EF Core 來刪除主體實體且已將相依實體載入記憶體中 (亦即針對所追蹤的相依項) 的情況下，才會套用 EF Core 模型中所設定的刪除行為。 必須在資料庫中設定對應的串聯行為，才能確保沒有受到內容追蹤的資料會套用必要的動作。 如果您使用 EF Core 來建立資料庫，將會為您設定此串聯行為。

就上述第二個動作而言，如果外部索引鍵值不可為 Null，將其設定為 Null 就會無效。 (不可為 Null 的外部索引鍵等同於必要關聯性)。在這些情況下，EF Core 會追蹤外部索引鍵屬性是否被標示為 Null，直到呼叫 SaveChanges 為止，屆時會擲回例外狀況，因為該變更無法保存至資料庫。 這與從資料庫收到條件約束違規類似。

如下表中所列，有四種刪除行為。

### <a name="optional-relationships"></a>組織關係
針對選擇性關聯性 (可為空值的外部索引鍵)，您「可以」儲存 Null 外部索引鍵值，這會造成下列影響：

| 行為名稱               | 對記憶體中相依項/子系的影響    | 對資料庫中相依項/子系的影響  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| **Cascade**                 | 將實體刪除                   | 將實體刪除                   |
| **ClientSetNull** (預設值) | 將外部索引鍵屬性設定為 Null | None                                   |
| **SetNull**                 | 將外部索引鍵屬性設定為 Null | 將外部索引鍵屬性設定為 Null |
| **Restrict**                | None                                   | None                                   |

### <a name="required-relationships"></a>必要關係
針對必要關聯性 (不可為空值的外部索引鍵)，您「無法」儲存 Null 外部索引鍵值，這會造成下列影響：

| 行為名稱         | 對記憶體中相依項/子系的影響 | 對資料庫中相依項/子系的影響 |
|:----------------------|:------------------------------------|:--------------------------------------|
| **Cascade** (預設值) | 將實體刪除                | 將實體刪除                  |
| **ClientSetNull**     | SaveChanges 擲回例外狀況                  | None                                  |
| **SetNull**           | SaveChanges 擲回例外狀況                  | SaveChanges 擲回例外狀況                    |
| **Restrict**          | None                                | None                                  |

在上表中，「無」會造成條件約束違規。 例如，如果將主體/子系實體刪除，但未採取任何動作來變更相依項/子系的外部索引鍵，資料庫將可能因外部條件約速違規而在 SaveChanges 時擲回例外狀況。

概要說明：
* 如果您有父系不存在便無法存在的實體，而想要讓 EF 負責自動刪除子系，則請使用 *Cascade*。
  * 父系不存在便無法存在的實體通常會使用必要關聯性，就此關聯性而言，預設值為 *Cascade*。
* 如果您有父系為可有可無的實體，而想要讓 EF 負責為您將外部索引鍵設定為 Null，則請使用 *ClientSetNull*
  * 即使父系不存在也能存在的實體通常會使用選擇性關聯性，就此關聯性而言，預設值為 *ClientSetNull*。
  * 如果您想要讓資料庫在即使未載入子系實體的情況下，也嘗試將 Null 值傳播到子系外部索引鍵，則請使用 *SetNull*。 不過，請注意，資料庫必須要能夠支援此設定，以這種方式設定資料庫可能造成其他限制，這在實務上常常會造成此選項不切實際。 這就是為什麼 *SetNull* 並非預設值的原因。
* 如果您不想要讓 EF Core 自動刪除實體或自動將外部索引鍵設定為 Null，則請使用 *Restrict*。 請注意，這會要求您的程式碼手動將子系實體與其外部索引鍵值保持同步，否則將會擲回條件約束例外狀況。

> [!NOTE]
> 不同於 EF6，在 EF Core 中，串聯影響不會立即發生，而是只有在呼叫 SaveChanges 時才會發生。

> [!NOTE]  
> **EF Core 2.0 中的變更：** 在舊版中，[*限制*] 會使追蹤相依實體中的選擇性外鍵屬性設定為 null，而且是選擇性關聯性的預設刪除行為。 在 EF Core 2.0 中，則導入了 *ClientSetNull* 來代表該行為，並成為選擇性關聯性的預設值。 *Restrict* 的行為在調整後變成一律不會對相依實體產生任何副作用。

## <a name="entity-deletion-examples"></a>實體刪除範例

對於可供下載並執行的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)，以下程式碼是其中的一部分。 此範例示範刪除父系實體時，選擇性和必要關聯性的每個刪除行為會發生什麼情況。

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

讓我們逐步進行每個變化來了解會發生什麼情況。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>與必要或選擇性關聯性搭配的 DeleteBehavior.Cascade

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* 部落格會標示為 Deleted
* 文章一開始會維持 Unchanged，因為串聯會等到執行 SaveChanges 時才發生
* SaveChanges 會先針對相依項/子系 (文章) 再針對主體/父系 (部落格) 傳送刪除
* 儲存之後，會將所有實體都中斷連結，因為現在已將它們從資料庫中刪除

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>與必要關聯性搭配的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* 部落格會標示為 Deleted
* 文章一開始會維持 Unchanged，因為串聯會等到執行 SaveChanges 時才發生
* SaveChanges 會嘗試將文章 FK 設定為 Null，但這會失敗，因為 FK 不可為空值

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>與選擇性關聯性搭配的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* 部落格會標示為 Deleted
* 文章一開始會維持 Unchanged，因為串聯會等到執行 SaveChanges 時才發生
* SaveChanges 會先嘗試將相依項/子系 (文章) 的 FK 都設定為 Null，再刪除主體/父系 (部落格)
* 儲存之後，會刪除主體/父系 (部落格)，但仍然會追蹤相依項/子系 (文章)
* 所追蹤相依項/子系 (文章) 的 FK 值現在會是 Null，且系統已移除它們對已刪除主體/父系 (部落格) 的參照

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>與必要或選擇性關聯性搭配的 DeleteBehavior.Restrict

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* 部落格會標示為 Deleted
* 文章一開始會維持 Unchanged，因為串聯會等到執行 SaveChanges 時才發生
* 由於 *Restrict* 會告知 EF 不要自動將 FK 設定為 Null，因此它會保持非 Null，而 SaveChanges 則會擲回例外狀況而不進行儲存

## <a name="delete-orphans-examples"></a>刪除失去關聯的項目範例

對於可供下載並執行的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)，以下程式碼是其中的一部分。 此範例示範切斷父系/主體與其子系/相依項之間的關聯性時，選擇性和必要關聯性的每個刪除行為會發生什麼情況。 在此範例中，會藉由從主體/父系 (部落格) 上的集合導覽屬性移除相依項/子系 (文章) 來切斷關聯性。 不過，如果改為將相依項/子系對主體/父系的參考設定為 Null，行為也會相同。

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

讓我們逐步進行每個變化來了解會發生什麼情況。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>與必要或選擇性關聯性搭配的 DeleteBehavior.Cascade

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* 文章會標示為 Modified，因為切斷關聯性造成將 FK 標示為 Null
  * 如果 FK 不可為 Null，則即使將其標示為 Null，實際值也不會變更
* SaveChanges 會針對相依項/子系 (文章) 傳送刪除
* 儲存之後，會將相依項/子系 (文章) 中斷連結，因為現在已將它們從資料庫中刪除

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>與必要關聯性搭配的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* 文章會標示為 Modified，因為切斷關聯性造成將 FK 標示為 Null
  * 如果 FK 不可為 Null，則即使將其標示為 Null，實際值也不會變更
* SaveChanges 會嘗試將文章 FK 設定為 Null，但這會失敗，因為 FK 不可為空值

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>與選擇性關聯性搭配的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* 文章會標示為 Modified，因為切斷關聯性造成將 FK 標示為 Null
  * 如果 FK 不可為 Null，則即使將其標示為 Null，實際值也不會變更
* SaveChanges 會將相依項/子系 (文章) 的 FK 都設定為 Null
* 儲存之後，相依項/子系 (文章) 的 FK 值現在會是 Null，且系統已移除它們對已刪除之主體/父系 (部落格) 的參照

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>與必要或選擇性關聯性搭配的 DeleteBehavior.Restrict

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* 文章會標示為 Modified，因為切斷關聯性造成將 FK 標示為 Null
  * 如果 FK 不可為 Null，則即使將其標示為 Null，實際值也不會變更
* 由於 *Restrict* 會告知 EF 不要自動將 FK 設定為 Null，因此它會保持非 Null，而 SaveChanges 則會擲回例外狀況而不進行儲存

## <a name="cascading-to-untracked-entities"></a>串聯至未追蹤的實體

當您呼叫 *SaveChanges* 時，串聯刪除規則將會套用至內容所追蹤的所有實體。 這是上述所有範例中發生的情況，也是為何產生 SQL 來同時刪除主體/父系 (部落格) 及所有相依項/子系 (文章) 的原因：

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

如果只載入主體 (例如對部落格進行查詢時，未使用可一併包含文章的 `Include(b => b.Posts)`)，則 SaveChanges 將只產生 SQL 來刪除主體/父系：

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

只有已在資料庫設定對應的串聯行為時，才會刪除相依項/子系 (文章)。 如果您使用 EF 來建立資料庫，將會為您設定此串聯行為。
