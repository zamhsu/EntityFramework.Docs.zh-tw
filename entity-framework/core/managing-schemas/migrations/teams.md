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
<a name="migrations-in-team-environments"></a><span data-ttu-id="f415e-102">在小組環境中的移轉</span><span class="sxs-lookup"><span data-stu-id="f415e-102">Migrations in Team Environments</span></span>
===============================
<span data-ttu-id="f415e-103">當小組環境中使用移轉，注意額外模型快照集檔案。</span><span class="sxs-lookup"><span data-stu-id="f415e-103">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="f415e-104">這個檔案會告訴您是否隊友的移轉會合併完全與您的是否您需要解決衝突，請重新建立您的移轉前共用文件。</span><span class="sxs-lookup"><span data-stu-id="f415e-104">This file can tell you if your teammate's migration merges cleanly with yours of if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

<a name="merging"></a><span data-ttu-id="f415e-105">合併</span><span class="sxs-lookup"><span data-stu-id="f415e-105">Merging</span></span>
-------
<span data-ttu-id="f415e-106">當您合併從您的小組成員的移轉時，您可能取得衝突您模型的快照集檔案中。</span><span class="sxs-lookup"><span data-stu-id="f415e-106">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="f415e-107">如果這兩項變更是不相關，merge 是簡單式，可同時存在兩個移轉。</span><span class="sxs-lookup"><span data-stu-id="f415e-107">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="f415e-108">例如，可能看起來像這樣的 customer 實體類型設定中會出現合併衝突：</span><span class="sxs-lookup"><span data-stu-id="f415e-108">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

    <<<<<<< Mine
    b.Property<bool>("Deactivated");
    =======
    b.Property<int>("LoyaltyPoints");
    >>>>>>> Theirs

<span data-ttu-id="f415e-109">因為這兩個屬性需要最終模型中存在，請加入這兩個屬性完成合併。</span><span class="sxs-lookup"><span data-stu-id="f415e-109">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="f415e-110">在許多情況下，版本控制系統可能會自動為您合併這類變更。</span><span class="sxs-lookup"><span data-stu-id="f415e-110">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="f415e-111">在這些情況下，您的移轉和移轉您的小組成員會彼此獨立。</span><span class="sxs-lookup"><span data-stu-id="f415e-111">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="f415e-112">其中任一無法先套用，因為您不需要任何額外變更您的移轉之前與您的小組共用。</span><span class="sxs-lookup"><span data-stu-id="f415e-112">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

<a name="resolving-conflicts"></a><span data-ttu-id="f415e-113">解決衝突</span><span class="sxs-lookup"><span data-stu-id="f415e-113">Resolving conflicts</span></span>
-------------------
<span data-ttu-id="f415e-114">有時您可能會遇到，則為 true 的衝突合併模型快照集時。</span><span class="sxs-lookup"><span data-stu-id="f415e-114">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="f415e-115">例如，您和您的小組可能每個重新命名相同的屬性。</span><span class="sxs-lookup"><span data-stu-id="f415e-115">For example, you and your teammate may each have renamed the same property.</span></span>

    <<<<<<< Mine
    b.Property<string>("Username");
    =======
    b.Property<string>("Alias");
    >>>>>>> Theirs

<span data-ttu-id="f415e-116">如果您遇到這類衝突，解決重新建立您的移轉。</span><span class="sxs-lookup"><span data-stu-id="f415e-116">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="f415e-117">請依照下列步驟：</span><span class="sxs-lookup"><span data-stu-id="f415e-117">Follow these steps:</span></span>

1. <span data-ttu-id="f415e-118">「 合併 」 和 「 復原您在合併之前的工作目錄為中止</span><span class="sxs-lookup"><span data-stu-id="f415e-118">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="f415e-119">移除您的移轉 （但保留模型的變更）</span><span class="sxs-lookup"><span data-stu-id="f415e-119">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="f415e-120">將您的小組成員變更合併至您的工作目錄</span><span class="sxs-lookup"><span data-stu-id="f415e-120">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="f415e-121">重新加入您的移轉</span><span class="sxs-lookup"><span data-stu-id="f415e-121">Re-add your migration</span></span>

<span data-ttu-id="f415e-122">執行此動作後的兩個移轉作業都可以套用正確的順序。</span><span class="sxs-lookup"><span data-stu-id="f415e-122">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="f415e-123">其移轉會先套用，重新命名的資料行*別名*，之後您移轉重新命名它*Username*。</span><span class="sxs-lookup"><span data-stu-id="f415e-123">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="f415e-124">小組的其餘部分，可以安全地共用您的移轉。</span><span class="sxs-lookup"><span data-stu-id="f415e-124">Your migration can safely be shared with the rest of the team.</span></span>
