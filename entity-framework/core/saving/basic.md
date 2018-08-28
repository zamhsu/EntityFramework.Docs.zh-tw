---
title: 基本儲存 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
ms.technology: entity-framework-core
uid: core/saving/basic
ms.openlocfilehash: ecf8f344a5baae37a5e7255a4affb1085f1b3ff3
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "42447748"
---
# <a name="basic-save"></a>基本儲存

了解如何使用您的內容和實體類別來新增、修改及移除資料。

> [!TIP]  
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/) \(英文\)。

## <a name="adding-data"></a>加入資料

使用 *DbSet.Add* 方法來新增實體類別的新執行個體。 當您呼叫 *SaveChanges*時，將會在資料庫中插入資料。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> 如[相關資料](related-data.md)一節中所述，Add、Attach 及 Update 方法對於傳遞給它們的整個實體圖表都適用。 或者，您也可以使用 EntityEntry.State 屬性來僅設定單一實體的狀態。 例如，`context.Entry(blog).State = EntityState.Modified`。

## <a name="updating-data"></a>更新資料

EF 會針對內容所追蹤的現有實體，自動偵測對實體所進行的變更。 這包括您從資料庫載入/查詢的實體，以及先前新增並儲存至資料庫的實體。

請直接修改指派給屬性的值，然後呼叫 *SaveChanges*。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>刪除資料

使用 *DbSet.Remove* 方法來刪除實體類別的執行個體。

如果實體已經存在於資料庫中，在 *SaveChanges* 期間將會予以刪除。 如果尚未將實體儲存至資料庫 (亦即，其追蹤狀態為已新增)，就會從內容中將其移除，而在呼叫 *SaveChanges* 時，便不再將它插入。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>在單一 SaveChanges 中執行多個作業

您可以將多個新增/更新/移除作業結合成對 *SaveChanges* 的單一呼叫。

> [!NOTE]  
> 對大多數資料庫提供者來說，*SaveChanges* 為交易式。 這意謂著所有作業不是成功就是失敗，作業一律不會只有部分套用的情況。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]
