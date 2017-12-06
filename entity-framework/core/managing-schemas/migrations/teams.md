---
title: "在小組環境的 EF Core 中移轉"
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 40cbc1c1bb0273bf733fadb884bffadcceeb162b
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="migrations-in-team-environments"></a>在小組環境中的移轉
===============================
當小組環境中使用移轉，注意額外模型快照集檔案。 這個檔案會告訴您是否隊友的移轉會合併完全與您的是否您需要解決衝突，請重新建立您的移轉前共用文件。

<a name="merging"></a>合併
-------
當您合併從您的小組成員的移轉時，您可能取得衝突您模型的快照集檔案中。 如果這兩項變更是不相關，merge 是簡單式，可同時存在兩個移轉。 例如，可能看起來像這樣的 customer 實體類型設定中會出現合併衝突：

    <<<<<<< Mine
    b.Property<bool>("Deactivated");
    =======
    b.Property<int>("LoyaltyPoints");
    >>>>>>> Theirs

因為這兩個屬性需要最終模型中存在，請加入這兩個屬性完成合併。 在許多情況下，版本控制系統可能會自動為您合併這類變更。

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

在這些情況下，您的移轉和移轉您的小組成員會彼此獨立。 其中任一無法先套用，因為您不需要任何額外變更您的移轉之前與您的小組共用。

<a name="resolving-conflicts"></a>解決衝突
-------------------
有時您可能會遇到，則為 true 的衝突合併模型快照集時。 例如，您和您的小組可能每個重新命名相同的屬性。

    <<<<<<< Mine
    b.Property<string>("Username");
    =======
    b.Property<string>("Alias");
    >>>>>>> Theirs

如果您遇到這類衝突，解決重新建立您的移轉。 請依照下列步驟：

1. 「 合併 」 和 「 復原您在合併之前的工作目錄為中止
2. 移除您的移轉 （但保留模型的變更）
3. 將您的小組成員變更合併至您的工作目錄
4. 重新加入您的移轉

執行此動作後的兩個移轉作業都可以套用正確的順序。 其移轉會先套用，重新命名的資料行*別名*，之後您移轉重新命名它*Username*。

小組的其餘部分，可以安全地共用您的移轉。
