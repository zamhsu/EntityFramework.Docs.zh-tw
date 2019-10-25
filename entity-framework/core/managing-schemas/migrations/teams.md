---
title: 在小組環境中進行遷移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: e6a1b86761a201cbcae34cced7e64f11df37a420
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811977"
---
# <a name="migrations-in-team-environments"></a>在小組環境中移轉

在小組環境中使用遷移時，請特別注意模型快照集檔案。 此檔案可告訴您組員的遷移是否與您完全整合，或您是否需要在共用之前重新建立您的遷移來解決衝突。

## <a name="merging"></a>合併

當您合併小組的遷移時，您可能會在模型快照集檔案中出現衝突。 如果這兩項變更無關，則合併很簡單，而且兩個遷移可以並存。 例如，您可能會在客戶實體類型設定中看到合併衝突，如下所示：

    <<<<<<< Mine
    b.Property<bool>("Deactivated");
    =======
    b.Property<int>("LoyaltyPoints");
    >>>>>>> Theirs

因為這兩個屬性都必須存在於最終模型中，所以請加入這兩個屬性來完成合併。 在許多情況下，您的版本控制系統可能會自動為您合併這類變更。

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

在這些情況下，您的遷移和組員的遷移彼此獨立。 因為其中一項可以先套用，所以您不需要對您的遷移進行任何額外的變更，就能與小組共用。

## <a name="resolving-conflicts"></a>解決衝突

有時候，在合併模型快照集模型時，您會遇到真正的衝突。 例如，您和您的組員可能會分別重新命名為相同的屬性。

    <<<<<<< Mine
    b.Property<string>("Username");
    =======
    b.Property<string>("Alias");
    >>>>>>> Theirs

如果您遇到這種衝突，請重新建立您的遷移來解決此問題。 請依照下列步驟：

1. 在合併之前中止合併並復原至您的工作目錄
2. 移除您的遷移（但保留您的模型變更）
3. 將您的組員變更合併到您的工作目錄
4. 重新加入您的遷移

這麼做之後，就可以依正確的順序套用兩個遷移。 首先會套用其遷移，將資料行重新命名為*別名*，之後您的遷移會將它重新命名為*Username*。

您的遷移可以安全地與小組的其他人共用。
