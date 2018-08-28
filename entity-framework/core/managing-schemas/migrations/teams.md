---
title: 在小組環境-EF Core 移轉
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: e8ff7f468d5ab6dbd6285f1abf9199e413288d10
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997691"
---
<a name="migrations-in-team-environments"></a>在小組環境中的移轉
===============================
在小組環境中使用移轉，請特別注意模型快照集檔案。 此檔案會告訴您，如果您小組成員的移轉合併完全與您的記錄，或如果您需要重新建立您的移轉，才能共用它所解決的衝突。

<a name="merging"></a>合併
-------
當您合併移轉從您的小組成員時，您可能會收到您模型的快照集檔案中衝突。 如果這兩個變更無關，只是一般規則合併，並可同時存在兩個移轉。 例如，可能看起來像這樣的客戶實體型別組態中會出現合併衝突：

    <<<<<<< Mine
    b.Property<bool>("Deactivated");
    =======
    b.Property<int>("LoyaltyPoints");
    >>>>>>> Theirs

因為這兩個屬性都必須存在於最終模型中，請加入這兩個屬性以完成合併。 在許多情況下，您的版本控制系統可能會自動為您合併這類變更。

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

在這些情況下，您的移轉和移轉您的小組成員會彼此獨立。 由於其中之一可以先套用，您不需要對您的移轉，才能分享您小組中的任何其他變更。

<a name="resolving-conflicts"></a>解決衝突
-------------------
有時候您會遇到，則為 true 的衝突合併 model 快照集時。 例如，您和您的小組可能每個已重新命名相同的屬性。

    <<<<<<< Mine
    b.Property<string>("Username");
    =======
    b.Property<string>("Alias");
    >>>>>>> Theirs

如果您遇到這類衝突，則會解析重新建立您的移轉。 請依照下列步驟：

1. 中止 「 合併 」 和 「 回復到您在合併之前的工作目錄
2. 移除您的移轉 （但保留模型的變更）
3. 您的小組成員變更合併至您的工作目錄
4. 重新加入您的移轉

完成之後，兩個移轉可以套用正確的順序。 移轉會先套用，重新命名的資料行*別名*，之後您的移轉重新命名它*Username*。

與小組的其餘部分，可以安全地共用您的移轉。
