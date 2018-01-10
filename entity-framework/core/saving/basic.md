---
title: "基本的儲存層 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
ms.technology: entity-framework-core
uid: core/saving/basic
ms.openlocfilehash: e99d755b8f7c42b15a73cfdb6a2f8999a405a739
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="basic-save"></a>基本的儲存

了解如何新增、 修改和移除使用您的內容和實體類別的資料。

> [!TIP]  
> 您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/)GitHub 上。

## <a name="adding-data"></a>加入資料

使用*DbSet.Add*方法，將實體類別的新執行個體。 資料會插入資料庫中，當您呼叫*SaveChanges*。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

## <a name="updating-data"></a>更新資料

EF 會自動偵測現有的實體會受到內容追蹤時所做的變更。 這包括您載入/查詢從資料庫中的實體和實體，先前加入及儲存到資料庫。

只需修改指派給屬性的值，然後呼叫*SaveChanges*。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>刪除資料

使用*DbSet.Remove*方法，以刪除您的實體類別的執行個體。

若實體已經存在資料庫中，則會刪除期間*SaveChanges*。 如果實體尚未儲存到資料庫 （也就是它會追蹤中加入） 然後從內容中移除，並將無法再插入時*SaveChanges*呼叫。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>多個在單一的 SaveChanges 作業

您可以結合多項新增/更新/移除作業的單一呼叫*SaveChanges*。

> [!NOTE]  
> 對於大部分的資料庫提供者， *SaveChanges*為交易式。 這表示所有作業將會成功或失敗，作業將會永遠不會向左部分套用。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]