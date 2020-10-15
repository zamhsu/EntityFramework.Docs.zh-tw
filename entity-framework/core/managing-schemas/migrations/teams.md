---
title: 在小組環境中進行遷移-EF Core
description: 在 team 環境中使用 Entity Framework Core 管理遷移和解決衝突的最佳作法
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 90549b369624301bc183e5a8a3cc1d6a92bb7008
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062499"
---
# <a name="migrations-in-team-environments"></a>在小組環境中移轉

在小組環境中使用遷移時，請特別注意模型快照集檔案。 這個檔案可以告訴您，您的組員遷移是否與您的遷移完全相同，或者，如果您需要在共用之前重新建立您的遷移，以解決衝突。

## <a name="merging"></a>合併

當您合併小組成員時，您可能會在模型快照集檔案中發生衝突。 如果這兩個變更都不相關，則合併是很簡單的，而且這兩個遷移可以並存。 例如，您可能會在如下所示的 customer 實體類型設定中出現合併衝突：

```output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

因為這兩個屬性都必須存在於最終模型中，所以請加入這兩個屬性來完成合併。 在許多情況下，您的版本控制系統可能會自動為您合併這類變更。

```csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

在這些情況下，您的遷移和組員的遷移彼此獨立。 由於您可以先套用其中一種，因此在與小組共用之前，您不需要對您的遷移進行任何額外的變更。

## <a name="resolving-conflicts"></a>解決衝突

當您合併模型快照模型時，有時會遇到真正的衝突。 例如，您和您的組員可能會將相同的屬性重新命名。

```output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

如果您遇到這類衝突，請重新建立您的遷移來解決此問題。 請遵循下列步驟：

1. 在合併之前中止合併並回復至您的工作目錄
2. 移除您的遷移 (但保持您的模型變更) 
3. 將您的組員變更合併到您的工作目錄
4. 重新新增您的遷移

這麼做之後，就可以依正確順序套用這兩個遷移。 首先會套用其遷移，並將資料行重新命名為 *別名*，之後您的遷移就會將它重新命名為使用者 *名稱*。

您可以安全地與小組的其他人共用您的遷移。
