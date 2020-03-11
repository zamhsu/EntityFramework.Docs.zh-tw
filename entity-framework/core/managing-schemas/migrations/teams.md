---
title: 在小組環境中進行遷移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 6c17c56277821159962884aef72d46c624442e20
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416767"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="16320-102">在小組環境中移轉</span><span class="sxs-lookup"><span data-stu-id="16320-102">Migrations in Team Environments</span></span>

<span data-ttu-id="16320-103">在小組環境中使用遷移時，請特別注意模型快照集檔案。</span><span class="sxs-lookup"><span data-stu-id="16320-103">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="16320-104">此檔案可告訴您組員的遷移是否與您完全整合，或您是否需要在共用之前重新建立您的遷移來解決衝突。</span><span class="sxs-lookup"><span data-stu-id="16320-104">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="16320-105">合併</span><span class="sxs-lookup"><span data-stu-id="16320-105">Merging</span></span>

<span data-ttu-id="16320-106">當您合併小組的遷移時，您可能會在模型快照集檔案中出現衝突。</span><span class="sxs-lookup"><span data-stu-id="16320-106">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="16320-107">如果這兩項變更無關，則合併很簡單，而且兩個遷移可以並存。</span><span class="sxs-lookup"><span data-stu-id="16320-107">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="16320-108">例如，您可能會在客戶實體類型設定中看到合併衝突，如下所示：</span><span class="sxs-lookup"><span data-stu-id="16320-108">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="16320-109">因為這兩個屬性都必須存在於最終模型中，所以請加入這兩個屬性來完成合併。</span><span class="sxs-lookup"><span data-stu-id="16320-109">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="16320-110">在許多情況下，您的版本控制系統可能會自動為您合併這類變更。</span><span class="sxs-lookup"><span data-stu-id="16320-110">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="16320-111">在這些情況下，您的遷移和組員的遷移彼此獨立。</span><span class="sxs-lookup"><span data-stu-id="16320-111">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="16320-112">因為其中一項可以先套用，所以您不需要對您的遷移進行任何額外的變更，就能與小組共用。</span><span class="sxs-lookup"><span data-stu-id="16320-112">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="16320-113">解決衝突</span><span class="sxs-lookup"><span data-stu-id="16320-113">Resolving conflicts</span></span>

<span data-ttu-id="16320-114">有時候，在合併模型快照集模型時，您會遇到真正的衝突。</span><span class="sxs-lookup"><span data-stu-id="16320-114">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="16320-115">例如，您和您的組員可能會分別重新命名為相同的屬性。</span><span class="sxs-lookup"><span data-stu-id="16320-115">For example, you and your teammate may each have renamed the same property.</span></span>

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="16320-116">如果您遇到這種衝突，請重新建立您的遷移來解決此問題。</span><span class="sxs-lookup"><span data-stu-id="16320-116">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="16320-117">請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="16320-117">Follow these steps:</span></span>

1. <span data-ttu-id="16320-118">在合併之前中止合併並復原至您的工作目錄</span><span class="sxs-lookup"><span data-stu-id="16320-118">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="16320-119">移除您的遷移（但保留您的模型變更）</span><span class="sxs-lookup"><span data-stu-id="16320-119">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="16320-120">將您的組員變更合併到您的工作目錄</span><span class="sxs-lookup"><span data-stu-id="16320-120">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="16320-121">重新加入您的遷移</span><span class="sxs-lookup"><span data-stu-id="16320-121">Re-add your migration</span></span>

<span data-ttu-id="16320-122">這麼做之後，就可以依正確的順序套用兩個遷移。</span><span class="sxs-lookup"><span data-stu-id="16320-122">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="16320-123">首先會套用其遷移，將資料行重新命名為*別名*，之後您的遷移會將它重新命名為*Username*。</span><span class="sxs-lookup"><span data-stu-id="16320-123">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="16320-124">您的遷移可以安全地與小組的其他人共用。</span><span class="sxs-lookup"><span data-stu-id="16320-124">Your migration can safely be shared with the rest of the team.</span></span>
